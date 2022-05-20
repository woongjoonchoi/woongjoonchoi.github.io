# Setting your Goal

머신러닝 모델을 훈련을 시킬때 , 한가지 모델만을 훈련시키고 끝나는 것이 아니라 여러가지의 모델을 훈련시킨후 이 중에서 가장 잘 작동할 법한 모델을 선택하게 됩니다. 이 때 , 어떠한 기준치로 모델을 선택하는데 , 이 기준치를 Metric 이라 합니다. 이 Metric은 정렬해서 가장 좋은 모델을 고르는건 어렵지 않지만, Metric을 어떻게 설정하는지 모른다면 좋은 모델을 고를 수 없습니다.

## Metric is case by case

Metric을 설정하는 것은 case by case 라는 것을 기억해야 합니다. Metric은 Machine Learning Project Team이 어떤 Goal을 지향하는지에 따라서 달라집니다. 어떤 Team은 low latency가 가장 중요할 수 도 있고 , 여러개의 지표를 한번에 고려할 수 도 있습니다. Team에서 회의를 통해 어떤 Metric을 중요시 할 지 결정하게 될 것입니다. 이를 가지고 , 어떻게 Metric을 잘 설정하면 좋을지 이에 대해 간단한 방법을 알아보도록 하겠습니다.



## Using Single Number Metric

여러 Metric을 전부 고려해야하는 경우 , 이를 한가지의 Number로 합칠 수 있는 경우가 있습니다. 예를들어 , Recall, Precision은 Model이 주어진 DataSet에 대하여 얼마나 좋은 performance를 보여주는지에 대한 Metric입니다. 이러한 Metric에 대한 평균값을 사용하여 , 좋은 값을 찾을 수 있습니다. 평균중에서도 산술평균, 기하평균,조화평균 을 사용하거나, Metric을 Parameter로 사용하는 새로운 function을 만들 수 도 있습니다. 



## Satisfying Conditions and Optimizing

위와 같은 경우가 아닌 , latency나 





## Train / Dev / Test 