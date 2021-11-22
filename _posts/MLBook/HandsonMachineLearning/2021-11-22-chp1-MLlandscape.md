---
title : "ML landscape"



excerpt: "Hands on machinelearning chp1"

categories:
  - Handsonmachinelearning
tags:
  - [Machine Learning,Handsonmachinelearning,orelly]

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



