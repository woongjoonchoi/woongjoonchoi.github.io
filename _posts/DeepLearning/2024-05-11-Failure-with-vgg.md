---
title : "Unefficient tiral with vgg reproducing"



excerpt: "Vgg를 reproducing 하면서 겪었던 여러 issuee들"

# categories:
#   - 
tags:
  - [deep learning]
# classes : wide
toc: true
toc_sticky: true
---
VGG network를 scratch부터 훈련시키면서 겪었던 이슈들과 그에 대한 해석 및 해결과정을 적은 글입니다. 이슈들을 해결하기 위해서 이 시점 이전에 나온 딥러닝 테크닉들만 사용하였고, 논문에서 명시적으로 언급했던 부분은 수정하지 않았습니다. 
## Proof of Correctness with CalTech256

우선 TinyDataset에 잘 수렴하는지 확인하기 위해서 caltech256 dataset을 골라서 model B에 학습을 진행하였습니다. 학습을 여러 번 진행하였지만, model의 loss는 수렴하지만, validation accuracy에 큰 진전을 보이지 못했습니다. weight start point가 문제일거라 생각해서 여러번 훈련을 재시작 하였지만, 큰 효과를 보지 못했습니다. 저는 Caltech256 dataset으로 훈련한 model의 output 에 대하여 error analysis를 해보았고 ,  model이 대부분 output을 comet 아니면 background로 predict함을 확인하였습니다. Caltech256의 dataset에대해  model이 잘 fit하지 못하였기에 , 좀 더 단순한 dataset에 대하여 학습을 진행해보기로 결정하였습니다.   
아래의 loss plot에서 주황색이 validation set에 대한 loss이고, 파란색이 train data에 대한 loss입니다. 

| <img src="https://github.com/woongjoonchoi/woongjoonchoi.github.io/assets/50165842/6a35508f-41cb-4e25-842a-66bf639efe56"  width="300" height="300">|<img src="https://github.com/woongjoonchoi/woongjoonchoi.github.io/assets/50165842/457ef00e-4ddb-4a95-991c-a71c692245a5"  width="300" height="300"> | <img src="https://github.com/woongjoonchoi/woongjoonchoi.github.io/assets/50165842/3540eb88-7a3c-4197-8107-cae157344047"  width="300" height="300">|  |
|:--: |:--: |:--:  | :--: |
| *trial1/loss*  |*trial1/top-1-error* |*trial1/top-5-error*|  |
| <img src="https://github.com/woongjoonchoi/woongjoonchoi.github.io/assets/50165842/359ab6ef-2111-4a0c-aad2-c4bb8cdd4057"  width="300" height="300">|<img src="https://github.com/woongjoonchoi/woongjoonchoi.github.io/assets/50165842/21c3cb6b-83c5-4efe-9cb0-e68b19bf850b"  width="300" height="300"> | <img src="https://github.com/woongjoonchoi/woongjoonchoi.github.io/assets/50165842/0a09b129-11aa-4a3a-9b63-da24349b9d9e"  width="300" height="300">|  |
| *trial2/loss*  |*trial2/top-1-error* |*trial2/top-5-error*|  |
| <img src="https://github.com/woongjoonchoi/woongjoonchoi.github.io/assets/50165842/36a16098-acc6-4563-93b9-a5be9970d494"  width="300" height="300">|<img src="https://github.com/woongjoonchoi/woongjoonchoi.github.io/assets/50165842/8dbf6c04-3c47-4eb3-92c2-36be8466ce57"  width="300" height="300"> | <img src="https://github.com/woongjoonchoi/woongjoonchoi.github.io/assets/50165842/d72706c7-3d1a-47d2-98fd-5797ff084077"  width="300" height="300">|  |
| *trial3/loss*  |*trial3/top-1-error* |*trial3/top-5-error*|  |
| <img src="https://github.com/woongjoonchoi/woongjoonchoi.github.io/assets/50165842/036054dd-8d87-49de-bce9-3e00d5e84ef6"  width="300" height="300">|<img src="https://github.com/woongjoonchoi/woongjoonchoi.github.io/assets/50165842/1da21b09-1cd7-4bfd-9d5a-dc5de327f4ec"  width="300" height="300"> | <img src="https://github.com/woongjoonchoi/woongjoonchoi.github.io/assets/50165842/d1b878fc-5d79-4600-b054-9e32ae164399"  width="300" height="300">|  |
| *trial4/loss*  |*trial4/top-1-error* |*trial4/top-5-error*|  |

