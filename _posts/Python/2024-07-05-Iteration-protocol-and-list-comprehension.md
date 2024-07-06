---
title : "Python Iteration Protocol, List Comprehension"



excerpt: "iteration details"

categories:
  - Python
tags:
  - [Python,loop,iterator, LearnigPython,LearningPython,Chp14 Iteration Protocol]
# classes : wide
toc: true
toc_sticky: true
---



Python에서 좀 더 심화된 iteration context에는 iterator , List Comprehension이 있습니다. Python에서는 for이라는 statement로 abstraction을 해서 `for a in A `같은 statement로 item을 조회할 수 있게 해줍니다. list function은 모든 iterable object를 argument로 받도록 추상화 되어있기에 `list(A)` 같은 statement로 쉽게 list로 반환하도록 합니다.
하지만, iterator나 list comprehension을 이해하게 된다면 , 좀 더 manual 하게 , 상황에 따라서 더 빠르게 실행되도록 할 수 있습니다 .

## Iteration Protocol

Python에서는 여러 built-in DataStructure를 제공한다. 거의 이것들 대부분은 iter API를 제공한다. Python은 iterable이라는 abstract class를 상속하도록 해서 for 같은 tool을 사용해서 자동적으로 item들을 조회할 수 있게 해준다. 
자세히 말하자면, Python은 Iteration Protocol이라는 규칙을 정의해서 item을 조회하게 해줍니다.이 Iteration Protocol을 이해한다면 Python 기반의 모든 framework에서 정의된 iterable object를 사용할 때  응용할 수 있습니다.
### Iterator , Iterable Object
우선 iterable objects가 무엇인지에 대하여 알아보겠습니다.iterable object는 한번에 object의 member(혹은 item)을 1개씩 return하는 object입니다. 이 iterable object에는 sequence (ex.list,tuple,str) , set(ex.dict,set),file-object, 그리고 `__iter__()` method 혹은  `__getitem__()` method를 정의한 어떤 class가 포함됩니다. 
이 때 , iterable object는 physically continuous 하거나 logically continuous 할 수 있습니다. 
Iterator object는 data의 stream을 의미합니다.  `__next()__` method를 통해서 연속적으로 item이 stream으로 return이 됩니다. 더이상 return할 item이 없다면 , `StopIteration` exception을 raise하게 됩니다. 

이 두가지 object를 통해서 iteration protocol을 정의할 수 있습니다.

* Iterable object에 `iter()` 를 통해서 `__iter__()`가 실행이 됩니다.
* `__iter__()`로 iterator object가 return이 되고 `__next__()` 는 `next()`에 의해 실행이 되고 item return이 종료되면 `StopIteration` exception을 raise합니다.  

for loop나 다른 iteration context를 사용하게 된다면 programmer 에게 `iter()`와 `next()` 그리고 exception 처리 과정이 숨겨져 있기에 자동적으로 item을 return하게 됩니다. 
### Manual Iteration Protocol

Iteration Protocol을 정의할 수 있으면 어떤 종류의 object를 manual하게 while 을 통해서 조회할 수 있습니다. 

``` python
    
    I = iter(A)

    while True :

        try :
            item = next(I)

        except StopIteration: 
            break

```

이렇게 작성하게 되면 iterable object를 manual하게 iterate 할 수 있습니다. 이렇게 하지 않고 for loop을 사용하면 되는거 아니냐는 의문이 들 수 있습니다. Built-in object라면 가능할 것입니다. 하지만, user-defined object의 경우 for이나 다른 iteration context에서 예상과 다르게 작동할 수 있습니다. 그럴 때에는 , manual하게 iterate 한다면 해결이 될 것입니다. 


### File Iterator 
File iterator는 `iter()`를 통해서 iterator를 return할 필요가 없습니다 . 왜냐하면, file object 그 자체가 iterator이기 때문입니다. 
``` python
    
    f = open('test.txt')

    while True :

        try :
            item = next(f)
            print(item)

        except StopIteration: 
            break


```

이렇게 file을 open하면 `next()` 를 통해서 계속 item을 return할 수 있습니다. 다른 itertaor와 마찬가지로 item을 전부 return했다면 `StopIteration` exception을 raise 하게 됩니다 .

file의 경우 built-in object이기에 for 를 사용하는게 좋습니다. 

```python

    for a in open('test.txt'):
        print(a)

```

이렇게 사용하게 되면, readlines를 사용할 때 보다 큰 장점이 있습니다. readlines()는 전체를 읽어야 하기에 메모리에 전부 load해야 하지만, iterator의 경우 item만 load하기에 memory 사용에서 이점을 줍니다.  


## List Comprehension
List Comprehension은 `[` 로 시작해서 임의의 loop statement를 작성후 `]` 로 끝나는 것이 정의입니다. list comprehension은 for이나 while 같은 다른 loop statement로 치환해서 같은 result를 만들 수 있습니다. 
### why fast
그렇다면, 왜 더 빠를까요? Python은 internal이 C로 구현된 High level Language입니다. 따라서, for와 list comprehension의 performance를 비교하기 위해서는 bytecode를 통해서 실제로 어떤 명령어를 실행하는지 확인해야 합니다 . 
```python


def for_loop(n):
    my_list =[]
    
    for x in range(n):
        my_list.append(x)
        
    return my_list


def list_comp(n):
    
    return [x for x in range(n)]

import dis

dis.dis(for_loop)
dis.dis(list_comp)
```


