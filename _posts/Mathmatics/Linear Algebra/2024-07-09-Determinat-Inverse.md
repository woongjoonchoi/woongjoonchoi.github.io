---
title : "Determinant and Inverse in terms of geometry and function"



excerpt: "3Blue1Brown Linear Algebra :Lec 06~07"

categories:
  - Linear Algebra
tags:
  - [Mathmathics,Machine Learning,deep learning,Linear algebra ,3blue1brown]
# classes : wide
toc: true
toc_sticky: true
---

Determinant 와 Inverse Matrix는 formula를 사용하면 numeric하게 계산을 할 수 있다. 계산을 하는 것도 중요하다 . 하지만 , 이를 다른 관점에서 해석하는 것도 Matrix Operation을 이해하는데 큰 도움을 준다. formula를 통해서 value를 구하는 것도 매우 중요하다. 실제 , Matrix Operation을 구현하는데 가장 중요할 것이다. 다양한 관점에서 해석할 수 있으면 상황에 따라 가장 적합한 Tool을 적용할 수 있을 것이다
## Determinant
두 vector의 Determinant는 두 vector가 이루는 면적이다. determinant 값은 음수, 0, 양수 가 가능하다. 면적인데 어떻게 음수가 나올 수 있는지 의문을 가질 수 있다. 이는 determinant를 구하는 두 vector의 위치에 따라 달라진다.   
두 vector를 column으로 하는 matrix를 생각해보자. determinant의 부호는 오른쪽 column vector에서 왼쪽 column vector 방향으로 오른손을 감았을 떄 위로 향하면 +이고 아래로 향하면 -이다. 물리학에서 배운 오른손의 법칙과 비슷하다. 
두 column vector가 이루는 각을 좁히다 보면 + 이다가 0이 되면서 두 벡터의 위치가 바뀐다 그리고 - 로 부호가 변한다.
 
### Det in Geometry View
 matrix 를 trnasformation 관점에서 보았을 때 Transformation을 표현하는 value이다. Transformation 이란 모든 input space를 다른 space로 mapping 시킨다. basis vector i,j 를 transformation하면 transformed 된 basis vector는 matrix의 column vector이다.   
 즉 , basis vector가 이루는 면적이 1에서 matrix의 determinant 값이 된다.   
 이를 일반적으로 생각하면 , 임의의 면적도 trasformation후에 determinant 만큼 scaling 된다. 왜냐하면, linear transformation은 $$ T(X+Y) = T(X) + T(Y) , T(cX)=c T(X)$$ 를 만족하기에 균등한 간격이 transformation 이후에도 유지가 됩니다. 따라서 , 작은 정사각형의 면적도 determinant 값 만큼 scaling 됩니다. Series 관점에서 보면 임의의 면적은 무수히 많은 작은 정사각형의 합이라 볼 수 있습니다. 매우 작은 정사각형의 한변을  $$ \Delta x $$ 라 하고 transformed 된 매우 작은 정사각형의 한변을  $$ \Delta x' $$ 라 하면 
 $$ \sum (\Delta x')^{2} = \sum det(A) (\Delta x)^{2} = det(A) \sum  (\Delta x)^{2}  $$  
 이므로 임의의 면적이 determinant 값 만큼 scaling 됩니다. 

### Indepedent to Coordinate System

Transformation Matrix는 어떤 coordinate system에서 적용하더라도 해당 coordinate system의 basis vector를 column vector로 변환시킵니다. coordinate system에 관계없이 basis vector가 항상 같기에 coordinate system에 indepdendent 하다고 할 수 있습니다. 따라서, 어떤 transformation matrix가 주어졌을 때 이의 determinant를 구하게 되면 어떻게 transformation을 하는지 알 수 있습니다 .




## Inverse

Inverse Matrix는 Matrix이기에 Transformation으로 볼 수 있습니다. Trasformed 된 space에 Inverse Matrix를 곱하면 다시 원래의 space로 돌아오게 됩니다. Inverse Matrix를 해석할 때에는 Matrix의 determinant 값이 0가 되는지 확인해야 합니다. 0이 되면 Inverse matrix가 존재하지 않기 때문입니다 .

 determinant 값이 0이 되는 것은 Input space가 수축되는 것을 의미합니다. 예를 들어 , input space가 3-d 라면 평면이 되거나 직선이 되거나 원점이 될 수 있습니다. determinant값이 0이 될 때 왜 inverse matrix가 존재하지 않는지를 space의 수축 관점에서 설명해볼려 합니다. 

### Function View
Transformation Matrix는 input space를 outpuut space로 mapping을 시킵니다. 따라서, function이라 볼 수 있습니다. Inverse matrix도 마찬가지로 function이라 볼 수 있습니다.  
만약에 , determinant 값이 0라면  output space에서 input space로 mapping 시킬려면 1: N관계의 mapping이 되어야 합니다. function은 input과 output의 관계가 1 : 1이 되어야 하는데 function의 정의에 위반되므로 determinant 값이 0라면 inverse matrix는 존재하지 않습니다. 

### All b View

$$ X = A^{-1} b $$ 는 inverse matrix가 존재한다고 가정할 때의 식입니다. 만약에 , determinant가 0이 되어 space가 수축하게 된다면, 모든 b는 input의 모든 point를 표현할 수 없습니다. 따라서 , b의 관점에서 어떤 b에 대해서는 solution X가 존재하지만 , 어떤 b는 존재하지 않기에 solution X를 구할 수 없습니다.  function은 1 : 1 mapping이기에 b로부터 기존 input space X 전부로 mapping이 되지 않습니다. 따라서, 모든 b의 관점에서 해석해도 determinant 가 0인 경우 inverse matrix는 존재하지 않습니다.

### Kerenl Space 


Kerenl Space 혹은 Null space라 불리는 것은   $$ AX  = 0 $$ 를 만족하는 zero vector와 non-zero vector의 set을 의미합니다. Input space가 3-d인 경우 Null Space는 2-d plane이 될 수도 있고 , 1-d line이 될 수 있습니다. 이 때 , kerenel space는 무조건 zero vector를 포함하게 됩니다. 왜냐하면 , zero vector는 무조건 zero vector로 transformation 되기 때문입니다. 