## 1 by 1 Convolution to Linear Layer
논문에서는 fully convolution으로 구현을 하였지만 , torchvision의 implementation은 fully connected layer로 구현하였습니다. fully connected layer나 1 by 1 convolution은 수학적으로 차이가 없지만 , avearage pooling layer를 어디서 적용하는지의 차이점이 있습니다. 따라서, 여기서 차이가 발생했다 생각하여 1 by 1 convolution을 fully connected layer로 대체하였지만, training에서 큰 효과를 보지 못하였습니다. average를 순서를 바꾸어도 수학적으로는 차이가 없고 , 실제로 차이가 없었기에 network의 구조를 fully convolution을 유지하기로 결정했습니다.   

| <img src="https://github.com/woongjoonchoi/woongjoonchoi.github.io/assets/50165842/377e7b46-1c0f-43e7-aa6e-b08a8cffac9e"  width="300" height="300">|<img src="https://github.com/woongjoonchoi/woongjoonchoi.github.io/assets/50165842/34fc3ec0-4bc6-4294-9aef-a5ec5ec76c2e"  width="300" height="300"> | <img src="https://github.com/woongjoonchoi/woongjoonchoi.github.io/assets/50165842/279b757c-f86e-424f-97d3-d6a70f9ca6d7"  width="300" height="300">|  |
|:--: |:--: |:--:  | :--: |
| *linear/loss*  |*linear/top-1-error* |*linear/top-5-error*|  |


## Dataset Chang From CalTech256 to Cifar100
Caltech256 dataset은 class당 이미지가 100개 정도였고, 분류해야할 class label이 257개 이고, class label의 분포가 imbalance 했습니다. 따라서, label당 이미지 개수가 500개이고, 분류할 class가 100개인 cifar100 dataset에 대하여 model B를 학습하였습니다.   
하지만, model B에 대해서도 학습이 잘 진행되지 않았습니다.  

| <img src="https://github.com/woongjoonchoi/DeepLearningPaper-Reproducing/assets/50165842/39dabb1e-7e1d-4211-9eeb-511d017f66ee"  width="300" height="300">|<img src="https://github.com/woongjoonchoi/DeepLearningPaper-Reproducing/assets/50165842/1c82260b-1996-4544-a59d-f1c053af524c"  width="300" height="300"> | <img src="https://github.com/woongjoonchoi/DeepLearningPaper-Reproducing/assets/50165842/45dad146-29ed-421c-bb38-7c9bcb897581"  width="300" height="300">|  |
|:--: |:--: |:--:  | :--: |
| *trial1/loss*  |*trial1/top-1-error* |*trial1/top-5-error*|  |
| <img src="https://github.com/woongjoonchoi/DeepLearningPaper-Reproducing/assets/50165842/7d13a9ed-d32b-4a7b-bfd2-a792635375ee"  width="300" height="300">|<img src="https://github.com/woongjoonchoi/DeepLearningPaper-Reproducing/assets/50165842/8b4d3dda-2070-4ca9-95d2-9dd7cbb5939f"  width="300" height="300"> | <img src="https://github.com/woongjoonchoi/DeepLearningPaper-Reproducing/assets/50165842/2b1baf0b-ed69-4da3-9f84-17fbfc07bfa0"  width="300" height="300">|  |
| *trial2/loss*  |*trial2/top-1-error* |*trial2/top-5-error*|  |


trial2부터는 train accuracy도 확인하기 위해서 train accuarcy를 plot하기 시작하였습니다. 주황색이 validation ,파란색이 train에 대한 metric입니다. 

## weight initialization method differ by layer
weight initialization 방법을 모든 layer의 weight에 대해 xavier initialization을 했다고 이해를 했었는데, 아래 문장을 자세히보면 다음과 같은 의미를 지닙니다. 
> . It is worth noting that after the paper submission we found that it is possible to initialise the weights without pre-training
by using the random initialisation procedure of Glorot & Bengio (2010).  

pre-training 없이 Xavier initialization을 사용해서 weight를 initialize가 가능하다라고만 적혀있습니다. 
그리고 , 이 문장이 나오기 전에 아래와 같은 문장이 등장했습니다.  
> The initialisation of the network weights is important, since bad initialisation can stall learning due to the instability of gradient in deep nets. To circumvent this problem, we began with training the configuration A (Table 1), shallow enough to be trained with random initialisation 

