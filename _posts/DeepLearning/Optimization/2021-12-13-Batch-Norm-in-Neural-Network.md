---
title : "Batch Norm in Neural Network Training and Test"



excerpt: "Practical batch norm in neural net"

categories:
  - Optimization
tags:
  - [Machine Learning,batch size,deep learning]
# classes : wide
toc: true
toc_sticky: true
---

## Batch Norm in Neural Network Training and Test

이번 글에서는 실제로 Neural Network Training 과 Test에서 Batch Norm을 어떻게 적용하는지 알아보도록 하겠습니다.



## Adding Batch Norm into Nueral Network

$$ X^{[1]}   \implies W^{[1]} , b^{[1]} \implies Z^{[1]} ,  a^{[1]} =  g(Z^{[1]}) \implies W^{[2]}  ,b^{[2]}  \implies Z^{[2]} , a^{[2]} = g(Z{[2]}) \implies  ... $$ 

[]가 hidden layer를 표현하는 notation이긴 한데 , 앞의 post를 읽으셧다면 이해가 될거라 생각합니다.😀

이게 전형적인 Neural Net의 feedforward 입니다. 그렇다면 batch norm을 추가시키면 어떻게 될까요 ??



$$ Z^{[1]} ,  \mu =  \frac {\Sigma z^{[2]}}{m}, \sigma = \frac{(\Sigma z^{[2]} - \mu )^2}{\sqrt {\mu}}  \implies {z_{norm}}^{[1]} = \frac {(\Sigma z^{[1]}  -  \mu )}{\sqrt {\sigma}} \implies  $$

$$ {z_{norm}}^{[1]} = \frac {(\Sigma z^{[1]} - \mu)} {\sqrt {\sigma^2 + \epsilon }} \implies \widehat{z} = \alpha z_{norm} + \beta  $$    



위와 같이 새로운 learnable parameters $$\alpha , \beta $$가 추가가 됩니다.  기존에는 $$W^{[1]} ,b^{[1]} ,W^{[2]} , b^{[2]}  ..$$ 가 있었습니다. 하지만 , 여기에 $$ {\alpha}^{[1]}  {\beta}^{[1]} , {\alpha}^{[2]} , {\beta}^{[2]} ..$$ 들이 추가가 됩니다. 

## Learnable parameters in Neural Network

여기서 , 생각해봐야 할 것은 이 모든 parameters들이 필요한가 입니다. 아래의 수식을 봅시다.

 $$ Z_{norm} = \frac {(\Sigma z - \mu)} {\sqrt {\sigma^2 + \epsilon}} $$ 는 znorm을 구하는 수식입니다. znorm을 구할 때 평균을 빼 주는데 , $$ \mu = \frac {\Sigma z }{m}  = \frac{\Sigma {(wx^T + b)} }{m} \implies Z_{norm} = \frac {\Sigma {wx^T}}{\sqrt {\sigma^2 + \epsilon}}$$ 입니다.   즉, bias term은 있으나 마나 영향을 주지 않습니다. 따라서 , 이 bias term을 지워주어도 영향을 주지 않습니다.



batch Norm을 사용한 training pseudo 코드를 알아보도록 하겠습니다.

```python
for epoch = 1 ... epochs :
	for  i=1 ... mini-batch :
		feedforward , not using bias term
        	(in each hidden layer replace z_norm with z_hat using alpha, beta )
        calculate db , dw ,da 
        b ,w ,a update using opitmization algorithm(e.g. Adam, RMSProp , SGD ,GD etc)
```





## Batch Norm at Test time

Batch Norm의 이론을 이해하셨다면 매번 batch의 mean, variance를 구해야 함을 알 수 있습니다. 하지만, Test time에는 어떻게 해야 할까요? 1개의 input이 들어오는데, 이에 대한 mean, variance를 구해야 할까요? 이건 말이 안되는 것을 알고 있으실 겁니다. 일반적으로 neural networks 에서는 . 기존의 알고리즘에서 훈련을 할 때 $$ \mu , \sigma $$에 해당하는 부분을 exponentially weighted average를 이용해서 추정을 하게됩니다.  이 때 , $$ \mu , \sigma $$는 각 hidden layer의 값을 이용합니다. 그리고 , 이를 exponentially weighted average 를 이용해서 매번 $$ \mu , \sigma $$ 를 update를 해줍니다. 이를 통해 , 대략적인 $$\mu , \sigma$$ 의 값을 알아내게 됩니다.   다시 말하자면, train에는 mini-batch를 통해서 mean, variance를 구합니다. 하지만, test에서는 1개만을 처리해야 하는 경우가 있을 수 있고, single example을 처리하기 위해서 mean, variance의 estimate를 사용하게 됩니다.  ***<u>이는 정확한 mean, variance를 구하지는 않지만, 정확하게 구하는 것에 비하여 꽤 robust 합니다.</u>*** 하지만, 실제로 딥러닝 프레임웍에서는 이러한 계산이 이와는 다르게 구현되어 있을 수 있습니다. 하지만, 어찌 됐든 잘 작동할 것입니다