---
title : "Vector,Matrix,Linear Transformation"



excerpt: "3Blue1Brown Linear Algebra :Lec 01~05"

categories:
  - Linear Algebra
tags:
  - [Mathmathics,Machine Learning,deep learning,Linear algebra ,3blue1brown]
# classes : wide
toc: true
toc_sticky: true
---

많은 딥러닝 엔지니어들이 DataCentric AI이라는 트렌드 , 100 billion이 넘는 parameter를 가진 Large Model(ex.LLM)의 대두로 적절하고 충분한 양의 Data만 주어진다면 원하는 Problem을 푸는 Model을 쉽게 만들어 낼 수 있게 되었습니다. 하지만, Data를 만들고 Parameter를 키우는 것은  상당한 Resource가 요구됩니다. 따라서, Engineer나 Scientist가 기존의 문제를 여러 Subproblem을 쪼개거나 아니면 많이 쓰이는 대중적인 모델의 세부적인 부분을 변경해서 사용하는 Problem Solving 스킬이 여전히 필요 합니다. DeepLearning Model을 low-level 관점에서 보게 되면 Matrix Multiplication으로 이루어져 있습니다. 즉, Matrix,Vector 연산에 대해 해석을 할줄 알아야 합니다. 단순히 , scalar의 곱셈으로 생각하기 보다는 어떤 수학적인 의미를 가지는지 알 필요가 있습니다.이 글에서는 그 시작으로 Vector,Matrix를 기하학적인 관점에서 보도록 하겠습니다. 


## Vector
DeepLearning은 Matrix의 Multiplication으로 이루어져 Vector와는 관련이 없어보입니다. 하지만, Matrix의 Row 나 Column은 Vector 입니다. 따라서, Matrix는 Vector의 Concatenation이라 볼 수 있습니다. 따라서, Matrix Multiplication을 이해하기 위해서는 Vector를 이해해야 합니다. 
### What is it?
Vector를 어떻게 생각하는지는  전공에 따라 달라질 것입니다.
- Physics : 길이와 방향을 가진 arrow로 생각합니다.
- Computer Science: Number의 List로 생각합니다. 각 Number는 어떠한 의미를 가지고 있습니다.
- Math : Add,Multiply 같은 연산을 할 수 있는 대상으로 생각합니다. 

<!-- $$ \begin{bmatrix} 2 \\ 3 \end{bmatrix} +  \begin{bmatrix} 2 \\ 3 \end{bmatrix}$$    -->


### Operation and Coordinate Systems 
Vector라는 것은 어떤 coordinate system에 놓여있는 arrow입니다. 시작점과 끝점이 있는데 , Physics에서와는 다르게 Linear Algebra 관점에서는 Vector는 어디에 위치해있든 항상 같은 vector입니다. 즉, xy plane에 vector가 있을경우 항상 원점에 시작점을 위치시킬수 있습니다.  
Computer Science 관점에서 Vector  $$ \begin{bmatrix} 2 \\ 3 \end{bmatrix}$$  는 y축에서 +2 만큼 떨어져 있고 , x축에서 +3만큼 떨어진 곳에 있습니다.   
Vector의 Addition은 A vector의 끝에 B vector의 시작점을 위치시켜 A vector의 시작점과 B vector의 끝점을 이은것입니다. 이를 Coordinate System에서 보게되면   $$ \begin{bmatrix} x1 \\ y1 \end{bmatrix} +  \begin{bmatrix} x2 \\ y2 \end{bmatrix}$$   는 $$ \begin{bmatrix} x1+x2 \\ y1+y2 \end{bmatrix}$$ 이 됩니다. 
Vector의 Multiplication은 Vector에 number를 곱하는 것입니다. 예륻를어 , $$ c \cdot \begin{bmatrix} 2 \\ 3 \end{bmatrix} = \begin{bmatrix} 2 \cdot c \\ 3\cdot c \end{bmatrix} $$ 가 됩니다.  Vector에 1.7을 곱하면 Vector의 길이는 1.7배가 될 것이고, Vector에 -3을 곱하면 Vector는 Vector가 있는 직선을 기준으로 horizaontal flip이 되고, 길이가 3배가 될 것입니다.  이를 "Scaling"이라고 부릅니다.  

 <!-- $$ c \cdot \begin{bmatrix} 2 \\ 3 \end{bmatrix} = \begin{bmatrix} 2 \cdot c \\ 3\cdot c \end{bmatrix} $$   -->

#### n-dim vector