학습의 지연을 해결하기 위해서 pre-training을 사용했다고 적혀있습니다. 따라서, pre-training없이 weight initialization이 가능하다는 말은 학습의 지연과 gradient의 instabilitiy를 방지하는 것을 xavier initialization을 통해서 가능하다는 말과 같습니다. 논문에서 , pre-trained weight로 앞부분과 뒷부분의 fully connected layer부분을 xavier initialization을 하였고, 나머지 layer는 random initialization을 하였다고 했습니다. 따라서, 논문처럼 앞부분과 뒷부분의 fully connected layer부분을 xavier initialization을 해주고, 나머지 layer는 mean이 0 std가 0.02인 normal distribution에서 weight sampling하여 initialize 해주었습니다. std를 0.2로 해주었던 이유는 최근에 봤었던 karphaty의 mingpt코드에서 gpt-2를 scratch부터 훈련할 때 std를 0.2로 두었기에 , 0.2로 두면 모델을 scratch부터 훈련할 때 도움이 되겠구나 싶어서 설정하였습니다. 그리고,bias는 전부 0으로 initialize해주었습니다. 이때, gradient explode problem으로 인해 loss값이 Nan값이 계산이 되었는데, 이는 gradient clip으로 gradient값을 조정해주었습니다. 그럼에도 불구하고 모델의 정확도에는 변함이 없었습니다. 

| <img src="https://github.com/woongjoonchoi/woongjoonchoi.github.io/assets/50165842/93b15631-2a6a-441a-9b16-5f069676fbc8"  width="300" height="300">|<img src="https://github.com/woongjoonchoi/woongjoonchoi.github.io/assets/50165842/0ebaf582-5deb-4434-82cd-01002617c062"  width="300" height="300"> | <img src="https://github.com/woongjoonchoi/woongjoonchoi.github.io/assets/50165842/bd9a1aa5-ccad-4fd1-b6bd-77675d65ceb5"  width="300" height="300">|  |
|:--: |:--: |:--:  | :--: |
| *caltech/loss*  |*caltech/top-1-error* |*caltech/top-5-error*|  |
| <img src="https://github.com/woongjoonchoi/woongjoonchoi.github.io/assets/50165842/d6ece40a-57df-4bd3-9ea0-bf47c878dcdb"  width="300" height="300">|<img src="https://github.com/woongjoonchoi/woongjoonchoi.github.io/assets/50165842/fbed2235-4a11-4255-aebb-ac80c3f11629"  width="300" height="300"> | <img src="https://github.com/woongjoonchoi/woongjoonchoi.github.io/assets/50165842/b763ead7-0050-4356-a3cd-b51388a399fe"  width="300" height="300">|  |
| *cifar/loss*  |*cifar/top-1-error* |*cifar/top-5-error*|  |



## number of random intialize layer increase 
DeepLearning Book pg301에 보면 다음과 같은 문장이 있습니다.  
> A further difficulty is that some initial points may be beneficial from the viewpoint of optimization but detrimental from the
viewpoint of generalization.   


accuracy가 나아지지 못하는 상황으로 보았을 때 , xavier initialize 로 인해서 weight가 sampling 되는 range가 좁기에 , convolution의 latent filter들이 각각 다른 feature들을 학습하지 못한다고 가정하였습니다.  따라서, random initialize하는 비율을 각각 다르게 증가시켜서 실험을 해보았습니다. 하지만, 어쩌다 한번 약간의 성능향상이 있을뿐, consistent한 결과를 얻지 못했습니다.


그림 vgg-2 std1e-1 전까지 

## Dataset Change From Cifar 100 to Cifar10, MNIST
Cifar10은 class당 tranining image개수가 5000개이고, Mnist는 class당 training image 개수가 6000개입니다. 뿐만 아니라, label의 분포가 balance 합니다.  여전히,loss function은   수렴하지만 , accuracy가 나아지지 않는걸 관측하였습니다. 따라서, 풀고자 하는 문제, 즉 dataset의 복잡함이 원인이 아니라고 판단하였습니다. 

그림 vgg-2 처음  Mnist,cifar10 
## Return back to standard deviation 0.01

