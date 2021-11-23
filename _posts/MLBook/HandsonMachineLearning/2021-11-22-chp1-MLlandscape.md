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

이 포스트는 Orelly 의 Hands on MachineLearning 책 chp1 을 공부하고 필자가 정리하는 글입니다. 아카이브 용으로 적는거라 표현이나 설명이 부족할 수 있습니다. 부족한 부분은 피드백 부탁드리겠습니다.

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