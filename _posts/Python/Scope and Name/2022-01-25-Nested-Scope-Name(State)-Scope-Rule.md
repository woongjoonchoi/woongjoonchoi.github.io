---
title : "How to state retention in nested scope"



excerpt: "Nested Scope"

categories:
  - Python
tags:
  - [Python,Scope]
# classes : wide
toc: true
toc_sticky: true
---
Python에서 프로그래밍을 할 때 , LEGB의 순서대로 scope를 탐색한다. built-in scope를 프로그래머가 manually하게 조작하지는 않습니다.  실제로 프로그래머가 state 를 저장하기 위해서  , 사용하는 scope는 LEG(local , encosing def, enclosing module(global)) 3개의 scope라 볼 수 있다.  

이 3가지의 scope에서 state를 의도하는 대로 보관할 수 있다면 , 프로그램의 유연성은 더 증가할 것 입니다.

## Nested Scope Name(State) Scope Rule

Python에서 state(name) 에 접근하는 방법은 reference  와 assignment 이 두가지 입니다 . 지난번에서 본 global scope와 조합해서 , LEG scope에서 어떻게 reference와 assignment가 진행되는지 알아야 합니다. 이번에는 Nested Scope에서의 scope rule에 대해서 detail 하게 알아보도록 하겠습니다. 

### Reference

python 에서 Reference란 어떤 statement에서 name(X) 만 있는 것을 의미합니다. Python에서 Nested SCope에서의 Name Reference Rule은 아래와 같은 순서로 진행됩니다.

1. currently local scope
2. lexically enclosing function  , from inner to outer
3. current global
4. built-in

여기서 , 2번에서 from inner to outer는 아래와 같은 scope를 의미합니다.

```python
def
	def
		#여기서부터 위로
		def
			
```

어떤 name을 referenece하는 statement가 주어지면  , 1번 부터 4번의 순으로 scope를 탐색해나갑니다.

### Assignment

python 에서 Assignment란 어떤 statement에서 name에 어떤 값을 assignment(X=value) 해주는 것을 의미합니다.  Python에서,  Nested Scope의  AssignmentRule은 아래와 같은 순서로 진행됩니다.

1. current local scope(default)

기본적으로 , local scope에서는 local scope에 name이 bind되는 것이 default입니다. 하지만 ,namespace declartion이 추가된다면, 다른 scope에 bind 됩니다.



- 만약에 global x  = value 가 function 안에서 선언된다면
  - X 는 enclosing module(global) scope에 존재하게 되거나 존재한다.
- 만약에 , nonlocal x = value가 function 안에서 선언된다면
  - X는 enclosing def(nonlocal) scope에 존재하게 되거나 존재한다.

이 nonlocal은 차후에 다른 post에서 다룰 예정이니 , 지금은 신경쓰지 않으셔도 됩니다.  

global declartion의 특징으로는 scope search 가 global scope부터 시작이 된다는 점입니다. local, encloisng def scope를 찾아보지 않고 바로 global scope , built-in scope순으로 search를 하게 됩니다. 

namespace declartion은 search 범위의 scope에 name이 존재하질 않으면 , name을 새로 만듭니다. 이를 다르게 말하자면, enclosing module안에서 global을 선언하게 되면 , 그 name은 global scope안에 존재하게 됩니다.

### Example

```python
X = 99

def f1():
    X = 88
    def f2():
        print(X)
    f2()
f1()
```

이 예제를 보면 , f2 는 f1 scope안에 있습니다. f1을 호출하게 되면 , f1안에 있는 function object  f2를 생성하게 됩니다. 이 f2는 f1 scope안에 존재하게 됩니다. X를 print하라하면 f2는 f1의 local scope에 있는 name을 Reference하게 됩니다. LEGB RULE에 따라서 , local에는 name X가 없었고 , enclosing module에 X가 존재하기에 enclosing scope에 있는 X를 reference 하게 됩니다 .

```python
def f1() :
    X = 88
    def f2() :
        print(X) 
    return f2

action = f1()
action()
```

이 예제는 , python을 사용하다보면 만날수 있는 pattern일 수도 있습니다. 이번에 f1은 f2를 return하게 됩니다. f1을 호출한 순간 아직 f2는 호출이 되질 않습니다. action을 호출하면서 f2를 호출하게 되는데 ,실질적으로 호출을 하게 될 때 f1 scope는 더 이상 존재하지 않음에도 , f2는 f1 scope에 있는 X를 reference해서 print 합니다.



## State Retention in Nested Scope

위에서 함수를 return하는 예제에서 f1 scope가 사라졌음에도  X가 살아있습니다 . 이러한 function을 factory function이라 합니다.factory function 뿐만 아니라 다양한 enclosing def의 state를 보존하는 방법을 알아보도록 하겠습니다. 

###  Factory function

이러한 동작을 closure function 혹은 factory function이라 부릅니다. return되는 function object는  enclosing scope에 있는 variable을 기억하게 됩니다. enclosing scope가 메모리에 있든 말든 상관이 없게 됩니다. 이는 Class attribute의 대안이 될 수 있습니다.

런타임시 조건이 발생하면 , 즉석에서 이벤트 핸들러를 만들어야 하는 상황이 생길 수 있습니다. 그 이벤트 핸들러가 state를 저장해야 할 때 , factory function을 이용할 수 있습니다.

