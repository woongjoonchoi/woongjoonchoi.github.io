---

title : 블록중심회전 vs 점 중심회전

# layout: "post"

excerpt: "프로그래머스 위클리 3주차에서 알게된 사실"

categories:
  - Codingtechnique
tags:
  - [Algorithm,CodingTest,CodingInterview,technique]
# classes : wide
toc: true
toc_sticky: true
---
## 블록 그 자체 회전 vs 점 , 블록 중심 회전

뭔가 이것을 표현할 방법이 없어서 나의 언어로 열심히 표현을 해보았습니다.

### 점 중심 회전

![img](https://upload.wikimedia.org/wikipedia/commons/thumb/3/3a/Matrix-font-linear-map001Rota.svg/300px-Matrix-font-linear-map001Rota.svg.png)

제가 말한 점 중심 회전은 ***<u>원점</u>*** 중심의 회전을 의미하는 것입니다.  원점을 중심으로 회전을 하면 위의 그림과 같은 방식으로 회전하게 됩니다. 

이 때 에는 회전 변환의 공식을 적용하면 됩니다.

```python
def rotate(x,y,i):
    if i==0 :
        return [-x,-y]
    elif i==1:
        return [y,-x]
    elif i==2 :
        return [-y,x]
```

코딩테스트 특성상 90도가 자주 나오기 때문에 , 90도 180도 270도 회전 변환 공식을 적용해봤습니다.

### 블록 중심 회전



![img](https://mblogthumb-phinf.pstatic.net/MjAxNjExMTdfNDQg/MDAxNDc5Mzc0ODEwMDMy.iPQD1x3dSSC9xiN1-otlcLLBaCoQzZzp0K0StWwMxVkg.p018XqVtiMbBrXBsYYF2piOkrIJWB8ng82uDG3SN4Bcg.PNG.dnpc7848/tetris_fig5.png?type=w800)

위와 같이 블록의 중심으로 회전하는 경우도 자주 나오는거 같습니다.  이런 경우 각각의 블록 한칸이 array에서 index를 의미하게 될 때 , 한번에 계산하는 방법이 있습니다.

```python
def rotate_b(x,y,n) :
    return [y,n-1-x]
```

예를 들어, 

|       |      | (0,2) |      |
| ----- | ---- | ----- | ---- |
|       |      |       |      |
| (2,0) |      |       |      |
|       |      |       |      |

(2,0) 는 90도 회전하면 (0,2) 로 가게 됩니다. 
일반적으로 말하면 행의 좌우 간격이 회전하면 열에서 유지가 되어야 하기 때문입니다.