---
title : "Transfer Learning,Multitask learning, end-to-end "

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
그림1  
Transfer Learning은 어떤 Task A를 수행하는 NeuralNetwork를 학습하게 된다면 학습한 NeuralNetwork를 이용하여 Task B를 수행하는 NeuralNetwork를 훈련하는 방법입니다. 이 때 , Task A를 학습하는 것을 Pre-training , Task B를 학습하는 것을 Fine-tuning이라고 합니다.  

Transfer Learning의 예시에는 다음과 같은 것들이 있습니다. 

* image recognition -> radiology diagnosis
* speech recognition -> wakeword/keyword detection  
  
그림2  

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

### Pros and Cons

### Key point in end-to-end