vector는 xyz plane에도 존재할 수 있고 , n-dimension plane에도 존재할 수 있습니다. 예를 들어 , $$ \begin{bmatrix} 2 \\ 3 \\ 4\end{bmatrix}$$ 라는 vector가 존재할 수 있습니다. 6-dimension 의 vector  $$ \begin{bmatrix} 2 \\ 3 \\ 4 \\5 \\ 6 \\ 7 \end{bmatrix}$$ 이 존재할 수 있습니다.  이 vector들에게도 덧셈,곱셈의 operation은 똑같이 적용이 됩니다. 


 <!-- $$ \begin{bmatrix} 2 \\ 3 \\ 4\end{bmatrix}$$ -->
## Linear Combination
Vector의 operation에 대해 보았는데 Vector이 Operation은 vector에 특정한 scalar를 곱해서 계산하는 것이였습니다. 실제 vector operation에서는 어떤 scalar가 곱해질 지 알 수 없기에 임의의 scalar를 곱한 vector의 operation을 해석할 줄 알아야 합니다. 이를 Linear Combination이라 하는데 , 이를  Geometrical한 관점에서 어떻게 해석하는지 알아보겠습니다.
### basis vector
예를들어 xy coordinate가 주어지면 우리는 이를 x 좌표 x1, y 좌표 y1으로 해석을 하게 됩니다. 하지만, 지금부터는 이를 그저 scalar로써 여기도록 하겠습니다.  
xy coordinate에는 $$\hat{i}  ,  \hat{j}$$ 라는 특별한 vector가 있습니다. $$\hat{i} = \begin{bmatrix} 1 \\ 0 \end{bmatrix}  ,  \hat{j} = \begin{bmatrix} 0 \\ 1 \end{bmatrix} $$ 가 됩니다.  
예를 들어 , vector $$ \begin{bmatrix} 3 \\ 2 \end{bmatrix} $$ 는 $$ 3\cdot \hat{i}  + 2  \cdot \hat{j}$$ 가 됩니다.

#### different vasis vector

만약에 basis vector 가 $$\hat{i} = \begin{bmatrix} 2 \\ 1 \end{bmatrix}  ,  \hat{j} = \begin{bmatrix} 1 \\ 2 \end{bmatrix} $$ 라고 하겠습니다. 이때 , vector $$ \begin{bmatrix} 3 \\ 2 \end{bmatrix} $$ 는 $$ \begin{bmatrix} 3 \\ 2 \end{bmatrix} $$ 는 $$ 3\cdot \hat{i}  + 2  \cdot \hat{j}  =3 \cdot \begin{bmatrix} 2 \\ 1 \end{bmatrix} + 2 \cdot \begin{bmatrix} 1 \\ 2 \end{bmatrix} =  \begin{bmatrix} 8 \\ 7 \end{bmatrix} $$   가 됩니다.
### Span
vector의 span은 basis vector에 임의의 scalar를 곱하였 을 때 얻어질 수 있는 vector의 집합입니다. 여러 예시에 대해 알아보도록 하겠습니다.

- $$ \begin{flushleft} \overrightarrow a + y \cdot \overrightarrow b   ,\qquad  \overrightarrow a \neq \emptyset , \overrightarrow b \neq \emptyset \end{flushleft}$$
  - 이 경우에는 임의의 직선이 span이 될 것입니다.
- $$ x \cdot \overrightarrow a + y \cdot \overrightarrow b   ,\qquad \overrightarrow a = c \cdot \overrightarrow b , \overrightarrow a \neq \emptyset , \overrightarrow b \neq \emptyset ,  c \neq 0 $$
  - 이 경우에도 임의의 직선이 span이 될 것입니다.
- $$ x \cdot \overrightarrow a + y \cdot \overrightarrow b   ,\qquad  \overrightarrow a  = \emptyset , \overrightarrow b =  \emptyset $$
  - 이 경우에는 원점이 될 것입니다.
  
#### Vector as Points
Vector를 고려할 때 Span을 고려하게 될텐데 , vector를 line으로 여기게 된다면 , 무수한 line들을 고려해야 합니다. 따라서, Vector를 Point로 고려할 것입니다. 

## Linear Transformation
Linear Combination은 Vector와 Matrix의 Multiplication으로 볼 수 있습니다. Vector Operation을 Geomterical View에서 해석했는데 , Vecotr와 Matrix의 Multiplication을 Geometrical View에서 해석하지 못했습니다. Vector와 Matrix의 Multiplication을 Geometrical 관점에서 해석해보도록 하겠습니다. 

### Why use term Transformation ?
Vector를 Point로 볼 때 , vector를 변환시키는 것은 함수라 볼 수 있습니다. Transformation은 vector를 시각화해서 모든 input vector를 움직여서 output vector를 만드는 것으로 볼 수 있습니다. 
### Non-linear vs Linear
임의의 Transformation은 정의하기가 복잡하지만, linear algebra에서는 Transformation이 특수한 형태로만 제한이 됩니다.
이를 Linear Transformation이라 부르며 아래와 같은 성질을 만족합니다.
- 모든 선들이 transformation 이후에도 , 휘지않고 직선이다.
- 원점은 transformation 이후에도 원점이다.
- grid가 Trnasformation 이후에도 "평행"하고 동일한 "간격"을 유지한다.

