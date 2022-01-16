---

title : Recursion to Iteration

# layout: "post"

excerpt: "Recursion , Iteratoin"

categories:
  - Algorithm
tags:
  - [Algorithm,Recursion,Iteration,Computer Science]

toc: true
toc_sticky: true
---



저번에 Recursion , Iteration 에 대해서 다루었습니다. Recursion , Iteration은 서로가 서로를 대체하는 것이 아닌, 상황에 맞게 바꿔서 사용하는 Algorithm 입니다. Computer Architecture 관점에서 보았을 때 , Recursion, Iteration 은 모두 같은 일을 합니다. 따라서, 서로 간의 전환이 언제든지 가능하다는 것입니다. 하지만, 이에 대한 일반적인 방법론은 찾아보았지만, 없었습니다. 제가 이를 시행착오 하면서 얻은 둘의 차이점에 대한 고찰과 제 나름의 방법론을 제시해볼려고합니다. (이번 글은 평소보다 가독성이 좀 떨어질 수 있습니다.)



## Recursion vs Iteration

Recursion 과 Iteration을 비교해보도록 하겠습니다. 우선 둘의 공통점은 특정 서브루틴(파이썬의 경우 `함수`) 의 수행을 완료하면 State를 변환시킨 다는 것입니다. 

```python
def fact(n) :
	if n == 1:
		return n
	return n * fact(n-1)

```

factorial 함수를 예로 들면  재귀함수의 호출이 완료되면 , 이전 caller에서의 state로 변환해줍니다. 

```python
a = 1
while n > 0:
	a *= n
	n-=1

```

while 구문에서 서브루틴은 while 안의 코드라 볼 수 있습니다.  매 iteration 마다 n을 1씩 감소시키는 것을 확인할 수 있습니다. 

```python
stack =[]
while n> 0 :
	stack.append(n)
	n-=1
a = 1
while stack :
	a *= stack.pop()

```

이와 같이 변환시킬수도 있습니다.  auxiliary stack을 이용해서 state를 복원할 수 있습니다. state 의 복원이 정해져 있지 않고  , branch에 따라 바뀌는 경우 ,  auxiliary stack을 사용해야 합니다.  저는, 일반적인 경우를 가정할 것이기에 auxiliary stack을 사용한다고 가정하겠습니다. 

둘의 차이점은 우선 namespace의 개수라 볼 수 있습니다. 

