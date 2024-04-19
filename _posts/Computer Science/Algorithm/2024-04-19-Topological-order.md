---

title : Topological Ordering

# layout: "post"

excerpt: "DAG or Cycle"

categories:
  - Algorithm
tags:
  - [Algorithm,6.006,Computer Science]

toc: true
toc_sticky: true
---
Computer Science의 알고리즘을 공부하게 되면서 가장 먼저 접하게 되는 알고리즘중 하나로 DFS가 있습니다. DFS의 응용사례로 Topological Ordering 혹은 Topological Sort가 소개됩니다. Topological Ordering은 Computer Science 학부생들이 많이 했갈려합니다. 따라서, 이 알고리즘을 이해하기를 포기합니다. 면접 시즌이 다가오면, 면접을 위한 답변과 공식을 외우게 됩니다. 
하지만, Topological Ordering은 많은 Computer Science의 idea들의 전제조건이 됩니다. 이를 모르게 되면, 전제조건을 설정할 수 없기에 Topological Ordering은 Software Engineer나 Computer Scientist라면 알아야 합니다. 이 글에서 Topological Ordering이 무엇인지 알아보도록 하겠습니다. 



## Why DAG is important?
Topological Ordering 을 구할 수 있다는 것은 주어진 Graph가 Directed Acyclic Graph라는 것을 의미합니다.혹은 Cycle이 존재하는  graph의 vertex들의 Topological Ordering을 구할 수도 있습니다. 이는 주어진 Topological Ordering 순으로 vertex를 traverse하게 된다면 주어진 graph의 DAG인 subgraph를 구하게 됨을 의미합니다 .Intro에서 언급했던 문장을 다시 설명하면 DAG는 많은 Computer Science의 idea들의 전제조건이 됩니다. 어떤 경우에 DAG를 적용해야 할지를 이해하지 못한다면 많은 Computer Science의 Idea들을 이해하지 못하고 그저 가져다가 사용해야 할 것입니다. 

DAG의 모든 vertex를 traverse하기 위해서는 topological order의 처음 나오는 vertex부터 방문을 해야합니다. topological order에서 특정 vertex에서 이전 vertex를 방문할 수 없습니다. 이는 특정 vertex의 property를 계산하게 된다면 vertex의 property는 추후 변동되는 일이 없을것을 보장하게 됩니다.  brute force를 할 때, exponential time의 space를 탐색해야 하는 문제가 있습니다. 문제를 DAG형태로 표현이 가능하다면 , 중간 computation의 결과값이  determinstic하기 때문에 Linear Time의 space를 탐색하고 문제를 해결할 수 있게 됩니다. 

### Example of Dag
- Shortest path 
  - shortest path를 구할 때 모든 vertex를 고려해야 합니다. 하지만, DAG의 구조를 이용한다면 중간 shortest distance가 determinstic하기에 문제를 Linear Time에 해결할 수 있게 됩니다.
- Automatic Differntiation
  - DeepLearning에서 각 parameter의 gradient를 구할 때 chian rule을 사용하게 됩니다. output에 가까운 parameter들의 gradient부터 계산후 이를 input에 가까운 parameter에 순차적으로 대입해서 gradient를 계산하는 방법입니다. 이전의 gradient들은 determinstic 합니다. 즉, 계산하는 순간 다시 계산할 필요가 없기에 , automatic differntiation은 parameter의 Linear Time에 수행될 수 있습니다. 


## Topological Ordering Definition
Topological Ordering이 어째서 필요한지 알아보았습니다. 위 내용만을 가지고 Topological Ordering을 RealWorld의 Problem에 적용하기 어렵습니다.  Topological Ordering을 일반적인 상황에서 적용하기 위해서는  Topological Ordering의 Formal한 Definition을 알아야 합니다.아래에서 Topological Ordering의 Definition을 알아보도록 하겠습니다.

### Formal Definition
<!-- $$ O(N! \cdot N) $$ -->
> Topological Order of a Graph G = (V,E) is an ordering f on the vertices such that : every edge (u,v) $$ \in $$ E satisfies f(u) < f(v)