random initialize시 weight의 distribution이 xavier initialization보다 더 커집니다.  뒷부분의 layer의 activation은 이전 layer의 weight와 actviation의 weighted sum입니다. weight의 distribution이 상당히 크기 때문에 뒷부분의 layer는 매번 다른 distribution의 input에 대해서 학습하는 상황을 맞이하게 되어 잘 generalize 하지 못한다고 생각이 들었습니다.   
Xavier initialization의 논문을 정확히 읽지 않고 , 그저 기계적으로 적용만 하였었는데, Xavier normalization 논문을 읽으면서 이 논문이 activation의 distribution과 gradient의 distribution을 layer별로 차이가 나지 않게 하여 학습에 도움을 주는 initialization strategy라는 것을 알게 되었습니다. 따라서, 다시 xavier initialization하는 layer의 수를 논문에서 언급된대로 수정하였고, std도 0.01로 수정하여 최대한 layer간의 activation gradient distribution이 비슷하도록 수정하였습니다.  
그렇게 하였더니, 모델 B가 cifar10, MNIST 데이터에 수렴하기 시작했습니다 .

새로 만든, vgg-2 mnist,vgg-4cifar10
## Trying Again on Cifar100 and find activation distribtion is important.
이를 토대로 하여 , CIFAR100에 모델을 다시 학습하기 시작하였습니다.  그러더니, CIFAR100에도 모델이 잘 fit하기 시작하였습니다. 이 당시 논문의 저자들이 activation이  saturate한 것을 해결하기 위해서 여러 방안들을 고려하다가 결국엔 model A로 학습을 한 후 이 weight들을 다른 model들을 학습하는데 사용한 것으로 보여집니다. 하지만, 논문의 저자들은 model A 없이 model을 학습하는 방향을 imagenet challange 제출후에도 고민하였고, 결국 weight initialization strategy를 적절히 적용함으로서 해결책을 알아낸거 같습니다. 

vgg-4, cifar 그림
## Trying on ImageNet , model B does not convergence well.
이러한 tiny dataset에 대하여 model을 fit한 이후 좀 더 큰 dataset인 imagenet에 대하여도 시도를 하였습니다. 이번에는 ,model A와 model B를 동시에 학습을 진행하였습니다.  하지만, model A에는 학습이 잘 진행되었지만, model B에는 학습이 잘 진행되지 않았습니다. 

vgg-B 실패그림,vgg-A성공그림
## increase xavier initializiation layer 
vgg model B와 vgg model A의 차이점은 layer depth가 2가 증가했다는 것입니다. hidden layer 2개를 추가적으로 random initialization을 해주는 것이 activation의 saturation에 영향을 끼쳤다고 생각했습니다. xavier initialization은 layer의 parameter수가 늘어날수록 weight를 sample하는 range를 줄여줍니다. parameter수가 늘어날 수록 weighted sum의 term의 개수가 늘어나기에 activation이 더욱더 saturate할 가능성이 높은데 , weight의 range를 줄여서 activation의 variance를 줄여주는 것입니다. vgg model의 경우 layer가 깊어질수록 parameter수가 늘어나는데, 이 부분을 normal distribution에서 random하게 sampling했기에 activation이 saturate하게 되었다고 가설을 세웠습니다.   
그렇다면, 아까 겪었던 weight의 range가 좁아지면서 convolution의 각 filter들이 각기 다른 feature들을 학습하지 못하는 가능성이 발생할 수 있습니다. 하지만, 이는 convolution과 fully connected layer의 backpropagation expression을 보면 발생하지 않는다는 것을 알 수 있습니다. 

convoloution layer의 actvation derivative는 $$dA \mathrel{+}= \sum _{h=0} ^{n_H} \sum_{w=0} ^{n_W} W_c \times dZ_{hw} $$ 이렇게 도출이 됩니다. random하게 initialize하는 layer의 convolution filter는 input activation , 즉 xavier initialization을 적용한 convolution layer의 output의 derivative에 영향을 줍니다. weight를 normal distribution에서 sampling 했기에 , weight의 range가 상대적으로 크기 때문에 dA의 range가 상대적으로 커지게 됩니다.  
convloution의 fiter의 derivative는 $$dW_c  \mathrel{+}= \sum _{h=0} ^{n_H} \sum_{w=0} ^ {n_W} a_{slice} \times dZ_{hw}  $$와 같이 도출이 됩니다. xavier intialization을 적용한 filter에 output , 즉 random initialization의 input 의 derivative가 곱해지기에 filter의 derivative range는 상대적으로 커지게 됩니다.  
따라서,back propagation을 하면서 , xaiver intialization을 한 filter들이 각기 다른 feature를 배우도록 학습할 수 있다고 생각했습니다. 

vgg-B 성공 ,vgg-C성공 
## loss function in cliff 



vgg-D실패 그림  

vgg-D 성공 그림 