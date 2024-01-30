---
title : "Integer Internal Implementation after 3.12"



excerpt: "Python Intger Internal"

categories:
- Python
tags:
- [Python,Object,Builtin,PythonInternal]
# classes : wide
toc: true
toc_sticky: true
---
# Integer Internal after Python 3.12

파이썬을 사용하면서 정수와 같은 numeric object를 사용하게 된다. 정수를 사용하다 보면, arbitary precision을 지원한다는 사실이 놀랍지만, 그냥 받아들이면서 했다. 이에 대해 신경쓰지 않아도 추상화가 잘 되어있기에 , 괜찮을 수 있다. 하지만, 좀 더 좋은 엔지니어가 되기 위해서는 high-level이아닌 low-level에서 어떻게 작동하는지 볼줄 알아야 한다고 생각한다. high-level로 추상화된 동작을 분해하는 것은 문제를 분해하는 것과 같은 이치기에 문제해결능력에 도움이 될 것이라 생각한다. 또한, low-level에 구현을 이해하면 다른 영역에서도 이 idea를 적용할 가능성이 높을 것이다. 

## Internal Representation
### Definition
```c
#define PyObject_HEAD                   PyObject ob_base;

typedef uint32_t digit;

typedef struct _PyLongValue {
    uintptr_t lv_tag; /* Number of digits, sign and flags */
    digit ob_digit[1];
} _PyLongValue;

struct _longobject {
    PyObject_HEAD
    _PyLongValue long_value;
};
```

파이썬의 int class는 _longobject라는 struct를 이용해서 구현됩니다. PyObject_HEAD는 PyObject의 초기 세그먼트를 정의한다는 의미입니다. PyObject는 모든 파이썬 Object의 기본타입입니다. 이 PyObject는 Compiler의 Parser Tree의 계층화 개념을 사용한 거 같습니다. Compiler의 PaserTree에서는 토큰을 계층화해서 최상위토큰에서 하위토큰으로 program을 구조화합니다. PyObject는 최상위토큰으로 보입니다. PyObject_HEAD는 객체의 참조 타입, 크기 등의 정보를 담고 있습니다. PyObject_HEAD는 파이선 C API에서 객체를 생성하거나 조작할 때 필요한 매크로입니다. 매크로란 특정한 입력 시퀸스(문자열)가 어떤 시퀸스로 매핑이 되는지에 대한 규칙이나 패턴입니다. 여기서는 #define이 매크로입니다.

lv_tag는 몇 자릿수인지 , 부호는 어떻게 되는지, 기타 연산에 필요한 flag 등을 저장합니다.
ob_digit은 32bit int에 대한 배열, 즉 포인터입니다. 하지만, 여기서 ob_digit가 크기가 1인 배열이라는 것을 확인할 수 있습니다. ob_digit은 값이 저장되는 배열인데, 크기가 1이라 지정되면 그 이상의 크기를 할당할 때, Segmentation Fault 오류를 발생시킵니다. 하지만, 파이썬은 arbitary precision을 지원하는 것으로 알려져 있습니다.
이는 c struct의 flexible array member를 사용한 것입니다. flexible array member는 struct 의 마지막 멤버에 가변길이의 배열을 설정할 수 있게 하는 개념입니다.  
```c
result = PyObject_Malloc(offsetof(PyLongObject, long_value.ob_digit) +
                             ndigits*sizeof(digit));
```
실제로 할당시에 ,필요한 digit만큼의 메모리를 할당해주어 arbiratry precision을 지원할 수 있게 됩니다. 
### ob_digit

이제는 ob_digit에 대해 알아보겠습니다. ob_digit에는 digit type의 값이 저장됩니다. digit은 uint32_t를 확장한 macro입니다. 앞으로, digit을 32bit integer라 여기고 digit이라는 용어만을 사용하겠습니다.

ob_digit은 정숫값을 나타내며, 배열의 각 원소는 자릿수입니다 .  0부터 1의 자릿수로 취급하며 index가 올라갈 때마다 자릿수가 올라가게 됩니다. 만약에, 정수를 10진법으로 표현하게 되면 상당히 많은 자릿수가 필요하게 됩니다. 32bit 크기의 data를 저장할 수 있도록 정의되었는데, 10까지의 숫자만을 저장하게 되면, 저장공간의 낭비가 됩니다. 따라서, Python은 10진법이 아닌 $$2^{30}$$ 진법, 즉 base가 $$2^{30}$$ 인 수 체계를 활용하게 됩니다. $$2^{30}$$ 인 이유는 overflow 및 underflow를 방지하기 위한 base의 최대치가 $$2^{30}$$이기 때문입니다. 

