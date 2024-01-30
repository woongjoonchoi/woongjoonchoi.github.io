---
title : "State Retention Compare"



excerpt: "Summary"

categories:
  - Python
tags:
  - [Python,Scope, LearnigPython]
# classes : wide
toc: true
toc_sticky: true
---
# Module scope State Retention



```python

def tester(start) :
	global state
    state = start
    def nested(label) :
        print(label , state)
        state +=1 
        
    return nested

F = tester(0)

F('spam')

F('ham')

G = tester(42)
```



module scope의 state에 보관하게 되면 이 state를 담고 있는 variable은 global variable이 되게 됩니다 . 이렇게 global state를 사용하게 되면 아래와 같은 문제점이 발생합니다.

- `global` declaration이 모든 코드 블록(ex.  fucntion,class) 에 적용이 되어야 한다.
- module namespace에 name collision의 가능성이 있다.
- ***<u>1개의 name으로 같은 state만 보관이 가능하다.</u>***

3번째 특성이 제일 중요한 특성입니다.  MultiProcessing에서 ,   fork된 processe들이  같은 공유 resource(ex.lock,mutex) 를 가지게 하기위해 의도적으로 사용할 수 있는 중요한 기능이 될 수도 있습니다. 하지만, 여러개의 event handler를 만들어야 할 경우에는 적합한 option이 아닙니다 .

Global Statement를 사용할 때는, cross-file change, control flow 등 여러가지 option등을 고려해야 합니다 .

참조 : [Python Global Statement and Gloal scope is dangerous - Woongjoon_AI (woongjoonchoi.github.io)](https://woongjoonchoi.github.io/python/Global-Scope/)

# Enclosing Function scope name Retention

위의 global state를 사용하게 되면 아래와 같은 문제점들이 있었습니다.

-  function이 값을 return 하지만, 각 call 사이에서 state들이 보존되어 전달되지 않는다.
- 대부분의 application들이 각기 다른 state들을 가지길 원한다.

```python
def tester(start) :
    state = start
    def nested(label) :
        nonlocal state
        print(label , state)
        state +=1 
        
    return nested

F = tester(0)

F('spam')

F('ham')

G = tester(42)
```



`nonlocal` 은 변경이 가능한 독립적인 state들을 메모리에 저장하게 됩니다. 이는 다음과 같은 상황에서 사용합니다.

- `global` 을 사용할 수 없을 때
- class를 사용하는 것이 너무 overkill 일 때 

NonLocal을 사용할 때에는 global과는 유사하지만, 차이점이 있으므로 이를 잘 인지하면서 사용해야 합니다. 그리고, enclosing function scope에 있는 name을 reference하는것만 필요하다면 factory(closure) function을 사용하는 것도 하나의 선택지가 될 수 있다. 

참조 : [Nonlocal statement - Woongjoon_AI (woongjoonchoi.github.io)](https://woongjoonchoi.github.io/python/Nonlocal/)

참조 : [How to state retention in nested scope - Woongjoon_AI (woongjoonchoi.github.io)](https://woongjoonchoi.github.io/python/Nested-Scope-Name(State)-Scope-Rule/)

# Class State Retention

class의 member 를 이용해서  state를 보존할 수도 있습니다. class를 사용하면 아래와 같은 기능을 사용할 수 있습니다

- inheritance
- method
- etc..

class의 member는 explicit하게 access 할 수 있는 반면 , nested function을 이용해서 enclosing function scope에 state를 보존하면 implicit하게 access 할 수 있습니다 .

class로 이러한 state 문제를 해결할 수 있지만, 단순히 state 1개를 보존하기 위해서 class를 사용하는 것은 overkill이라 볼 수 있습니다.



# Function Attribute

Python에서 Functoin attribute를 사용하면 2.X, 3.X에서도 nested function에 function attribute를 추가하게 됩니다. 

```python
def tester(start) :
    def nested(label) :
        print(label , state)
        state +=1 
    nested.state = start
    return nested

F = tester(0)

F('spam')

F('ham')

G = tester(42)
```

마치 class attribute처럼 어디서든 이 attribute에 access할 수 있게 됩니다 . 이는 nonlocal scope에 name을 할당하는 것이 아닌 , nested라는 function object에 attribute를 추가하는 것입니다. 

하지만, 이 기능은 nonlocal같은 비교적 최신 기능과 많이 중복되는 부분이 있는 오래된 구식 유물이라 생각이 듭니다 .

## mutable in function

```python
def tester(start):
 	def nested(label):
         print(label, state[0]) # Leverage in-place mutable change
         state[0] += 1 # Extra syntax, deep magic?
 state = [start]
 return nested
```

Python에서는 이러한 syntax가 허용이 됩니다.  enclosing scope에서 mutable object를 변경이 가능합니다 . 하지만 , 이는 보는 사람에게 혼란을 줄 수 있는 syntax로 이런식으로 작성을 하는 것은 피해야 합니다 .

그저 legacy 코드에서 이러한 코드를 보게 된다면 어떤 동작을 하는 지 정도만 이해하면 됩니다 .