```
  3           0 BUILD_LIST               0
              2 STORE_FAST               1 (my_list)

  5           4 LOAD_GLOBAL              0 (range)
              6 LOAD_FAST                0 (n)
              8 CALL_FUNCTION            1
             10 GET_ITER
        >>   12 FOR_ITER                14 (to 28)
             14 STORE_FAST               2 (x)

  6          16 LOAD_FAST                1 (my_list)
             18 LOAD_METHOD              1 (append)
             20 LOAD_FAST                2 (x)
             22 CALL_METHOD              1
             24 POP_TOP
             26 JUMP_ABSOLUTE           12

  8     >>   28 LOAD_FAST                1 (my_list)
             30 RETURN_VALUE

```
위는 for loop의 byte code입니다. 

```
 13           0 LOAD_CONST               1 (<code object <listcomp> at 0x7fddbda45d40, file "test.py", line 13>)
              2 LOAD_CONST               2 ('list_comp.<locals>.<listcomp>')
              4 MAKE_FUNCTION            0
              6 LOAD_GLOBAL              0 (range)
              8 LOAD_FAST                0 (n)
             10 CALL_FUNCTION            1
             12 GET_ITER
             14 CALL_FUNCTION            1
             16 RETURN_VALUE

Disassembly of <code object <listcomp> at 0x7fddbda45d40, file "test.py", line 13>:
 13           0 BUILD_LIST               0
              2 LOAD_FAST                0 (.0)
        >>    4 FOR_ITER                 8 (to 14)
              6 STORE_FAST               1 (x)
              8 LOAD_FAST                1 (x)
             10 LIST_APPEND              2
             12 JUMP_ABSOLUTE            4
        >>   14 RETURN_VALUE

```

위는 list comprehension의 byte code입니다.  

FOR ITER 부분부터 loop의 시작이므로 거기서부터 비교를 하면 됩니다. for loop의 경우 LOAD FAST(list) 다음에 LOAD METHOD(append)를 실행하는데 , 이는 cpython에서 지금 다루는 object의 type이 list임을 확인하고 그 중에서 append method를 찾아서 실행함을 의미합니다. 이렇게 실행이 되는 이유는 Cpython에서 object를 argument로 받을 때 Pyobject라는 abstract type으로 받은 다음에 매크로를 통해 특정 object로 확장해 나가기 때문입니다.  
하지만, 이렇게 list를 만드는 작업은 자주 하는 명령이기 때문에 , Python에서 이를 위한 특별한 bytecode인 LIST_APPEND를 만들어 주어서 list comprehension시 더 빠른 속도로 실행하게 됩니다. 


### extended

List Comprehension은 basic syntax에 몇가지를 더 추가해서 확장된 version으로 사용할 수 있습니다.

우선은 if을 사용해서 filtering을 할 수 있습니다.

```python
    a = [x for x in range(n) if x %2 == 0]

    a=[]
    for x in range(n):
        if x %2 == 0 :
            a.append(x)
```

마찬가지로, for loop으로도 똑같은 결과를 만들 수 있습니다. 이 경우 , compare 하는 부분이 추가되기에 속도는 좀 더 느려질 것입니다.

그리고 , 다중 for loop을 사용해서 nested loop statement를 만들 수 있습니다.

```python

    a = [x for x in range(n) for _ in range(n)]

    a=[]
    for _ in range(n):
        for x in range(n):
            if x %2 == 0 :
                a.append(x)

```

위와 같이 nested loop statement를 사용할 수 있습니다 .  



## Other Built-in iteration tool
for 이외에도 iteration을 지원하는 tool들이 있습니다. 이러한 tool 중에서 left에서 right 로 scan하는 어떤 tool이든 iteration protocol을 적용해서 iterate를 하게 됩니다. 
그리고, 이러한 tool들 대부분 iterable object를 argument로 받습니다.

이러한 tool중에서 주목할 만한 것들 몇개를 알아보겠습니다.

* range는 연속된 정수의 sequence를 iteration해주는데 , len 과 indexing을 지원합니다. 


* zip,filter,map은 iterator를 여러개  return해도 fetching 한 position이 여러 iterator들 사이에서 공유되지만 range는 각각 개별의 position을 갖는 iterator를 가질 수 있습니다.




## Advanced notion of iteration protocol 

이번에는 iteration protocol이 적용되는 심화 예시 및 iteration tool들을 비교해 보겠습니다.

* map은 list에 argument로 주면 , list를 return하는데 차이점으로는 map은 function을 argument로 필요합니다.

* list의 extend method와 append method는 extend는 iterable object가 argument로 들어오면 iteration protocol을 적용하여 개별 item을 저장하지만, append 는 iterable object 그 자체를 저장합니다.

* map은 각 item에 function을 적용하지만, reduce는 function 의 결과를 accumulate하여 적용합니다.

* sequence unpack, special argument * , in membership, slice assignment 모두 iteration protocol이 적용됩니다. 

## References

[Why list comprehension so fast](https://www.youtube.com/watch?v=U88M8YbAzQk)

[Python Docs gloassary-iterable,iterator ](https://docs.python.org/3/glossary.html#term-iterable)

[Learning Python chp 14 iteration and list comprehension](https://www.amazon.com/Learning-Python-5th-Mark-Lutz/dp/1449355730)