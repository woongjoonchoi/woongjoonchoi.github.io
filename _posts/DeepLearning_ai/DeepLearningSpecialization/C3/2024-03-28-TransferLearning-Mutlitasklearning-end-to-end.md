---
title : "Transfer Learning,Multitask learning, end-to-end Learning"

excerpt: "ML Project structing "

categories:
  - DLS_C3
tags:
  - [Machine Learning,Coursera,deep learning ,DLSC3,ML Strategy]
# classes : wide
toc: true
toc_sticky: true
---
NeuralNetwork를 학습할 때 , 원하는 만큼의 data가 주어진다면 NeuralNetwork를 학습하는데 전략을 세울 필요가 없습니다. 하지만, realworld에서는 data가 충분히 주어지는 경우가 드뭅니다. 따라서, 상황에 따른 학습 전략들을 알고 있다면 큰 도움이 될 것입니다. 

## Transfer Learning
![image](https://1drv.ms/i/c/7e81bbcd99889380/IQPzJrVtcL4jT5_Hh47sGhFfAScwCAmYd0tmRZC4tKauyos?width=588&height=278)  

Transfer Learning은 어떤 Task A를 수행하는 NeuralNetwork를 학습하게 된다면 학습한 NeuralNetwork를 이용하여 Task B를 수행하는 NeuralNetwork를 훈련하는 방법입니다. 이 때 , Task A를 학습하는 것을 Pre-training , Task B를 학습하는 것을 Fine-tuning이라고 합니다.  

Transfer Learning의 예시에는 다음과 같은 것들이 있습니다. 

* image recognition -> radiology diagnosis
* speech recognition -> wakeword/keyword detection  
  
![image](https://1drv.ms/i/c/7e81bbcd99889380/IQPEmziav-dfRJRUx_7zY2ZdAan91LOB6qjkohYtJmVak9U?width=416&height=195)

Transfer Learning의 경우 Task A의 data가 Task B의 data보다 개수가 더 많을 경우 적용할 수 있습니다.  이는, Task A의 data가 Task B의 data보다 상대적으로 더 가치가 있다고 볼 수 있습니다. 
### when to apply transfer learning
Transfer Learning을 적용하기 위해서는 다음과 같은 조건이 붙는다고 할 수 있습니다. 
* Task A,B의 input이 같다(같은 format). 
* Task A의 data 수가 Task B의 data 수 보다 많다.
* Task A에서 학습한 low-level feature들이 Task B를 학습하는데 도움이 된다. 


## Multitask Learning
MultiTask Learning은 여러 task를 **동시에** 훈련하여 서로 다른 task의 공통점 및 차이점을 이용하여 performance를 올리는 방법입니다.  
MulitTask Learning의 예시로는 다음과 같은 것들이 있습니다.  

* multi-label classification 
  * 보통 classification은 label을 1개를 사용하는데 , multi-label 은 1개의 이미지에 label이 여러개 mapping된 task를 의미합니다. 따라서, Loss function을 계산시 각 label에 대한 loss를 합산해서 parameter update를 하게됩니다.
* object detection
  * Object Detection은 가장 유명한 multi-task learning입니다. Object Detection은 output으로 object exist, bounding box detection , label classification을 동시에 출력합니다. 마찬가지로 ,Loss 계산시 이를 더하여 parameter를 update하게 됩니다.

Multi-task learning의 특징중 하나는 label에 일부 오류가 발생하더라도 학습이 가능하다는 점입니다. ***label된 y의 loss만을 계산하여 update를 하게 됩니다.*** 예를 들어 , object detection의 경우 exist 값이 0일 경우, 나머지 label들은 labeling 되지 않습니다. 하지만, 학습할 때 , bounding box, label classifiaction을 제외한 , 즉 exist probability만을 가지고 loss를 계산하여 parameter를 update 합니다.


### when to apply multitask learning
Multi-task learning은 다음과 같은 상황에서 고려할 수 있습니다.  

* 여러 task들이 low-level feature를 공유하는것이 훈련할 때 도움이 된다.
* **일반적으로** , 각 task의 data의 크기가 비슷하다. 
  * Multi-task Learning은 100개의 task가 각각 1000개의 data를 가지고 있다면 ,  99000개의 data를 사용한 task로 pretraining 후 1개의 task가 fine-tuning하는 것과 마찬가지입니다. 만약, 1개의 task의 data가 상당히 많다면, pre-training task에 비해 fine-tuning하는 task의 data수가 더 많아서 transfer learning이 잘 이루어지지 않을 것입니다.(transfer learning은 multitask learning의 상위개념)
* 충분히 큰 Neural Network를 훈련한다.
  * 만약 Neural Network가 충분히 크지 않다면 , 각 task를 개별적으로 하는 Neural Network가 Peformance가 더 뛰어날 것입니다. 


## End-to-End 
End-to-End는 data로부터 바로 output을 도출하는 Neural Network를 의미합니다. Computer Science분야에서 어떠한 문제를 해결할 때에는 그 문제들을 작은 단위로 쪼개서 작은 문제들을 해결하고 이를 결합하여 원래의 문제를 해결합니다. Neural Network도 문제를 작은 단위로 나누어서 각 문제를 해결하는 Neural Network나 Domain Knowledge를 사용하여 원래의 문제를 해결할 수 있습니다.  
예시로는 다음과 같습니다. 
* Speech Recognition
  * End-to-End : audio -> transcript
  * middle : audio -> phonemes -> ...-> transcript
  * subproblem : audio -> phonemes -> words ->...-> transcript
* Face Recognition
  * End-to-End : Image -> Face Identity
  * subproblem : Image -> Face Detection -> Face Identity
* Machine translation
  * End-to-End : English -> French
  * subproblem :English -> Text Analysis -> ... -> French

### Pros and Cons  
End-to-End 를 적용하게 되면 다음과 같은 장,단점이 존재합니다.  
* Pros
  * Data 만으로 문제해결 : Data를 input으로 주면 원하는 output을 얻을 수 있습니다.
  * Subproblem을 덜 해결 : 문제를 작은 문제들로 분해해서 푸는 과정을 상대적으로 덜 하기 때문에 비교적 사람의 노력이 덜 들어간다고 할 수 있습니다.
* Cons
  * Data가 많이 필요 : Machine Learnig은 문제가 복잡할 수록 더 많은 Data가 필요하게 됩니다. SubProblem으로 기존의 문제를 나누게 되면, 각 subproblem은 data가 상대적으로 덜 필요하기 때문에, Subproblem을 나눌수록 Data가 덜 필요하게 될 가능성이 높습니다. Subproblem으로 나누지 않고 Neural Network를 만들 경우 Data가 더 많이 필요하게 될 것입니다.
  * 유용한 Subproblem을 Skip할 가능성이 올라간다.: 이는 항상 옳다고 할 수는 없지만 , 일반적으로 옳을 것입니다.예를 들어, Speech Recognition의 경우 phoneme을 detect하는 subproblem을 해결후  , 이를 transcript로 변환하는 subproblem을 해결해서 기존의 문제를 해결합니다. 하지만, 이는 현재관점에서 가장 좋은 방법이지, 추후 더 좋은 방법이 발견될 수 있기에 항상 옳다고 할 수 없습니다.

### Key point in end-to-end

End-to-End를 적용할 때에는 X-> Y로 mapping하는 function의 complexity를 학습하기 위한 충분한 data가 존재하는지를 항상 고민하는 것이 Keypoint라 할 수 있습니다.  
예를 들어 , 자율주행의 경우 image,radar,lidar 등의 센서만으로 자동차를 조종하기에는 수많은 data가 필요합니다.  
따라서, 이를 input -> Object Detection -> 경로 설정-> 조종 의 단계로 나누어서 요구되어지는 data의 수를 줄입니다.  object detection에서는 deepLearning 알고리즘이 사용되어지고 , 경로 설정에는 motion planning, 조종에서는 control 역학이 사용되어질 것입니다. 