---
title : " BackPropagatoin Mathmatical Derivation   "



excerpt: "FCN backpropagation in Mathematical view"

categories:
  - Optimization
tags:
  - [Machine Learning,deep learning ,derivation]
# classes : wide
toc: true
toc_sticky: true
usemathjax : true
---
지난 번 [post](https://woongjoonchoi.github.io/dlarchitecture/Feed-Forward-Network/) 에서는 FeedForwrad Network의 forward propagtion을 수식으로 유도하는 과정을 보였습니다. 이번에는 Backward propagation을 수식으로 유도하는 과정을 보이도록 하겠습니다. 


## $$ \frac{\delta J }{\delta W^{[l]}}  ,  \frac{\delta J }{\delta b^{[l]}} $$ 


우선 $$ \frac{\delta J }{\delta W_{(0,0)}^{[l]}} $$ 에 대해서만 생각해보도록 하겠습니다.  
$$
\begin{align}
 \frac{\delta J }{\delta W_{(0,0)}^{[l]}}  &= \sum_{i=0}^m \frac{\delta J }{\delta Z_{(0,i)}^{[l]}}  \frac{\delta Z_{(0,i)}^{[l]}}{\delta W_{(0,0)}^{[l]}}  \newline &=  \sum_{i=0}^m \frac{\delta J }{\delta Z_{(0,i)}^{[l]}}  a_{(0,i)}^{[l]}
 \end{align}
 $$  
weight matrix의 (0,0) 에 대해서만 고려하면 위와 같은 식이 성립이 됩니다.  만약에 , W의 row를 변화시킨다면 Z의 row가 parameter화 되어 변할것입니다. W의 column을 변화시킨다면 A의 row가 변할것입니다.  따라서, 다음과 같은 식을 유도할 수 있습니다.  

$$
\begin{align}
 \frac{\delta J }{\delta W_{(j,k)}^{[l]}}  &= \sum_{i=0}^m \frac{\delta J }{\delta Z_{(j,i)}^{[l]}}  \frac{\delta Z_{(j,i)}^{[l]}}{\delta W_{(j,k)}^{[l]}}  \newline &=  \sum_{i=0}^m \frac{\delta J }{\delta Z_{(j,i)}^{[l]}}  a_{(k,i)}^{[l]}
 \end{align}
 $$   

 이를 vectorize해서 matrix의 형태로 만들기 위해서 우선 W의 element 1개만을 고려해보도록 하겠습니다. matrix로 만들기 위해서는  (1,m) 의 vector 와 (m,1)의 vector를 곱한 형태가 되어야합니다. Z의 derivative는 column이 증가하는 형태이고 A도 column이 증가하는 형태입니다. cache로 얻는 matrix가 A이고 Z는 계산을 할 것이기 때문에 A를 transpose로 하는게 자연스럽습니다.


$$ 
\begin{align*}
&
\begin{bmatrix}
\frac{\partial{ \partial J}}{\partial w_{1, 1}^{[l]}} & \dots & \frac{\partial{ \partial J}}{\partial w_{1, k}^{[l]}} & \dots & \frac{\partial{ \partial J}}{\partial w_{1, n^{[l-1]}}^{[l]}} \\
\vdots & \ddots & \vdots & \ddots & \vdots \\
\frac{\partial{ \partial J}}{\partial w_{j, 1}^{[l]}}& \dots & \frac{\partial{ \partial J}}{\partial w_{j, k}^{[l]}}& \dots & \frac{\partial{ \partial J}}{\partial w_{j, n^{[l-1]}}^{[l]}}\\
\vdots & \ddots & \vdots & \ddots & \vdots \\
\frac{\partial{ \partial J}}{\partial w_{ n^{[l]} , 1}^{[l]}}& \dots & \frac{\partial{ \partial J}}{\partial w_{ n^{[l]} , k}^{[l]}}& \dots & \frac{\partial{ \partial J}}{\partial w_{ n^{[l]} , n^{[l-1]}}^{[l]}}
\end{bmatrix} \\
&=
\begin{bmatrix}
\frac{\partial{ \partial J}}{\partial Z_{1, 1}^{[l]}} & \dots & \frac{\partial{ \partial J}}{\partial Z_{1, i}^{[l]}}  & \dots & \frac{\partial{ \partial J}}{\partial Z_{1, m}^{[l]}} \\
\vdots & \ddots & \vdots & \ddots & \vdots \\
\frac{\partial{ \partial J}}{\partial Z_{j, 1}^{[l]}} & \dots & \frac{\partial{ \partial J}}{\partial Z_{j, i}^{[l]}} & \dots & \frac{\partial{ \partial J}}{\partial Z_{j, m}^{[l]}} \\
\vdots & \ddots & \vdots & \ddots & \vdots \\
\frac{\partial{ \partial J}}{\partial Z_{n^{[l]}, 1}^{[l]}} & \dots & \frac{\partial{ \partial J}}{\partial Z_{n^{[l]}, i}^{[l]}}& \dots & \frac{\partial{ \partial J}}{\partial Z_{n^{[l]}, m}^{[l]}}
\end{bmatrix} \notag \\
&\ \cdot
\begin{bmatrix}
a_{1, 1}^{[l - 1]} & \dots & a_{k, 1}^{[l - 1]} & \dots & a_{n^{[l - 1]}, 1}^{[l - 1]} \\
\vdots & \ddots & \vdots & \ddots & \vdots \\
a_{1, i}^{[l - 1]} & \dots & a_{k, i}^{[l - 1]} & \dots & a_{n^{[l - 1]}, i}^{[l - 1]} \\
\vdots & \ddots & \vdots & \ddots & \vdots \\
a_{1, m}^{[l - 1]} & \dots & a_{k, m}^{[l - 1]} & \dots & a_{n^{[l - 1]}, m}^{[l - 1]}
\end{bmatrix},

\end{align*}
 

$$ 

최종적으로 수식을 전개하면 위와같이 전개가 될 것입니다.  이를 matrix 형태로 vectorize하면 다음과 같이 나올 것입니다. 

$$\frac{\delta J }{\delta W^{[l]}}  = \frac{\delta J }{\delta Z^{[l]}} \cdot {A^{[L-1]}}^T$$   

이번에는 $$ \frac{\delta J }{\delta b_{j}^{[l]}} $$ 에 대해서만 생각해보도록 하겠습니다.  
$$ \frac{\delta J }{\delta b_{j}^{[l]}} =  \sum_{i=0}^{m} \frac{\delta J }{\delta Z_{j,i}^{[l]}}$$ 
 모든 b에 대해  vectorize해서 적으면 다음과 같이 될 것입니다. 

$$ \frac{\delta J }{\delta b^{[l]}}  = \sum_{axis=1} \frac{\delta J }{\delta Z^{[l]}} $$ 


## $$ \frac{\delta J }{\delta Z^{[l]}} $$

이번에는 $$ Z_{0,0}^{[l]}$$ 에 대해서만 생각해보도록 하겠습니다.  

$$ \frac{\delta J }{\delta Z_{(0,0)}^{[l]}}  = \sum_{p=0} \frac{\delta J }{\delta A_{(k,0)}^{[l]}} \frac{\delta A_{(k,0)}^{[l]}}{\delta Z_{(0,0)}^{[l]}}$$   
이를 일반화해서 $$ Z_{j,i}^{[l]}$$ 에 대해서 생각해보겠습니다.  
$$ \frac{\delta J }{\delta Z_{(j,i)}^{[l]}}  = \sum_{p=0} \frac{\delta J }{\delta A_{(k,i)}^{[l]}} \frac{\delta A_{(k,i)}^{[l]}}{\delta Z_{(j,i)}^{[l]}}$$  

(j,i) 만을 고려하면 vectorize를 할려면 $$(1,n^{[l]})  (n^{[l]},1)$$의 vector를 곱해야합니다. j를 일반화해서  vectorize를 하려면  matrix의 row가 $$n^{[l]}$$ 의 범위를 가집니다. 즉, 다음과 같은 derivation이 유도됩니다 .

$$ 
\begin{equation*}
\begin{bmatrix}
\frac {\partial J}{\partial z_{1, i}^{[l]}} \\
\vdots \\
\frac{ \partial J}{ \partial z_{j, i}^{[l]}} \\
\vdots \\
\frac{ \partial J}{ \partial z_{n^{[l]}, i}^{[l]}}
\end{bmatrix} =
\begin{bmatrix}
\frac{\partial a_{1, i}^{[l]}}{ \partial z_{1, i}^{[l]}} & \dots & \frac{\partial a_{j, i}^{[l]}}{ \partial z_{1, i}^{[l]}} & \dots & \frac{\partial a_{n^{[l]}, i}^{[l]}}{ \partial z_{1, i}^{[l]}} \\
\vdots & \ddots & \vdots & \ddots & \vdots \\
\frac{\partial a_{1, i}^{[l]}}{ \partial z_{j, i}^{[l]}} & \dots & \frac{\partial a_{j, i}^{[l]}}{ \partial z_{j, i}^{[l]}} & \dots & \frac{\partial a_{n^{[l]}, i}^{[l]}}{ \partial z_{j, i}^{[l]}} \\
\vdots & \ddots & \vdots & \ddots & \vdots \\
\frac{a_{1, i}^{[l]}}{ \partial z_{n^{[l]}, i}^{[l]}} & \dots & \frac{\partial a_{j, i}^{[l]}}{ \partial z_{n^{[l]}, i}^{[l]}} & \dots & \frac{\partial a_{n^{[l]}, i}^{[l]}}{ \partial z_{n^{[l]}, i}^{[l]}}
\end{bmatrix}
\begin{bmatrix}
\frac{ \partial J}{\partial a_{1, i}^{[l]}} \\
\vdots \\
\frac{ \partial J}{\partial a_{j, i}^{[l]}} \\
\vdots \\
\frac{ \partial J}{\partial a_{n^{[l]}, i}^{[l]}}
\end{bmatrix},
\end{equation*}
$$  

i에 대해서 일반화를 시도하면 , i가 변할때 마다 row가 매번 변해야 함을 알 수 있습니다. 따라서, 완전하게 matrix로 vectorize는 어렵고 , 부분적으로 vectorize를 해야합니다. 

$$
\frac {\partial J}{\partial z^{[l]}} = \frac{\partial \vec{a}_{:, i}^{[l]}}{\partial \vec{z}_{:, i}^{[l]}} \frac{\partial J}{\partial \vec{a}_{:, i}^{[l]}}
$$  
그리고 , 
$$ \frac {\partial J}{\partial \vec{a}_{:, i}^{[l]}} \in \mathbb{R}^{n^{[l]}}   ,  \frac {\partial \vec{a}_{:, i}^{[l]}}{\partial \vec{z}_{:, i}^{[l]}} \in \mathbb{R}^{n^{[l]} \times n^{[l]}}$$
  과 같은 real number집합에 포함됩니다.  
이를 최종적으로 정리하면 아래와 같이 정리할 수 있습니다.  

$$


\frac{\partial J}{\partial \vec{Z}^{[l]}} =
\begin{bmatrix}
\frac{\partial J}{\partial \vec{z}_{:, 1}^{[l]}} & \dots & \frac{\partial J}{\partial \vec{z}_{:, i}^{[l]}} & \dots & \frac{\partial J}{\partial \vec{z}_{:, m}^{[l]}}
\end{bmatrix}
 

$$ 

## $$ \frac{\delta J }{\delta A^{[l-1]}} $$

이번에는 A(0,0)에 대해서만 생각해보도록 하겠습니다.  
$$ 
\frac{\partial J}{\partial a_{0, 0}^{[l - 1]}} = \sum_j \frac{\partial J}{\partial z_{j, 0}^{[l]}} \frac{\partial z_{j, 0}^{[l]}}{\partial a_{0, 0}^{[l - 1]}} = \sum_j \frac{\partial J}{\partial z_{j, 0}^{[l]}} w_{j, 0}^{[l]}. 
$$
j에 대해서 sum을 하는것을 알 수 있습니다. 이는 나중에 vectorize로 표현이 가능할 것입니다.  이제는, 일반화된 k,i에 대해서 생각해보겠습니다.  

$$ 
\frac{\partial J}{\partial a_{k, i}^{[l - 1]}} = \sum_j \frac{\partial J}{\partial z_{j, i}^{[l]}} \frac{\partial z_{j, i}^{[l]}}{\partial a_{k, i}^{[l - 1]}} = \sum_j \frac{\partial J}{\partial z_{j, i}^{[l]}} w_{j, k}^{[l]}. 
$$

A의 unit 1개에 대해서만 고려하면, (1,n[l]) (n[l],1) vector의 곱이라 볼 수 있습니다.  이를 임의의 node에 대해 vectorize하면 다음과 같이 유도할 수 있습니다.  

$$\begin{equation*}
\begin{split}
&
\begin{bmatrix}
\frac{\partial J}{\partial a_{1, 1}^{[l - 1]}} & \dots & \frac{\partial J}{\partial a_{1, i}^{[l - 1]}} & \dots & \frac{\partial J}{\partial a_{1, m}^{[l - 1]}} \\
\vdots & \ddots & \vdots & \ddots & \vdots \\
\frac{\partial J}{\partial a_{k, 1}^{[l - 1]}} & \dots & \frac{\partial J}{\partial a_{k, i}^{[l - 1]}} & \dots & \frac{\partial J}{\partial a_{k, m}^{[l - 1]}} \\
\vdots & \ddots & \vdots & \ddots & \vdots \\
\frac{\partial J}{\partial a_{n^{[l - 1]}, 1}^{[l - 1]}} & \dots & \frac{\partial J}{\partial a_{n^{[l - 1]}, i}^{[l - 1]}} & \dots & \frac{\partial J}{\partial a_{n^{[l - 1]}, m}^{[l - 1]}}
\end{bmatrix} \\
&=
\begin{bmatrix}
w_{1, 1}^{[l]} & \dots & w_{j, 1}^{[l]} & \dots & w_{n^{[l]}, 1}^{[l]} \\
\vdots & \ddots & \vdots & \ddots & \vdots \\
w_{1, k}^{[l]} & \dots & w_{j, k}^{[l]} & \dots & w_{n^{[l]}, k}^{[l]} \\
\vdots & \ddots & \vdots & \ddots & \vdots \\
w_{1, n^{[l - 1]}}^{[l]} & \dots & w_{j, n^{[l - 1]}}^{[l]} & \dots & w_{n^{[l]}, n^{[l - 1]}}^{[l]}
\end{bmatrix} \\
&\  \cdot
\begin{bmatrix}
\frac{\partial J}{\partial z_{1, 1}^{[l]}} & \dots & \frac{\partial J}{\partial z_{1, i}^{[l]}} & \dots & \frac{\partial J}{\partial z_{1, m}^{[l]}} \\
\vdots & \ddots & \vdots & \ddots & \vdots \\
\frac{\partial J}{\partial z_{j, 1}^{[l]}} & \dots & \frac{\partial J}{\partial z_{j, i}^{[l]}} & \dots & \frac{\partial J}{\partial z_{j, m}^{[l]}} \\
\vdots & \ddots & \vdots & \ddots & \vdots \\
\frac{\partial J}{\partial z_{n^{[l]}, 1}^{[l]}} & \dots & \frac{\partial J}{\partial z_{n^{[l]}, i}^{[l]}} & \dots & \frac{\partial J}{\partial z_{n^{[l]}, m}^{[l]}}
\end{bmatrix},
\end{split}
\end{equation*}

$$  

이는 다음과 같이 표현할 수 있습니다.

$$

\frac{\partial J}{\partial \vec{A}^{[l - 1]}} = {\partial \vec{W}^{[l]}}^{T} \frac{\partial J}{\partial \vec{Z}^{[l]}}

$$

## Summary 

Fully Connected Network는 l번째 layer에서 실제로 forward propagation을 하면서 A[l-1] , Z[l] ,W[l] ,b[l] 을 저장하고, backpropagation을 하면서 dA[l]을 parameter로 전달 받습니다. 그리고, 위에서 본 derivation에 cache된 값과 dA[l]을 대입하여 다음 layer인 l-1 layer에 다시 parameter를 넘겨줍니다. 