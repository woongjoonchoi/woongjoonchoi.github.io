---
title : "TransferLearning in NLP"



excerpt: "DeepLearning.ai nlp course"

categories:
  - DeepLearningainlp qa
tags:
  - [Machine Learning,Coursera,deep learning , nlp]

toc: true
toc_sticky: true
---
## 개요

deeplearning.ai 의 NLP 특화 과정의 일부를 듣고 기록하는 것입니다. 본 글은 상업적인 목적으로 작성하는 것이 아니기 때문에 , deeplearning.ai 의 post를 이용하도록 하겠습니다.





## QA task

Qa task는 크게 2가지 방법으로 문제를 해결합니다. 

### 1.Context-based QA

![image-20211120215759003](C:\Users\Woong\AppData\Roaming\Typora\typora-user-images\image-20211120215759003.png)

context-based는 ***<u>Question</u>*** 과 ***<u>context</u>***  가 동시에 주어지면 Model에서 이를 읽고 정답을 찾아내는 것입니다.

### 2. Closed Domain QA

![image-20211120220021543](C:\Users\Woong\AppData\Roaming\Typora\typora-user-images\image-20211120220021543.png)

이는 ***<u>question</u>***이 주어지면 바로 정답을 찾아내는 것입니다.   PLM이 학습한 데이터의 양이 방대해서 ***<u>context 없이</u>*** 정답을 찾아낼 수 있습니다. 





## Transformer 기반 PLM

![image-20211120220244035](C:\Users\Woong\AppData\Roaming\Typora\typora-user-images\image-20211120220244035.png)

기존의 NLP는 각 task의 특화되도록 parameter를 학습해왔기에 downstream task  가 불가능했습니다. 

![image-20211120220411025](C:\Users\Woong\AppData\Roaming\Typora\typora-user-images\image-20211120220411025.png)

하지만, Transformer,더 정확히는 Bert 등장 이후에는 ***<u>Fine-tuning</u>***이  가능해졌습니다. 위의 예시는, Sentiment Classification으로 pre-training을 한후 downstream task로 QA를 푸는 예시입니다.





## Bert, T5

![image-20211120220646988](C:\Users\Woong\AppData\Roaming\Typora\typora-user-images\image-20211120220646988.png)

Bert의 특징은 ***<u>Bi-directional</u>*** 이라는 것입니다. Bert는  Transformer 가 Encoder-Decoder 형식인것에 비해 Encoder만을 사용하여 ***<u>attention mask</u>***를 적용하지 않습니다.



![image-20211120220835853](C:\Users\Woong\AppData\Roaming\Typora\typora-user-images\image-20211120220835853.png)

기존의 모델은 ***<u>한 모델당 하나의 task</u>***를 할 수 있습니다. 하지만 , T5는 한 모델이 classification , QA 등등 ***<u>multi task</u>***를 하는 multi task model입니다.



![image-20211120221021518](C:\Users\Woong\AppData\Roaming\Typora\typora-user-images\image-20211120221021518.png)

T5는 800GB나 되는 데이터를 사용했고, 일반적으로 데이터가 많을 수록 좋은 performance를 보여주기에 T5의 성능이 더욱 더 좋습니다.





## TransferLearning in NLP

NLP에서 transferlearning은 여러 case로 나눌 수 있습니다.



![image-20211120221241570](C:\Users\Woong\AppData\Roaming\Typora\typora-user-images\image-20211120221241570.png)



우선 ***<u>Feature-based, Fine-tuning</u>***의 차이부터 살펴보겠습니다.



### Feature based vs Fine-tuning

![image-20211120221358045](C:\Users\Woong\AppData\Roaming\Typora\typora-user-images\image-20211120221358045.png)

Feature-based는 word embedding 모델을 pre-training 한뒤 다른 Model에서 ***<u>embedding layer</u>***로 사용하는 것입니다. 

Fine-tuning은 ***<u>어떤 task에 대하여 Pre-train</u>***을 한 다음에 Downstream task를 정한후 ***<u>fine-tuning</u>*** 하는 것입니다. 

### Fine-tuning

![image-20211120221545373](C:\Users\Woong\AppData\Roaming\Typora\typora-user-images\image-20211120221545373.png)

Fine-tuning을 할 때는 모든 layer를 freeze를 풀고 훈련시키는 것이 아니라 ***<u>output layer의 freeze</u>***만을 해제한 후에 학습을 합니다.

![image-20211120221706927](C:\Users\Woong\AppData\Roaming\Typora\typora-user-images\image-20211120221706927.png)

일반적으로 Data가 많을 수록 pre-training의 성능이 높습니다.



### self-supervised learning

![image-20211120221750910](C:\Users\Woong\AppData\Roaming\Typora\typora-user-images\image-20211120221750910.png)

세상에는 unlabeled text data가 labeled text data보다 더 많습니다. 

![image-20211120221829768](C:\Users\Woong\AppData\Roaming\Typora\typora-user-images\image-20211120221829768.png)

따라서, self-supervised task인  masked language model을 학습합니다.  

![image-20211120221942144](C:\Users\Woong\AppData\Roaming\Typora\typora-user-images\image-20211120221942144.png)

그런 다음, 원하는 downstream task에 대하여 fine-tuning을 진행합니다.