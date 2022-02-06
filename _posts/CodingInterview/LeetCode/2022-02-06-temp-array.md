---

title : Array Rotation Program

# layout: "post"

excerpt: "Array Rotation problem in geeksforgeeks"

categories:
  - CodingInterview
tags:
  - [Algorithm,CodingTest,CodingInterview]
# classes : wide
toc: true
toc_sticky: true
---
Problem Link : [Rotate Array | Practice | GeeksforGeeks](https://practice.geeksforgeeks.org/problems/rotate-array-by-n-elements-1587115621/1#)
rotation array라는 문제가 있습니다. 이 문제는 array의 element들을 왼쪽으로 d칸씩 이동시키는 문제입니다. offset 0을 벗어나는 element들은 마지막 offset에 element가 위치하게 됩니다.  이 문제들을 점진적으로 솔루션을 개량시켜나가면서 풀어보도록 하겠습니다.

> **Your Task:**
> Complete the function **rotateArr**() which takes the array, D and N as input parameters and rotates the array by D elements. The array must be modified in-place without using extra space. 
>
> **Expected Time Complexity:** O(N)
> **Expected Auxiliary Space:** O(1)
>
> **Constraints:**
> 1 <= N <= `10**6`
> 1 <= D <= `10**6`
> 0 <= arr[i] <= `10**5`





## temp array

```python
class Solution:
    #Function to rotate an array by d elements in counter-clockwise direction. 
    def rotateArr(self,A,D,N):
        #Your code here
        D = D%N
        temp  =  A[:D]
        
        for i in range(D,N) :
            A[i-D] = A[i]
        A[N-D:] = temp
```

1. 우선 D 값이 N 값보다 클 수 있습니다. N칸을 시계 반대방향으로 회전하면 위치가 그대로 이므로 , D = m * N +r 이라 가정했을 때 , m 번만큼의 N칸 이동은 element의 위치에 영향을 주지 않습니다. 따라서, modular 연산을 이용해서 r칸을 구하도록 합니다.
2. Array를 시계 반대방향으로 회전시키므로 왼쪽의 D칸의 element들을 저장합니다. 왜냐하면, 왼쪽을 저장하면 중간에 state를 저장할 필요가 없지만, 오른쪽 부분을 저장할 경우 중간 state를 저장해야해서 operation 수행 횟수가 증가하므로 왼쪽부분을 저장합니다.
3. 오른쪽 부분을 D칸만큼 이동시키는데 , 각 작업이 반복적인 pattern이므로 for문을 사용하여 이동시킵니다.
4. 저장했던 왼쪽 부분을 오른쪽 부분에 복사합니다. 

> **Time complexity :** O(n) 
> **Auxiliary Space :** O(d)



## rotation each

위와 같이 하면 Big-o notation으로 linear time complexity를  보장할 수 있지만, Auxiliary Space가 발생합니다. Auxiliary Space가 허용되지 않는다면 다른 방법을 간구해야 합니다.

```
class Solution:
    #Function to rotate an array by d elements in counter-clockwise direction. 
    def rotateArr(self,A,D,N):
        #Your code here
        D = D%N
   
        for _ in range(D) :
            temp = A[0]
            for j in range(1,N) :
                A[j-1] = A[j]
            A[-1] = temp
        
```

1. 우선 D 값이 N 값보다 클 수 있습니다. N칸을 시계 반대방향으로 회전하면 위치가 그대로 이므로 , D = m * N +r 이라 가정했을 때 , m 번만큼의 N칸 이동은 element의 위치에 영향을 주지 않습니다. 따라서, modular 연산을 이용해서 r칸을 구하도록 합니다.
2. 첫번째 , 원소를 저장합니다.
3. 나머지 원소를 왼쪽으로 1칸씩 이동합니다. 반복적인 pattern이므로 for문을 사용합니다. 마지막 위치에 저장했던 첫번째 원소를 대입합니다.
4. 위 3번작업을 반복하므로 for문을 사용합니다.

> **Time complexity :** O(n * d) 
> **Auxiliary Space :** O(1)





## juggling

위의 solution은 Auxiliary Space가 constant time이지만, Time Complexity 가 O(n*d) 로 증가하였습니다. 문제의 제약사항이 Time Complexity 는 O(n) 이고 Auxiliary Space 가 O(1) 이면 다른 방법을 간구해야 합니다.  Auxiliary Space가 constant time인 idea는 위의 문제에서 다루었습니다. element를 array의 size에 비례하지 않은 개수 만큼 저장을 하는 것입니다. Time Complexity가 linear time이 될려면 , 위에서 보았듯이 element를 1번만 이동해야 합니다. 위 두가지 idea를 어떻게 조합하면 될까요?

```python
class Solution:
    #Function to rotate an array by d elements in counter-clockwise direction. 
    def gcd(self,a,b) :
        if b == 0 :
            return a
        else :
            return self.gcd(b , a%b)
    def rotateArr(self,A,D,N):
        #Your code here
        D = D%N
   
        g = self.gcd(N,D)
        for i in range(g) :
            temp  = A[i]
            for j in range(1,N//g) :
                A[(i+(j-1)*D)%N] = A[(i+j*D)%N]
            else :
                A[(i-D)%N] = temp
                
```

맨 처음 원소를 저장하고 이전 원소를 D칸씩 이동하는 idea가 위의 두 풀이방법을 합친 idea라 볼 수 있습니다. 하지만, 이 idea를 적용할 때에는 주의할점이 있습니다. 

n,d의 gcd가 존재한다면 $$ n = m * g \quad d = q *  g$$ 의 관계를 만족하게 됩니다. m , q 는 서로소의 관계를 만족합니다.  d칸 왼쪽으로 이동을 할 때 offset 의 범위를 벗어난다면 회전의 관계이므로 N을 더해주게 됩니다. 이는 modular 연산과 과정이 일치하므로 , modular연산으로 구할 수 있습니다. 마찬가지로, 현재 위치에 들어오는 값의 offset(index) 은 현재 위치에서 오른쪽으로 +해준 값입니다.  만약에 , offset이 범위를 벗어난다면 N을 빼주게 되므로 , modular연산의 결과와 일치하게 됩니다. 

d칸씩 왼쪽으로 매번 이동을 한다고 했을 때 , 시작점을 offset 0의 위치로 가정하고 , d칸을 왼쪽으로 이동하게 된다면 다음 offset은 $$ -D\ , \bmod\ , N $$ 이 될 것입니다. 2번째 이동을 하게 된다면 $$ ({(-D\, \bmod\, N)} - D ) \bmod N $$ 가 될 것입니다. mod연산을 중첩해서 적용해도 결과값은 같으므로  $$(-D\, \bmod\, N) - (D  \bmod N)$$ 이므로 이는 modular 연산의 성질에 의하여  $$ ({(-2D\, \bmod\, N)} - D ) \bmod N $$ 가 됩니다. mathmatical induction을 적용하면 k 일 때 $$(- kD\, \bmod\, N) $$ 가 성립한다고 가정하면 비슷하게 k+1일 때 도

$$
\begin{align}

(-kD\, \bmod\, N) - (D  \bmod N) &= (-k-D\, \bmod\, N) \\

&= (-(k+1)D\, \bmod\, N)

\end{align}
$$

가 성립함으로 일반식을 도출할 수 있습니다. D는 q * g 이므로 이를 m번 반복한다면 다시 원래 위치로 돌아오게 될 것입니다. 즉, 처음 위치에서 i를 1증가시키고 m번 이동하는 operation을 d번 반복해야 하는 것을 알 수 있게 됩니다. 

1. 우선 D 값이 N 값보다 클 수 있습니다. N칸을 시계 반대방향으로 회전하면 위치가 그대로 이므로 , D = m * N +r 이라 가정했을 때 , m 번만큼의 N칸 이동은 element의 위치에 영향을 주지 않습니다. 따라서, modular 연산을 이용해서 r칸을 구하도록 합니다.
2. cycle이 생길수도 있으므로 gcd를 구합니다. 
3. m번을 이동하는 pattern이 반복적이므로 for문을 사용해주도록  합니다. 오른쪽에서 왼쪽으로 이동시키는 문제이므로 위에서 풀었던 solution을  응용하여 오른쪽에 있는 값을 왼쪽으로 이동시키는 방법을 적용합니다. 처음값을 저장하는 것을 잊으면 안됩니다.
4. m번 이동하는 pattern을 d번 반복적으로 시행하므로 for문을 사용하도록 합니다. 시작 index를 바꾸어 주어도 , 대칭적이기 때문에 m번 이동하는 pattern을 같은 pattern을 적용해도 의도한대로 operation이 작동합니다. 
5. m번 이동에서 마지막 이동은 맨 처음값을 회전된 offset에 assign하도록 합니다. 

## reverse

a 의 Sequence의 순서가 반대로 되어질 때  , 이를 ra라 하겠습니다. 어떤 시퀸스를 두 부분 a,b 로 나눈다면, S = ab 라 하겠습니다.

이때 , rS = rbra가 됩니다. 순서가 반대로 되어지면, 마지막 offset이 시작 offset이 되어집니다. b의 시작 offset이 b의 length 만큼 떨어진 곳에 위치하게 됩니다. element 간의 관계가 바뀌질 않으므로 시작 offset보다 b의 length 만큼 떨어진 곳에는 b의 element만 들어있고 , 따라서 , rb 가 첫 부분이 됩니다. 마찬가지로, ra도 적용될 수 있습니다.

따라서 , rarb 라는 시퀸스를 reverse한다면 ba 라는 시퀸스를 얻을 수 있습니다 . 

```

class Solution:
    #Function to rotate an array by d elements in counter-clockwise direction. 
    def reverse(self,A,s,e) :
        for i in range(0 , (e-s+1)//2) :
            temp = A[s+i]
            A[s+i] = A[e-i] 
            A[e-i] = temp

        
    def rotateArr(self,A,D,N):
        #Your code here
        D = D%N
   
        self.reverse(A,0,D-1)
        # print(A)
        self.reverse(A,D,N-1)
        # print(A)
        A = self.reverse(A,0,N-1)
        # print(A)
```

1. reverse 함수는 구간의 시작, 끝 offset 의 합이  각 offset이 중앙을 향해 변해도 일정함을 이용하여 구간 길이의 절반 만큼만 점진적으로 변하게 하면서 element를 swap해준다. 반복적인 pattern이므로 for문을 사용한다. 
2. A를 D칸 기준으로 slicing 한다음에 reverse한다.
3. 나머지 부분도 reverse를 한다. 
4. 전체를 reverse한다.