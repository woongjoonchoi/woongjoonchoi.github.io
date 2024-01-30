---
title : "Batch vs Mini batch vs Stochastic"



excerpt: "Batch size"

categories:
  - Optimization
tags:
  - [Machine Learning,batch size,deep learning,DLSC2]
# classes : wide
toc: true
toc_sticky: true
---
이 내용은 DeepLearning Specialization , BoostCourse AI tech 2기 내용을 듣고 제 맘대로 정리한 내용입니다.

## Batch Size란?

batch size란  GD(gradient Descent) 로 optimization을 진행할 때 얼마만큼의 data에 대해 loss를 계산해서 update할지 정하는 hyperparameter 입니다. 이 batch size를 얼마나 주느냐에 따라 각각의 장.단점 및 특징이 있습니다. 이에 대해 알아보고자 합ㄴ다.

## Batch vs Mini-Batch

Batch 와 Mini-Batch의 차이점을 알아보도록 하겠습니다.

$$ X = {X^{(1)} , X^{(2)} , X^{(3)} ...  X^{(1000)} ,X^{(1001)} , .... X^{(m)}} $$ 

기존의 Batch GD는 m개의 sample에 대한 loss를 계산후 Gradient Descent를 수행했습니다. 

$$ X^{\left\{ 1 \right\}} = {X^{(1)} , X^{(2)} , X^{(3)} ...  X^{(1000)} } $$

하지만, mini-batch는 1000개의 sample에 대하여 gradient descent를 진행합니다. 예를 들어 , m이 5000000개라면 , Batch 는 5000000개에 대한 loss를 계산하고 , mini-batch는 1000개에 대한 loss를 계산합니다.

mini-batch GD를 pseudo code로 작성해보도록 하겠습니다. 



```
for t in range(5000) :
	for X(1) , X(2) ... X(t) in  X{t} :
		feedforwarding
    loss
    back_propagtion
    ## feedforwarding은 vectorization으로 한번에 계산이 된다.
   
	
```

이렇게 진행이 됩니다. 

## stochastic batch GD vs mini-batch GD vs batch GD

![image](https://user-images.githubusercontent.com/50165842/145401371-5219e32d-0a70-470f-a86b-942ed5eb87bc.png)

Batch GD , Mini-batch GD의 차이점을 극단적으로 보여주는 예시입니다.  Mini-Batch GD를 사용할 경우 cost function 이 oscillation할 수 있습니다. 왜냐하면 , 전체 데이터의 일부를 sampling 하는 것이기 때문에 mini-batch 가 bad example로 뽑힐 수 도 있습니다. batch GD 보다 noise가 더해지기 때문에 ,cost function이 불안정 할 수 밖에 없습니다.

![image](https://user-images.githubusercontent.com/50165842/145404443-a2506483-9f52-4090-8909-49582b7cf636.png)

- Stochastic gradient Descent
  - batch size가 1인 GD
  - vectorization으로 speed의 이점을 얻지못함
- mini-batch gradient Descent
  - 전체 training data size m보다 작은 data 이용
  - Vectorization 을 이용한 speed 이점을 활용가능
  - 전체 training dataset에 대하여 feedforward를 하지 않고 update가 가능함
- Batch
  - 전체 training data size m을 batch size로 지정
  - 전체 data로 update를 하기 때문에 매 update의 iteration 간격이 길다.



## mini-batch gradient descent in mathmatical view

mini-batch를 수학적인 관점에서 바라보면 local-minima 에 수렴하는 일이 발생하지 않음을 알 수 있습니다. 이는, non-convex objective function에서 사용이 가능함을 의미합니다.

![image](https://user-images.githubusercontent.com/50165842/145405606-66576664-2691-4da2-a083-8a249494df1c.png)

전체 파라미터가 아닌 일부 파라미터를 이용한 loss의 기댓값을 사용하게 됩니다. 

![image](https://user-images.githubusercontent.com/50165842/145405896-6515bed6-5bc0-4577-aba6-5fc449739acc.png)

mini-batch의 경우 매번 다른 mini-batch를 선택하기 때문에 매번 objective function이 바뀌게 됩니다.  이를 통해서 local optima로 수렴하는 것을 방지할 수 있습니다. 



