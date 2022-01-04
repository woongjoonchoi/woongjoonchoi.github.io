---
title : "Weighted Average and Momentum"



excerpt: "Momentum and Exponentially Weighted Average"

categories:
  - Optimization
tags:
  - [Machine Learning,Optimization,pyhsics ,deep learning]
# classes : wide
toc: true
toc_sticky: true
---
2021-01-04 Intro , Exponentially Weighted Average 내용추가

이번 글은  Exponentially Weighted Average , Momentum을 다루어 볼려 합니다. Exponentially Weighted Average는 각 step에 대한 Gradient 만을 가지고   stochastic gradient가 가지는 문제점을 해결할 수 있는 solution을 제공해줍니다.이번에도 복습한번 하고 3일후 Closed Book으로 작성하였습니다.

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

![image](https://user-images.githubusercontent.com/50165842/147992840-1c568f11-f3b6-441b-ae32-f5c7c853e9c4.png)

어떠한 기하급수적으로 감소하는 그래프가 있습니다. 여기에 날씨의 값을 element wise를 해서 곱해준 것들을 더해준게 바로 exponentially weighted average입니다. 

그리고 , Andrew 교수님은 대략적으로 $$1 \over {1-\beta} $$ 개수 만큼을 반영하는 average 것에 대한 이유를 설명해주시는데 , 

$$ (1 - \epsilon) ^{1 \over \epsilon} = {1 \over e } $$ 이기 때문에 $$ {1-\beta} $$ 만큼의 최신 데이터를 반영한다고 합니다. 등비급수로도 계산을 해보았는데 ,  대략 0.7의 비율이 나오고, 교수님께서는 rule of thumb이라 하는데 , 일단은 그냥 받아들이기로 했습니다.

계산을 할려면 ,$$ \sum_{k=1}^10 { (1- \beta) \beta^{k-1}} $$ 식을 계산하게 됩니다.  최신의 10개의 온도의 데이터 반영 비율은 약 0.7입니다. 이는 $$ n \to \infty$$ 가 되도 , 마찬가지 입니다. rule of thumb이라는 표현을 쓰신게 경험적으로 $$1 \over {1-\beta} $$ 개수 만큼의 data를 반영했기에 그렇게 표현 하신거 같습니다



## Gradient Descent Momentum



### gradient descent vs gradient descent momentum

![image](https://user-images.githubusercontent.com/50165842/147993452-2b86a0af-a5ac-418c-99ed-32ce11c60458.png)







$$dV = \beta dV + (1 - \beta ) dW $$ 

$$ W = : W - \alpha dV $$

위와 같은 Rule을 통해서 update를 진행합니다.  

Andrew 교수님 께서는 

> 기존에 달리고 있는 속도(dV)에 마찰력이 가해지고 ($$\beta$$ )힘을 주는 만큼 가속도가 붙는 ( $$(1 - \beta ) dW $$)  물리학의 법칙과 유사하다고 하셨습니다. 

Weighted Average 와 Gradient Descent를 비교하면 Gradient Descent 는 그 순간의 gradient만을 가지고 weight를 udpate합니다.

따라서, gradient descent는  좀 더 급격하게 방향이 변하는데, momentum 은 기존의 속도에 가속도에 따라 방향을 유지하면서 전진하기 때문에 , 급격하게 방향이 변하지 않습니다. 이를 용어로 설명하면, gradient descent 는 Hessian Matrix와 stochastic (mini-batch gradient descent) 의 variance 때문에 안좋은 값이 계산되면 영향을 받기 쉽습니다.  하지만 , momentum은 기존의  Hessian Matrix 의 값과 기존의 data의 값을 반영하기에 , 안좋은 값이 계산이 들어왔을 때 영향을 덜 받습니다. 

![image](https://user-images.githubusercontent.com/50165842/148001548-bff35d70-8816-484d-86ac-0128d84733df.png)

저는 이 식이 다른 표현으로 여러 자료에서 사용된 것을 확인할 수 있었습니다. 위 두개의 사진은 v를 gradient와 같은 방향으로 하였고 , 

아래 두개의 사진은 gradient의 반대방향(최소가 되는 방향)으로 하였습니다. 하지만, 물리학에서의 속도와 가속도의 이론의 컨셉을 사용하는 것은 변하지 않습니다.

momentum이 빠른건 알겠지만, 정확히 어느정도 빨라지는지는 말하지 않았습니다. 이를 수학,물리학을 이용해서 증명해보도록 하겠습니다. 

결론 부터 말하자면 , stochastic gradient descent에 비해서 $$ {1 \over (1-\beta) }  $$ 만큼 빨라지게 됩니다. 

그리고 ,  gradient가 일정하다면 , 가속도(gradient) 와 마찰력((1-$$\beta$$)v) 가 같아지는 순간 v는 더 증가하지 않게 됩니다. 이를 terminal velocity라 합니다. 

$$ friction - accel  = 0$$

$$ (1-\beta)v = \epsilon d \theta $$ 

$$ v = \epsilon  {1 \over (1-\beta) } d \theta $$ 

예를 들어 , $$\alpha (or \beta)$$ 가 0.9이면 terminal velocity 는 gradient의 10배가 되고 , stochastic gradeint descent보다 약 10배가 빠르게 됩니다. 

### omit learning rate

$$dV = \beta dV + \alpha dW $$ 

$$ W = : W -  \alpha dV $$

위의 식을 learning rate를 생략해서 사용하는 것이 논문에서도 보여진다고 합니다. deeplearning book에서도 learning rate를 weight udpate에서 생략해서 설명하는 것으로 알고 있습니다.  이에 대한 자세한 설명은 없지만 저의 생각은 이렇습니다

 $$dV = \beta dV +  dW $$ 

$$\alpha dV = \alpha \beta dV +  \alpha dW $$ 

$$ dV \approx \alpha \beta dV +  \alpha dW $$ 

$$ W = : W -  dV $$

위 와 같이 되는게 아닌가 싶습니다.(저도 잘은 모르겠습니다. 아시는 분 있으시면 댓글이나 메일 로 알려주시면 감사하겠습니다.)



### why use viscous drag ?

지금 부터 말하는 내용은 보기 불편한 내용일 수 있습니다 . 편하게 스킵하셔도 괜찮습니다. 최대한 쉽게 적으려 노력했습니다.(설명이 정확하지 않다면 저한테 댓글이나 이메일로 알려주시면 감사하겠습니다.)

가속도(acceleration ) 와 마찰력(friction) 이 둘 다 존재하는 것은 이해하셧을 겁니다. 이제부터 , 이 마찰력이라는 단어를 항력이랑 번갈아가면서 사용할텐데 , 같은 의미라 보시면 됩니다.  이번에는 의 항력의 종류에 대한 설명입니다. 세상에는 여러 종류의 항력 들이 있습니다. dry friction , viscous drag ,  turbulent drag .. 등등이 있습니다. 여기서 저희가 사용하는 항력은 v에 비례하는 항력 viscous drag입니다 . 그렇다면 , 이러한 의문이 들 수 있습니다.

> 왜 굳이 viscous drag를 사용했어야 했을까? dry friction , turbulent drag 를 사용하면 안되는 걸까?

![img](https://upload.wikimedia.org/wikipedia/commons/thumb/e/e5/Free_body_diagram2.svg/200px-Free_body_diagram2.svg.png)

dry friction 에 대해서 간단하게 설명을 하면  , 고정된 힘이 가해지는 것입니다 . 예를 들면, 비탈면에 서있는 질량 m 짜리 물체는 중력(상수)에 비례하는 마찰력이 가해집니다. 

viscous drag는 속도에 비례하는 drag(힘) 가 가해지는 것입니다. turbulent drag 는 속도의 제곱(square of v) 에 비례하는 drag(힘)가 가해지는 것입니다.

$$ v := v - c $$

dry friction는 항상 고정된 힘을 particle에 가하는 것이므로 , particle이 $$\theta$$ 에서 local minima에 도달하지 못하고 , velocity가 0이 될 것입니다. 

viscous drag 가 작용할 경우 ![{\displaystyle v(t)={\frac {(\rho -\rho _{0})\,V\,g}{b}}\left(1-e^{-b\,t/m}\right)}](https://wikimedia.org/api/rest_v1/media/math/render/svg/50c3c92f93b494a34fbf0ed7555ca8b3ba8a548a)이와 같은 속력이 공식화 가 됩니다.

turbulent drag(자유낙하 운동)가 작용할 경우 ![ v(t) = \sqrt{ \frac{2mg}{\rho A C_d} } \tanh \left(t \sqrt{\frac{g \rho C_d A}{2 m}} \right). \,](https://wikimedia.org/api/rest_v1/media/math/render/svg/32c270eb2f615809be38ee5926955ec86b64d8b1) 이와 같은 속력이 공식화가 됩니다.

![img](https://upload.wikimedia.org/wikipedia/en/thumb/3/3a/Speed_vs_time_for_objects_with_drag.png/800px-Speed_vs_time_for_objects_with_drag.png)

![img](https://upload.wikimedia.org/wikipedia/commons/6/63/Inclinedthrow.gif)

맨 위의 그림은 turbulent drag의 시간/속도 그래프이고 , 아래 그래프는 viscous drag의 height /distance 그래프를 나타낸 것입니다. 정확한 설명보다는 컨셉적인 이해를 위해서 위 사진들을 가져왔습니다. 각각 , drag만이 작용한다고 했을 때 , turbulent drag의 경우 속도가 일정하게 유지 됨을 볼 수 있고 , viscous drag는 stroke 운동을 할 때  속도가 0이 되어 일정 height까지 올라갔다가 다시 내려감을 확인할 수 있습니다.

정리하면 , dry friction은  너무 극단적으로 속도가 줄기에 local minma에 가지 못할 수 있습니다. turblent drag의 경우 속도가 0이 되지 않기에  , 여기에 가속도(gradient) 가 더해질 경우 항상 시작점으로 부터 $$ O( \log t) $$ 의 속도로 영원히 멀어질 것입니다. 이러한 양극단의 성질을 해결하고자 viscous drag를 사용하면 drag가 dry friction 만큼 강하지 않기에 local minima에 도달할 수 있고 , gradient의 방향이 최소화 하는 방향이 아니라면 속도를 줄일 수 있습니다.

## implementation in deep learning

![image](https://user-images.githubusercontent.com/50165842/148003562-5984c8b1-99af-436a-aee3-17c66e7cbf42.png)

실제로 구현을 할 때 위의 식을 그대로 적용하게  되면 , $$ V_0 = 0$$ 으로 초기화 하고 시작하기 때문에 , 초기에는 매우 예상하는 값보다 값이 적게 나오게 됩니다. 따라서, $$ 1 \over 1 - {\beta}^t $$ 로 나누어 주어 scaling을 해줍니다. t가 점점 커질수록  scaling factor는 1에 가까워집니다. 이는  weighted average가 $$1 \over {1-\beta} $$  의 개수 만큼의 최신 데이터를 반영하기 때문입니다.

### memory efficient

gradient descent을 이용해서 momentum과 같이 최신 데이터를 반영하게 할 수 있습니다. 예를들어 ,$$\beta$$ 가 0.99 인 경우 100개의 data에 대한 gradient를 메모리에 올려놓으면 됩니다.  하지만, momentum은 코드 1줄로 이를 해결할 수 있습니다. 통계학이 필수적인것은 아니지만, 알고리즘을 고도화 하는데 있어서 좋은 도구가 될 수 있다고 느꼈습니다.

## Reference

[1] [Improving Deep Neural Networks: Hyperparameter Tuning, Regularization and Optimization - 홈 | Coursera](https://www.coursera.org/learn/deep-neural-network/home/welcome)

[2] [deeplearningbook optimization](https://www.deeplearningbook.org/contents/optimization.html)

[3] [Hands-On Machine Learning with Scikit-Learn, Keras, and TensorFlow: Concepts, Tools, and Techniques to Build Intelligent Systems: Géron, Aurélien: 9781492032649: Amazon.com: Books](https://www.amazon.com/Hands-Machine-Learning-Scikit-Learn-TensorFlow/dp/1492032646)

[4] [Drag (physics) - Wikipedia](https://en.wikipedia.org/wiki/Drag_(physics))

