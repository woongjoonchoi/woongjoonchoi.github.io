---
title : "Python loop statement general format and general definition"



excerpt: "for and while detail in Python"

categories:
  - Python
tags:
  - [Python,loop,Statemens]
# classes : wide
toc: true
toc_sticky: true
---
# Python loop statements general format

Python에서 사용할 수 있는  loop statement로는 2가지가 있습니다. 하나는, for statement, 다른 하나는 while statement입니다. 어떻게 사용하는지는 다들 대략적으로 알고 있습니다. 

하지만, 이에 대한 semantic한 definition 과 general 한 syntax에 대해서는 잘 알지 못할것입니다. 이렇게 , 공부하는 것은 Programming language를 배울때 안좋다고 하지만, 저는 formulate하는 것이 이러한 logic을 처음 보는 문제에 응용하는 좋은 방법이라고 믿고 있습니다. for statment, while statement, loop 에서만 허용된 statement에 대해서 semantic 한 definition과 general 한 syntax로 나타내보도록 하겠습니다.





# while

> while statement는 general 한 loop 를 나타냅니다. 

즉, general한 proecedure 를 만들기 위한 statement입니다. while loop로는 모든 종류의 loop를 만들수 있습니다. 

while의 general한 format은 아래와 같습니다.

```
while test : # test expression
	statements # intended expression
	if test : continue  # Go to top of loop
	if test : break  #Exit loop
[else :] # Optional
	statements
```





# for

> for loop은 iterable objects나 sequence 안에 있는 item들을 조회해나가는 statement 입니다.

iterable objects에는 list,tuples, sets, user-defiend iterable objects등이 포함될 수 있습니다.

for statement의 general한 format은 아래와 같습니다.

```
for target in iterable :  # Assign item in iterable to target
	statements
	if test : continue  # Go to top of loop
	if test : break  #Exit loop
[else :] # Optional
	statements
	
```

## target assignment

`target in iterable`  은 assignment statement라 봐도 괜찮습니다. 즉 , iterable 안의 item이 target에 assign 됩니다.

assignment이면서 어떤 종류의 assignent도 제한이 없습니다.



```
for (a,b) in S :
	print(a,b)
```

tuple 형태의 assignment도 가능합니다.

```
for target in S :
	a,b = target
```

이와 같이 sequence를 unpack할 수도 있습니다.

뿐만 아니라 , target에 어떤형태의 expression도 가능하기에 , nested expression도 가능합니다.

```
for ((a,b),c) in S :
	print(a,b,c)
	
```

뿐만 아니라 , target에 어떤형태의 expression도 가능하기에 , nested expression도 가능합니다





```
for (a, *b, c) in S :
	print(a,b,c)
```

starred expression도 가능합니다.



***<u>즉 , Python assignment에서 허용된 syntax라면 전부 사용할 수 있습니다.</u>***

# Continue, break, loop else

## continue , break

continue, break는 자주 쓰이지만, 이에 대해서 define해보도록 하겠습니다.

continue statement는 즉각적으로 바로 loop의 맨 위로 가게 하는 statement입니다.

break statement는 바로 loop를 나오게 하는 statement 입니다. 

## loop else

loop else는 loop statement의 test가 False가 되면 실행이됩니다.  혹은, for loop에서 iterable이 소진이 되면 사용될 수 있습니다. 하지만, break statement로 loop가 종료가 되면 실행되지 않습니다.

이는 try except statement와 비슷한데, try statament에서 else는 except가 실행되지 않으면 실행되고 , for loop or while loop에서는 break가 실행되지 않으면 else가 실행됩니다.

loop 에서 flag status를 이용하여 loop를 실행할 때 , 원하는 조건을 만족하지 못하고 전체 loop를 iterate하는 경우, flag를 이용한 coding structure를 자주 사용하게 됩니다. 하지만, loop else를 사용하면 좀더 explicit한 coding structure를 제공해준다고 볼 수 있습니다.

즉, flag status 나 다른 condition을 설정하지 않고 , loop flow에서 벗어나는 flow를 control 할 수 있게 해줍니다.

```
for n in range(2, 10):
...     for x in range(2, n):
...         if n % x == 0:
...             print(n, 'equals', x, '*', n//x)
...             break
...     else:
...         # loop fell through without finding a factor
...         print(n, 'is a prime number')
...
2 is a prime number
3 is a prime number
4 equals 2 * 2
5 is a prime number
6 equals 2 * 3
7 is a prime number
8 equals 2 * 4
9 equals 3 * 3
```

소수를 구하는 예제인데 , factor를 발견하지 못한다면, loop else를 사용하여 소수임을 알려줍니다.

```
for n in range(2, 10):
		flag =False
...     for x in range(2, n):
...         if n % x == 0:
...             print(n, 'equals', x, '*', n//x)
				flag = True
...             break
...     if not flag  :
			print(n, 'is a prime number')
...
2 is a prime number
3 is a prime number
4 equals 2 * 2
5 is a prime number
6 equals 2 * 3
7 is a prime number
8 equals 2 * 4
9 equals 3 * 3
```

만약에 , loop else가 없다면 flag status를 이용하여 자연스럽지 않은 coding structure를 사용하게 될 것입니다.

```
while x: # Exit when x empty
     if match(x[0]):
         print('Ni')
         break # Exit, go around else
     x = x[1:]
else:
     print('Not found') # Only here if exhausted x
```

while의 loop를 통해 발견하지 못한다면 loop else 로 그 flow를 처리해줄 것입니다.

```
found = False
while x and not found:
     if match(x[0]): # Value at front?
         print('Ni')
         found = True
     else:
         x = x[1:] # Slice off front and repeat
if not found:
     print('not found')	
```

loop else를 사용하지 않는다면 flag status로 따로 loop 밖에서 발견하지 못한  flow를 처리해 주어야 할 것입니다.