### 3.12 이전과 차이점
Integer의 internal을 다룬 모든 post는 아래와 같은 struct를 기준으로 설명을 합니다.  
```c
struct _longobject {
    PyObject_VAR_HEAD
    digit ob_digit[1];
};

typedef struct {
    PyObject ob_base;
    Py_ssize_t ob_size; /* Number of items in variable part */
} PyVarObject;
```
제가 위에서 설명한 struct와 정의가 다름을 알 수 있습니다.  이에 대해 열심히 찾아보다가 cpython의 issue에서 이에 대한 내용을 발견하였습니다. 

```
I think in the What's New in 3.12 we should at least mention the change in the struct (calling out that using ob_size and ob_digits is no longer supported) and the new unstable public APIs (and what they're for).

```
https://github.com/python/cpython/issues/101291#issuecomment-1646132302  

이는 gvanrossum, 파이썬 창시자의 comment입니다. 더 이상 ob_size가 사용되지 않고 lv_tag라는 새로운 representation으로 대체 되었다는 얘기입니다. 사실상, 이전과 기능의 차이는 나지 않기에 만약에 cpython의 source code를 살펴본다면 , 기억만 해두면 좋을거 같습니다.  

## Add
```c
/* Add the absolute values of two integers. */

static PyLongObject *
x_add(PyLongObject *a, PyLongObject *b)
{
    Py_ssize_t size_a = _PyLong_DigitCount(a), size_b = _PyLong_DigitCount(b);
    PyLongObject *z;
    Py_ssize_t i;
    digit carry = 0;

    /* Ensure a is the larger of the two: */
    if (size_a < size_b) {
        { PyLongObject *temp = a; a = b; b = temp; }
        { Py_ssize_t size_temp = size_a;
            size_a = size_b;
            size_b = size_temp; }
    }
    z = _PyLong_New(size_a+1);
    if (z == NULL)
        return NULL;
    for (i = 0; i < size_b; ++i) {
        carry += a->long_value.ob_digit[i] + b->long_value.ob_digit[i];
        z->long_value.ob_digit[i] = carry & PyLong_MASK;
        carry >>= PyLong_SHIFT;
    }
    for (; i < size_a; ++i) {
        carry += a->long_value.ob_digit[i];
        z->long_value.ob_digit[i] = carry & PyLong_MASK;
        carry >>= PyLong_SHIFT;
    }
    z->long_value.ob_digit[i] = carry;
    return long_normalize(z);
}

```

Python 임의의 자릿수 정수 a, b의 덧셈 구현입니다. a, b라는 두 정수를 parameter로 받는데, a의 자릿수가 항상 b보다 크게끔 설정을 해줍니다.
그리고, a보다 자릿수가 하나 더 많은 수 z를 생성해 줍니다.
1의 자릿수부터 더해 나가기 시작하는데 carry와 PyLong_MASK라는 것의 and 연산 결과를 1의 자릿수에 할당해 줍니다.
PyLong_MASK는 bit 30개가 1로 masking 되어 있는 정수입니다. 001111…. 111과 같이 32bit가 구성되어 있습니다. 파이썬의 integer는 base가 $$2^{30}$$ 인 수이기 때문에, 30개의 bit의 값만을 z의 i 번째 자릿수에 할당해야 합니다. 또한, 명시적으로 overflow 및 underflow를 방지하는 기능을 하기도 합니다.
carry의 bit를 30개 shift 함으로써 새로운 자릿수에 대한 덧셈을 지속하게 됩니다. 가장 작은 자릿수의 정수에 대한 덧셈 후 남은 자릿수에 대한 덧셈을 진행하게 됩니다.

