---
title : "AutoML 이론"



excerpt: "Boostcampai model optimziation 3"

categories:
  - ModelOptimization
tags:
  - [Machine Learning,Boostcourse,deep learning ]
# classes : wide
toc: true
toc_sticky: true
---

이 자료는 필자가 만든 자료가 아닌 부스트코스 ai tech 2기 교육자료임을 명시합니다. 이 자료는 상업적으로 이용할 수 없습니다.


## Conventional DL Training Pipeline

![image](https://user-images.githubusercontent.com/50165842/143345477-9045327c-48fb-40cd-882f-7d00bdbe3e09.png)

전통적인 DeepLearning Training Pipeline은 사람이 HyperParameter Tuning,Model selection을 하고 train/evaluation을 하는 과정의 반복이였습니다.

![image](https://user-images.githubusercontent.com/50165842/143347960-9cbdbd3a-fde1-4182-9286-7747452ad408.png)

위 그림은 위 과정을 재밌게 표현한 짧은 컷툰입니다.

## AutoML

![image](https://user-images.githubusercontent.com/50165842/143352436-4b5007b4-0d90-46b6-84c6-3c188b9cf0a8.png)

위를 자동화 해보자 하고 나온게 AutoML 입니다.이 tuning 과정의 중간에서 사람의 일을 없애서  End-to-End로 만들어 보고자 하는 시도입니다.

### define

![image](https://user-images.githubusercontent.com/50165842/143353455-647e6484-9290-4d10-8452-78669576dd95.png)

Train,Dev data  , HyperParameter set $$\lambda$$ 가 있을 때 , 모델의 objective function을 최소로 만드는 $$\lambda$$ 를 찾고자 하는 것입니다.

### configuration

- Categorical
  - Optimizer(SGD,Adam,AdamW,RMSProp .. etc)
  - Learning Rate
- ***<u>Conditional</u>***
  - ADAM은 beta1,beta2라는 parameter가 필요하지만, SGD는 그렇지 않다.
  - Module(Conv,LSTM,RNN,Transformer..etc)에 따라 cofiguration 설정이 조금씩  달라진다.

Conditional configuration에 따라 search space가 조금씩 달라질 수 있습니다.

### automl pipeline

![image](https://user-images.githubusercontent.com/50165842/143356598-37bc9545-4870-4fe0-8adc-b8bfc444bc35.png)

어떤 HyperParameter set $$\lambda$$ 가 있을 때 ,모델을 train합니다.  이 Train한 모델이 성능이 잘 나오는지는 black box 입니다.이에 대한 objective function f를 구합니다.이 때 , 이 objective function은 목적에 따라 달라질 수 있습니다.예를 들어, 속도보단 정확성을 요구할 수 있고, 어느정도의 속도가 나왔으면 좋겠다라는 요구사항이 있을 수 있습니다.  이 objective function을 maximize하는 hyperparameter lambda를 찾는 것 입니다. 

## bayesian optimization

![image](https://user-images.githubusercontent.com/50165842/143364616-243ebc22-cc20-45c7-8ccc-4e982414a0e1.png)



이러한 AutoML의 최적화 방법중 bayesian optimization이라는 것이 있습니다. 이는 ,  surrogate function을 update하고 , 는 acquisition function을 통해 람다를 찾아가는 pipeline 입니다.

여기서  Surrogate function 은$$f(\lambda) $$ 의 regression model입니다.

Acquisition function은 $$f(\lambda) $$ 의 search space를 결정하는 함수 입니다.

### GPR

![image](https://user-images.githubusercontent.com/50165842/143365239-ca3f1954-f144-481d-b4e2-5afdf6badae9.png)

이 중에 Gaussian Process Regression이라는 것이 있습니다.(자세한 내용은 최성준 교수님 edwith 강의를 듣고 정리해서 링크를 update 하겠습니다.)

1. 먼저 $$f(\lambda) $$  를 sampling(observation) 합니다.
2. objective function을 계산합니다.
3. 그리고 , surrogate function을 update 합니다.(보라색 영역이 surrogate function이고 , 아직 관찰하지 못한 영역입니다.)
4. Acquisition function을  update하면서 다음 search space를 정합니다.

Gaussian Process의 장점은 uncertainty를 모델링 할 수 있다는 것입니다.

#### 수식으로 간단하게 설명



![image](https://user-images.githubusercontent.com/50165842/143366316-d7f95bf7-1ab3-401f-be6c-f0718030e64f.png)

Regression task란 어떤  input이 주어졌을 때 output을 최대한 잘 예측하는 것이다 라고 문제정의를 할 수 있습니다.

X,Y를 train 라 하고 $$X_{\displaystyle *}$$  $$Y_{\displaystyle *}$$ 를 test라 하겠습니다.

우리가 알고자 하는 것은 $$Y_{\displaystyle *}$$ 입니다.  Gaussian Proceess Regression을 사용하게된 motiviation은 X,Y ,  $$X_{\displaystyle *}$$가 positive 이든 negative이든 어떠한 상관관계가 있지 않을까 라는 생각에 문제를 GPR을 통해 해결하고자 한 것입니다.   X,Y ,  $$X_{\displaystyle *}$$ 와  $$Y^{\displaystyle *}$$의 연관관계는 커널함수 K로 표현하게 됩니다.

이 때 , 이 f라는 것을 X,Y에 대한 multivariate Gaussian Joint Distribution 라 정의합니다.

따라서 , 이에 대한 확률분포(mean, covraiance)를 구할 수 있습니다.

예를들어 , 알고 있는 point가 50개이고 , $$f_{\displaystyle *}$$ 를 구하고자 할 때 , $$K(X,X)$$ 는 $$ 50\times50 $$ 의 shape이고 , $$K(X,X_*) $$는 $$ 50\times 1$$ 의 형태가 될 것입니다. $$K(X_*,X) $$ 는 $$1\times50$$의 shape이고 ,   $$K(X_*,X_*) $$ 의 $$1\times1$$의 형태가 될 것입니다.

![image](https://user-images.githubusercontent.com/50165842/143366902-ea731902-d56e-4fd9-8d5c-9e11b349c2cc.png)

위의 좋은점은 $$f ,f_*$$의 분포를 정의만 해놓으면 Gaussian Identities를 이용해서 계산을 할 수 있다는 것입니다.(자세히는 잘 모르겠습니다. 최성준 교수님 강의듣고 잘 정리 해보겠습니다.)

#### 시각화

![image](https://user-images.githubusercontent.com/50165842/143367153-db4430ee-b613-4a57-b2ca-1c14b0737bbd.png)

왼쪽 그림은 maginality의 distribution의 gaussian distritubtion이다임을 보여주는 것입니다.

오른쪽 그림은 conditional distribution도 gaussian distribution임을 보여주는 것입니다

증명은 아니고 이해를 돕기위한 자료라고 합니다.

![image](https://user-images.githubusercontent.com/50165842/143367288-ceb40d1b-01bd-4e68-b85f-51915188f2e5.png)

위의 과정들을 수식으로 정리하면 위와 같이 됩니다.

#### surrogate model

objective functino $$f(\lambda)$$가 있을때 , 얘의 objective function을 예측하는 regression 모델입니다.

#### Acquisition model

![image](https://user-images.githubusercontent.com/50165842/143370052-8686909c-d833-42bb-8d11-a251c2017ade.png)

- surrogate model로부터 어디서부터 $$\lambda$$를 탐색할지를 결정 해주는 함수입니다.
- Exploration("안 가본곳") vs Exploitation("이미 가본곳")
  - 안 가본곳 과 이미 가본곳의 적절한 밸런스를 통해서 다음 탐색할 곳을 정합니다.
-  가본 곳 근처의 안가본 영역에 maxmize 값이 있을것이고, 여기를 찾으면 좋겠다 라고 계산을 하는 함수입니다.

### TPR

GPR의 문제점으로는 

- $$O(n**3)$$ 의 complexity를 가진다.
- ***<u>conditional , con,dist configuration이 혼재되어 있는것을 고려할 때 적용하기 힘들다.</u>***

​	

$$ P(f \rvert\lambda) $$ 를 구하는 대신에 $$P(\lambda\lvert f)   ,P(\lambda)$$ 를 구하는 것을 TPR이라고 합니다.

TPE를 통한 계산방법 

- 현재까지의 observation을 qunatile(inverse CDF)로 구분을 합니다. 

  (75% bad, 25% good)

- good obseravation을 p(g) ,bad obserbation을 p(b) 로 표기

- p(g)/p(b) 는 EI(Expected Improvement , acquisition function 중 하나)에 비례함이 밝혀져 있음

- 

#### EI 증명

![image](https://user-images.githubusercontent.com/50165842/143382794-5031c29d-734c-498b-bece-4dd2e0887e2a.png)

이를 직관적으로보면 l(x) 가 높은 곳을 선호하되 , g(x) 가 낮은곳도 찾아보자는 의미라 볼 수 있습니다.

## 한계 및 연구

### 문제점

![image](https://user-images.githubusercontent.com/50165842/143382850-076953bf-fd12-4600-b554-ab9ebae09abe.png)

저의 경우 간단한 모델을 fine-tuning 하는 경우가 대부분이라 30~40분이면 끝나는데 , 엄청나게 큰 모델의 경우 training 하는데  3~4일 정도 걸릴 수 있기에 이를 몇백번 반복하게 되면 시간이 굉장히 오래 걸리게 됩니다.



이를 해결하기 위한 방법들이 여러개 있습니다.

- HyperParameter에 대해서 Gradient Descent를 적용해서 찾아보자
  - 학습을 하면서 동시에 탐색도 하자.
- Meta-Learning(AutoML에 대해 Auto 를 통해 Automl을 잘하는 모델을 찾자.)
- Multi-fidelity optimization
  - Data의 subset 활용
  - 적은 epoch 활용
  - RL을 활용한 적은 trial
  - Image DownSampling



### 절충안

- 사람의 prior을 통해 search space를 좁게잡는다.
- 데이터가 대표성을 잘 띄는 좋은 subset 데이터를 찾는다.
- 학습 과정을 보고 early terminate를 한다.

***<u>오늘 배운 내용을 사람이 계속 반복해서 수행하기보다 좋은 configuration을 찾을 수 있다</u>***는 것이 이 강의에서 말하고 싶은 바였습니다.



다음강의: 절충안 노하우, 모델을 어떻게 만드는지, 하이퍼파라미터는 어떻게 sampling하는지