---
title : "ModelOptimization Introduction"



excerpt: "Boostcampai model optimziation 1"

categories:
  - ModelOptimization
tags:
  - [Machine Learning,Boostcourse,deep learning ]
# classes : wide
toc: true
toc_sticky: true
---

이 자료는 필자가 만든 자료가 아닌 부스트코스 ai tech 2기 교육자료임을 명시합니다. 이 자료는 상업적으로 이용할 수 없습니다.



## 모델 경량화란?

![image](https://user-images.githubusercontent.com/50165842/143047467-d4a22e15-9143-4488-a94a-aa3808ecbc13.png)

기존의 ai는 외부 서버(ex클라우드) 에 데이터를 전송하여 추론을 한후 다시 단말기기(edge-device)에 전달하여 결과를 보여주는 방식이였습니다. 이는 , 단말기기(ex,스마트폰,센서 etc)의  ***<u>컴퓨팅 파워 , 램 사용량 , 저장공간 , 저전력사용(수명)</u>***   등의 ***<u>제약사항</u>*** 으로 인해 어쩔수 없는 선택이였습니다. 하지만, 요새 많은 기업들은 on-device ai 에 집중하고 있습니다.  그 이유는 아래와 같습니다.

> 1. low latency
>
>    클라우드 에 전송하고 결과를 다시 받는 과정을 거치지 않고 edge-device내에서 직접 연산을 수행하기 때문에 지연시간이 상대적으로 적어지게 됩니다.
>
> 2. 보안
>
>    클라우드로 모든 데이터가 집결되기 때문에 , 중앙화로 인한 보안문제가 이슈가 되는데 , 제 개인적인 생각으론 이건 클라우드 서비스를 제공하는 회사의 보안 기술 레벨에 따라 다르지 않을까 싶습니다.
>
> 3. 서버의 에너지사용량
>
>    대용량의 서버가 운용이 되다보면 그 서버들이 단말기에서 수집한 데이터를 갖고있어야 하는 경우도 있고 ,이를 항상 켜놓은다면 이에 따른 ***<u>탄소 배출량</u>***도 어마어마할 것입니다.





## 경량화의 여러 분야

경량화의 여러 분야를 소개해보도록 하겠습니다.



### Efficient Architecture design

![image](https://user-images.githubusercontent.com/50165842/143047428-7f4ded62-4453-4f57-8fd8-8c05de8d75e4.png)



매년 여러 아키텍쳐들이 쏟아져 나오는데 , 이 중 오른쪽에 보이는 SqueezeNet이 파라미터 대비 성능이 가장 좋다고 합니다. 저도 안써봐서 딱히 할말은 없네요.

#### AutoML, Neural Architecture Search

![image](https://user-images.githubusercontent.com/50165842/143049875-c2c2efd6-cd5f-4718-8920-6d0db0a9f115.png)

위 그림은 ***<u>모델을 찾는 네트워크</u>***를 학습을 시키는 것입니다. 

모델을 찾는 네트워크가 모델을 제안하고 , 학습을 통해 accuracy를 구하고 , 이 accuracy로 모델을 찾는 네트워크를 학습하는 iterative 한 방식으로 작동합니다.

![image](https://user-images.githubusercontent.com/50165842/143049817-79147fd5-f5be-46fc-a1ad-0cffb4c3433b.png)



결국 이 과정이 진행 되다 보면 왼쪽은 사람이 이해할 수 있는 직관적인 아키텍쳐 였지만, 오른쪽의 모델과 같은 ***<u>직관적인지는 않지만 성능은 더 좋은 모델</u>***을 찾아내게 됩니다.



#### Netowrk Pruning

![image](https://user-images.githubusercontent.com/50165842/143049761-46d95009-015e-473c-9090-5c9256b88378.png)





이는 모델이 추론하는데 중요도가 낮은 파라미터를 제거하는것입니다. 예를들어, L2 norm이 크면, 그 weight의 모델이 끼치는 영향력이 크다는 것을 의미하고 , parameter에 대한 loss gradient가 크면 , parameter에 따라서 ,loss값이 잘 변한다는 의미이므로 중요하다고 기준을 잡을 수도 있습니다.



![image](https://user-images.githubusercontent.com/50165842/143050068-46c0cae1-3369-43b9-8b32-0112bcf996e8.png)

Structured Pruning은 ***<u>파라미터 그룹 단위</u>***를 기준으로 하는 pruning입니다. 예를들어, conv network에서의 conv layer가  중요도를 계산했을 때 중요하지 않은  것들을 지울 수 있습니다. 

![image](https://user-images.githubusercontent.com/50165842/143050446-d3b683b0-c189-4613-874d-f7036136362a.png)

pruning을 진행할 수록 matrix는 sparse 하게 되는데 ,device 상에서는 이를 compress 한 정보만을 들 고 있습니다. 여기서 , matrix를 다시 복원해서 계산할 수도 있고 , 아니면 compress 된 정보를 연산에 사용할 수 있습니다.후자의 경우에 대한 ***<u>알고리즘을 잘 짠다면 굉장히 효율적</u>***일 것입니다.



#### Knowledge distillation



![image](https://user-images.githubusercontent.com/50165842/143051470-451e8e97-425f-4109-887a-6584e4f73229.png)

이건 아직 잘 모르겠습니다. 다시 알게되면 수정해서 올리겠습니다.



![image](https://user-images.githubusercontent.com/50165842/143052600-1950011b-f8bd-4fe1-b4ac-906e8dc1af61.png)

#### Matrix/Tensor decomposition

![image](https://user-images.githubusercontent.com/50165842/143052624-2fd5f314-eab4-4318-a03c-8bc6581ae0f4.png)

이것도 잘 모르겟어요.. ㅠㅠ



#### Network Quantization

![image](https://user-images.githubusercontent.com/50165842/143052554-cd387129-c91d-44b9-a5bb-c769f531276d.png)





데이터의 ***<u>비트 수를 줄여서</u>*** 연산을 수행하면 차지하는 메모리의 수가 줄어들기 때문에 효율적이게 됩니다.하지만,

비트를 변환한 후 다시 변환하게 되면 , 결과값이 달라지게 됩니다. 하지만 , 이러한 ***<u>Quantization Error에 대해서 robust하게 작동한다는 것이 경험적으로 밝혀져 있다</u>***.   

![image](https://user-images.githubusercontent.com/50165842/143052524-ae8964ef-1ec9-4c0d-9264-80c739cf2e61.png)

어떤 경우는 , int 로변환해도 특정 cpu에서 속도가 느려지는 경우도 있다고 한다. 하드웨어마다 천차만별이다. 



#### Network Compiling



![image](https://user-images.githubusercontent.com/50165842/143059389-d8310fd6-09c2-46b4-90fc-c18cdfe10689.png)

사실상 ***<u>속도에 가장 큰 영향</u>***을 미친다. 자세하게 다루기엔 어려운 분야이다.

![image](https://user-images.githubusercontent.com/50165842/143059350-ceb6bc1e-175b-4270-bd76-d299967f2c0f.png)



pre-defined된 rule이 tf-lite에는 200개가 있다고 합니다. 제가 정확히 이해햇는지는 모르겟지만, tf-lite의 conv는 기존의 convolution연산을 수행하면서 clamping연산(relu)을 같이 하도록 하는것 입니다. 라고 이해했습니다. tf-lite의 convolution을 수행하면 기존의 convolution 연산후 relu연산을 하는것과 같다고 이해가 됩니다.



![image](https://user-images.githubusercontent.com/50165842/143059986-fdac26d0-f8d3-4a40-a28c-091545dedb3e.png)



문제는  프레임워크도 다양하고 , 적용해야할 하드웨어의 조합도 많습니다. 이러한 하드웨어들의 코어,유닛수,instruction set, 가속 라이브러리가 달라서 문제가 발생합니다.



![image](https://user-images.githubusercontent.com/50165842/143059323-bb46dda2-549f-4ab0-9237-41aacedeb390.png)  













같은 하드웨어 회사임에도 성능의 차이가 발생한다고 합니다. 위예시는 , V100 ,K80에서의 compile 성능차이를보여줍니다.

![image](https://user-images.githubusercontent.com/50165842/143059792-5239b4a7-7077-4d32-9459-86656782ce54.png)

software 2.0(AutoML)로 좋은 graph fusion을 찾아내고자 하는 시도가 있습니다. Compile의 경우 성과가 있지만, 앞에서 보았던 문제점들이 있습니다.



![image](https://user-images.githubusercontent.com/50165842/143318100-a2b0b085-1a80-4b1e-8617-717463a9770a.png)

이상입니다.