기본 덧셈인 x_add가 정의된 다음에 다양한 덧셈에 대하여 x_add가 호출됩니다. 파서 트리의 상위 토큰이 기본토큰으로 계층화되는 구조를 차용해 덧셈을 구현한 듯합니다. 
## Sub
```c
/* Subtract the absolute values of two integers. */

static PyLongObject *
x_sub(PyLongObject *a, PyLongObject *b)
{
    Py_ssize_t size_a = _PyLong_DigitCount(a), size_b = _PyLong_DigitCount(b);
    PyLongObject *z;
    Py_ssize_t i;
    int sign = 1;
    digit borrow = 0;

    /* Ensure a is the larger of the two: */
    if (size_a < size_b) {
        sign = -1;
        { PyLongObject *temp = a; a = b; b = temp; }
        { Py_ssize_t size_temp = size_a;
            size_a = size_b;
            size_b = size_temp; }
    }
    else if (size_a == size_b) {
        /* Find highest digit where a and b differ: */
        i = size_a;
        while (--i >= 0 && a->long_value.ob_digit[i] == b->long_value.ob_digit[i])
            ;
        if (i < 0)
            return (PyLongObject *)PyLong_FromLong(0);
        if (a->long_value.ob_digit[i] < b->long_value.ob_digit[i]) {
            sign = -1;
            { PyLongObject *temp = a; a = b; b = temp; }
        }
        size_a = size_b = i+1;
    }
    z = _PyLong_New(size_a);
    if (z == NULL)
        return NULL;
    for (i = 0; i < size_b; ++i) {
        /* The following assumes unsigned arithmetic
           works module 2**N for some N>PyLong_SHIFT. */
        borrow = a->long_value.ob_digit[i] - b->long_value.ob_digit[i] - borrow;
        z->long_value.ob_digit[i] = borrow & PyLong_MASK;
        borrow >>= PyLong_SHIFT;
        borrow &= 1; /* Keep only one sign bit */
    }
    for (; i < size_a; ++i) {
        borrow = a->long_value.ob_digit[i] - borrow;
        z->long_value.ob_digit[i] = borrow & PyLong_MASK;
        borrow >>= PyLong_SHIFT;
        borrow &= 1; /* Keep only one sign bit */
    }
    assert(borrow == 0);
    if (sign < 0) {
        _PyLong_FlipSign(z);
    }
    return maybe_small_long(long_normalize(z));
}
```
뺄셈은 x_sub이라는 기본 뺄셈이 정의됩니다. loop에서 뺄셈 후 i번째 자릿수에 masking 과 뺄셈의 결과의 and 연산을 할당합니다.
저는 처음에 이 부분이 헷갈렸습니다. 왜 저렇게 할당되는 것인지 이해하는 데 시간이 걸렸습니다. c언어의 정수 자료형의 최상위 bit는 sign bit입니다. 막연히, sign bit라고 알고 있었지만 , 이는 bit 연산에서 굉장히 중요한 역할을 합니다. 바로, 최상위 bit가 1이 되면 이는 $$-2^{31}$$을 의미합니다. 2의 보수나 1의 보수표현에서 최상위 bit가 1이라는 것이 그저 상징적인 의미인 줄 알았는데, 내부 동작에서는 음수를 의미하는 것이었습니다.
만약에, 자릿수의 계산값이 음수가 나온다면, 최상위 bit 2개는 1로 설정이 될 것입니다. 이를 PyLong_MASK와 and 연산을 하게 되면, 결과적으로 $$ 2^{30}$$ 을 더하게 됩니다. 즉, 위의 자릿수로부터 1을 빌려오게 되는 것과 같은 효과입니다.
그다음에, 결괏값을 30bit만큼 오른쪽으로 shift 한 다음에 sign bit가 1인지 확인을 합니다. 만약에, 1이라면 borrow를 1로 설정한 다음에 다음 자릿수 연산할 때 1을 빼주면서 시작하게 됩니다.
이렇게 되면 저희가 아는 뺄셈을 구현했다는 것을 알 수 있습니다.  

