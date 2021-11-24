---

title : 기본 알고리즘의 중요성

# layout: "post"

excerpt: "이를 수정하게 되면 무슨일이 발생하는가"

categories:
  - Codingtechnique
tags:
  - [Algorithm,CodingTest,CodingInterview,technique]
# classes : wide
toc: true
toc_sticky: true
---
## 기존 알고리즘을 사용할 때 주의점

BFS,DFS,Dijkstra,etc...정말 기존 알고리즘 중 많이 나오는 알고리즘 top3라고 개인적으로 생각한다. 필자의 경우 DFS는 recursion , while  반복문 2가지 방법으로 모두 구현할 수 있다.  지금부터 ,필자가 얘기할 건 굉장히 무언가 기초적인 사실이고 남들은 다 아는 얘기일 가능성이 높다. 필자는 어렴풋이 인지하지만 적용은 안했던 사실을 회고느낌으로 적는 것이다.

## BFS,DFS

bfs의 기초적인 형태는 다음과 같다.

![image](https://user-images.githubusercontent.com/50165842/143145307-e6bd69ae-572c-446c-8d72-94ec7d4767a8.png)

3가지의 색깔(state) 를 노드마다 칠해주면서 queue안의 모든 노드를 비우면 끝나는 것이다.  

필자는 이를 응용해서 프로그래머스 위클리 3주차 문제를 해결하려 했다 .

```python
def bfs(matrix ,x , y,color) :
    n = len(matrix)
    dist = [ [0 for _ in range(n)] for _ in range(n)]
    q= deque()
    q.append((x,y))
    ans = []
    while q :
        s_x,s_y = q.popleft()
        dist[s_x][s_y] = 1
        ans.append([s_x, s_y])
        matrix[s_x][s_y] = 1-color
        for i in range(4):
            n_x = s_x + dx[i]
            n_y = s_y + dy[i]
            if check(n_x, n_y , n ) and matrix[n_x][n_y] ==color and dist[n_x][n_y] == 0 :
                q.append((n_x,n_y))
    return ans
```

기본적인 bfs의형태와 유사하지만, 한가지 다른점은 필자는 matrix의 값을 바꿔주면서 진행을 했다는 점이였다.자세하게 설명은 안하겟지만, 저 로직에 대해 필자가 모든 case를 고려하지 않아서  , 30퍼센트의 테스트케이스 오류가 발생하였엇다. 



## 기본 블록(라이브러리)

필자가 머리가 그렇게 좋지 않아서 대처못한 오류일 수도 있다.  필자는 이러한 실수를 하는 상황이 자주 나왔기에 ***<u>왜 이런게 발생했을까?</u>*** 를 고민했다. 이유는 간단했다. 

> ***<u>남의 라이브러리(코드)를 멋대로 고쳐쓰고 동작하는 방법은 바꾸지 않았기 때문이다.</u>***

우리가 파이썬에서 collections,math 같은 내장 라이브러리를 쓰면서 프로그램을 만들때 오류가 나면 이 라이브러리에 대한 의심은 하지 않는다.  애초에 남이 만든 잘 작동하는 라이브러리인데, 우리가 확인한다고 달라져?

이런 심리를 필자는 예전에 가지고 있었다. 뭐, 가끔 가다가 진짜로 잘못되서 오류가 발생하는 부분도 있지만 , (ex,pytorch,tf의 resize) 필자는 요새는 다르게 생각한ㄷ.

> ***<u>우리가 고쳐쓸 수 없기에 의심을 하지 않는다.</u>***

변하지 않으니까 의심을 하지 않는것이라고 개인적으로 생각한다.  bfs라는 알고리즘은 위에 첨부한 사진 형태로 알고리즘을 짯을때만 동작을 잘하는 것이다. 저걸 내부에서 맘대로 고쳐쓰면 다른 상황에서 동작을 잘 안할 수도 있다. 저걸 , 바꿀때 마다 일일이 생각하는 거보다 , 그냥 바꾸지 않고 외부에 새로운 기능을 만드는 것이 항상 내가 저 모든 경우들을 인지하지 않아도 할 수 있는 습관을 들이는게 좀더 낫다고 생각합니다.

시간이 제한적이라면 , 기존에 쓰던걸 바꿔서 이에 따라 발생할 수 있는 모든 경우를 고려하기 보단 , 그대로 사용하면 남는 시간에 다른 추가적인 문제를 해결할 수 있다고 생각한다.



```python
    for i in range(g) :
        for j in range(g) :
            if game_board[i][j] == 0 :
                g_z = go_zero(bfs(game_board, i, j,0))
                len_gb = len(g_z)
                if len_gb in all_t.keys():
                    # print('yes')
                    for ind,s in enumerate(all_t[len_gb]) :
                        if check_t[len_gb][ind] == 1 : continue
                        if idd(s,g_z) == True :
                            check_t[len_gb][ind] = 1
                            answer+=len_gb
                            break
```

위는 필자가 실수한 부분을 그대로 가져온 것이다.

```python
    for i in range(g) :
        for j in range(g) :
            if game_board[i][j] == 0 :
                g_z = go_zero(bfs(game_board, i, j,0))
                o_g = bfs(game_board, i, j,0)      
                g_z = go_zero(copy.deepcopy(o_g))
                len_gb = len(g_z)
                if len_gb in all_t.keys():
                    # print('yes')
                    for ind,s in enumerate(all_t[len_gb]) :
                        if check_t[len_gb][ind] == 1 : continue
                        if idd(s,g_z) == True :
                            check_t[len_gb][ind] = 1
                            answer+=len_gb
                            for g1,g2 in o_g:
                                game_board[g1][g2] = 1
                            break
```

이는 필자가 수정을 해서 통과를 한 코드이다.

위의 코드는 필자가 bfs안에서 matrix를 수정하도록 bfs라는 알고리즘을 수정한 것이고 , 아래는 필자가 bfs랑 matrix 수정을 분리한 것이다. 맨 아래의 for구문이 그 부분을 분리한 것이다. 



이건 아카이브용이라 남들이 이해하는걸 염두하고 작성한 글이 아니므로, 궁금한 점이 있다면 댓글로 문의를 부탁드립니다.