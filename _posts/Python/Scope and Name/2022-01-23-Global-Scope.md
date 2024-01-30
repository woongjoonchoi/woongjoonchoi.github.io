---
title : "Python Global Statement and Gloal scope is dangerous"



excerpt: "Global Scope"

categories:
  - Python
tags:
  - [Python,Scope, LearnigPython]
# classes : wide
toc: true
toc_sticky: true
---
global variable은 저번 글에서 설명했듯이 global scope에 속해있는 name입니다. 예전에, global variable을 사용할 때에는, 함수 내부에서 global을 사용하면 사용할 수 있다고 알고 있었습니다. global statement를 사용하게 되면, local scope에서 쉽게 global variable에 access 할 수 있습니다. 하지만, global scope에 대한 명확한 이해가 뒷받침되지 않으면 위험한 결과를 초래할 수 있습니다.

## Global Statement

Python 에서 global statement는 type 이나 size declartion 문이 아닙니다 .이는 namespace declaration 문입니다.즉 , function 내부에서 global scope(enclosing moduel scope)에 있는 name을 변경하겠다고 선언을 하는 것입니다. global statements의 성질은 다음과 같습니다.

- 함수 내부에서 global statement 없이 global name은 reference가 될 수 있다.
- 함수 내부에서 global name에 assignment를 할려면 global name이 반드시 declaration이 되어야 한다.

이에 대한, 구체적인 예를 보도록 하겠습니다.

```python
X = 88 # Global X
def func():
 global X
 X = 99 # Global X: outside def
func()
print(X) # Prints 99
```

global declartion을 함수 내부에서 사용했기에 , X에 assign을  할 수 있습니다.

```python
y, z = 1, 2 # Global variables in module
def all_global():
 global x # Declare globals assigned
 x = y + z # No need to declare y, z: LEGB rule
```

y,z는 함수 내부에서 global이라 declartion 되지 않았기에 , global scope입니다. LEGB look rule에 의해서 global scope에 있는 y,z를 찾게 될 것입니다. x는 global 이라 declartion 되었기에 , global scope에 있습니다.

## Be careful with using global variable

global statement를 사용하면 scope가 어떻게 되는지 알아보았습니다. 이제 , global statement로 program이 다양한 동작을 하도록 짜러 갈 수 있지만, global name을 사용했을때 일어날 수 있는 위험한 pattern들에 대하여 알아야합니다. 

### Minimize global name in function

모듈과 function이 어떤 state를 주고 받기 위해서 global statement를 사용가능합니다.하지만, 이는 바람직하지 않습니다. function은 argument, return 값으로 모듈과 소통하는 것이 바람직합니다. 예를 보면서, 설명해보도록 하겠습니다.

```python
X = 99


def a():
    global X
    X = 32
def b() :
    global X
    X= 56
    
# Q .what is value of X?
# A. It 's dependent on what you calledd

b()
print((X))
```

이 프로그램에서 X값은 어떻게 될까요? a 를 호출하면 32가 될 것이고 , b를 호출하면 56이 될 것입니다. 즉, 어떤 함수를 먼저 호출하냐에 따라 달라지게 됩니다. 이렇게 되면, 아래와 같은 문제점들이 발생하게 됩니다.

- global variable이 외부 call에 의해서 영향을 받게된다.
- 전체 flow를 다 알아야 코드의 흐름을 알 수 있다.
- 전체  flow를 다 알아야 이 코드를 모듈화 할 수 있다.
- 이 프로그램은 함수에 의존적이다 . 

위 예제는 , 단순히 X값을 출력하기 위한 프로그램이기에 아래와 같이 바꿔쓸 수 있습니다.

```python
X = 99


def a(X):
    
    X = 32
    print(X)
def b(X) :
    
    X= 56
    print(X)
    
# Q .what is value of X?
# A. It 's dependent on what you calledd

b()
```

여기에서 , 주목할 점은 우리의 목적이 state를 보존하는 것이 아니다이다. 만약에 , state를 보존해야 하는 경우였다면, global variable들도 좋은 선택지 중 하나가 될 수 있습니다. OOP에서는 global variable로 state를 보존하는 것이 가장 직접적인 방법입니다. 

global variable을 state를 보존하기 위해 사용하는 경우는 multithreading이 있을 수 있습니다. 

1. Single Module이 global variable을 update한다.
2. global variable 값에 따라 , multi-threading을 사용해서 병렬처리를 한다.

scope에 대한 모든 case를  알지 못하고있다면, global은 안쓰는게 정석이라 알려져 있습니다.

### Minimizing Cross file change

```python
# first.py
X = 99


```



```python

# second.py
import first

print(first.X)
first.X=88
```

`first` ,`second` 라는 두 개의 파일이 있습니다.  `second` 에서 `fisrt` 를 import 해서 사용할려 합니다. 여기서 보면, `fisrt.X` 로 `first` 의 global scope를 access하는 것을 볼 수 있습니다. 이는, module의 global scope는 module object의 namespace attribute가 됩니다. 따라서,module 이 import 되면 importer 는 file의 모든 global variable에 접근할 수가 있습니다.

지금 위 예제에서는 , `second` 에서 `first` 의 X에 assignment를 하는데 , 이는 굉장히 implicit하다 할 수 있습니다 . 왜냐하면, `first` 가 아닌 다른  module에서 이를 변경하게 되는데 , 이를 first module에서 알기가 굉장히 어렵습니다. 두 module이 서로 엮이게 되므로 , 두  module은 서로 엮여있는 상태가 됩니다. 이는 OOP의 Encapsulation 개념을 위반했다 보시면 편합니다.

좀 더 구체적으로 설명을 하자면, 직접 directly 하게 access가 가능하다면 어떤 값으로든 바꿀 수 있습니다 . invalid 한 state로도 바꿀 수 있기에 , implicit하다고 하는 것입니다. 이를 , 좀더 explicit 하게 function을 통해서 assignment 되도록 바꿔보겠습니다.

```python
X = 99

def setX(num) :
    global X
    X = num
    # X = 88
```

```python
import first

num = 88 
first.setX(num)
print(first.X)
```

함수를 통해서만 access가 가능하기 때문에 ,  임의로 X를 변경하는 것이 불가능 합니다. 이러한, OOP개념은 나중에 다시 다루도록 하겠습니다.
