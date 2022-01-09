---

title : Threaded Binary tree and Morris Traversal

# layout: "post"

excerpt: "Binary tree"

categories:
  - DataStructure
tags:
  - [Algorithm,Datastructure,Computer Science]

toc: true
toc_sticky: true
---
이번에는 Threaded Binary Tree와 Morris Traversal에 대해서 다루어 보도록 하겠습니다.  Threaded Binary Tree는 Recursive Binary Tree Traversal 의 한계점을 극복하기 위해서 제안된 DataStructure입니다 . Morris Traversal은 Threaded Binary Tree의 idea를 이용해서 나온 binary tree traversal algorhithm 입니다. Recursive BinaryTree Traversal의 한계점이 무엇이며 , 이 한계점을 극복하기 위해서 어떤 idae를 썻는지 알아보도록 하겠습니다.



## Recursive Binary Traversal 's Issue

### using stack in worst O(n)

Recursive Algorithm을 사용하게 되면 python 기준 새로운 stackframe가 namespace가 생성이 됩니다 . 그리고 , 이 새로운 stackframe에 name이 bind 됩니다. 

![5-2. [자료구조] 이진트리(binary tree)](https://t1.daumcdn.net/cfile/tistory/9969463D5B33AEFA0A)

만약, binary tree가 완벽한(perfect) binary tree라면 tree의 높이는$$O(\log n)$$ 이 나올 것입니다. 따라서, 최소 $$ O(\log n)$$ 의 stackframe이 생성이 될것입니다.

![Time Complexity of Binary Search Tree | Gate Vidyalay](https://www.gatevidyalay.com/wp-content/uploads/2018/08/Time-Complexity-of-Binary-Search-Tree-Worst-Case.png)

만약에 binary tree 가 치우쳐져(skewed) 있다면 , tree 의 높이는 $$ O(n)$$ 가 될 것입니다. 따라서 , stackframe은 최악의 경우(worst case) $$O(n) $$ 개수 만큼 생성 될 것입니다.  Stackframe 생성에 제한이 있는 environment 라면, 상당히 큰 이슈가 될 것입니다.

 

### waste space

binary tree 의 properties 중 null link 는 $$ n + 1$$ 개라는 성질이 있습니다. 이를 간단히 증명하면 아래와 같습니다.

> node의 개수가 n개라면 pointer의 개수가 2n 개임은 자명하다. n-1개의 incoming edges를 가지고 있다. 따라서 ,null pointer의 개수는 2n - (n-1) = n+ 1이다.

leaf node는  left pointer, right pointer 가 null value를 가리키고 있습니다. 즉 , 이 공간은 낭비되고(wasted) 있습니다.  node를 struct 혹은 class로 구현한다고 가정했을때  , $$ n+1 $$ 만큼의 bit 가 낭비되고 있는 것입니다. 



### algorithm works in only root

Tree Traversal 알고리즘은 아주 큰 단점을 가지고 있습니다. 바로 , Root에서 시작을 할 때만 사용할 수 있는 알고리즘이라는 것입니다. 예를 들어 , Inorder Traversal을 할 때  , 왼쪽 subtree의 root 에서 부터 시작을 하면 , 오른쪽 subtree 의 root를 탐색할 방법이 없습니다. 오른쪽 subtree를 가리키는 pointer가 없기 때문입니다.  따라서, 제한적인 상황에서만 사용할 수 있는 알고리즘입니다.

## TBT(Threaded Binary Tree)

![스레드 이진 트리 - 위키백과, 우리 모두의 백과사전](https://upload.wikimedia.org/wikipedia/commons/thumb/7/7a/Threaded_tree.svg/1200px-Threaded_tree.svg.png)

Threaded Binary Tree, 즉 TBT는 stack frame을 사용하지 않고 , space wasted 를 해결하고 , root가 아니더라도 traversal이 가능하게끔 

하자는 motivation 에서 나왔습니다. 

Threaded Binary tree의 간단한 defintion은 아래와 같습니다.

> left null link ->  pointing preceeder
>
> right null link -> pointing suceeder
>
> except), leftmost null link , rightmost null link

놀랍게도 , 간단하게 위 세 줄을 정의한 것으로 위 3가지의 문제점이 모두 해결이 됩니다.

null link를 leftmost, rightmost 노드를 제외하고 preceeder ,suceeder node를 가리키게 함으로 , 공간의 낭비가 사라집니다. 다음에 , 어떤 노드로 갈지(suceeder)를 알기에 , stack으로 state를 복원해줄 필요가 없습니다. 따라서, stack이 필요가 없습니다. 

Inorder Traversal의 simulation을 해보도록 하겠습니다. (Inorder Traversal은 안다는 가정하에 설명하도록 하겠습니다.)

> 먼저 , left subtree 를 pointer를 통해 찾아가면서 A를 방문하게 됩니다.    ans : a
>
> 그 다음에 , right pointer가 B를 가리키기에 B를 방문합니다.   ans :  a,b
>
> 그 다음에 , 다시 알고리즘을 반복합니다.  현재 , 노드는 E가 됩니다.ans : a, b, c, e 
>
> E의 right 가 root이기에 F를 방문합니다. ans  : a,b,c,e,f
>
> 그 다음에 , right subtree를 방문합니다. ans : a,b,c,e,f , g ,h ,i



### TBT Implementation

TBT의 simulation 을 보면 , null link 였던, pointer와 , null link가 아니였던 pointer의 operation이 다릅니다 . 따라서 , null link였는지 아니였는지 추가적인 저장공간을 이용해서 표현해야합니다.

```c
struct TBTnode
{
	int value ;
    TBTnode * rlink;
    TBTnode * llink;
    bool r_null ;
    bool l_null;
};

// 오랜만에 c 언어 작성했는데, 문법 맞았는지 모르겟네여.. ㅠㅠ
```

1bit 짜리 2개로 각 rlink , llink가 null 값인지 알려준 다면 , operation을 구별해서 진행할 수 있습니다.

### TBT traversal 

TBT traversal은 iterative Indorder 유사합니다. 

```python
## psuedocode
## 대충 적은거 같지만 기분탓이다.
while :
    1. if l_null is not null :
        curr = curr.llink
        continue
    2. visit root
    3. if r_null is not null : 
        curr = curr.rlink
        continue
    curr = curr.rlink
```

iterative 하면서 , stack없이 inorder traversal이 구현이 될 수 있음을 알 수 있습니다 . 

### TBT analysis

하지만, 이게 항상 완벽한 자료구조는 아닙니다. 아래와 같은 단점을 가지고 있습니다.

#### DisAdvantages

- Insert/Delete operation이  너무 어렵고 복잡하다.
- boolean Value를 위한 extra space가 필요하다.

그렇다면, 이 TBT는 언제 사용하면 좋을까요?

#### When to use it

- Insert/Delete operation이 거의 일어나지 않는다.
- stack space가 제한이 되어 있다.

제 개인적인 생각으로는 , 읽기 전용의 Datastructure라고 느껴졌습니다.

## Morris Traversal

위에서 말했듯이  TBT를 construct를 하는 것은 굉장히 까다롭고 어렵습니다. Coding Interview에서 Inorder Traversal을 $$O(n)$$ 의 space complexity로 구현하라고 했을 때 , TBT를 construct하는 것은 굉장히 비효율 적입니다. Coding Interview 문제를 푼다고 가정한다면, 이 경우는 Tree를 쓰고 버린다는 전제조건이 깔린 것입니다. 즉, Tree를 Transformation을 해도 상관이 없습니다.(Tree를 Transformation을 해도 되는지 안되는지 물어는 봐야 할거 같습니다.)

Idea는 간단합니다. 이 tree를 skewed tree로 만드는 것입니다.  대략적인 psuedocode(입코드)는 다음과 같습니다.

```
1 . go to current left node
2. go to rightmost node , rightmost node right link point to current node
3. curr left link point to null
4 . if left node is null , visit curr node
5 .if left and right all null ,terminate algorithm
```

위 알고리즘을 무한히 반복하면  stack 없이 inorder traversal의 결과를 얻게 됩니다.

![Morris Traversal · LeetCode](https://ttzztt.gitbooks.io/lc/content/assets/Morris_Inorder.png)



친절하게 그림을 첨부하도록 하겟습니다. 위와 같은 순서로 노드를 방문합니다.

위의 psuedocode 보다 좀더 자세하게 구현한 psuedocode를 제공하도록 하겠습니다.

```
while :
	left child exist:
		morr <- curr.left
		mor  <- rightmost(morr)
		morr.right -> curr
		curr <-  curr.left
		morr->right->left -> curr
	left child none  and right is not NOne:
		ans <- curr
		curr <- curr.right
```

[CodingTest/94. Binary Tree Inorder Traversal3.py at main · woongjoonchoi/CodingTest (github.com)](https://github.com/woongjoonchoi/CodingTest/blob/main/leetcode/explore/binary_search_tree/inorder_traversal/94. Binary Tree Inorder Traversal3.py)

위의 링크는 제 github인데 leetcode morris traveral 문제를 implementation했습니다. 참고하실분은 참고하시면 됩니다.
