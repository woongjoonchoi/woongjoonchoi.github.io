# Setting your Goal

머신러닝 모델을 훈련을 시킬때 , 한가지 모델만을 훈련시키고 끝나는 것이 아니라 여러가지의 모델을 훈련시킨후 이 중에서 가장 잘 작동할 법한 모델을 선택하게 됩니다. 이 때 , 어떠한 기준치로 모델을 선택하는데 , 이 기준치를 Metric 이라 합니다. 이 Metric은 정렬해서 가장 좋은 모델을 고르는건 어렵지 않지만, Metric을 어떻게 설정하는지 모른다면 좋은 모델을 고를 수 없습니다.

## Metric is case by case

Metric을 설정하는 것은 case by case 라는 것을 기억해야 합니다. Metric은 Machine Learning Project Team이 어떤 Goal을 지향하는지에 따라서 달라집니다. 어떤 Team은 low latency가 가장 중요할 수 도 있고 , 여러개의 지표를 한번에 고려할 수 도 있습니다. Team에서 회의를 통해 어떤 Metric을 중요시 할 지 결정하게 될 것입니다. 이를 가지고 , 어떻게 Metric을 잘 설정하면 좋을지 이에 대해 간단한 방법을 알아보도록 하겠습니다.



## Using Single Number Metric

여러 Metric을 전부 고려해야하는 경우 , 이를 한가지의 Number로 합칠 수 있는 경우가 있습니다. 예를들어 , Recall, Precision은 Model이 주어진 DataSet에 대하여 얼마나 좋은 performance를 보여주는지에 대한 Metric입니다. 이러한 Metric에 대한 평균값을 사용하여 , 좋은 값을 찾을 수 있습니다. 평균중에서도 산술평균, 기하평균,조화평균 을 사용하거나, Metric을 Parameter로 사용하는 새로운 function을 만들 수 도 있습니다. 



## Satisfying Conditions and Optimizing

위와 같은 경우가 아닌 , latency와 precision을 동시에 고려해야 하는 경우가 있다면 , 이 두 metric을 합치는 것은 어색합니다. 따라서 , 이러한 경우에는 제일 우선시하는 metric를 선정하고 나머지 metric이 만족해야하는 조건을 설정하는 방법이 있습니다. 보통 , 나머지 metric들은 어느 조건을 만족하게 되면 , 그 이상을 잘하더라도 신경쓰지 않는 경우가 많습니다 . N개의 metric이 있다고 가정을 한다면 , N-1개의 metric이 조건을 만족한다면 나머지 1개의 metric으로 best model을 선정할 수 있습니다 .

예를 들면 , 만족해야 하는 metric이 latency , precision , user response time , memory size 라고 하겠습니다. 가장 중요시 되는 metric이 precision이라 설정하면 , latency는 100ms 이하 , user response time은 150ms 이하 memory size는 1gb 이하등으로 설정할 수 있습니다. 



## Train / Dev / Test 

Data를 수집하게 되면, Train , Dev , Test로 나누게 됩니다 . Train data는 model의 weight를 update하는데 사용되어 지는 data이고 , dev data는 훈련된 model이 unseen data에서 얼마나 잘하는지를 평가하는 data이고 , test data는 평가한 model이 실제 real world의 data가 주어질 때 얼마나 잘할지를 측정하는 data입니다 . 

data를 3개의 category로 나누게 될 때 , dev,test의 분포가 다르게 되면 model이 real world에서 예상 performance와는 다른 performance를 보여줄 수 있습니다 . 

예를 들어 , 글로벌 얼굴 인식 모델을 만든다고 가정하겠습니다. dev에는 asia 사람들의 data, africa 사람들의 data , test에는 europe ,america등 기타지역 사람들의 data를 사용하기로 결정했습니다. 만일 이렇게 된다면 ,      asai, africa 사람들에 대해서 잘 작동하는 model이 될 것입니다 .