예를 들어 , graph G가 V = {A,B,C,D}  , E = (A,B)  ,(B,D) , (C,D) , (A,C) 라고 가정하겠습니다. 이 때 topological order f는 다음과 같은 value들을 가질 것입니다.
> f(A) = 1 , f(B) = 2, f(C) = 3 ,f(D) = 4

(A,B) 에 대하여 f(A) < f(B) 를 만족하고 , (B,D) 에 대하여 f(B) < f(D)  , (C,D) 에 대하여 f(C) < f(D)  ,  (A,C) 에 대하여 f(A) < f(C) 를 만족합니다. 다음과 같은 ordering f를 고려해보겠습니다.
> f(A) = 1 ,f(B) = 3, f(C) = 2,f(D) = 4 

f(B)와 f(C)의 값이 바뀌었습니다. 하지만,(A,B) 에 대하여 f(A) < f(B) 를 만족하고 , (B,D) 에 대하여 f(B) < f(D)  , (C,D) 에 대하여 f(C) < f(D)  ,  (A,C) 에 대하여 f(A) < f(C) 를 만족합니다.  

따라서, topological order는 unique한 order가 아님을 알 수 있습니다. 


## Find Topological Ordering
Topological Ordering의 Ordering의 Formal 한 Definition을 알았으니 Vertex(or Node)의 Sequence가 주어진다면 이 Vertex들이 Topological Ordering인지 아닌지 알 수 있습니다. 만약에, Topological Ordering이 주어지지 않고 구해야 한다면 vertex의 모든 Permutation에 대해 고려를 해야할 것입니다. 이 경우 Topological Ordering을 구하는 데 $$ O(N! \cdot N) $$의 UpperBound를 가지게 될 것입니다.(Vertex의 Size를 N이라 설정했습니다.) 따라서 , 이를 Linear Time에 구할 수 있는 알고리즘이 필요하게 됩니다.


### Finish Ordering
 그래프 탐색 알고리즘중 Linear Time에 탐색 할 수 있는 DFS를 활용하여 Topological Ordering을 $$ O(V+E) $$ 에 구할 수 있습니다. 그러기 위해서 , Finishing Order라는것을 정의해야 합니다 . Finishing Order는 DFS의 호출이 끝나는 순서를 오름차순으로 나열한 것입니다. DFS의 psuedo code를 통해 알아보도록 하겠습니다. 
 ```python
 def DFS(u,finish_order) :
  for v in $$adj^{+}(u)$$ :
    if v not in visited :
      DFS(v,finish_order)
  finish_order.append(u)
 ```
모든 outgoing edge를 고려한후 DFS가 종료되기 때문에 가장 마지막에 호출한 node가 가장 먼저 finish 하게 됩니다. 이 finish order를 reverse 하고 , vertex에 finish order의 index를 mapping하게 된다면  , topological order를 얻을 수 있게 됩니다.