그렇다면 , Non-linear transformation에는 어떤 예시들이 있을까요?
- grid line이 휘어지게 만드는 Transformation
- 직선은 유지하지만, 원점이 이동
- grid line이 평행을 유지 , 원점 유지 , grid line의 간격이 변화 따라서 임의의 직선이 곡선으로 transformation 


### Vector view
linear transformation을 vector 관점에서 보도록 하겠습니다. vector 관점에서 볼려면, 두 개의 basis vector $$ \hat i , \hat j$$ 이 어떻게 transformation이 되는지 알면 됩니다. 다른 vector들의 transformation은 transformed basis vector로 구하게 됩니다.  
예를 들어 $$ \overrightarrow v =  -1 \cdot \hat i + 2 \cdot \hat j $$ 라고 하겠습니다.  
transformation 후의 $$ \overrightarrow v $$ 는 transformation 후의 $$ \hat i$$ 의 -1 배 , $$\hat j $$의 2배의 덧셈입니다. 왜냐하면 , transformation 전에 $$ \overrightarrow  v $$ 는 basis vector $$ \hat i , \hat j $$ 로 이루어져 있고 transformation 후에도 같은 linear combination 을 유지합니다. 
즉 , $$ \overrightarrow transformed \ v =  -1 \cdot \hat transformed \ i + 2 \cdot \hat transformed \ j $$ 가 됩니다. 따라서 , transformed된 $$ \hat i , \hat j $$를 알 수 있다면 transformed된 $$ \overrightarrow v $$를 추론할 수 있습니다. 

<!-- $$ \overrightarrow transformed \ v =  -1 \cdot \hat transformed \ i + 2 \cdot \hat transformed \ j $$ -->
### Matrix View
이를 vector 관점이 아닌 matrix 관점에서 볼 수 있습니다 . 
예를들어 , $$ \begin{bmatrix} a & b \\ c &  d \end{bmatrix}  \begin{bmatrix} x \\ y \end{bmatrix}$$ 라는 matrix,vector를 고려해보겠습니다. 이는 $$ x \cdot \begin{bmatrix} a \\ b \end{bmatrix}  + y \cdot \begin{bmatrix} c \\ d \end{bmatrix} $$ 와 같은 equation입니다. 즉 ,  $$ \begin{bmatrix} a \\ b \end{bmatrix} $$ 를 $$ \hat transformed \ i $$  , $$ \begin{bmatrix} c \\ d \end{bmatrix} $$ 를 $$ \hat transformed \ j $$ 로 생각하면 됩니다. x,y는 basis vector에 대한 scaler가 될 것입니다. 
예를 들어 , clockwise 90 degree rotation의 경우 $$ \begin{bmatrix} 0 & 1 \\ -1 &  0 \end{bmatrix}  $$ 가 될 것이고 , $$ \hat transformed \ i  =  \begin{bmatrix} 0 \\ 1  \end{bmatrix} , \hat transformed \ j = \begin{bmatrix}  -1 \\  0 \end{bmatrix} $$  가 될 것입니다. 

<!-- $$ \begin{bmatrix} 0 \ 1 \\ -1 \  0 \end{bmatrix}  $$ -->
<!-- $$ \begin{bmatrix} a \\ b \end{bmatrix} $$ -->


 <!-- $$ x \cdot \begin{bmatrix} a \\ b \end{bmatrix}  + y \cdot \begin{bmatrix} c \\ d \end{bmatrix} $$ -->
<!-- $$ \hat i , \hat j$$ -->

### Composition of Matrices
Linear Transformation을 Matrix 관점에서 보는 법을 알아보았습니다. 그렇다면 , Linear Transformation을 연속적으로 적용하게 되면 어떻게 될까요?   
> 임의의 linear transformation이 될 것입니다.  


