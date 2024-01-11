---
title : "Train,Dev,Test의 error를 어떻게 해석해야 할까요?"

excerpt: "ML Project structing "

categories:
  - DLS_C3 
tags:
  - [Machine Learning,Coursera,deep learning ]
# classes : wide
toc: true
toc_sticky: true
---
프로젝트에서 Machine Learning Model을 사용하기로 했습니다. 그러면, Model을 개발할 때, 어떤 Model을 고르고, 데이터를 증강하는지, 어떤 알고리즘을 쓰는지 등등이 중요해집니다. 이는 모델이 Over fit인지, Variance가 높은지 판단해야 좋은 선택을 할 수 있습니다..  

# Error-rate를 확인하면 어떻게 해야할까?
Error-rate를 train,dev, test에 대해서 logging 할 것입니다. 이 log 값들을 가지고 어떻게 해야 할지 결정하기 위해서는 몇 가지 용어들을 정의해야 합니다.  

## Term
여기서는 human-level performance와 avoidable bias라는 용어의 정의와 이에 대해 간략한 설명을 하겠습니다.

### Human-level performance
![image](https://onedrive.live.com/embed?resid=7E81BBCD99889380%217819&authkey=%21AFm_FOTf9RHr9A4&width=611&height=351)

Machine Learning의 성능은 시간이 지나면서 사람의 능력을 뛰어넘게 되었습니다. 하지만, 이론상 최고 성능에는 도달하지 못하고 계속 근접하게 됩니다. 이론상 최고치를 Bayes optimal error라고 합니다. 여기서, 사람의 능력을 human level performance라고 합니다. Bayes error는 이론상의 최대치이고 계산할 수 있지만 조건에 따라 계산이 매우 복잡하여 수치적으로 근사하는 것이 최선일 수 있습니다. 즉, ***언제나 정확한 Bayes error 값을 알 수 없습니다.*** Bayes error를 human level performance가 넘을 수는 없지만, human level performance는 Bayes error에 상당히 근접해 있습니다. 따라서, human level performance를 Bayes error의 approximation이라 여기고 Bayes error 대신 사용하게 됩니다. 

머신러닝 에서는 human level performance에 도달하기 전까지는 모델의 performance를 빠르게 올릴 수 있습니다. 왜냐하면, bias, variance를 설정할 수 있으므로 목표를 어떻게 잡아야 할지 명확히 알 수 있습니다. 하지만, human level performance를 넘게 된다면 performance를 끌어올리기 어려워집니다. Bayes error를 정확히 알 수 없기에 목표 지점을 정확히 잡을 수 없습니다. 특히, Online Ads, Recommendation, Loan Approval 같은 **Structured Data** 을 활용하는 Model은 이미 사람을 뛰어넘었다고 알려져 있습니다. 이러면, bias, variance를 활용한 방법이 아닌 다른 방법들을 마련해야 합니다. 하지만, 시각, 청각, 사고 같은 **자연적 인지** 에서는 아직 인간을 딥러닝이 따라잡지 못하고 있습니다. 따라서, human-level performance를 아는 것이 중요하다고 할 수 있습니다.  

### Avoidable bias
avoidable bias는 **Bayes error approximation 과 training error 간의 차이**입니다. 여기서, approximation은 human level performance가 됩니다. 이 avoidable bias를 bias로 설정하고,  variance는 human level performance와 dev error와의 차이로 설정해서
모델의 performance를 높이기 위한 전략을 세우게 됩니다. 이때, avoidable bias는 0 이상이어야 하므로, training error의 upper bound가 human level performance임을 내포하고 있습니다.  

## bias reduction or variance reduction ?
|error type|Approach|
|-------------|-----------------------------------------|
|High Avoidable Bias| - Traing Bigger Model </br> - Train Longer </br> - Better Optimization Algorithm </br> - Architecture,hyperparameter search|
|High Variance| - More Data </br> - regularization </br> - Architecture,hyperparameter search|  

Avoidable bias가 높다는 것은 모델이 Training data를 잘 학습하지 못한다는 뜻이므로, parameter가 더 많은 모델을 사용하거나 훈련을 더 길게 하거나 optimization 알고리즘이 다른 걸 사용할 수 있습니다.
Variance가 높다는 것은 모델이 과적합 되었다는 뜻이므로, 더 많은 데이터를 수집하거나 Regularization 기법을 사용할 수 있습니다.  

# Error를 분석하자
bias문제인지 variance 문제인지  error-rate 를 통해 판단이 되었습니다. 그렇다면, 구체적인 error의 내용을 분석해서 구체적인 전략을 세워야 합니다. 

## error-label check
dev, test, train의 error를 사람이 확인해서 구체적인 insight를 얻을 수 있습니다. 예를 들어, 동물을 분류하는 multi classifier 문제의 경우에 아래와 같은 식으로 어떻게 잘못 분류했는지를 검사할 수 있습니다.  

|Image|Dog|Cat|....|Comments|
|-------------|-------------|-------------|-------------|-------------|
|1| |$$\checkmark$$ | |Pitball|
|2|$$\checkmark$$ | | |Lion|
|...| | | | |  

체크를 하다 보면, labeling이 잘못되어 있는 경우가 있을 수 있습니다. 이 경우에는 label miss라는 마크를 따로 달아두는 것이 좋다고 합니다.  
|Image|Dog|Cat|....|miss label|Comments|
|-------------|-------------|-------------|-------------|-------------|-------------|
|1| |$$\checkmark$$ | ||Pitball|
|2|$$\checkmark$$ | | ||Lion|
|3| | | | $$\checkmark$$|miss labeling|
|...| | | | ||    

이러한 analysis를 통해 insight를 얻게 되면, 바로 성능향상 작업에 들어가서는 안 됩니다. 특정 error나 mislabeling이 나왔다고 해서 전부 고치는 것이 항상 정답은 아닙니다. 예를 들어, dog를 잘못 분류하는 예제가 10개가 나왔고, dog를 잘 분류하도록 알고리즘을 설계하는 작업에 착수했습니다. 하지만,  이 예제가 전체 오답률의 1퍼센트라면 이는 미미한 성능향상을 끌어낼 것입니다. 또, labeling을 잘못되어 있어서 이를 수정하는 데 자원을 소모했습니다. 그렇지만, 이 비율이 2퍼센트 정도라면 큰 성능향상을 끌어내지 않을 것입니다.
만약에, 특정 예제나 miss labeling이 비율이 오답의 30%~40%라면 유의미한 성능향상을 보일 것입니다.
error label을 검사할시 몇 가지 tip이 있습니다.  

error label을 사람이 체크하고 수정할 시, dev set에 했던 process를 test set에서도 해야 합니다. machine learning model은 dev, test가 같은 distribution에서의 data라 예상하고 model의 성능을 평가합니다. 따라서, dev에서도 일반화가 된다면 test에서도 일반화가 되어야 합니다. dev와 test가 같은 distribution을 가져야 하므로, dev에서 label을 고쳤다면, test에서도 label을 고쳐줍니다.  

error를 확인할 때 맞춘 것도 보는 것이 도움이 됩니다. Model이 운이 좋아서 정답을 맞힌 가능성이 있기 때문입니다. 만약에, 틀린 예제만을 점검할 시, 모델의 error 추정치가 과대평가 될 수 있습니다. 하지만, 이는  Model의 성능이 좋으면 좋을수록 하기 어렵습니다. 예를 들어, 모델의 정확도가 98퍼센트이고 error 비율이 2퍼센트라면면 98퍼센트의 데이터를 보는 것은 많은 자원이 소모되기 때문입니다. 

실제로는, training data의 error를 확인하는 것이 정석이지만, dev ,test에 비해 data의 size가 상당히 크기 때문에 자원이 상당히 많이 소모됩니다. 딥러닝 알고리즘은 random 한 error에 robust 하므로 training data의 random 한 error에 대해서는 영향을 거의 받지 않을 것입니다. 

## Build System First
실제로 ML project를 임할 때에는 error analysis에 집중하기 보다는 bias, variance를 통해 model의 performance를 향상하게 시키는 system을 구축하는 것이 중요하다고 합니다. error analysis나 distribution 차이는 그다음에 해결해도 괜찮다고 합니다. 우선은, model performance를 향상하게 시키는 system을 구현하는 것이 훨씬 중요하다고 합니다.  

## Mismatched data distribution
위의 Error Analysis 과정을 거치면서, 특정 label이나 mislabel 문제가 아님이 밝혀질 수 있습니다. Train 과 dev, test의 distribution 차이에서 오는 variance의 문제일 수도 있습니다. 

### Distribution Eqaulity
예를 들어, 모바일 앱에서 구동하는 ML application을 개발한다고 하겠습니다. Train Data 약 20만 개를 web에서 수집하고, dev, test에는 mobile app에서 수집한 data 1만 개를 각각 5천개씩 나누어서 사용했습니다. 이렇게 되면, train과 dev, test 간의 distribution의 차이로 문제가 발생할 수 있습니다. web에서의 image data는 화질이 좋지만,, mobile app의 image data는 화질이 좋지 않을 가능성이 있습니다.  

이때 , train, dev,test data를 단순하게 shuffle 해서 같은 distribution으로 만들어 주는 방법을 사용할 수 있습니다. 하지만, 이 새로운 data는 mobile data 반영률이 약 4.7퍼센트 정도밖에 되질 않을 것입니다. ML model이 학습하고자 하는 distribution은 mobile의 distribution인데, model은 전혀 다른 distribution을 target으로 삼고 있는 것입니다.
대신에, dev, test를 각각 2500개 정도로 설정하고 5천 개를 train에 포함하므로 train의 distribution을 dev, test에 더 가깝게 만드는 방법을 사용할 수 있습니다.  하지만, 여전히 train과 dev, test의 distribution은 다릅니다. 

어떤 방법을 적용할지는 팀의 상황과 project의 제한조건 등에 따라서 다를 것이므로 , 정답이 없다고 여겨집니다. 하지만, Andrew 교수님은 후자의 방법이 장기적으로 더 좋은 효과를 보일 것이라 얘기하십니다.   

### Data Augmentation and Collect Data
더 많은 data를 확보하는 것도 좋은 방법이 될 수 있습니다. 이때, data augmentation과 collect data를 고려하게 됩니다.
collect data는 말 그대로 data를 추가로 수집을 하는 것입니다. train에 mobile에서의 data가 부족하다면, mobile data를 추가로 수집해서 train data에 포함할 수 있습니다.
Data Augmentation은 기존에 가지고 있는 data로 새로운 data를 합성해 내는 것입니다. 예를 들어, 사람의 목소리와 차의 소음 소리를 결합해 차 속에서 말하는 사람의 목소리를 만들 수 있습니다. 아니면, 기존의 자동차 사진으로 새로운 자동차 사진을 생성해 낼 수 있습니다. 하지만, data augmentation 시 주의할 점이 있습니다. **적은 data로 augmentation 시 train은 여전히 sample space의 작은 subset일 가능성이 있다**는 것입니다. 차의 소음 소리 data가 만 시간 정도가 있고, 사람의 목소리 data가 100만 시간이 있다고 가정하겠습니다. 여기서, 사용할 수 있는 방법은 차의 소음 소리를 100번 반복해서 사람의 목소리와 합성하는 것입니다. 사람의 귀에는 다 다른 소음 data로 들리지만, computer 입장에서는 전부 같은 소음 data로 보일 수 있습니다. 또한, 20개의 차 이미지로 새로운 차 이미지를 만들어 냈다고 했을 때, 사람의 눈에는 달라 보이지만 computer 입장에서는 같은 차 이미지일 수 있습니다.  small subset만을 학습하게 된다면, model을 sample space의 일부에 overfit이 될 것입니다.  
### Train-dev 
Train data와 dev, test의 data가 서로 다른 distribution인지 아닌지 모를 수도 있습니다. 이럴 때는, train-dev라는 새로운 data set을 만듦으로써 확인할 수 있습니다. train data에서 무작위로 train-dev를 sampling 하므로 train-dev와 train은 같은 distribution을 갖게 됩니다. 따라서, 이 model이 unseen data에 대해서 예측을 잘하는지를 판단할 수 있게 됩니다. 

# Decision Making in General Foramt
|Error Type|Comapre|
|-------------|-----------|
|high avoidable bias|human-level vs train error|
|high variance|train error vs train-dev error|
|data mismatch|train-dev error vs dev/test error|  

위에서 작성된 decision 전략에서 train-dev error와의 차이가 추가되었습니다.  알고리즘이 일반화가 잘 되는지를 dev, test가 아닌 train-dev와 비교하는 것이 좀 더 일반화된 형태의 decision making이라 볼 수 있겠습니다. 


# Conclusion 

ML project team에서 ML의 새로운 model의 performance를 올릴 때는 아래와 같은 순서를 따른다고 볼 수 있습니다.

1. bias, variance 값에 따라 approach를 적용할 수 있는 system을 만든다.
2. system build 후 error analysis를 통해 distribution mismatch인지 아닌지를 점검한다.
3. distribution이 mismatch가 아니라면 error case 중 비율이 높은 것을 우선시한다.



# Reference 

[DeepLearning Specialization](https://www.coursera.org/learn/machine-learning-projects/home/week/1)