## Mul
곱셈은 karatsuba 알고리즘으로 구현이 되어 있습니다. Karatsuba 알고리즘은 큰자릿수의 곱셈에 대하여 효과적입니다. 이는 두 n자리수 곱셈에 대하여 $$O(n^{\log_{2}{3}})$$ 의 upper bound를 가집니다. 고전적인 $$ O(n^{2})$$ 의 알고리즘 보다 더 좋은 성능을 보여줍니다. 예를들어 , n=1024인경우 기존의 알고리즘은 1,048,576 회의 한자리 곱셈이 필요하지만, karatsuba의 경우 59,049 회의 한자리 곱셈이 필요하게 됩니다.  
자릿수가 작을 경우 기존의 곱셈 알고리즘을 수행하도록 합니다. 기존의 곱셈 알고리즘을 수행할 때, 두 수의 자릿수가 같다면 워털루 대학의 [참고자료](https://cacr.uwaterloo.ca/hac/about/chap14.pdf)에 있는 곱셈 알고리즘을 수행합니다. 자릿수가 다르다면 학교에서 배우는 곱셈 알고리즘을 수행합니다. 
```python
for (i = 0; i < size_a; ++i) {
            twodigits carry = 0;
            twodigits f = a->long_value.ob_digit[i];
            digit *pz = z->long_value.ob_digit + i;
            digit *pb = b->long_value.ob_digit;
            digit *pbend = b->long_value.ob_digit + size_b;

            SIGCHECK({
                    Py_DECREF(z);
                    return NULL;
                });

            while (pb < pbend) {
                carry += *pz + *pb++ * f;
                *pz++ = (digit)(carry & PyLong_MASK);
                carry >>= PyLong_SHIFT;
                assert(carry <= PyLong_MASK);
            }
            if (carry)
                *pz += (digit)(carry & PyLong_MASK);
            assert((carry >> PyLong_SHIFT) == 0);
        }

``` 

여기서 f는 a의 i번째 자릿수의 값이고, pz는 곱셈 결과의 자릿수를 나타내는 포인터이고, pb는 b의 자릿수를 가리키는 포인터, pbend는 b의 가장 앞 자릿수를 가리키는 포인터입니다. 그저 c언어의 곱하기를 수행한 다음에 overflow 및 underflow를 방지하고 carry를 shift 해주는 코드입니다. `carry += *pz + *pb++ * f;` 는 z의 i번째 자릿수에 a의 i번째 자릿수 * b의 i번째 자릿수를 더합니다. inplace operator로 pb의 값을 즉, 자릿수를 증가시킵니다. `*pz++ = (digit)(carry & PyLong_MASK);` 에서 PyLong_MASK로 pz i번째 자릿수에 base의 크기보다 작은 값을 할당해 줍니다. 그러면서, pz의 값, 즉 z의 자릿수를 증가시킵니다. pointer의 산술연산과 inplace operation이 결합하여 최적화가 되어있는 부분이 헷갈릴 수도 있기에 따로 설명을 해보았습니다.

## Divide
Python의 나눗셈 내부를 보면 한 자릿수의 나눗셈은 그냥 c언어의 나눗셈 연산을 사용하여 진행됩니다. 하지만, 자릿수가 커질 경우 Donald Knuth 교수님이 제안하신 Algorithm D를 사용하여 최적화 되어있습니다. 이는 The Art of Computer Programming, Vol.2 4.3.1 chapter에 실려있습니다. 이에 대해서는 따로 소개하지 않도록 하겠습니다. 여러 나눗셈 토큰들이 기본 연산인 x_divrem이라는 활용하는데, x_divrem은 내부적으로 Algorithm D로 구성돼 있다고 알고 있으면 좋을 거 같습니다.
그뿐만 아니라, Modular operator와 floor operation 그리고 몫과 나머지를 전부 반환하는 operation도 구현이 되어있는데, single bit의 경우 c언어의 기본연산을 활용하여 구현되어 있고, 긴 자릿수의 경우 x_divrem을 사용하여 구현되었습니다.

## 256까지의 정수

간단한 얘기지만, 256까지의 정수는 미리 메모리에 caching이 되어 있습니다. 따라서, 매번 operation을 할 때, object를 생성하게 되는데 256까지의 정수는 memory에 있는 값을 reference 하기에 object가 새로 생성되지 않습니다.
## Conclusion
여태까지, +,-,*,/,%,// 등등의 operator를 사용하면서 실제 연산이 어떻게 구현되는지는 관심이 없었습니다. 실제로 내부 구현을 보면서 2의 보수표현이 왜 효과적인지 깊이 있게 알게 되었습니다. 정수는 32bit의 int로 arbitrary precision을 지원하지만, float의 경우 c의 double type을 그대로 활용하기에 연산이 상대적으로 간단하였습니다. divide,mul을 제외한 기본연산들은 자릿수를 넘기는 간단한 방식의 연산을 지원하지만, 이 과정에서 사용된 여러 idea 등을 low-level에서 어떻게 구현되는지 알게 되었습니다. Computer Architecture의 공부를 다시 깊이 있게 해야 함을 느끼게 되었습니다. 
## References

https://www.codementor.io/@arpitbhayani/how-python-implements-super-long-integers-12icwon5vk  


https://rushter.com/blog/python-integer-implementation/  


https://github.com/python/cpython/pull/102464  


https://github.com/python/cpython/blob/main/Include/cpython/longintrepr.h#L89  


https://github.com/python/cpython/issues/101291  


https://github.com/python/cpython/blob/main/Include/cpython/longintrepr.h#L47C1-L55C27


https://ko.wikipedia.org/wiki/%EC%B9%B4%EB%9D%BC%EC%B6%94%EB%B0%94_%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98