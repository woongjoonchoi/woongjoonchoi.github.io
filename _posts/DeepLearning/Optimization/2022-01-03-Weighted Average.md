---
title : "Weighted Average and Momentum"



excerpt: "Practical batch norm in neural net"

categories:
  - Optimization
tags:
  - [Machine Learning,batch size,deep learning]
# classes : wide
toc: true
toc_sticky: true
---
이번 글은 DLS에서 다룬 Exponentially Weighted Average , Momentum을 다루어 볼려 합니다. 이번에도 복습한번 하고 3일후 Closed Book으로 작성하였습니다.

## Exponentially  Weighted Average

DLS에서는 London이라는 도시의 평균 강우량을 계산을 하는 예제로 Exponentially  Weighted Average를 설명합니다.

![image](https://user-images.githubusercontent.com/50165842/147940628-ee8eafe9-4ec7-4d85-88bd-4051da795128.png)



$$ V_0 =  (1-\beta) \theta_0  $$

$$ V_1 =  \beta V_1 + (1 - \beta ) \theta_{0} $$

$$... $$

$$ V_n =  \beta V_n + (1 - \beta ) \theta_{n-1} $$  

![image](https://user-images.githubusercontent.com/50165842/147940701-fad10109-5d98-4c94-a202-9b47944b59f3.png)



이의 통계적인 의미는 V 는 대략적으로 $$1 \over {1-\beta} $$ 개수 만큼을 반영하는 average라는 것입니다.   
예를들어 , $$\beta$$ 가 0.9 라면 V는 10일의 날씨를 반영한다는 뜻입니다.

따라서, $$\beta$$ 가 커지면 , 그래프는 완만하게 되는 반면, $$\beta$$ 가 작아지면, 반영하는 날의 수가 적어지므로 급격하게 변하는 그래프를 보여줍니다.

## Weighted Average in statistical view

위의 식을 전개하면 

$$ V_n =  {\beta}^n (1-\beta) \theta_0 +{\beta}^{n-1}  (1 - \beta ) \theta_{1} + .. +  (1 - \beta ) \theta_{n}$$  

입니다. 

어떠한 기하급수적으로 감소하는 그래프가 있습니다. 여기에 날씨의 값을 element wise를 해서 곱해준 것들을 더해준게 바로 exponentially weighted average입니다. 

그리고 , Andrew 교수님은 대략적으로 $$1 \over {1-\beta} $$ 개수 만큼을 반영하는 average 것에 대한 이유를 설명해주시는데 , 

$$ (1 - \epsilon) ^{1 \over \epsilon} = {1 \over e } $$ 이기 때문에 $$ {1-\beta} $$ 만큼의 최신 데이터를 반영한다고 합니다. 등비급수로도 계산을 해보았는데 ,  대략 0.7의 비율이 나오고, 교수님께서는 rule of thumb이라 하는데 , 일단은 그냥 받아들이기로 했습니다.



## Gradient Descent Momentum

Weighted Average 와 Gradient Descent를 비교하면 Gradient Descent 는 그 순간의 gradient만을 가지고 weight를 udpate합니다.



$$dV = \beta dV + (1 - \beta ) dW $$ 

$$ W = : W - \alpha dV $$

위와 같은 Rule을 통해서 update를 진행합니다.  

Andrew 교수님 께서는 

> 기존에 달리고 있는 속도(dV)에 마찰력이 가해지고 ($$\beta$$ )힘을 주는 만큼 가속도가 붙는 ( $$(1 - \beta ) dW $$)  물리학의 법칙과 유사하다고 하셨습니다. 

저는 이 설명이 매우 와닿았습니다.

### omit learning rate

$$dV = \beta dV + \alpha dW $$ 

$$ W = : W -  \alpha dV $$

위의 식을 learning rate를 생략해서 사용하는 것이 논문에서도 보여진다고 합니다. deeplearning book에서도 learning rate를 weight udpate에서 생략해서 설명하는 것으로 알고 있습니다.  이에 대한 자세한 설명은 없지만 저의 생각은 이렇습니다

 $$dV = \beta dV +  dW $$ 

$$\alpha dV = \alpha \beta dV +  \alpha dW $$ 

$$ dV \approx \alpha \beta dV +  \alpha dW $$ 

$$ W = : W -  dV $$

위 와 같이 되는게 아닌가 싶습니다.(저도 잘은 모르겠습니다. 아시는 분 있으시면 댓글이나 메일 로 알려주시면 감사하겠습니다.)

### implementation in deep learning



실제로 구현을 할 때 위의 식을 그대로 적용하게  되면 , $$ V_0 = 0$$ 으로 초기화 하고 시작하기 때문에 , 초기에는 매우 예상하는 값보다 값이 적게 나오게 됩니다. 따라서, $$ 1 \over 1 - {\beta}^t $$ 로 나누어 주어 scaling을 해줍니다. t가 점점 커질수록  scaling factor는 1에 가까워집니다. 이는  weighted average가 $$1 \over {1-\beta} $$  의 개수 만큼의 최신 데이터를 반영하기 때문입니다.

### gradient descent vs gradient decent momentum

gradient descent을 이용해서 momentum과 같이 최신 데이터를 반영하게 할 수 있습니다. 예를들어 ,$$\beta$$ 가 0.99 인 경우 100개의 data에 대한 gradient를 메모리에 올려놓으면 됩니다.  하지만, momentum은 코드 1줄로 이를 해결할 수 있습니다. 통계학이 필수적인것은 아니지만, 알고리즘을 고도화 하는데 있어서 좋은 도구가 될 수 있다고 느꼈습니다.

