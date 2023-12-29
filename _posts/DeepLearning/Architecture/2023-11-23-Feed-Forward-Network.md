---
title : " 임의의 숫자의 parameter를 가진 FeedForward Network의 일반항을 정의하고 vectorize하는 과정을 수식으로 도출해보자   "



excerpt: "Mathmatical view"

categories:
  - DLArchitecture
tags:
  - [Machine Learning,deep learning ]
# classes : wide
toc: true
toc_sticky: true
usemathjax : true
---


## Why we need understand Neural Network in Mathmatical view?
NeuralNetwork를 인간이 이해할 수 있는 logic으로 표현하는 방법중 하나는 바로 수식입니다. 수식으로 표현하는 방법을 알아야만 하는 이유로 크게 몇가지 있다고 생각합니다.
첫번째로 , Neural Network를 공부하는 이유는 이 neural Network를 컴퓨터로 구현하여 사용하기 위함입니다. 컴퓨터로 구현한다는 것은  머릿속의 로직을 코딩함을 의미합니다. 
## Term Definition
여기서의 Term에 관한 Symbol이나 Notation 방식은 Andrew 교수님의 DeepLearning 강의를 참고하였습니다.  

|기호|설명|
|-------------------------------|-----------------------------------------------------------------------------------|
|$$l$$| $$l $$번째  layer  . $$ l $$ =   0, ... , L  은 weights와 bias를 가지는  layer의 번호를 의미한다.|
|$$n^{[l]}$$| $$ l $$ 번째 layer의 node의 개수를 의미합니다.|
|$$j$$|$$j  = 0,..., n^{[l]}$$|
|$$k$$|$$k = 0, .... n^{[l-1]} $$|
|$$m$$|$$m$$ 은 training step에서의 batch size 입니다.|
|$$i$$|$$i = 0,..... m$$|
|$$w_{j,k}^{[l]}$$| $$l $$ 번째 layer의 weight $$ W^{[l]} $$ , $$ W^{[l]} \in \mathbb{R}^{n^{[l]} \times n^{[l-1]} } $$ . $$ W^{[l]} $$ 의 $$(j,k) $$ 원소를 의미한다.|
|$$z_{j,i}^{[l]}$$| $$l$$ 번째 layer의 bias를 더한 output의 $$(i,j)$$ 성분입니다 . |
|$$a_{j,i}^{[l]}$$|$$l$$ 번째 layer의 activation의 $$(i,j) $$성분입니다 .|
|$$b^{[l]}$$|$$l$$ 번쨰 layer의 bias입니다.$$ b^{[l]} \in \mathbb{R}^{n^{[l]}  } $$ |
|$$g_{j}^{[l]}$$| $$l$$ 번째 layer activation function $$g_{j}^{[l]} : \mathbb{R}^{n^{[l]}} \rightarrow \mathbb{R}^{n^{[l]}} $$  , $$g_{j}^{[l]} \in \mathbb{R}^{n^{[l]}  } $$|  

## Explanation in Mathmatical View

