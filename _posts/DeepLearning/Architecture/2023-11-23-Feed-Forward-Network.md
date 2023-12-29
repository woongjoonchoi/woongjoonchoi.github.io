---
title : " 임의의 숫자의 parameter를 가진 FeedForward Network의 일반항을 정의하고 vectorize하는 과정을 수식으로 도출해보자   "



excerpt: "Mathematical view"

categories:
  - DLArchitecture
tags:
  - [Machine Learning,deep learning ]
# classes : wide
toc: true
toc_sticky: true
usemathjax : true
---


## Why we need understand Neural Network in Mathematical view?
FeedForward Network는 가장 기본적인 형태의 neural net입니다. 이는 노드에 weight matrix를 곱하고 bias를 더 해줌으로써 구현됩니다. neural net을 수식으로 이해함으로써 neural net에 대한 이해도가 올라가기 때문에 수학적으로 이해하는 것이 딥러닝 엔지니어의 역량을 기르는데 많은 도움이 된다고 여겨집니다. 이 글은 임의의 layer의 node의 일반항을 도출하여 vectorize하는 과정을 수식으로 도출할 것입니다 .
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
|$$z_{j,i}^{[l]}$$| $$l$$ 번째 layer의 bias를 더한 output의 $$(j,i)$$ 성분입니다 . |
|$$a_{j,i}^{[l]}$$|$$l$$ 번째 layer의 activation의 $$(i,j) $$성분입니다 .|
|$$b^{[l]}$$|$$l$$ 번쨰 layer의 bias입니다.$$ b^{[l]} \in \mathbb{R}^{n^{[l]}  } $$ |
|$$g_{j}^{[l]}$$| $$l$$ 번째 layer activation function $$g_{j}^{[l]} : \mathbb{R}^{n^{[l]}} \rightarrow \mathbb{R}^{n^{[l]}} $$  , $$g_{j}^{[l]} \in \mathbb{R}^{n^{[l]}  } $$|  

## Explanation in Mathematical View

### output z 
![image](https://kau365-my.sharepoint.com/personal/oongjoon_kau_kr/Documents/%EB%B8%94%EB%A1%9C%EA%B7%B8%EC%9D%B4%EB%AF%B8%EC%A7%80/DeepLearning/Architecture/feedforward%20-%20%EB%B3%B5%EC%82%AC%EB%B3%B8.png)

다른 교육자료들을 보면 쉽게 설명하기 위해서 3개의 node로 한정짓거나 하는 방식으로 설명을 하게 됩니다. 하지만, 여기서는 일반식을 정의하기 위해서 $$ n^{[l-1]}$$개의 node에서  $$ n^{[l]}$$개의 node로 변환시키는 $$ l$$번째 layer에  대해서 설명을 하겠습니다. (bias는 그림에서만 생략하였습니다 . ) . 위의 그림은 $$ l$$번째 layer 를 나타낸 그림입니다. 동그라미가 node이고 연결선이 weight입니다.

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

이를 수식으로 표현하면 다음과 같습니다.  

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

vector space는 다음과 같이 정의됩니다 .$$ \vec{Z}^{[l]} \in \mathbb{R}^{n^{[l]} \times m} , \vec{A}^{[l - 1]} \in \mathbb{R}^{n^{[l - 1]} \times m}$$


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

vector space는 다음과 같이 정의됩니다 .$$ \vec{a}_{:, i}^{[l]} \in R^{n^{[l]}} $$  

위의 수식은  전체 activation 중 1개의 node를 의미합니다. 이를 전체 activation에 대해 확장해보도록 하겠습니다 . 

$$
\vec{A}^{[l]} =
\begin{bmatrix}
\vec{a}_{:, 1}^{[l]} & \dots & \vec{a}_{:, i}^{[l]} & \dots & \vec{a}_{:, m}^{[l]}
\end{bmatrix},
$$  

vector space는 다음과 같이 정의됩니다. $$ \vec{A}^{[l]} \in R^{n^{[l]} \times m} $$  

## Conclusion

임의의 layer의 vectorize 과정을 수학적으로 직접 도출해보았습니다. 아래와 같은 이유로 도움이 될 것이라 여겨집니다. 

1. neural net에 대한 이해도가 올라간다.
neural net의 작동 원리를 깊게 이해하면, 모델의 성능을 향상시키고 문제를 해결하는 데 도움이 됩니다. 일반항을 도출하면 neural net의 내부 작동 방식을 더 잘 이해할 수 있습니다

2. neural net이 어떻게 계산을 최적화 하는지 이해
vectorization 과정을 수식으로 도출하면, 어떻게 최적화할 수 있는지에 대한 통찰력을 얻을 수 있습니다.

3. 문제 해결 능력 
벡터화 과정을 수식으로 도출하면, 모델이 예상대로 작동하지 않을 때 문제를 진단하고 해결하는 데 도움이 될 것입니다.. 이는 디버깅 및 최적화 과정에서 매우 유용합니다.\

4. 커뮤니케이션 능력 향상
이러한 수식을 도출하고 이해하는 능력은 다른 엔지니어, 연구원, 이해관계자와의 커뮤니케이션에서 중요합니다. 이를 통해 복잡한 개념을 명확하게 전달하고, 팀의 협업을 촉진할 수 있습니다.

5. 가장 기본이다.
feedforward network는 가장 기본적인 형태이기에 vectorize 과정을 수식으로 도출을 해본다면, 다른 neural net을 도출해내는 데에도 도움이 될 것입니다. 뿐만 아니라, 다른  neural net 모델들을 분해해보면 feedforward 가 사용되어지는 경우가 많습니다. 




## Reference

1.  [feedforward-neural-networks-part-1/journalsim From Jonas Lalin ](https://jonaslalin.com/2021/12/10/feedforward-neural-networks-part-1/)    

2. [wikiepdia](https://en.wikipedia.org/wiki/Feedforward_neural_network)