### Proof
임의의 edge (u,v) 에 대해서 증명하겠습니다.  (u,v) 는 크게 2가지 case로 나눌 수 있습니다. 
#### case 1
![image](https://1drv.ms/i/c/7e81bbcd99889380/IQNwehDd1CjiS5vF-7P1bnOYAclgdntFu4yPQHeDw3JGIYI?width=1024)  
이 경엔 DFS(u) 가 DFS(v)를 호출(call)하게 됩니다.따라서, DFS(v)가 먼저 호출되었기에 DFS(v)가 DFS(u)보다 먼저 종료됩니다. 따라서, finish order에 v가 u보다 먼저 포함됩니다 . 이를 reverse 하면 u가 v보다 순서상 앞에 있게 됩니다. 따라서, f(u) < f(v)가 성립하게 됩니다. 
#### case2 
![image](https://1drv.ms/i/c/7e81bbcd99889380/IQOaVP27YOkORL8q758x8uz4AZmndgB9kY4wUF8_k8BC6Yw?width=1024)  
이 경우에는 DFS(v)를 호출하고 종료한다음에 DFS(u)를 호출하고 종료하게 됩니다.  따라서, finish order는 v가 u보다 먼저 포함됩니다. 마찬가지로, f(u) < f(v)가 성립하게 됩니다.  
## Is it DAG?
Topological Ordering의 Formal한 Definition과 이를 Linear Time에 구하는 알고리즘을 알게되었습니다. Topological Ordering의 궁극적인 목표는 DAG인지 판별하는 것입니다 . 추가적으로 , Cycle이 있다면 Cycle인 부분을 알고 싶을 것입니다 .위에서 배운 내용으로 , 주어진 Graph가 DAG인지 판별해보도록 하겠습니다. 
### Cycle Detection

DAG는 Topological Order를 가진다는 것을 확인했습니다. DAG라는 명제를 P , Toplogical Order를 가진다라는 명제를 Q라고 하면 , $$ \neg P \rightarrow \neg Q $$
가 성립하게 됩니다. 즉, Topological Order를 가지지 않는다면 Cyclig Graph여야 합니다. 따라서 ,  reverse finish order가 topological order의 property를 만족하는지 확인하면 DAG인지 아닌지 알 수 있습니다. 이는 $$ O(V +E) $$에 확인할 수 있습니다. 
### Cycle Return 
단순히 Cycle을 Detection할 뿐만 아니라 Cycle에 어떤 vertex가 포함되는지 궁금할 수 있습니다. 이러한 cycle을 return하기 위해서 아래와 같은 Claim을 설정하고 증명하도록 하겠습니다.
#### Claim and Proof
> If graph G contains Cycle, Full-DFS traverse an edge from v to ancestor of v . 
여기서 ancestor는 아직 호출(call)이 끝나지 않은 vertex를 의미합니다.  
 Cycle 이 vertex (v0,v1,....vk,v0) 로 구성되어 있다고 가정하겠습니다.

 일반성을 위해서 , v0를 Full-DFS 알고리즘을 처음 호출하는 vertex라 가정하겠습니다. 임의의 vertex v-i 는 vertex v-i+1을 계속 호출하게 됩니다. 이는 v-i 의 호출(call)이 종료되기 전에 v-i+1의 호출이 먼저 종료되게 됩니다.  따라서, v0의 호출보다 v-k의 호출이 먼저 종료되게 될 것입니다. 
 (v-i,v-i+1) edge를 고려하면서 v-i+1이 아직 방문되지 않았다면 , v-i+1 edge를 방문하게 될 것입니다. 
 (vk,v0) edge를 고려할 때 , v0는 이미 방문하였지만, vk 를 방문중이기에 아직 호출이 종료되지 않았습니다. 따라서, ancestor에 있고 , (vk,v0) edge를 고려하게 되면서 vk의 ancestor인 v0로의 traverse를 고려하게 됩니다. 

#### How we do in code?

이를 code에서 어떻게 적용할 수 있을까요? 아래와 같이 적용할 수 있습니다.

```python
 def DFS(u,finish_order,anscestor,cycle) :
  ancestor.add(u)
  for v in $$adj^{+}(u)$$ :
    if v in ancestor :
      cycle = list(anscetor.deepcopy)
      cycle.append(v)
    if v not in visited :
      DFS(v,finish_order,anscestor,cycle)
  finish_order.append(u)
  ancestor.delete(u)

```
edge (u,v)를 고려하는데 v가 u의 anscestor라면 cycle을 detect하게 코드를 변경하면 됩니다.  
## Summary

오늘은 Topological Ordering에 대해서 알아보았습니다. 

오늘의 내용은 아래와 같이 요약될 수 있습니다. 

- Topological Ordering이 존재한다는 것은 주어진 Graph는 Directed Acyclic Graph이므로 여러 Computer Science Idea에 적용될 수 있다.
- Topological Ordering을 구하기 위해서는 DFS의 finish order의 reverse를 계산해야 한다.
- 주어진 Graph가 Directed Acyclic Graph인지 알기 위해서는 Topological Ordering의 Definition이 위배되는지 확인하고 Cycle을 Return하기 위해서는 DFS의 code에 ancestor를 방문하는지 체크하는 코드를 추가한다. 


## References

[Mit Topological order](https://www.youtube.com/watch?v=IBfWDYSffUU)