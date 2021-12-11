---
title : "Why  Batch Norm Works"



excerpt: "Batch Norm why"

categories:
  - Optimization
tags:
  - [Machine Learning,batch size,deep learning]
# classes : wide
toc: true
toc_sticky: true
---
이 글은 Coursera의 batch norm강의를 듣고 작성한 강의 입니다. 강의 내용을 그대로 정리하기 보다는 나의 말로 표현한 글입니다.



## Why batch norm works

저번 글에서는 Batch Norm을 어떻게 implementation을 하고 어떤 learnable parameters를 설정하는지에 대한 내용을 적었습니다. 이번 글은 batch norm이 왜 작동하는 지를 좀 더 low-level에서 바라볼 예정입니다.

## Covariance shift

![image](https://user-images.githubusercontent.com/50165842/145677607-cca1342b-037f-4857-8cf5-e592cd38d6b8.png)



고양이를 분류하는 '이미지 분류' 모델을 만든는 시나리오를 가정하겠습니다. 저희의 train dataset은 검은 고양이의 사진만 들어있습니다. 

![image](https://user-images.githubusercontent.com/50165842/145677619-f0fcd942-ff8d-4518-b5a4-1789736b05a8.png)

하지만, test dataset에는 컬러풀한 고양이 사진들이 들어있습니다. 

![image](https://user-images.githubusercontent.com/50165842/145677666-4cd7f905-3735-4a87-b0bb-9694d70c5d18.png)

모델이 train에서는 잘 작동하더라도 test에서는 잘 작동하지 않을 가능성이 높습니다. ***<u>이는 data의 분포가 다르기 때문에 test에서 잘 분류하지 못할 가능성이 높습니다.</u>*** 이러면 , 모델을 처음부터 다시 학습을 시켜야 할 수 있습니다.  ground truth가 변하지 않더라도 다시 학습을 시켜야 할 것입니다. ***<u>이러한 분포의 차이를 covariance shift 라고 부릅니다.</u>***

## Batch Norm In Neural Net

![image](https://user-images.githubusercontent.com/50165842/145677792-35461997-97df-4974-b98f-0f1b7b585538.png)

두 번째 히든 레이어의 아웃풋인 $$ a^{[2]} $$가 있습니다. ***<u>전체 3번째, 4번쨰 ,5번째 hidden layer 관점에서는</u>*** $$ a^{[2]} $$ ***<u>라는 input에 잘 generalization이 될 수 있도록 다음 hidden layer들의 parameter들을 update를 진행합니다.</u>***

![image](https://user-images.githubusercontent.com/50165842/145678460-dcf38ef3-1bf6-43b5-8af1-913b8511740e.png)

하지만, 첫번째 , 두번쨰 layer의 hidden layer 학습을 통해 parameter가 update가 됩니다. 즉, 매번 $$ a^{[2]} $$  ***<u>의 분포가 바뀌므로 , 전체 Neural Net 관점에서는 매번 새로운 input에 대해 generalization을 시도하게 됩니다.</u>*** 즉, covaraince shift가 발생합니다. 따라서 , batch norm을 통하여 mean, variance를 통일을 시켜주어서 좀 더 빠르게 convergence할 수 있도록 해주는 것 입니다. 다시 한번 상기하자면, mean, variance가 normalization을 한후 N(0,1) 을 항상 따르는 것이 아닙니다. $$ \alpha , \beta $$ 라는 learnable parameter를 이용해서 mean,variance도 generalization을 잘 하는 값으로 update를 해 나갑니다.



## Batch Norm이 본질적으로 하는것

Batch Norm은 본질적으로 w2, b2의 변화량을 줄여줍니다. $$ \alpha 2 , \beta 2$$  를 정함으로써 $$ a^{[2]} $$  ***<u>의 범위를 제한시킵니다.</u>*** $$ a^{[2]} $$  가 갈 수 있는 범위를 제한함으로써 좀 더 안정적으로 Neural Net을 학습할 수 있게 해줍니다. ***<u>이를 다른 말로 분포가 이동하는 양을 줄인다고 표현할 수 있습니다. 이전의 hidden layer들이 이후의 hidden layer들이 배워야 할 양을 줄여준다고 볼 수 있습니다.</u>***   다른 말로 하면, w3, b3가 $$ a^{[2]} $$  에 대해서 ***<u>학습을 다른 layer들과 독립적으로 진행하게 됩니다. 다른 layer들의 관점에서 보아도 독립적으로 됩니다.</u>***  결과적으로 네트워크의 convergence 속도를 올려주게 됩니다. Batch Norm을 적용 안 해줬을 때 convergence가 잘 안돼서 performance의 영향을 미치는 parameter라 생각할 수도 있지만, 좀 더 정확하게 보면 우리의 인내심이 performance가 나올 때 까지 기다리지 못하는 것이라고 봐도 괜찮다고 개인적으로 생각합니다. 

## Unexpected regularization

Neural Net에서 Batch Norm을 적용할 경우 의도하지 않은 Regularization을 적용하게 됩니다. Batch Norm은 말 그대로 mini-batch에 대해서 gradient를 계산 후 mean, variance를 update를 해나가는 방법론입니다. ***<u>하지만, 전체 데이터가 아닌 mini-batch이기 때문에 전체 데이터의 mean, variance에 비해서 mean, variance에 약간의 noise가 더해지게 됩니다</u>***. 전체 train Dataset 의 mean, variance의 약간의 noise를 더하게 되므로 overfitting 되지 않고 regularization이 되는 효과가 발생하게 됩니다. 마치, dropout으로 매번 hidden layer output에 약간의 noise를 줌으로써 regularization을 하는 것과 비슷합니다. ***<u>하지만, 주의할 점은 말 그대로 unexpected regularization이기 때문에 batch norm을 regularization을 위해서 사용하면 안 됩니다.</u>***

 