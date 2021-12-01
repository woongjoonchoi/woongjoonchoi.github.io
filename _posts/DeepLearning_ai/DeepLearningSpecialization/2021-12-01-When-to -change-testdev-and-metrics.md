---
title : "When to change Dev/test sets and metrics"



excerpt: "DeepLearning.ai Dls ML strategy"

categories:
  - DLS_C3
tags:
  - [Machine Learning,Coursera,deep learning ]
# classes : wide
toc: true
toc_sticky: true
---
## Change Metrics

Example : Cat vs Dog

저희가 Cat, Dog 를 검출하는 Simple model을 만들어서 배포하는 시나리오를 가정해보겠습니다. 

| Algorithm   | Classification Error |
| ----------- | -------------------- |
| A algorithm | 3%                   |
| B algorithm | 5%                   |

Dev/test에서 이러한 지표가 나왔습니다. 자연스럽게 A를 선택하게 됩니다. 하지만, 현실의 문제는 그렇게 간단하지 않습니다. 만약에, A 알고리즘은  음란물을 검출할 수 있습니다. 하지만, B 알고리즘은 음란물을 검출하지 않습니다. 이럴 경우, 다시 B를 선택하게 됩니다.  기존 metric은 다음과 같습니다.

$$ Error = {1 \over {m_{dev}}} \sum_{i=1}^{m_{dev}} L( \widehat{y}^{(i)} \neq y^{(i)} ) $$ 

위 metric으로는 음란물과 개, 고양이 사진에 같은 가중치를 부여합니다. 따라서, B라는 알고리즘이 더 적합함에도 B의 metric은 best metric이 될 수 없습니다. 알고리즘을 다음과 같이 수정해보도록 하겠습니다.



$$ Error = {1 \over \Sigma w} \sum_{i=1}^{m_{dev}} w^{(i)} L( \widehat{y}^{(i)} \neq y^{(i)} ) $$ 

$$ w^{(i)} =  \begin{cases} 1 ,  & \mbox{if  }\quad x^{(i)}\quad\mbox{nonpornographic } \\ 10, & \mbox{if  }\quad x^{(i)}\quad \mbox{	pornographic} \end{cases} $$

이렇게 metric을 재정의하면 정확률과 음란물 검출을 전부 고려한 metric이 됩니다.



## Metric에 너무 열중하지 마라

처음부터 완벽한 metric을 만들 필요는 없습니다. metric이 바뀌어도, 학습 결과가 바뀌진 않습니다. 여기에 좋은 guideline이 있습니다.

1. 우선 metric을 설정하고 모델을 훈련한 후 선택합니다.
2. 그 모델이 user case에서 안 좋은 결과가 나오면 다시 재정의합니다.



## Change Dev/test set

이번에는 Dev/test error의 경우를 추가해보도록 하겠습니다.

![image](https://user-images.githubusercontent.com/50165842/144239085-3845a0ce-b07c-4cc0-a2b2-bc63779f7e80.png)

저희의  Dev/test set이 이러한 고양이들의 사진이라고 하겠습니다. Metric을 위와 같이 Pornographic을 잘 검출하도록 하여서 B 알고리즘을 선택했다고 가정하겠습니다. 하지만, 이 알고리즘을 실제로 user application에서는 잘 작동 안 할 수 있습니다.

![image](https://user-images.githubusercontent.com/50165842/144239316-75995f59-7f6e-42bd-8cf1-f80fdd895bb4.png)

user application의 이미지는 대부분 이러한 이미지일 수도 있습니다. user가 고화질의 카메라를 사용하거나, 초점을 맞춰서 사진을 찍을 가능성은 현저히 낮습니다. 이러면 Dev/test set을 바꾸어야 합니다.

## 결론

Dev/test에서 상당히 좋은 결과를 보여줬지만,  user-application에서 잘 작동하지 않는 경우 1. 모델의 error case를 분석한 후 metric을 다시 설정한다. 2. Dev/test의 user-application과 mismatch인지 확인한다. 라는 이 2가지의 접근 방법으로 해결해보시면 좋을 거 같습니다.