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

## Dataset Chang From CalTech256 to Cifar100
Caltech256 dataset은 class당 이미지가 100개 정도였고, 분류해야할 class label이 257개 이고, class label의 분포가 imbalance 했습니다. 따라서, label당 이미지 개수가 500개이고, 분류할 class가 100개인 cifar100 dataset에 대하여 model B를 학습하였습니다.   
하지만, model B에 대해서도 학습이 잘 진행되지 않았습니다. 따라서, 좀 더 단순한 dataset에 대하여 학습을 진행하기로 하였습니다. 
## Dataset Change From Cifar 100 to Cifar10, MNIST
Cifar10은 class당 tranining image개수가 5000개이고, Mnist는 class당 training image 개수가 6000개입니다. 뿐만 아니라, label의 분포가 balance 합니다.  여전히,loss function은   수렴하지만 , accuracy가 나아지지 않는걸 관측하였습니다. 따라서, 풀고자 하는 문제, 즉 dataset의 복잡함이 원인이 아니라고 판단하였습니다. 

## 1 by 1 Convolution to Linear Layer
논문에서는 fully convolution으로 구현을 하였지만 , torchvision의 implementation은 fully connected layer로 구현하였습니다. fully connected layer나 1 by 1 convolution은 수학적으로 차이가 없지만 , avearage pooling layer를 어디서 적용하는지의 차이점이 있습니다. 따라서, 여기서 차이가 발생했다 생각하여 1 by 1 convolution을 fully connected layer로 대체하였지만, training에서 큰 효과를 보지 못하였습니다. 
## weight initialization method differ by layer

## number of random intialize layer increase and standard deviation increase


## number of output random initialize layer increase

## Return back to standard deviation 0.01

## Trying Again on Cifar100

## Trying on ImageNet , model B does not convergence well.

## increase xavier initializiation layer 

## loss function in cliff 
