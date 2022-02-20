---
title : "Python Scope"



excerpt: "Scope"

categories:
  - Python
tags:
  - [Python,Module,Package,Scope]
# classes : wide
toc: true
toc_sticky: true
---
## Scope

파이썬에서 함수를 만들고 , 모듈을 만들고 ,패키지를 만듭니다. 어떤 Module(혹은 단순 script)를 실행하면, 여러 변수, 즉 name 들을 namespace에 할당하게 됩니다. 저는, 여태까지 이러한 과정들을 함수안에 생성하면 지역변수 , 함수밖에 전역변수 이 두 가지 카테고리로 분류했습니다 . 파이썬을 좀 더 디테일 하게 사용하기 위해서 이러한 과정들을 좀 더 디테일하게 알아볼려 합니다.

## name and scope

파이썬에서 scope라는 term은 namespace를 의미합니다. 파이썬에서 variable에 value가 assign 되면 name이 그 순간 부터 존재하기 시작합니다.  파이썬에서 variable을 사용할 때 먼저 초기화를 해야만 하는 이유입니다. name은 name을 선언하기전에는 존재할 수 없기에 ,  파이썬에서 name이 assign 되는 위치가 어떤 namespace에 속하게 되는지를 결정하게 됩니다.

## namespace

파이썬에서 기본적으로 함수는 추가적인 namespace를 생성합니다. function안에서 assign 된 name은 function과 관련이 있다는 말입니다. 이를 다시말하자면,

- def 안에서 assign 된 name은 def의 namespace에서만 사용할 수 있습니다.  다른, namespace에서는 사용할 수 없습니다. 
- 또한, def안에서 assign 된 name은 def 바깥의 namespace에서의 name과 충돌하지 않습니다.  예를들어 , def 바깥의 name X 과 def 안의 name X는 다른 name입니다.

 namespace는 def 안을 local namespace라 하고,  def 의 바깥을 global namespace라 하고  , def를 둘러쌓고 있는 def의 안을 non local namspace로 분류합니다.



```python
X= 100
def a() :
	X= 98
```

예를 들어 , 위와 같은 varialbe 들이  X 로 name이 정해지면, def 의 바깥에 있는 X=98 은 X라는 이름의 local variable을 생성하고 , X =100은 X라는 name을 가진 global variable을 생성합니다. 



## Scoping Rule

Python에서 namespace에는 Rule이 있습니다.

- 전체를 둘러싸고 있는 Module은 global scope입니다.

  - 실행하는 moduel file안에 있는 variable의 name들이 속하는 namespace입니다.
  - Global variables들은 Module 바깥에서는 module의 attribute로써 사용될 수 있습니다.

- global namespace는 각 파일에 1개 존재합니다.

  - global namespace가 파이썬을 실행하면 1개라고 생각하면 안됩니다. global namespace는 각 모듈에 각각 존재합니다.

- 함수를 새로 호출하면 함수안의 name을 local namespace에 할당합니다.

  - def ,lambda 를 새로운 local namespaece를 만드는 syntax라 생각해도 괜찮습니다.
  - Python은 함수가 함수(자기 자신)을 호출해서 반복할 수 있습니다.(Recursion ). 즉, 함수를 호출하면 , 그 안에서 새로운 local namespace를 생성하게 됩니다.

- local namspace에서 Assign 된 name들은 global 혹은 non local이라고 선언되지 않으면  , local namespace에 속하게 됩니다.

  - global 키워드를 통해서 global namespace에 있는 name을 local namespace에서 사용할 수 있습니다.

  - def 안에 def가 있을 때 , 제일 안쪽 def에서 def을 둘러쌓는  namespace에 있는 name을 사용할려면 non local 키워드를 이용하면 됩니다.

이를 제가 참조한 책에서는 LEGB Rule이라고 명명합니다. L은 local, E는 enclosing defs , G는 global, B는 built in을 의미합니다.

## Reference

[1] Learning python chp17 Scope :  [Learning Python chp 17](https://www.google.co.kr/url?sa=t&amp;rct=j&amp;q=&amp;esrc=s&amp;source=web&amp;cd=&amp;cad=rja&amp;uact=8&amp;ved=2ahUKEwjZ7d_Fj471AhXLdXAKHYArDi8QFnoECAQQAQ&amp;url=https%3A%2F%2Fwww.amazon.com%2FLearning-Python-Powerful-Object-Oriented-Programming-ebook%2Fdp%2FB00DDZPC9S&amp;usg=AOvVaw3OQRO0BHmts707N8L_VY0O)

