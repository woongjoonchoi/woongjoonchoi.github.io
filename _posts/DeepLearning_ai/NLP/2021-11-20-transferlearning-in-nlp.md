---
title : "TransferLearning in NLP"



excerpt: "DeepLearning.ai nlp course"

categories:
  - DeepLearningainlp qa
tags:
  - [Machine Learning,Coursera,deep learning , nlp]
# classes : wide
toc: true
toc_sticky: true
---
## 개요

deeplearning.ai 의 NLP 특화 과정의 일부를 듣고 기록하는 것입니다. 본 글은 상업적인 목적으로 작성하는 것이 아니기 때문에 , deeplearning.ai 의 post를 이용하도록 하겠습니다.





## QA task

Qa task는 크게 2가지 방법으로 문제를 해결합니다. 

### 1.Context-based QA

![image](https://user-images.githubusercontent.com/50165842/142728447-eccf660c-144f-439c-b2a8-54c12692aba2.png)

context-based는 ***<u>Question</u>*** 과 ***<u>context</u>***  가 동시에 주어지면 Model에서 이를 읽고 정답을 찾아내는 것입니다.

### 2. Closed Domain QA

![image](https://user-images.githubusercontent.com/50165842/142728460-f29a52b0-301e-45fb-9708-9585e81be6ef.png)

이는 ***<u>question</u>***이 주어지면 바로 정답을 찾아내는 것입니다.   PLM이 학습한 데이터의 양이 방대해서 ***<u>context 없이</u>*** 정답을 찾아낼 수 있습니다. 





## Transformer 기반 PLM

![image](https://user-images.githubusercontent.com/50165842/142728474-f346ac17-5e4a-4fc6-a66f-c3d4ee0189e4.png)

기존의 NLP는 각 task의 특화되도록 parameter를 학습해왔기에 downstream task  가 불가능했습니다. 

![image](https://user-images.githubusercontent.com/50165842/142728482-70c5d7e8-1a0c-41f9-afce-8bc17709d33f.png)

하지만, Transformer,더 정확히는 Bert 등장 이후에는 ***<u>Fine-tuning</u>***이  가능해졌습니다. 위의 예시는, Sentiment Classification으로 pre-training을 한후 downstream task로 QA를 푸는 예시입니다.





## Bert, T5

![image](https://user-images.githubusercontent.com/50165842/142728492-30692ae8-040e-41b5-819d-e2028f392d24.png)

Bert의 특징은 ***<u>Bi-directional</u>*** 이라는 것입니다. Bert는  Transformer 가 Encoder-Decoder 형식인것에 비해 Encoder만을 사용하여 ***<u>attention mask</u>***를 적용하지 않습니다.



![image](https://user-images.githubusercontent.com/50165842/142728500-9e6854d5-28c3-4b4e-b523-2aaa55890fdd.png)

기존의 모델은 ***<u>한 모델당 하나의 task</u>***를 할 수 있습니다. 하지만 , T5는 한 모델이 classification , QA 등등 ***<u>multi task</u>***를 하는 multi task model입니다.



![image](https://user-images.githubusercontent.com/50165842/142728506-72caa8b3-175f-42f2-99c0-ac10fa01ec94.png)

T5는 800GB나 되는 데이터를 사용했고, 일반적으로 데이터가 많을 수록 좋은 performance를 보여주기에 T5의 성능이 더욱 더 좋습니다.





## TransferLearning in NLP

NLP에서 transferlearning은 여러 case로 나눌 수 있습니다.



![image](https://user-images.githubusercontent.com/50165842/143018896-613bbdd5-47ea-4b5f-a614-b54fbdd1f4ca.png)



우선 ***<u>Feature-based, Fine-tuning</u>***의 차이부터 살펴보겠습니다.



### Feature based vs Fine-tuning

![image](https://user-images.githubusercontent.com/50165842/142728512-eba48f8c-38ca-4a23-8c60-e33a2b80efcd.png)

Feature-based는 word embedding 모델을 pre-training 한뒤 다른 Model에서 ***<u>embedding layer</u>***로 사용하는 것입니다. 

Fine-tuning은 ***<u>어떤 task에 대하여 Pre-train</u>***을 한 다음에 Downstream task를 정한후 ***<u>fine-tuning</u>*** 하는 것입니다. 

### Fine-tuning

![image](https://user-images.githubusercontent.com/50165842/142728520-f978ffaa-5cdb-4c43-835d-d6a6df563668.png)

Fine-tuning을 할 때는 모든 layer를 freeze를 풀고 훈련시키는 것이 아니라 ***<u>output layer의 freeze</u>***만을 해제한 후에 학습을 합니다.

![image](https://user-images.githubusercontent.com/50165842/142728524-8343b7d7-b83f-4a96-9242-2ec45b09b0cc.png)

일반적으로 Data가 많을 수록 pre-training의 성능이 높습니다.



### self-supervised learning

![image](https://user-images.githubusercontent.com/50165842/142728528-dfcbb9d2-ad0b-4a86-951e-3aab85f60ef5.png)

세상에는 unlabeled text data가 labeled text data보다 더 많습니다. 

![image](https://user-images.githubusercontent.com/50165842/142728532-878a8f3e-f5d8-4d8a-99fe-395670b01c51.png)

따라서, self-supervised task인  masked language model을 학습합니다.  

![image](https://user-images.githubusercontent.com/50165842/142728540-ee18a3fa-711f-4afc-a0e5-2b218b6e2527.png)

그런 다음, 원하는 downstream task에 대하여 fine-tuning을 진행합니다.

