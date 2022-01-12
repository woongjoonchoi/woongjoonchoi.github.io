---

title : Recursion vs Iteration

# layout: "post"

excerpt: "Recursion , Iteratoin"

categories:
  - Algorithm
tags:
  - [Algorithm,Recursion,Iteration,Computer Science]

toc: true
toc_sticky: true
---


요새, 자료구조와 알고리즘을 공부하는데, Recursion을 이용하는 문제가 자주 나옵니다. Recursion 의 solution 자체도 쉽지는 않지만, 이를 Iterative 하게 바꾸는 것은 더 어려운거 같습니다. Recursion 자체의 stack frame 생성 문제 때문에 Iterative를 써야만 하는 상황이 생길 수 있습니다. 이러한 상황에서 변환을 할 때 , 변환을 어떻게 해야하는지에 대한 기준이 없어서 매번 solution을 보고 외우게 되는거 같다고 생각이 들었습니다. 이는 Recursion에 대해서 그저 return 할 때 f(n-1) 을 호출 하는 것 , Iterative를 for,while을 쓰는 것으로 인지를 하고 있어서 , 기준 없이 문제를 풀기만 한다 느꼈습니다. 이에 대한 명확한 기준을 잡기 위해서 Recursion  , Iterative를 학부생의 마음가짐으로 이론적으로 접근해봤습니다.

이 내용은 MIT 6.001 recursion 강의를 듣고 작성했습니다.

## what is Recursion Algorithm

Eric Grimson 교수님이 Recursion 을 2가지 관점에서 정의를 해주십니다. 이는 교수님이 설명해주신 그대로 가져왔습니다.

- Algorithm
  - a way to solve problems by ***<u>divide-and-conquer</u>*** or ***<u>decrease-and-conquer</u>***
- Semantically
  - a programming technique where a ***<u>function call itself</u>***
    - in programming ,goal is NOT to have infinite Recursion
      - must have ***<u>1 or more base cases</u>*** that are easy to solve
      - must solve same problem on ***<u>some other input</u>*** with goal of simplifying the large input

왠만하면, 설명은 그대로 안가져오는데, 정의하신게 너무 훌륭해서 가져왔습니다. 이 부분은 공유를 무조건 해야한다고 생각이 들었습니다. 여태까지, 들었던 Recursion 에 대한 설명중 가장 훌륭했다고 생각합니다.  Algorithm 관점에서는 divide and decrease라 설명한 것은   base case에 도달하기 위해서 input이 변하기 때문입니다. `1 or more base cases` 부분을 강조해서 설명하십니다. Recursion은 계속 자기 자신을 호출하게 됩니다. 이 과정은 언젠가 종료가 되어야합니다. 따라서, base case로 어디서 종료를 할지 설정을 해줍니다. 이는, Program이 멈추는 방법이라 보시면 됩니다. `some other input` 이라는 부분을 강조하시는데 , 이는 어떠한 input이 주어지더라도 , 같은 문제를 푼다는 의미입니다  . input이 들어오면 같은 문제를 풀고 , `base case`에 도달하면 종료한다. 이것이 Recursion입니다.



## What is Iterative Algorithm

이 부분도 설명을 그대로 가져왔습니다.

- looping constructs(while and for loops) lead to iterative algorithms
- can capture computation in a set of state variables that update on each iteration through loop

iterative algorithm 은 state variable을 가져옵니다. 그리고 , state variable을 update 해나가면서 , 매 iteration 마다 iteration 내부에 갖고 있는 value를 computation 해서 update 합니다  . state variable이 특정 조건을 만족하게 되면, 종료합니다. 예시를, 들어보겠습니다.

### MULTIPLICATION - ITERATIVE SOLUTION

a * b 는 a를 자기 자신에 b번 더해주는 것과 같습니다.

- capture state by
  - an iteratoin number(i) starts at b
    - i  <- i-1  and stop when 0 
  - a current value of computation(result)
    - result <- result + a 

```
def mul(a,b) :
	result = 0
	while b > 0:
		b -= 1
		result += a
	return result
```



### Multiplication - recursive solution

a * b를 어떻게 recusrive 하게 생각하는지 알아보도록 하겠습니다. 어떻게 문제를 simple하고 더 small한 문제로 줄여나가는지를 고민해보시면 좋을거 같습니다.
$$
\begin{align}
a *b &= a+ a+ a .... a \\
&=  a + ( a+ a+ ..a) \\
& = a + a*(b-1)
\end{align}
$$


a *b 라는 problem을 a *(b-1) 이라는 좀더 작은 문제로 줄였습니다. 이 과정을 계속해서 반복해나가면서, base case에 도달하게 됩니다.

```
def mult(a,b) :
	if b== 1:
		return a
	else :
		return a + mult(a,b-1)
```

 

## Recursive in memory view