이는 matrix의 곱셈으로 표현이 됩니다. 예시를 들어보겠습니다. 
90 degree clockwise rotation인 $$\begin{bmatrix} 0 \ 1 \\ -1 \  0 \end{bmatrix} $$와 sheer transformation인  $$\begin{bmatrix} 0 \ 1 \\ -1 \  0 \end{bmatrix} $$ 를 적용해보도록 하겠습니다. 새로운 $$ \hat i , \hat j $$는 각각  $$ \begin{bmatrix} 1 \\ 1 \end{bmatrix}  , \begin{bmatrix} -1 \\ 1 \end{bmatrix}$$ 가 될 것입니다.  즉 , $$ \begin{bmatrix} 1 &-1 \\ 1& 0 \end{bmatrix} $$ 가 최종적인 linear transformation이 됩니다. 이는 2개의 연속적인 transformation이 아닌 하나의 transformation으로 볼 수 있습니다.  
따라서 $$ \begin{bmatrix} 1& 1 \\ 0& 1 \end{bmatrix}  \begin{bmatrix} 0 &-1 \\ 1& 0 \end{bmatrix}  =  \begin{bmatrix} 1 &-1 \\ 1 &0 \end{bmatrix}  $$ 이므로 , 두 matrix의 product는 geometric 관점에서 한 transformation을 적용하고 다른 transformation을 적용한 것과 같습니다.   
여기서, 오른쪽의 transformation을 먼저 적용하고 왼쪽의 transformation을 적용한다는 것을 기억해야합니다. 합성함수인 f(g(x))에서 유래했기에 오른쪽에서 왼쪽으로 읽어야 합니다. 

임의의 Matrix $$ \begin{bmatrix} 0 & 2 \\ -1 & 0 \end{bmatrix}  \begin{bmatrix} 1 &1 \\ -2& 0 \end{bmatrix}  =  \begin{bmatrix} ?& ? \\ ?& ? \end{bmatrix}  $$ 에 대해서 고려해보도록 하겠습니다.  여기서 새로운 matrix는 최종적인 $$ \hat i ,\hat j $$가 될 것입니다. 
왼쪽부터 Matrix를 각각 M1,M2라 명명하겠습니다. M2 transformation 이후 $$ \hat i , \hat j$$ 는 $$ \begin{bmatrix} 1 \\ 1 \end{bmatrix}   , \begin{bmatrix} -2 \\ 0 \end{bmatrix}  $$ 가 됩니다. 연속적으로 M1 transformation을 적용한다면 $$ \hat i $$ 는 $$\begin{bmatrix} 0 &2 \\ -1 &0 \end{bmatrix} \begin{bmatrix} 1 \\ 1 \end{bmatrix} \begin{bmatrix}  2 \\ 1\end{bmatrix} $$ , $$ \hat j $$ 는 $$\begin{bmatrix} 0 &2 \\ -1& 0 \end{bmatrix} \begin{bmatrix} -2 \\ 0 \end{bmatrix} \begin{bmatrix}  0 \\ -2\end{bmatrix} $$ 가 될 것입니다. 

### Matrix Multiplication Order is Important?
Matrix Product의 순서는 중요할까요? 결론은 그렇습니다.  
이를 수식적으로 증명할 수 있지만, geomteric 관점에서 생각해보는 것도 좋습니다. geometric 관점에서 보면 서로 다른 두 basis vector에 서로 다른 transformation을 적용하므로 항상 같지는 않을 것입니다. 
### Matrix associativity 
$$ (AB)C = A(BC) $$ 라는 식이 성립하는 것을 associativty rule이라 합니다. 이는 임의의 matrix를 설정하여 증명할 수 있지만, geometric 관점에서도 증명할 수 있습니다.   
(AB)C의 경우 C의 transformation을 적용 후 (AB) 의 transformation을 적용하는 것입니다. matrix의 product는 연속적인 transformationi을 나타내므로 연속적으로 B ,A의 transformation을 적용한 basis vector가 나올것입니다. 
A(BC)의 경우 (BC)도 연속적인 transformation을 나타내므로 C, B의 transformation을 적용후 A의 transformation을 적용하게 될 것입니다. 같은 순서대로 동일한 transformation을 적용하게 되므로 associativity rule은 성립하게 됩니다. 
<!-- $$ \begin{bmatrix} 1 1 \\ 0 1 \end{bmatrix}  \begin{bmatrix} 0 -1 \\ 1 0 \end{bmatrix}  =  \begin{bmatrix} 1 -1 \\ 1 \ 0 \end{bmatrix}  $$  -->
## Summary

이번 글에서 Vector, Matrix,Linear Transformation을 Geometrical View에서 보았습니다. 오늘의 내용을 요약하면 아래와 같습니다.
- 임의의 scalar 에 대한  linear combination은 특정 dimension의 space를 나타내게 되고 이를 span이라 부른다.
- Linear Combination은 Vector-Matrix Multiplication으로 나타낼 수 있고, 이는 basis vector를 transformation하는 것이다.
- Linear Transformation을 여러번 하는 것은 그에 상응하는 Matrix를 Multiplication 하는 것과 동일하다 .


## References
[3blue1brown-linear algebra](https://www.youtube.com/watch?v=XkY2DOUCWMU&list=PLZHQObOWTQDPD3MizzM2xVFitgF8hE_ab&index=4)