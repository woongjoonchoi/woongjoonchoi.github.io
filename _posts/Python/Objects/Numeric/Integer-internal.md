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

파이썬의 int class는 _longobject라는 struct를 이용해서 구현이 되어집니다. PyObject_HEAD는 PyObject의 초기 세그먼트를 정의한다는 의미입니다. PyObject는 모든 파이썬 Object의 기본타입입니다. 이 PyObject는 Compiler의 ParserTree의 계층화 개념을 사용한거 같습니다 . Compiler의 PaserTree에서는 토큰을 계층화해서 최상위토큰에서 하위토큰으로 program을 구조화합니다. PyObject는 최상위토큰으로 보여집니다. PyObject_HEAD는 객체의 참조 타입,크기 등의 정보를 담고 있습니다. PyObject_HEAD는  파이썬 C API에서 객체를 생성하거나 조작할 때 필요한 매크로입니다. 매크로란 특정한 입력 시퀸스(문자열)가 어떤 시퀸스로 매핑이 되는지에 대한 규칙이나 패턴입니다. 여기서는 #define이 매크로입니다.  

lv_tag 는 몇 자리수인지 , 부호는 어떻게 되는지 ,기타 연산에 필요한 flag등을 저장합니다.  
ob_digit은 32bit int에 대한 배열, 즉 포인터입니다. 하지만, 여기서 ob_digit가 크기가 1인 배열이라는 것을 확인할 수 있습니다.  ob_digit은 값이 저장되는 배열인데 , 크기가 1이라 지정되면 그 이상의 크기를 할당할 때, Segmentation Fault 오류를 발생시킵니다.  하지만, 파이썬은 arbitary precision을 지원하는 것으로 알려져 있습니다. 
이는 c struct의  flexible array member를 사용한 것입니다. flexible array member는 struct의 마지막 멤버에 가변길이의 배열을 설정할 수 있게하는 개념입니다.  
```c
result = PyObject_Malloc(offsetof(PyLongObject, long_value.ob_digit) +
                             ndigits*sizeof(digit));
```
실제로 할당시에 ,필요한 digit만큼의 메모리를 할당해주어 arbiratry precision을 지원할 수 있게 됩니다. 
### ob_digit

이제는 ob_digit에 대해 알아보겠습니다. ob_digit에는 digit type의 값이 저장되어집니다. digit은 uint32_t를 확장시킨 macro입니다. 앞으로 ,digit을 32bit integer라 여기고 digit이라는 용어만을 사용하겠습니다.  

ob_digit은 정수값을 나타내며 , 배열의 각 원소는 자릿수 입니다 .  0부터 1의 자릿수로 취급하며 index가 올라갈 때 마다 자릿수가 올라가게 됩니다. 만약에, 정수를 10진법으로 표현하게 되면 상당히 많은 자릿수를 필요로 하게 됩니다. 32bit 크기의 data를 저장할 수 있도록 정의되었는데, 10까지의 숫자만을 저장하게 되면, 저장공간의 낭비가 됩니다. 따라서, Python은 10진법이 아닌 $$2^{30}$$ 진법 , 즉 base가 $$2^{30}$$ 인 수 체계를 활용하게 됩니다. $$2^{30}$$ 인 이유는 overflow 및 underflow를 방지하기 위한 base의 최대치가 $$2^{30}$$이기 떄문입니다. 

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


## Sub

## Masking

## Mul

### k_mul

카라추바
## Divide

### x_divrem

알고리즘d


## References

https://www.codementor.io/@arpitbhayani/how-python-implements-super-long-integers-12icwon5vk  


https://rushter.com/blog/python-integer-implementation/  


https://github.com/python/cpython/pull/102464  


https://github.com/python/cpython/blob/main/Include/cpython/longintrepr.h#L89  


https://github.com/python/cpython/issues/101291  


https://github.com/python/cpython/blob/main/Include/cpython/longintrepr.h#L47C1-L55C27


