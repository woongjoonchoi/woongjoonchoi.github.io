---
title : "ML landscape"



excerpt: "Hands on machinelearning chp1"

categories:
  - Handsonmachinelearning
tags:
  - [Machine Learning,Handsonmachinelearning,orelly]
# classes : wide
toc: true
toc_sticky: true
---



## 개요



![img](https://www.coursehero.com/thumb/98/25/9825575fc357c78fa33b0c783b851da1ce7dfe04_180.jpg)

이 포스트는 Orelly 의 Hands on MachineLearning 책 chp1 을 공부하고 필자가 정리하는 글입니다. 아카이브 용으로 적는거라 표현이나 설명이 부족할 수 있습니다. 제가 먼저 정리하고 다시 책을 참조하고 부족한 부분은 채워넣는 식으로 글을 작성합니다. 부족한 부분은 피드백 부탁드리겠습니다.

## What is ML?

이 책에서 필자는 첫장에서 이러한 질문을 던집니다. ***<u>ML이란 무엇인가?</u>***  저는 처음에 ML이란 기***<u>계를 알고리즘을 통해 학습시키는 것</u>***이다.  라고 스스로 답했습니다. 필자의 답은 비슷하지만 좀 달랐습니다.

> ***<u>Machine Learning is about the building system that can learn from data.</u>***

제 답과 배운다는 점은 동일하지만, 저의 답에는 ***<u>data</u>*** 에 대한 언급이 빠져있습니다.  필자가 인용한 정의들은 아래와 같습니다.

> ***<u>[Machine Learning is the] field of study that gives computers the ability to learn without being explicitly programmed.</u>*** —Arthur Samuel, 1959
>
> ***<u>A computer program is said to learn from experience E with respect to some task T and some performance measure P, if its performance on T, as measured by P, improves with experience E.</u>*** —Tom Mitchell, 1997

 배우는 과정 도 중요하지만 ***<u>배울 대상이 없으면</u>*** 안됩니다.  그렇다고 해서, ***<u>단순히 wikipedia로 부터 데이터를 다운 받기만 해서도 안됩니다.</u>***   

## Why use ML?

ML을 왜 사용하는 가에 대해서는 이번에는 저 자신이 스스로 잘 대답했습니다. ***<u>전통적인 rule-based 알고리즘으로 풀 수 없는 복잡한 문제를 ML로 비교적 효율적으로 해결할 수 있기 때문입니다.</u>***


이 책에서는 예시로 , 'SPAM 문자 필터'를 예시로 들었습니다.

![image](https://user-images.githubusercontent.com/50165842/142877003-57c162c8-8ab7-4cec-b73b-f1a6a4b43450.png)

위 사진은  전통적인 rule-based 방식의 spal filter 를 어떻게 만든지 도식화 한 것입니다. 예를 들어 , '안녕하세요 고객님' 과 같은 문구로 시작을 하게 되면 spam으로 처리하도록 로직을 짯다고 가정을 하겠습니다. 그러면, 다음 번엔  '안7녕7하7세요 고갱님' 이런식으로 올 수도 있습니다. 사실 이정도는 정규표현식이나 Trie구조(trie구조로 정규표현식을 만들었는지는 잘 모르겟습니다..) 로 커버가 가능할 수 있습니다.  요새는, '★★★★★★★★ ...'이런식으로 오거나 '[웹발신]  '. '[국제발신]'  등등의 형태로 오는데 ***<u>이걸 매번 logic을 다시 짜는건</u>*** 비효율적입니다.  

ML을 'SPAM 필터' 에 사용하게 된다면 이를 자동적으로 분류할 수 있습니다.

![image](https://user-images.githubusercontent.com/50165842/142879855-5ad02930-1744-45cf-a9ab-d0fae2a67c11.png)



![image](https://user-images.githubusercontent.com/50165842/142883566-34deb5f7-5b5a-4f14-9915-5e19bce47eeb.png)



ML 알고리즘을 사용하면 'SPAM 메일' 를 분류하는 함수를 학습합니다. Evaluate 과정을 통해 새로운 유형의 spam이 들어오면 다시 학습을 합니다. 
![image](https://user-images.githubusercontent.com/50165842/142883620-48ef4aba-1181-47d2-88b5-340462554aef.png)

이 ML이 ***<u>엄청나게 많은 양의 data</u>*** 의 데이터를 학습하게 된다면 예상치 못했던 혹은 새로운 유형의 스팸패턴들도 스팸으로 분류할 수 있게 됩니다. 이러한 패턴들을 파헤쳐서 사람들의 문제에 대한 통찰력을 더 길러주게 됩니다.이러한 과정을 ***<u>data mining</u>*** 이라고 합니다.  



## ML의 예시들

책에 여러가지 예시들이 나오는데 다 적진 않고 재미있어 보이거나 관심있는거만 적겠습니다.

> Analyzing images of products on a production line to automatically classify them This is image classification, typically performed using convolutional neural net‐ works (CNNs; see Chapter 14).
>
> Detecting tumors in brain scans This is semantic segmentation, where each pixel in the image is classified (as we want to determine the exact location and shape of tumors), typically using CNNs as well
>
> Automatically flagging offensive comments on discussion forums This is also text classification, using the same NLP tools.
>
> Summarizing long documents automatically This is a branch of NLP called text summarization, again using the same tools.
>
> Making your app react to voice commands This is speech recognition, which requires processing audio samples: since they are long and complex sequences, they are typically processed using RNNs, CNNs, or Transformers (see Chapters 15 and 16).
>
> Representing a complex, high-dimensional dataset in a clear and insightful diagram This is data visualization, often involving dimensionality reduction techniques (see Chapter 8).
>
> Building an intelligent bot for a game This is often tackled using Reinforcement Learning (RL; see Chapter 18), which is a branch of Machine Learning that trains agents (such as bots) to pick the actions that will maximize their rewards over time (e.g., a bot may get a reward every time the player loses some life points), within a given environment (such as the game). The famous AlphaGo program that beat the world champion at the game of Go was built using RL.

다 할려면 시간을 허투루 쓰면 안될거 같습니다.

## ML 의 종류

일반적으로 ML의 종류는 아래와 같은 기준을 따라 된다고 합니다.

> 1. Human Supervised vs Unsupervised 
>
>    이것을 저는 사람이 desired 한 solution을 제공해주는가 아닌가의 차이라 생각합니다.
>
> 2. online versus batch
>
>    여기서 online은 mini batch를 의미하는데, 왜 online을 mini-batch라 하는지는 모르겠습니다.그 이유를 아시는 분이 계신다면 알려주세요.ㅠ
>
> 3. instance-based vs model-based
>
>    기존에 갖고 있는 data들과 유사도를 측정해서 predict하거나 아니면 주어진 데이터에 대해 학습을 하는 모델을 만드냐의 차이를 의미합니다



### Supervised Learning

![image](https://user-images.githubusercontent.com/50165842/143243537-2be48766-3f3a-445f-8e1a-ccc6a5300b83.png)

Supervised Learning은 ***<u>data가 주어지고 , target(label)이라는 desired solution이 주어졌을 때</u>*** , loss function의 차이를 최소화 하는 모델입니다.

대표적인 예시로는 , ***<u>classification , regression</u>***이 있습니다. 

그 외의 예시로는 , 

> k-Nearest 
>
> Linear Regression
>
> Logistic Regression
>
> SVM
>
> Decision Trees and Random Forests
>
> Neural Networks

물론 Neural Netowork는 ***<u>unsupervised learning</u>***의 모델이 될 수 있습니다.



### Unsupervised Learning

이는 데이터를 그룹으로 나누는 모델입니다. Supervised Learning과 차이점은 ***<u>solution(label) 이 주어지지 않는 unlabeld data로 학습</u>***을 한다는 점입니다. \

대표적인 예시입니다.



> Clustering
>
> - ​	K-means
> - ​	DBSCAN
> - ​	HCA
>
> Anomaly detection and novelty detection
>
> - ​	One-class SVM
> - ​	Isolation Forest
>
> Visaulization and dimensionality  reduction
>
> - ​	PCA
> - ​	Kernel PCA
> - ​	Locally LInear Embedding
> - ​	t-Distributed Stochastic Neighbor Embedding(t-SNE)
>
> Association rule learning
>
> - ​	Apriori
> - ​	Eclat

필자는 여기서 Visualizatoin and dimensionality reduction 에 흥미가 있습니다.



![image](https://user-images.githubusercontent.com/50165842/143244495-e8c01f91-8d6e-4222-a3e8-be2642251e4c.png)

필자는 시각화도 unsupervsied learning의 범주에 들어간다는 사실을 처음 알았습니다.



### Semi-supervised Learning

전체 데이터에서 몇몇의 데이터 만이 라벨링이 되어있는 상황이 있을 수 있습니다. 하지만, ***<u>모든 데이터를 라벨링을 하는것은 돈이 상당히 많이 듭니다</u>***.  따라서, 몇개의 라벨링 된 데이터와의 유사도를 이용해서 라벨링을 하는것을 semi-supervised learning이라고 합니다.

![image](https://user-images.githubusercontent.com/50165842/143244993-5af74db9-6aa0-4875-87fd-7b438dd217d6.png)

실생활에서 볼 수 있는 되게 유명한 예시는 Google Photo가 있습니다.Google Photo 는 



### Reinforcement Learning

강화학습은 좀 다른 종류 입니다. 이 강화학습에는 ***<u>context(environment) 가 있고 , 이 context안에서 agent는 action</u>***을 합니다. 이 때 , reward(이 때 , ***<u>reward는 negative가 되어서 penalty로써 작용될 수</u>*** 있습니다.)를 받게되는데 , 이 ***<u>reward가 최대</u>***가 되게끔 행동하도록 하는 학습법이 Reinforcement Learning 입니다. 

![image](https://user-images.githubusercontent.com/50165842/143246513-01236c0d-e05c-4483-a8db-d99dbd1d2dab.png)

유명한 예시로는 , DeepMind의 Alphago가 있습니다.



## Batch and Online Learning

이번의 관점은 ***<u>점진적</u>***으로 학습을 시킬것인가 아닌가를 결정해서 학습방법을 정하는 것입니다.

### Batch Learning

Batch Learning은 데이터를 ***<u>한번에 학습</u>***을 시키는 것입니다.  이렇게 하게 되면 ***<u>학습속도가 매우 느리다</u>***는 단점을 가지고 있습니다. 따라서, 빠른 변화가 필요할 떄는 적합하지 않습니다. 이것은 off-line learning이라고 부르기도 합니다

### Online Learning



![image](https://user-images.githubusercontent.com/50165842/143248334-62ece434-474b-4668-a347-1d7e32be1f31.png)

Online Learning은 Batch Learning과는 다르게 ***<u>점진적</u>***으로 학습을 시켜나갑니다. Online Learning은 지속적으로 들어오는 data flow에 적합합니다.여기서, ***<u>변화에 얼마나 빨리 적응하는가</u>***를 정하는 중요한 파라미터중 하나는 ***<u>learning rate</u>***입니다.  단점으로는 ***<u>나쁜 데이터에 영향</u>***을 받기 쉽게 됩니다. mini-batch가 나쁜 데이터라면 영향을 쉽게 받게 될 것입니다.out-of-core learning이라고 부르기도 합니다.



## Model based and instance based

위 둘을 정하는 기준은 ***<u>어떻게 일반화</u>***를 하는가의 차이입니다. 

### instance based

![image](https://user-images.githubusercontent.com/50165842/143250856-4944db72-ec37-47d8-8a6f-02ff0d416f46.png)

주어진 데이터를 기준으로 ***<u>유사도</u>***를 측정해서 모델을 일반화시켜나갑니다.

### model based

![image](https://user-images.githubusercontent.com/50165842/143250917-61705b52-61d7-451c-a003-a80f0cebf2b4.png)

주어진 데이터에 ***<u>모델을 fit</u>***해가면서 일반화를 시켜 나갑니다.

## ML challanges

### Unsufficent quantity of training data

![image](https://user-images.githubusercontent.com/50165842/143254204-19bddb4c-9442-4f8b-a01a-776c39da49ee.png)

데이터가 양의 차이가 난다면 성능의 차이가 난다고 경험적으로 밝혀졌습니다. 위 사진은 복잡한 NLP문제 애대해 ***<u>데이터의 수와 알고리즘의 관계</u>***를 보여줍니다. 이는 2001년에 논문에서, Microsoft researchers Michele Banko and Eric Brill 가 발표했던 내용 입니다.여기서, 저자는 

> '방대한 데이터를 모으는 것과 알고리즘을 개발하는 것에서의 tradeoff를 잘 고려해야 한다 ' 

라고 말합니다. 

### Nonrepsentavie Trianing Data

만약, 훈련 데이터가 ***<u>앞으로 들어올 instance들을 표현하지 못한다면</u>*** ,  이 모델은 일반화가 잘 되지 못한것입니다.

이에 대해 , Sampling Bias라는 유명한 예제가 있습니다.

1936년 미국 대통령선거 에서 Literay Digest가 설문조사를 실시하였습니다. 설문조사 결과는 Landon이 57퍼센트의 득표율을 차지할것이라 예측했습니다. 하지만 ,실제로는 RooseVelt가 62퍼센트의 득표율을 차지했습니다.

이렇게 된 원인은 다음과 같습니다.

> Literay Digest는 전화번호북, 잡지구독자, 클럽 멤버쉽 등으로 부터 주소를 수집하였습니다. 이는 , 주로 부유한 계층의 사람들의 번호가 모였으며 ,이들은 Repulblican(?)(Landon을 선호함) 입니다.
>
> 주소지의 사람들은 25퍼센트의 비율로 응답을 하였는데, 이는 정치에 관심이 없고 ,Literay Digest를 싫어하고 ,다른 중요한 그룹들을 베제 했습니다.

### Poor Quality of data

데이터에 노이즈가 있고 , 에러가 있다면 이 데이터들로는 모델을 일반화  하기가 어려울 것입니다.

### Irreleveant Features

feature engieering이라는 과정을 통해서 상관없는 feature들을 프로세싱 합니다.

> Feature Selection - 유용한 feature들을 선택합니다.
>
> Feature Extraction  - feature들을 결합해서 유용한 feature들을 만들어 냅니다.



### Overfitting

train에 과도하게 generalized 된것을 overfitting이라고 합니다. 이러한 것을 방지하기 위해서 여러 방법이 있습니다.

> 덜 complex한 모델을 사용한다.
>
> training data를 더 많이 모은다.
>
> training data의 noise를 줄인다.
>
> regualrization 기법을 사용한다.-예를들어, 자유로운 파라미터가 2개 있을때 하나만 자유롭게 변하도록 한다.

### Underfitting

train data에 잘 generalized 되지 않은 것을 underfitting이라 합니다. 

> 좀더 complex한 모델을 사용한다.
>
> Good Feature를 feed해줍니다
>
> Regularization 파라미터를 줄입니다.

## Test and Validation



### Hyperparameter search and model selection 

모델이 잘 일반화되엇는지 확인하는 것은 중요합니다. 따라서 , data를 ***<u>train,test로 나누는 방법</u>***은 좋은 방법입니다.하지만, 매번 test에 대해 측정을 하고 test에 잘 맞게끔 Hyperparameter를 tuning하기 때문에 ***<u>어떤 특정한 data에 대해 일반화</u>*** 됩니다 . 이때의 해결방법으로는 ***<u>validation set</u>***을 따로 만들어 두는 것입니다.  많은 hyperparameter에 대해서 train을 하고 v***<u>alidation에서 가장 performance가 잘 나오는 hyperparameter</u>***을 선정합니다. 이를 다시 ***<u>valdiation ,train을 합친 full dataset으로 다시 학습</u>***을 시킵니다. validation의 크기가 작다면 ***<u>cross-validation</u>***이라는 여러개의 validation set을 이용하는 방법이 해결책이 될 수 있습니다.





### Data mismatch

Training Data를 쉽게 얻었지만, 실제로 production 단계에서 보게되는 ***<u>data의 representative가 아닌 경우</u>***가 있습니다.예를들어 , 스마트폰의 Flower detection 모델을 만든다고 했을 때 , training data는 web crawling으로 모으고 , validation data는 스마트폰에서 찍은 사진을 이용한다고 가정을 하겠습니다. 가장 중요한 것은 , validataion ,test dataset이 실제 스마트폰으로 찍은 representative한 dataset이여 한다는 것입니다. (여기서, 이 2가지 dataset에 duplicate가 없어야 합니다.)

이제 ,여기서 만약에 overfitting이 발생한다면 생각할 수 있는 이유는 2가지입니다.

> 1. Training data에 Overfitting 이 되었다.
> 2. Training data가 Validation data를 잘 표현하지 못하는(no representative) 데이터이다.

한가지 해결책은 train-data를 train,train-dev로 나누는 것입니다. 모델은 train에만 훈련을 시키고 train-dev는 evaulate를 하는데 확인을 하는 것입니다. 

> 1. 만약 training 에 overfitting이 되었는데 , train-dev에서 performance 가 잘 나오질 않았다면 , 이는 training-data에 overfitting 된것입니다.
> 2. 만약, training에 overfitting이 되었는데, train-dev에서 performance가 잘 나왔다면 ,이는 train이랑 dev,test set의 분포가 달라서 발생할 문제 일 수 있습니다. 

1.의 경우 위에서 보았듯이, 덜 complex한 모델을 사용하거나 , data의 noise를 제거하거나 ,regularization 기법을 사용하는 방법이 있습니다. 

2.의경우에는 training이 test,dev를 잘 representative 하도록 data를 preprocessing 하고, model을 재훈련시켜야합니다. 

### 