---
title : "문자열 조작 : 여러가지 솔루션과 내부구현 비교"



excerpt: "Python String Operation"

categories:
- Python
tags:
- [Python,Object,Builtin,PythonInternal,String]
# classes : wide
toc: true
toc_sticky: true
---
String은 Python에서 자주 사용하는 Built-in object이다.  object는 결국 process 하거나 create되는 대상이다. 같은 결과를 얻기 위한 다양한 opeartion들이 존재한다. 이 방법들을 비교해보자. 
## Concatenation
String Concatenation이란 2개 이상의 문자열을 연결하는 것을 의미합니다.  String Concatenation을 Python에서는 join method, + operator , append method를 통하여 수행할 수 있습니다. 각각의 Operation의 Internal Implementation을 통해 구체적으로 어떻게 concatenation이 일어나는 지를 알아보겠습니다.  
String의 Concatenation은 Cpython의  Objects/unicodeobject.c 파일에 구현되어있습니다. 
###  + operator , += operator
\+ operator는 PyUnicodeObject_Concat이라는 function으로 구현되어 있습니다.([링크](https://github.com/python/cpython/blob/1e4f00ebd8db44a031b61eed0803b4c3d731aed7/Objects/unicodeobject.c#L10944))  
이 function은 Python object의 최상위 토큰인 PyObject를 return합니다. 이 PyObject를 통해서 concatenation된 문자열을 return합니다.  내부 구현에서 핵심적인 코드들을 보면 아래와 같습니다. 
```c

    maxchar = PyUnicode_MAX_CHAR_VALUE(left);
    maxchar2 = PyUnicode_MAX_CHAR_VALUE(right);
    maxchar = Py_MAX(maxchar, maxchar2);
    new_len = left_len + right_len;
        /* Concat the two Unicode strings */
    result = PyUnicode_New(new_len, maxchar);
    if (result == NULL)
        return NULL;
    _PyUnicode_FastCopyCharacters(result, 0, left, 0, left_len);
    _PyUnicode_FastCopyCharacters(result, left_len, right, 0, right_len);
    assert(_PyUnicode_CheckConsistency(result, 1));
    return result;
```
여기서,left,right는 concatenation하고자 하는 string 2개를 의미합니다.  max_char를 계산하는 이유는 1byte만 사용하는지, 2byte를 사용하는지, 4byte를 사용하는지를 체크하기 위해서 입니다. 문자열의 인코딩 방식에 따라서 차지하는 메모리 크기가 달라지기에 체크를 해주는 것입니다. left의 길이와 right의 길이를 더하여 새로운 길이 new_len을 구합니다. new_len길이와 max_char를 사용하여 새로운 빈 문자열 result를 만듭니다. 이 result에 차례대로 left, right를 copy합니다. 그리고 , result를 return하게 됩니다.   
\+= operator는 위와 유사하게 left string에 right string을 copy하는 것입니다. 이 operation은 PyUnicode_Append라는 function으로 정의되어 있습니다. ([링크](https://github.com/python/cpython/blob/1e4f00ebd8db44a031b61eed0803b4c3d731aed7/Objects/unicodeobject.c#L10993
))  
내부 구현에서 중요한 부분은 다음과 같습니다.  
```c

    left_len = PyUnicode_GET_LENGTH(left);
    right_len = PyUnicode_GET_LENGTH(right);

    new_len = left_len + right_len;

    if (unicode_modifiable(left)
        && PyUnicode_CheckExact(right)
        && PyUnicode_KIND(right) <= PyUnicode_KIND(left)
        /* Don't resize for ascii += latin1. Convert ascii to latin1 requires
           to change the structure size, but characters are stored just after
           the structure, and so it requires to move all characters which is
           not so different than duplicating the string. */
        && !(PyUnicode_IS_ASCII(left) && !PyUnicode_IS_ASCII(right)))
    {
        /* append inplace */
        if (unicode_resize(p_left, new_len) != 0)
            goto error;

        /* copy 'right' into the newly allocated area of 'left' */
        _PyUnicode_FastCopyCharacters(*p_left, left_len, right, 0, right_len);
    }
    else {
        maxchar = PyUnicode_MAX_CHAR_VALUE(left);
        maxchar2 = PyUnicode_MAX_CHAR_VALUE(right);
        maxchar = Py_MAX(maxchar, maxchar2);

        /* Concat the two Unicode strings */
        res = PyUnicode_New(new_len, maxchar);
        if (res == NULL)
            goto error;
        _PyUnicode_FastCopyCharacters(res, 0, left, 0, left_len);
        _PyUnicode_FastCopyCharacters(res, left_len, right, 0, right_len);
        Py_DECREF(left);
        *p_left = res;
    }
    assert(_PyUnicode_CheckConsistency(*p_left, 1));
    return;


```

우선 PyUnicode_KIND로  left의 크기가 right의 크기보다 크고 , unicode_resize로 left가 new_len만큼의 크기로 resize가 가능한지 확인합니다. 만약에 가능하다면, left에 left_len 다음 위치에 right를 복사합니다.  그렇지 않다면 , PyUnicode_Concat과 같은 동작을 수행합니다. 
### Join
Join은 주어진 seperator를 이용하여 문자열 시퀀스를 연결하고 결과로 유니코드 문자열을 반환합니다. Join은 Cpython에서 _PyUnicode_JoinArray로 구현이 되어있습니다. ([링크](https://github.com/python/cpython/blob/1e4f00ebd8db44a031b61eed0803b4c3d731aed7/Objects/unicodeobject.c#L9586
))  
```c
for (i = 0; i < seqlen; i++) {
        size_t add_sz;
        item = items[i];
        add_sz = PyUnicode_GET_LENGTH(item);
        item_maxchar = PyUnicode_MAX_CHAR_VALUE(item);
        maxchar = Py_MAX(maxchar, item_maxchar);
        if (i != 0) {
            add_sz += seplen;
        }

        sz += add_sz;

        last_obj = item;
    }

    res = PyUnicode_New(sz, maxchar);
    /* Catenate everything. */
```
우선 join에 참여하는 시퀸스의 각 item의  size를  add_sz에 assign하고 첫번째 item 이후에는 seperator의 size를 더해줍니다. 이를 sz에 전부 더해주고 maxchar(최대 byte 수)와 sz를 이용해서 빈 unicode를 생성해줍니다. 

```c
if (use_memcpy) {
        for (i = 0; i < seqlen; ++i) {
            Py_ssize_t itemlen;
            item = items[i];

            /* Copy item, and maybe the separator. */
            if (i && seplen != 0) {
                memcpy(res_data,
                          sep_data,
                          kind * seplen);
                res_data += kind * seplen;
            }

            itemlen = PyUnicode_GET_LENGTH(item);
            if (itemlen != 0) {
                memcpy(res_data,
                          PyUnicode_DATA(item),
                          kind * itemlen);
                res_data += kind * itemlen;
            }
        }
        assert(res_data == PyUnicode_1BYTE_DATA(res)
                           + kind * PyUnicode_GET_LENGTH(res));
    }
    else {
        for (i = 0, res_offset = 0; i < seqlen; ++i) {
            Py_ssize_t itemlen;
            item = items[i];

            /* Copy item, and maybe the separator. */
            if (i && seplen != 0) {
                _PyUnicode_FastCopyCharacters(res, res_offset, sep, 0, seplen);
                res_offset += seplen;
            }

            itemlen = PyUnicode_GET_LENGTH(item);
            if (itemlen != 0) {
                _PyUnicode_FastCopyCharacters(res, res_offset, item, 0, itemlen);
                res_offset += itemlen;
            }
        }
        assert(res_offset == PyUnicode_GET_LENGTH(res));
    }


    return res;
```
memcpy를 사용할 수 있다면 , memcpy를 사용하여 sep* kind(문자size) 만큼 seperator를 복사해주고, 그 다음에 item을 memcpy를 사용하여 복사해줍니다. memcpy를 사용할 수 없다면 내부 copy functinon을 사용해서 copy해줍니다.

### Join vs operator

그렇다면 , Join과 operator중 어느것이 더 빠를까요? operator를 이용한 concatenation은 문자열 2개를 연결하지만, Join을 이용한 concatenation은 문자열의 시퀀스를 연결합니다. 여러개의 문자열을 연결한다면 , 문자열의 시퀸스의 개수만큼 새로운 result 문자열을 생성하고 복사하는 작업이 반복될 것입니다. 하지만, Join을 사용한다면 문자열 시퀸스의 길이의 총합 만큼 복사를 한번만 할 것입니다. 따라서, LowerBound는 operator가 더 크다고 볼 수 있습니다. 

## Search
이번에는 쿼리가 문자열의 substring인지 체크하는 동작에 대해 알아보도록 하겠습니다. 파이썬에서는 str.find() 메소드와 in operator를 통해서 substring인지 확인할 수 있습니다.
### find
파이썬에서 find는 any_find_slice -> ##_find_slice 순으로 호출하게 됩니다. 이를 찾다보면 ,ucslib,asciilib,ucs2lib,ucs4lib 이라는 접두사가 붙은 find_slice function을 호출하게 됩니다. 하지만, 이를 github에서 검색을 하게 되면 찾을 수 없습니다. 이는 다음과 같은 이유 때문입니다.
```c

#define STRINGLIB(F)             ucs1lib_##F

#define STRINGLIB(F)             ucs2lib_##F

#define STRINGLIB(F)             asciilib_##F

```  
위 매크로는 STRINGLIB(F) 는 접두사로 ucslib,ucs2lib,asciilib을 사용하겠다는 뜻입니다. 
```c

Py_LOCAL_INLINE(Py_ssize_t)
STRINGLIB(find_slice)(const STRINGLIB_CHAR* str, Py_ssize_t str_len,
                     const STRINGLIB_CHAR* sub, Py_ssize_t sub_len,
                     Py_ssize_t start, Py_ssize_t end)
{
    return STRINGLIB(find)(str + start, end - start, sub, sub_len, start);
}
```
실제로 Objects/stringlib/find.h에 위와 같은 function이 정의되어 있습니다. 즉 ,STRINGLIB(find_slice)는  ascliib_find_slice ,ucs1lib_find_slice,ucs2lib_findslice와 동일하다고 볼 수 있습니다. STRINGLIB(find_slice)는 내부적으로 booyer_more로 구현되어 있다고 합니다.

### in operator 

```c
static PySequenceMethods unicode_as_sequence = {
    (lenfunc) unicode_length,       /* sq_length */
    PyUnicode_Concat,           /* sq_concat */
    (ssizeargfunc) unicode_repeat,  /* sq_repeat */
    (ssizeargfunc) unicode_getitem,     /* sq_item */
    0,                  /* sq_slice */
    0,                  /* sq_ass_item */
    0,                  /* sq_ass_slice */
    PyUnicode_Contains,         /* sq_contains */
};



```
```c


    switch (kind1) {
    case PyUnicode_1BYTE_KIND:
        result = ucs1lib_find(buf1, len1, buf2, len2, 0) != -1;
        break;
    case PyUnicode_2BYTE_KIND:
        result = ucs2lib_find(buf1, len1, buf2, len2, 0) != -1;
        break;
    case PyUnicode_4BYTE_KIND:
        result = ucs4lib_find(buf1, len1, buf2, len2, 0) != -1;
        break;
    default:
        Py_UNREACHABLE();
    }

```


in operator는 내부적으로 PyUnicode_Contains를 호출하게 됩니다. PyUnicode_Contains는 find와 마찬가지로 STRINGLIB(find)을 사용합니다. 

### in vs find

in operator 나 find 모두 STRINGLIB function을 사용하므로 performance상 차이는 그닥 없어보일거 같습니다. 


## String Transformation
### map
### list comprehension

## Split
Python에서 문자열을 split 할 수 있는 방법은 re 라이브러리의 re.split을 사용하거나 built-in split을 사용하는 것입니다. 일반적으로 built-in split이 re보다 더 빠릅니다.
## replacing
마찬가지로, re 라이브러리의 re.sub 과 built-in method인 replace 2가지 방법이 있습니다.역시, built-in method인 str.replace()가 성능면에서 더 뛰어납니다 
## prefix , suffix
Python에서는 prefix나 suffix를 얻기위해서는 built-in method를 사용하거나 아니면 slicing을 활용하는 방법이 있습니다. 

### startswith,endswith


Python에서 suffix,prefix를 얻기 위한 built-in method에는 stratswith,endwith가 있습니다. startswith,endswith는 loop를 통해 startswith,endswith는 내부적으로 tailmatch function을 호출합니다.

```c

if (PyUnicode_READ(kind_self, data_self, offset) ==
        PyUnicode_READ(kind_sub, data_sub, 0) &&
        PyUnicode_READ(kind_self, data_self, offset + end_sub) ==
        PyUnicode_READ(kind_sub, data_sub, end_sub)) {
        /* If both are of the same kind, memcmp is sufficient */
        if (kind_self == kind_sub) {
            return ! memcmp((char *)data_self +
                                (offset * PyUnicode_KIND(substring)),
                            data_sub,
                            PyUnicode_GET_LENGTH(substring) *
                                PyUnicode_KIND(substring));
        }
        /* otherwise we have to compare each character by first accessing it */
        else {
            /* We do not need to compare 0 and len(substring)-1 because
               the if statement above ensured already that they are equal
               when we end up here. */
            for (i = 1; i < end_sub; ++i) {
                if (PyUnicode_READ(kind_self, data_self, offset + i) !=
                    PyUnicode_READ(kind_sub, data_sub, i))
                    return 0;
            }
            return 1;
        }
    }

    return 0;
```
tailmatch는 substring과 subject string의 size가 같으면 memcmp를 활용하여 비교를 하고 , 같지 않다면 모든 경우의 수를 따져서 비교해야 합니다. startswith,endswith의 경우 비교대상과 substring의 크기가 같기에 memcmp만 호출이 될 것입니다 .

### slicing  
```c
static PyMappingMethods unicode_as_mapping = {
    (lenfunc)unicode_length,        /* mp_length */
    (binaryfunc)unicode_subscript,  /* mp_subscript */
    (objobjargproc)0,           /* mp_ass_subscript */
};

```

Python 문자열에서는 mapping subscript를 unicode_subscript라는 function을 정의해서 사용한다고 정의되어 있습니다.
```c
        src_kind = PyUnicode_KIND(self);
        src_data = PyUnicode_DATA(self);
        if (!PyUnicode_IS_ASCII(self)) {
            kind_limit = kind_maxchar_limit(src_kind);
            max_char = 0;
            for (cur = start, i = 0; i < slicelength; cur += step, i++) {
                ch = PyUnicode_READ(src_kind, src_data, cur);
                if (ch > max_char) {
                    max_char = ch;
                    if (max_char >= kind_limit)
                        break;
                }
            }
        }
        else
            max_char = 127;
        result = PyUnicode_New(slicelength, max_char);
        if (result == NULL)
            return NULL;
        dest_kind = PyUnicode_KIND(result);
        dest_data = PyUnicode_DATA(result);

        for (cur = start, i = 0; i < slicelength; cur += step, i++) {
            Py_UCS4 ch = PyUnicode_READ(src_kind, src_data, cur);
            PyUnicode_WRITE(dest_kind, dest_data, i, ch);
        }
        assert(_PyUnicode_CheckConsistency(result, 1));
        return result;
```

unicode subscript의 내부구현을 보면  slice 길이만큼의 dest unicode를 생성후 character를 1개씩 dest에 write합니다.  

### slicing vs starswith,endswith
slicing은 substring 1개만을 비교하지만, startswith,endswith는 substring 여러개를 tuple형태로 인자로 받아서 비교할 수 있습니다. 


## reverse

파이썬에서 문자열을 뒤집는 방법은 2가지가 있습니다. slicing을 이용하거나 아니면 reversed와 join을 사용한 방법입니다.   slicing이나 join모두 dest unicode를 설정한 후 src data를 dest unicode에 copy합니다. 하지만,reversed를 호출하게 되면 그만큼의 overhead가 발생하여 slicing을 이용하는 방법이 더 빠를 것입니다. 