![image](https://user-images.githubusercontent.com/50165842/149150164-eb63ac0b-91d9-4e20-afbc-fbe567208a81.png)

Recursive Program을 computer memory 관점에서 보도록 하겠습니다. Recursive Programming은 자기 자신을 호출하는 함수입니다. 함수가 자기 자신을 호출하면 아래와 같은 과정이 일어납니다.

1.  create new frame, scope
2. bind variables in scope , these variables are not change by recursive call
3. when reach base case, flow of control passes back to previous scope



## Recursion vs Iteration

![image](https://user-images.githubusercontent.com/50165842/149150508-005f6b5f-4d4c-4a7c-bde0-82df184a49c2.png)

그렇다면 Recursion , Iteration 을 비교해보도록 하겠습니다.

- Recursion 은 Iteration 보다 직관적이다.
- Recursion 은 Programmer의 관점에서 효율적이다.
- Recursion은 Computer의 관점에서 ***<u>효율적이지 못하다.</u>***

Recursion이 왜 컴퓨터 관점에서 효율적이지 못할까요. Recursion 이 모든 기계에서 효율적이지 않은것은 아닙니다. 기계에 따라 다릅니다.

예제의 컴퓨터는 매번 함수를 호출할 때 마다 Frame을 새로 생성합니다. 따라서, 컴퓨터 관점에서는 비효율적이라 볼 수 있습니다. 하지만, 프로그래머 관점에서는 알아보기 쉽기에 효율적이라 할 수 있습니다

## Recursion Proof

Recursive가 좋은건 알겠는데, 이것은 왜 작동할까요? 



### inductive proof

```
def mul(a,b) :
	result = 0
	while b > 0:
		b -= 1
		result += a
	return result
```

Iterative mutliplication은 종료할까요? 종료합니다.  왜냐하면,  b는 초기값으로 loop 내부에서 매 iteration 마다 b가 1씩 감소하고 있고 ,  loop의 실행 조건으로 b 가 0보다 클때로 설정했습니다. 따라서 , 언젠가는 1보다 작아질 것이고 , 종료함을 알 수 있습니다.



```
def mult(a,b) :
	if b== 1:
		return a
	else :
		return a + mult(a,b-1)
```

 recursive multiplication은 종료할까요? 

- b = 1:  호출된 mult(a,b)  는 더 이상의 recursive call이 없기에 종료할 것입니다.
- b>1 :  호출된 mult(a,b) 는 좀 더 작은 b값에 대한 mult의 recursive call을 할 것입니다. b는 점점 감소하므로 결국엔 b=1 recursive call을 하는 순간에 도달하게 됩니다.

### mathmatical induction proof

이번에는 수학적 tool을 사용해보겠습니다. 수학은 프로그램에 대해서 해석하는 방법을 주는 좋은 tool입니다. 여기서  , 우리가 사용하게 될 것은 수학적 귀납법 입니다.  어떤 주장을 증명하고 싶으면 , 우리는 이를 정수에 의해 reference 할 수 있게 할 것입니다.(f(n)) . 

모든 자연수에 대해서 이를 증명할려면 , 우리는 보통 n= 0 , n=1 에 대해서 성립함을 증명해야 합니다. 위에서 사용했던 프로그램인 , mult(a,b) 를 수학적 귀납법으로 증명해보겠습니다.

1.  b = 1 :   mult(a,b)  =  a  * 1 = a 
2. b = k :  mult(a,k)  가 정확한 값을 리턴한다고 가정하겠습니다.
3. b= k+1 : mult(a,k+1)  = a + mult(a,k)  ,  정확한 값을 리턴합니다.



다른 예제에 대해서도 해보시면 좋을거 같습니다.



![러닝스텝 하노이탑 9단 : 다나와 가격비교](http://img.danawa.com/prod_img/500000/975/998/img/1998975_1.jpg?shrink=330:330&_v=20210520134149)



하노이 타워라는 유명한 예제입니다. 이 예제는 재귀적으로 풀 수 있다고 알려져 있습니다. 

```
def printMove(fr, to):
 	print('move from ' + str(fr) + ' to ' + str(to))
def Towers(n, fr, to, spare):
 if n == 1:
	printMove(fr, to)
 else:
    Towers(n-1, fr, spare, to)
    Towers(1, fr, to, spare)
    Towers(n-1, spare, to, fr)

```

이를 수학적 귀납법으로 증명해보도록 하겠습니다.

1.  n = 1, 타워를 옮길 수 있습니다. 
2.  n = k-1 , Towers(k,fr,to,spare) 가 정확한 answer를 도출한다 가정하겠습니다.
3. n =k , Towers(k,fr,spare,to)  =  Towers(k-1,fr,spare,to) ,Towers(1,fr,to,spare) ,  Towers(k-1 , fr,spare, to)   ,  k도 정확한 answer를 도출합니다.

