---
title : "Batch Norm introduction"



excerpt: "Batch Norm intro"

categories:
  - Optimization
tags:
  - [Machine Learning,batch size,deep learning]
# classes : wide
toc: true
toc_sticky: true
---
This article was expressed in my own words after listening to the DLS batch norm lecture.
## Input Normalization 과 유사점

이전에 보았던 Input Normalization은 Input 의 range(scale)를 조정해줘서  , weight의 update가 균일하게 끔 되게 해주었습니다. 따라서, 상대적으로 수렴이 빠르게 되도록 해주는 역할을 하였습니다.

![Neural Network 적용 전에 Input data를 Normalize 해야 하는 이유](https://camo.githubusercontent.com/1f4da8632894c7220f804bcddc6e50bc708e6481/68747470733a2f2f692e696d6775722e636f6d2f32784e423261552e6a7067)

## 왜 batch normalization을 사용하는가?

batch normalization은 이를 hidden layer의 input의 적용해주는 것입니다. 이를 통해 수렴을 빠르게 되서 더 빠른 hyperparmeter search가 가능해집니다.

## Batch Norm을 어떻게 하는가

예를 들어 보겠습니다.

두번째 hidden layer의 평균, 분산을 구합니다. 

$$ \Sigma z^{(2)}_{i} $$  

$$ \mu = \frac {\Sigma z^{(2)}_{i}} {m} $$ 

$$ \sigma = \frac {(\Sigma z^{(2)}_{i} - \mu )^2} {m} $$

$$ z_{norm}^{(i)}  =  \frac {(\Sigma z^{(2)}_{i} - \mu )}{\sqrt {\sigma} } $$

하지만 , 이렇게 하면  $$\sigma$$ 가 0이 될 경우 계산을 할 수 없습니다. 따라서, 아래와 같이 계산을 합니다.

$$ z_{norm}^{(i)}  =  \frac {(\Sigma z^{(2)}_{i} - \mu )} {\sqrt {\sigma^2 + \epsilon}  } $$

 

## Batch Norm의  실제 적용

$$ z_{norm}$$ 를 그대로 사용하게 된다면 분포가 N(0,1)을 따르게 됩니다. 하지만, 우리는 모든 z의 분포가 N(0,1)을 따르기를 원하지 않습니다. 이 식을 좀더 발전을 시켜서 

$$ \widehat{z} = \alpha z_{norm} + \beta $$  

로 작성할 수 있습니다. 이렇게 작성을 하게 된다면  $$ \alpha =  {\sqrt {\sigma^2 + \epsilon} }, \beta = \mu $$ 가되는데 이를 통해 평균,분산을 원하는 분포를 따르도록  $$ \alpha , \beta $$를 learnable parameters로 사용하게 됩니다 .