### output z 
![image](https://kau365-my.sharepoint.com/personal/oongjoon_kau_kr/Documents/%EB%B8%94%EB%A1%9C%EA%B7%B8%EC%9D%B4%EB%AF%B8%EC%A7%80/feed1.png)

다른 교육자료들을 보면 쉽게 설명하기 위해서 3개의 node로 한정짓거나 하는 방식으로 설명을 하게 됩니다. 하지만, 여기서는 일반식을 정의하기 위해서 $$ n^{[l-1]}$$개의 node에서  $$ n^{[l]}$$개의 node로 변환시키는 $$ l$$번째 layer에  대해서 설명을 하겠습니다. (bias는 그림에서만 생략하였습니다 . )

여기서 , $$z_{k,i}^{[l]}$$ 에는 $$ n^{[l-1]} $$ 개의 node가 연결되어 있습니다.    
따라서 ,   

$$ 
\begin{equation}
z_{j,i}^{[l]} = \sum_{k=0}^{n^{[l-1]}}  w_{j,k}^{[l]} \cdot a_{k,i}^{[l-1]} + b_{j}^{[l]} 
\end{equation}
$$    

위 수식을 `(1)` 이라 하겠습니다.  

vector space는 다음과 같이 정의됩니다.  $$ \vec{a}_{:, i}^{[l-1]} \in \mathbb{R}^ {n \times {n^{[l-1]}} }, \vec{w}_{j, :}^{[l]} \in \mathbb{R}^ {n \times {n^{[l-1]}}}  $$  .  

딥러닝에서는 이러한 multiplication을 sequential하게 하는것이 아닌 parallell 하게 진행합니다.(ex.Numpy) 따라서, 우리는 `(1)` 을 vectorization 해야 합니다 .  위 식은 sum을 나타내지만 , 이는 vector $$ w_{j,:}^{[l]} $$ 와 vector $$ a_{:,i}^{[l-1]}$$  의 multiplication이라 볼 수 있습니다. 위 식에서 변수 j의 범위는 $$ 0<= j <=n^{[l]} $$ 입니다.  따라서 , 이를 확장하면 아래와 같이 vectorize할 수 있습니다.


$$
\begin{align*}
\begin{bmatrix}
z_{1, i}^{[l]} \\
\vdots \\
z_{j, i}^{[l]} \\
\vdots \\
z_{n^{[l]}, i}^{[l]}
\end{bmatrix} &=
\begin{bmatrix}
w_{1, 1}^{[l]} & \dots & w_{1, k}^{[l]} & \dots & w_{1, n^{[l - 1]}}^{[l]} \\
\vdots & \ddots & \vdots & \ddots & \vdots \\
w_{j, 1}^{[l]} & \dots & w_{j, k}^{[l]} & \dots & w_{j, n^{[l - 1]}}^{[l]} \\
\vdots & \ddots & \vdots & \ddots & \vdots \\
w_{n^{[l]}, 1}^{[l]} & \dots & w_{n^{[l]}, k}^{[l]} & \dots & w_{n^{[l]}, n^{[l - 1]}}^{[l]}
\end{bmatrix}
\begin{bmatrix}
a_{1, i}^{[l - 1]} \\
\vdots \\
a_{k, i}^{[l - 1]} \\
\vdots \\
a_{n^{[l - 1]}, i}^{[l - 1]}
\end{bmatrix} +
\begin{bmatrix}
b_1^{[l]} \\
\vdots \\
b_j^{[l]} \\
\vdots \\
b_{n^{[l]}}^{[l]}
\end{bmatrix},
\end{align*}
$$  

이를 수식으로 표현하면 다음과 같이 적을 수 있습니다.  

$$\vec{z}_{:, i}^{[l]} = \vec{W}^{[l]} \vec{a}_{:, i}^{[l - 1]} + \vec{b}^{[l]} $$ 

vector space는 다음과 같이 정의됩니다 .$$ \vec{z}_{:, i}^{[l]} \in \mathbb{R}^{n \times {n^{[l]}}} , \vec{W}^{[l]} \in \mathbb{R}{n^{[l]} \times n^{[l - 1]}}  , \vec{b}^{[l]} \in \mathbb{R}^{n \times {n^{[l]}}} , \vec{a}_{:, i}^{[l - 1]} \in \mathbb{R}^{n \times {n^{[l - 1]}}}  $$  

이는 1개의 training data에 대한  math expression입니다. 이를 이제 $$ m $$개의 batch data의 size로 확장하여 vectorize를 해보겠습니다.   

$$
\begin{align}
\vec{Z}^{[l]} &=
\begin{bmatrix}
\vec{z}_{:, 1}^{[l]} & \dots & \vec{z}_{:, i}^{[l]} & \dots & \vec{z}_{:, m}^{[l]}
\end{bmatrix}  \\
&= \vec{W}^{[l]}
\begin{bmatrix}
\vec{a}_{:, 1}^{[l - 1]} & \dots & \vec{a}_{:, i}^{[l - 1]} & \dots & \vec{a}_{:, m}^{[l - 1]}
\end{bmatrix} +
\begin{bmatrix}
\vec{b}^{[l]} & \dots & \vec{b}^{[l]} & \dots & \vec{b}^{[l]}
\end{bmatrix} \notag \\
&= \vec{W}^{[l]} \vec{A}^{[l - 1]} + broadcast(\vec{b}^{[l]}), \notag \\
\end{align}
$$  

vector space는 다음과 같이 정의된다 .$$ \vec{Z}^{[l]} \in \mathbb{R}^{n^{[l]} \times m} , \vec{A}^{[l - 1]} \in \mathbb{R}^{n^{[l - 1]} \times m}$$


### output a 


$$ Z^{[l]}$$ 을 계산하게 되면, 이를 $$g_{j}^{[l]}$$ 에 parameter로 넘겨주어 계산하게 됩니다. 아래와 같은 식으로 표현할 수 있습니다.  

$$ 
\begin{equation}
a_{j, i}^{[l]} = g_j^{[l]}(z_{1, i}^{[l]}, \dots, z_{j, i}^{[l]}, \dots, z_{n^{[l]}, i}^{[l]}). 
\end{equation}
$$  

위 수식을 `(2)` 이라 하겠습니다.  

마찬가지로, sequential하게 하는것이 아닌 parallell 하게 진행되기 때문에 `(2)` 를 vectorize 해보도록 하겠습니다.    

$$
\begin{align*}
\begin{bmatrix}
a_{1, i}^{[l]} \\
\vdots \\
a_{j, i}^{[l]} \\
\vdots \\
a_{n^{[l]}, i}^{[l]}
\end{bmatrix} &=
\begin{bmatrix}
g_1^{[l]}(z_{1, i}^{[l]}, \dots, z_{j, i}^{[l]}, \dots, z_{n^{[l]}, i}^{[l]}) \\
\vdots \\
g_j^{[l]}(z_{1, i}^{[l]}, \dots, z_{j, i}^{[l]}, \dots, z_{n^{[l]}, i}^{[l]}) \\
\vdots \\
g_{n^{[l]}}^{[l]}(z_{1, i}^{[l]}, \dots, z_{j, i}^{[l]}, \dots, z_{n^{[l]}, i}^{[l]}) \\
\end{bmatrix}
\end{align*}
$$
  
이를 수식으로 표현하면   

$$
\vec{a}_{:, i}^{[l]} = \vec{g}^{[l]}(\vec{z}_{:, i}^{[l]})
$$  

vector space는 다음과 같이 정의된다 .$$ \vec{a}_{:, i}^{[l]} \in R^{n^{[l]}} $$  

위의 수식은  전체 activation 중 1개의 node를 의미합니다. 이를 전체 activation에 대해 확장해보도록 하겠습니다 . 

$$
\vec{A}^{[l]} =
\begin{bmatrix}
\vec{a}_{:, 1}^{[l]} & \dots & \vec{a}_{:, i}^{[l]} & \dots & \vec{a}_{:, m}^{[l]}
\end{bmatrix},
$$  

vector space는 다음과 같이 정의된다. $$ \vec{A}^{[l]} \in R^{n^{[l]} \times m} $$  

## Conclusion
 feedforward network의 임의의 layer에 대하여  math expression을 정의하였습니다. 이를 , 모든 일반적인 feedforward network에 적용할 수 있습니다. 전체적으로 feedforward network가 아니더라도 , 부분부분 feedforward network가 사용되어 집니다. 이를 직접 implementation 할 때 , 수식을 알고 있다면 큰 도움이 될 것입니다 . 





## Reference

1.  [feedforward-neural-networks-part-1/journalsim From Jonas Lalin ](https://jonaslalin.com/2021/12/10/feedforward-neural-networks-part-1/)    

2. [wikiepdia](https://en.wikipedia.org/wiki/Feedforward_neural_network)