![image](https://user-images.githubusercontent.com/50165842/149150164-eb63ac0b-91d9-4e20-afbc-fbe567208a81.png)

recursion program의 경우 자기 자신을 호출할 때 마다 새로운 stack frame을 형성합니다.  연속된 주소에 할당하지 않고 , 임의의 주소에 할당하게 됩니다.

```
import inspect
def fact(n) :
    if n==1 :
        print(inspect.stack())
        return 1 
    return n * fact(n-1)

fact(4)

>>frame=<frame at 0x7fa1c0b5f3a0,
>>frame=<frame at 0x7fa1c0b5f1f0,
>><frame at 0x7fa1c3fc0dc0
...
```

보면 stackframe간 의 byte 차이를 보면 n이 클수록 overhead가 될 수 있음을 알 수 있습니다. n이외에도 다른 값들이 frame안에 저장되기에 address간의 차이가 integer size보다 더 큼을 알 수 있습니다. 실제로 , python 의 recursion 에는 limit가 걸려있습니다.

반면, iteration 은 global scope의 namespace 1개만 생성이 됩니다 .  이러한 , 차이점으로 굉장히 중요하면서 큰 차이점이 생깁니다.

## where to go?

Python 뿐만 아니라 대부분의 programming language는 recursion concept을 지원합니다 . 함수(서브루틴)를 수행후 이전 caller의 frame(scope)으로 돌아가게 됩니다. 이는, 각 frame 안에  이전 caller의 frame에 대한 포인터가 있음을 의미합니다. 하지만, iteration의 경우 frame이 1개입니다 . 돌아갈 frame이 없습니다. 이를 다른 말로 표현하면, ***<u>recursion은 이전 state를 caller 의 frame으로 돌아가서복구할 수  있지만, iteration은 이전의 frame으로 돌아가는 방법으로 state를 복구할 수 없습니다</u>*** . 따라서, iteration 의 경우 state를 복구해주어야 한다면, 추가적인 저장공간이 필요합니다. 따라서, auxiliary stack이 필요하게 됩니다. 추가적인 stack을 이용해서 이전 state로 복구를 해주는 것입니다. 



## Steps : Recursion to Iteration

이제 , Iteration의 stack을 복구하는 방법을 알았으니 변환을 해줄 차례입니다.Recursion에서 Iteration으로 바꾸는 과정이 그리 쉽지는 않다고 개인적으로 생각합니다. factorial은 직관적인 예제이고 , 굳이 stack을 쓰지 않아도 되는 예제이기에 변환이 쉽다고 생각합니다. 하지만, branch를 통해서 어떤 state를 복원할지 정하는 예의 경우 상당히 복잡해진다고 생각합니다. 대표적인 예로 , binary tree traversal이 있다고 생각합니다.  유명한 예제를 외워도 되겠지만, Recursion에서 Iteration을 바꾸는 step을 정해놓고 바꾸는 연습을 하면 좀 더 체계적으로 바꿀 수 있다고 생각합니다. 제가 소개할 것은 일반적인 solution이 아닌 , 어떠한 과정을 수행해야 하는지를 단계별로 정리해 놓은 것입니다.(제 개인적인 시행착오에 의한 결과임으로 정확하지 않습니다 .)

### 1. Recursion 작성

우선은 Recursion 프로그램으로 작성할 수 있는게 첫번째 조건이라고 생각합니다. Recursion 프로그램은 인간이 직관적으로 볼 수 있기에 여기서 부터 출발할 수 있으면 좋다고 생각합니다. 여기서 가장 중요한 것은 Recursion 프로그램을 작성할 때 대충 PseudoCode로 작성하는게 아닌 정의에 따라서 엄밀하게 작성하는 것이 좋다고 생각합니다. Recursion의 정의를 다시 회상해보도록 하겠습니다.

- Programming technique that function call itself
  - In programming, the goal is not to have infinite Recursion
    - ***<u>must</u>*** have 1 or more base cases
    - ***<u>must</u>*** solve same problem on other input with goal of simplifying larger input

그림슨 교수님의 정의를 다시 가져왔는데 , 여기서 must라는 단어가 사용된 것을 알 수 있습니다. 즉 , ***<u>반드시 지켜야만 하는 rule이라는 것입니다</u>***.  Recursion은 base case를 기준으로 branch가 이루어집니다. Base case에서의 서브루틴을 수행할지 , 일반적인 case에서의 루틴을 수행할지 결정합니다. 이를 정하지 않으면, iteration에서 어떤 state를 저장할지 정할수 가 없습니다. 그리고 , 매번 같은 문제를 풀어야 하는것을 반드시 지키는 것이 중요합니다. 이 조건 역시 branch를 어떻게 정하는지 와 연결이 되기에 명확하게 지켜주어야 합니다.

```
procedure inorder(node)
    if node = null
        return
    inorder(node.left)
    visit(node)
    inorder(node.right)
```

inorder traversal은 위와 같이 작성이 될 것입니다. 

### 2.어떤 state를 복원해야 하는가

이제 , Recursion을 엄밀하게 작성을 했다면, 어떤 state로 복원을 해야할지 정해주어야 합니다. Recursion 프로그램을 보고 Branch를 만들면 비교적 쉽게 할 수 있습니다 .

예를 들어, Tree순회의 경우 base case는 node가 null인가 아닌가 입니다. 따라서, 분기 조건은 아래와 같게 됩니다.

- node가 null 값이다
  - base case
- node가 null 값이 아니다.
  - 함수 실행

```
procedure iterativeInorder(node)
    stack ← empty stack
    while not stack.isEmpty() or node ≠ null
        if node ≠ null

        else

```

작성한 Recursion 프로그램을 살펴보면 , base case일경우 그저 return하는 것을 확인할 수 있습니다. 이 말은 직접 state를 복구하는 과정을 프로그램이 대신 해줘야 함을 의미합니다 .inorder의 경우 node를 방문했을 때 null 값이면 root를 방문후 right subtree를 방문하면 될 뿐입니다. 이 경우는 간단하게 아래와 같이 작성할 수 있습니다.

```
procedure iterativeInorder(node)
    stack ← empty stack
    while not stack.isEmpty() or node ≠ null
        if node ≠ null
        
        else
            node ← stack.pop()
            visit(node)
            node ← node.right
```

null 값이 아니라면 , 서브루틴을 실행하게 됩니다. Recursion의 경우 left subtree를 방문한 후 다시 서브루틴을 실행하게 됩니다. 따라서, 아래와 같이 작성하면 될 것입니다.

```
procedure iterativeInorder(node)
    stack ← empty stack
    while not stack.isEmpty() or node ≠ null
        if node ≠ null
            stack.push(node)
            node ← node.left
        else
            node ← stack.pop()
            visit(node)
            node ← node.right
```

inorder에서 node가 null인 경우 따로 명시는 안했지만 , state를 복구하는 과정이 단순했습니다.  단순한 의식의 흐름대로 코드를 작성해도 잘 동작을 합니다. 하지만, 이 restore 과정에 대해 집중을 할 필요가 있습니다. postorder의 경우 restore 과정에 집중을 해야 합니다. 

```
procedure postorder(node)
    if node = null
        return
    postorder(node.left)
    postorder(node.right)
    visit(node)
```

postorder의 경우도 base의 case는 다른 tree traversal 알고리즘과 같습니다. 따라서, 아래와 같이 iterative 알고리즘으로 변환이 될 것입니다 . 방문한 node가 null이 아닐경우 서브루틴을 실행하게 될 것입니다.

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def postorderTraversal(self, root: Optional[TreeNode]) -> List[int]:
        
        stack = []
        curr = root
        ans = []

        if root is None : return []
        while stack  or curr is not None:
            if curr is not None :
                stack.append(curr)
                curr = curr.left
            else :

        return ans
```

방문한 node가 null일 경우 case가 inorder보다 비교적 어렵습니다.  

 Inorder Traversal를 코드를 자세히 보면, null인 경우 stack에서 다음에 방문할 노드를 pop해서 정해줍니다. 이는 , 강제적으로 다음에 방문할 노드를 정해주는 것입니다. Inorder의 경우 항상 stack의 top을 방문합니다. 어떤 경우에도 base case에 도달하면 이전 caller의 state를 복원해서 사용하게 됩니다. 하지만, postorder는 그렇지 않습니다. stack에 있는 노드가 아닌 stack의 right를 방문하게 됩니다. 

즉, ***<u>어떤 state를 복원하게 되는지 잘 알고 있어야 합니다.</u>***   ***<u>postorder는 leftsubtree를 방문한후 stack의 top을 복원하는 것이 아닌 새로운 state를 갖고 다시 서브루틴을 수행하게 됩니다</u>***.  Recursion을 정의에 입각해서 만든 후 어떤 state를 복원하는지 체크를 해야합니다. 

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def postorderTraversal(self, root: Optional[TreeNode]) -> List[int]:
        
        stack = []
        curr = root
        ans = []
        # stack.append(curr)
        lastvisitnode = None
        if root is None : return []
        while stack  or curr is not None:
            if curr is not None :
                stack.append(curr)
                curr = curr.left
            else :
                temp = stack[-1].right
                if temp is None : 
                    ans.append(stack[-1].val)
                    temp = stack.pop()
                    while stack and stack[-1].right == temp :
                        temp = stack.pop()
                        ans.append(temp.val)
                else :
                    curr = stack[-1].right
        return ans
```

postorder의 자세한 디테일은 생략하겠습니다. 중요한 것은 curr이 모든 branch에서 가리키는 대상이 바뀌지 않는 것을 주목해야 합니다. curr은 Recursion에서 argument 값이라 볼 수 있습니다. Recursion에서 Argument가 바뀌는 것은 새로운 stack frame을 생성할 때입니다. Recursion Program은 여러개의 stack frame을 만들 수 있고 , 한 프로그램으로 여러개의 branch를 가지는 것과 같은 효과를 보여줍니다 .하지만, Iterative Program은 모든 state에서 호출이 종료되면, 어떻게 복구해야할지 알려줘야 합니다 . 각 분기별로, 알고리즘을 작성해도 괜찮지만, 그렇게 될 경우 recursion 프로그램을 변환한 것이 아닌 새로운 알고리즘을 작성해 준 것입니다. 아까 말했던, 매번 작아지는 다른 input에 대해 같은 문제를 풀지 않은 것입니다. ***<u>같은 문제를 풀기 위해서 는 , state를 복원을 해주는 코드를 따로 작성해주는 것이 좋다</u>***고 개인적으로 생각합니다. ***<u>State 를 복원하는 과정에서 curr(Recursion에서 argument 역할)은 새로운 서브루틴을 실행하기 전까지 base case(여기서는 null) 값을 가지고 있게 해서 복원하는 중임을 명시해주는 것이 좋다고 생각합니다.</u>***