```python
def maker(N) :
    def action(X) :
        return X**N
    return action

f=maker(2)

print(f)

print(f(3))
print(f(4))

g = maker((3))


print(g(3))
print(g(4))
```

이 예제를 보시면 , f , g가 각각 maker 함수를 호출해서 factory function을 return받고 function object를 다시 호출하게 되면, f는 제곱을 , g는 세제곱을 호출하게 됨을 알 수 있습니다 .즉, 각 factory function은 각각의 state information을 갖는다 볼 수 있습니다. 

일반적으로는, class 는 attribute를 explicit하게 만들지만, factory function은 explict하지 않고 implict 합니다. 

#### Class vs Closure

Class와 Closure를 비교하면 아래와 같습니다.

| Class |                      | Closure |
| ----- | -------------------- | ------- |
| O     | inheritance          | X       |
| O     | operator overloading | X       |
| O     | methods              | X       |

state 보존이 목적이면 , closure는 class 보다 좀 더 가벼운 대안이라 볼 수 있습니다.



### Retain scope in python 2.X : using default

Python 2.X버전을 사용할 일이 거의 없겠지만, 드물게 있을수도 있습니다. Python 2.X는 Factory Function 이 지원되지 않습니다. 왜냐하면 , Python 2.X버전에서는 scope rule이 아래와 같기 때문입니다.

- local(f2)
- global
- built-in 

enclosing function의 scope를 skip하기에 enclosing fuction scope의 name에 접근하면 error가 발생합니다. 따라서, 이러한 이전 버전에서는 default argument value를 사용하게 됩니다 

```python
def f1() :
    X = 88
    def f2(X=X) :
        print(X)
    f2()


f1()

def f1() :
    X = 88
    f2(X)  # Forward Reference okay

def f2(X) :
    print(X)
    
f1()
```



argument arg가 f2에 들어가기 전에 val이라는 값이 default 값이 되므로 여전히 f1에 있는 val값을 참조할 수 있게 됩니다. 하지만 , 첫번째 case와 같이 함수를 작성하게 된다면 ,  default argument에 value가 assignment가 되는 순간에 의존하게 됩니다. f1안에 def 문이 실행되고 나서야  X값이 결정이 됩니다. 또한, 이는 약간 복잡해 보입니다. 따라서, 두번째 case와 같이 함수를 2개 작성하는게 좀더 괜찮은 방법입니다. 실제로 f1이 호출되기 전에 , f2가 호출이 된다면 작동합니다. 이를 일반적으로 표현하자면, 함수안에서 호출할 함수 뒤에 정의될 함수를 호출했다 라고 표현합니다.  

### Nested Scopes and lambdas

lambda 또한 이러한 방법이  적용될 수 있습니다. lambda는 새로운 function을 생성하는 expression입니다. 이는 def가 사용될 수 없는 곳에서 사용되어 집니다.예를 들어 , list안이나 dictionary 안이 있습니다. 새로운 local scope 를 새로운 function을 위해서 만들게 되므로 , 위에서 보았던 Rule들이 그대로 적용되어 집니다.

```python
def func() :
    x = 4
    action = (lambda n : x **n) # x rememeberd , x from enclosing def
    return action


f  = func()

print(f(2)) 

def func() :
    x = 4
    action = (lambda n , x=x: x **n)  # x passed as default argument value
    return action

f  = func()

print(f(2))
```

첫번째 func은 enclosing scope에 있는 x를 조회할 수 있습니다. 여전히, scope  rule이 잘 작동합니다  . 또한 , scope에 있던 default value를 lambda 의 argument에 default value로 넘겨줄 수 있습니다. 



### Scopes versus default with variables

```python
def makeActions() :
    acts=[]
    for i in range(5): # Tries to remember each i
        acts.append(lambda x : i ** x) # But all remember same last i
        
    return acts

acts = makeActions()


print(acts[0])


print(acts[0](2))
print(acts[1](2))
print((acts[4](2)))

print('--------------------')

```

enclosing function의 loop안에 def , or lambda가 nested이면 nested function은  enclosing scope안에 있는 loop variable을 참조할 수 있습니다. 그리고 , loop에서 생성되는 함수들은 마지막 loop의 iteration의 loop variable을 갖습니다. 

왜냐하면, nested function이 호출될 때 , enclosing scope의 variable을 조회하기 떄문입니다. 위의 예제의 경우 makeActions를 assign하게 되면 , for문의 i 는 4가 됩니다. 그리고 , `acts[0][2]` 로 lambda function을 호출하게 되면 함수를 수행하게 되는데 , 이 때  i = 4가 됩니다.

이러한 case에서는 enclosing scope에서의 value를 default value로넘겨줘야 하는 case입니다. 

되게 난해한 성질이지만, 연속적으로 call-back handler(ex.button handler)  를 생성해야 할 때 , 이용될 수 있습니다 



### Loop variables may require defaults, not scopes

이 case는 좀 신기한 case라서 보다가 이해가 안되시면 skip하셔도 무방합니다 . 

```python
def makeActions() :
    acts=[]
    for i in range(5) :  # Diffenreec . Use default i
        acts.append(lambda x , i = i : i**x) 
        
    return acts

acts  =  makeActions()

print(acts[0](2))

print(acts[2](2))

print(acts[4](2))

```

