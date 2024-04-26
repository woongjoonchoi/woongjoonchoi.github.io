---
title : "Convolution Neural Network : Variation"



excerpt: "Convolution Variation"

categories:
  - DLArchitecture
tags:
  - [Machine Learning,Convolution,deep learning ,DLSC4]
# classes : wide
toc: true
toc_sticky: true
---

이전 Post들에서 기본적인 Convolution의 Operation과 다른 architecture에 영향을 끼친 Residual Block을 다루었습니다. 기본적인 Convolution은 이미지의 복잡한 feature를 찾는데 효과적이었지만, 몇가지 문제점들이 있습니다. 이러한 문제점을 해결하기 위해서 Convolution의 여러 variation들이 나왔는데 , 이에 대해서 알아보도록 하겠습니다.

## 1 X 1 Conv
1 X 1 convolution은 Inception Network에서 channel수를 줄여 computation양을 줄였다고 널리 알려져 있습니다. 주된 목적은 그것이 맞지만, 그 이외에도 여러 목적들이 있습니다. 이 Idea들은 Convolution 뿐만 아니라 다른 딥러닝 architecture에서도 적용될 수 있기에 알아둘만한 가치가 있습니다.
### Network in Network
![iamge](https://1drv.ms/i/c/7e81bbcd99889380/IQMjA-bDqJWjQ42GbatCPisrATXSUqmsfEtUqVqD2r3SUc8?width=512)  

1 X 1 convolution은 2013의 Network in Network라는 paper의 Idea를 발전시킨 것입니다. 이 논문의 저자는 cnn에서 feature map은 의미있는 feature를 나타내는데 , 이 feature를 다르게 표현한 여러 variation들을 학습하는 점에 주목합니다. 이를 구체적으로 설명하면 , convolution의 feature map은 이전 layer의 여러 channel의 feature map에 각각 kernel를 적용하여 이를 더하게 됩니다. 각 kernel은 feature를 표현하는 방법 중 하나라 볼 수 있습니다.이 논문에서는 이를 latent concept이라 표현합니다.  즉 , 같은 feature를 표현하기 위해서 다양한 concept들을 학습하게 됩니다.다시 말하자면, convolution network는 각 kernel 들이 모두 다른 concept들을 나타내는 전제하에 학습이 진행이 됩니다.이를 다른말로 표현하면, linearly seperable 입니다. neural net의 parameter 들이 직접적으로 무엇을 의미하는지 확인할 수 없기에 latent concept 이라 할 수 있습니다. 다음 layer에서 convolution 연산을 하면서 , 이 모든 latent concept들 고려하기 위해 channel수를 이전 layer 보다 더 많이 사용하는 건  비효율적이라고 주장합니다 .  

NIN paper에서는 convolution layer 사이에 local patch에 대해 multilayer perceptron을 추가하면 local latent concept에 대해 더 discriminative 한 model이 될 수 있다고  주장합니다. Multilayer Perceptron은 radial basis neural network 와 더불어 잘 알려진 universal function apprximator 입니다. latent concept에 대한 distribution을 알 수 없기에 임의의 variable을 학습하는 방법론인 universal function approximation을 사용해야 한다고 주장합니다. 
또한 , Multilayer perceptron을 사용한 이유로 2가지를 언급합니다. 
- Multilayer perceptron은 backpropagation시 convolution  nueral network와 호환이 되는 architecture입니다.
- Multilayer preceptron은 deep neural network이며 input variable인 local patch를 deep layer를 거쳐서 의미있는 특징으로 만들게 됩니다. 


![image](https://1drv.ms/i/c/7e81bbcd99889380/IQNU8i5lt8kJS4uLYdFxHi-EAQ3abUCC71Drj-t1VJTYsdY?width=256)  

기존의 local patch에 대해서 convolution연산을 수식으로 표현하면 위와 같습니다.feautre map에서 각 channel에 대해 kerenl을 적용하여 feature를 찾아냅니다.

![image](https://1drv.ms/i/c/7e81bbcd99889380/IQM1_tn-jTrzSptEZ-RTkiSeATaLHV14a092c-6xYO_otdU?width=256) 
하지만, multilayer perceptron의 경우 feature를 찾을 때 , 모든 channel을 한번에 고려하여 deep layer를 거쳐서 feature를 추출하게 됩니다. 즉 , 이전 layer의 모든 latent concept들을 fully connected layer를 통해 새로운 concept으로 만들게 됩니다 .   

이 논문에서의 주목할 점은 feature map의 모든 channel을 고려하여 모든 latent concept을 고려하기 위해서 channel수를 더 늘려서 고려할 수 있도록 한것이 아닌 줄인것이라고 생각합니다. 이 논문에서 명시적으로 언급이 되지는 않았지만, 기존의 convnet이 over-complete-set 의 filter를 사용하고, 하나의 concept을 계산하는데 너무 많은 filter를 사용한다고 언급을 하고 이를 mlp conv로 해결을 했다고 주장합니다.또한, 이 연산을 parametric cross channel pooling 연산이라 볼 수 있다고 합니다. cross channel pooling 연산은 channel을 줄이는 연산으로 사용됩니다.  따라서, 기존의 conv처럼 channel수를 늘리는 것이 아닌 줄여서 모든 latent concept을 고려해서 의미있는 concept을 계산했다고 보는것이 타당하다고 생각합니다. 
<!-- mlp convolution을 적용한 output에 대해 gradient를 고려하게 될 때 , 모든 latent concept에 대해서가 아니라  여러 layer를 거쳐서 transformed된 의미있는 concept에 대하여 gradient를 계산하게 될 것입니다.   -->
결론적으로 , 기존의 convolution은 이전 layer로 부터의 모든 정보들을 고려하기 위해서 너무 많은 정보들을 계산해왔습니다.  의미있는 정보만을 output으로 계산하여 다음 convolution layer에 input으로 넘겨주는것이 mlp convolution 수식에 담긴 의도입니다. 
### Network in Network vs Inception
Google 에서 발표한 Going Deeper with Convolution이라는 논문에서는 Network in Network의 MLP Convolution layer를 1 X 1 convolution의 형태로 변형을 하였습니다. MLP convolution 과 달리 이 논문에서는 input patch를 1x1의 범위로 제한시켰습니다. 1x1 의 범위에 대하여 모든 channel을 고려하게 되면, 이는 결과적으로 1 X 1 convolution연산과 동일하게 됩니다. 이 paper의 main idea가 correlated unit들을 찾는 것인데 , 그 unit들이 lower layer에서는 local region에 밀집되어 있다고 합니다. 따라서, 1 x 1 convolution으로 충분히 찾을 수 있다고 판단했다고 합니다. 
### Inception Network
![image](https://1drv.ms/i/c/7e81bbcd99889380/IQPORDogo6XZTLSpSaFZAxB3AUlAf0SbvShkdhBvmR6rd90?width=660)  

Inception Network의 논문은 어떤 논문의 idea를 기반으로 작성되었습니다. 그 논문은 Arora라는 사람과 여러 연구원들이 작성한 논문인데 References에 링크를 첨부하도록 하겠습니다.이 논문에 따르면, data의 probability distribution을 구하는 deep neural network가 매우 크고  , 깊고 , sparse 하다면 , 마지막 layer의 activation의  correlation statistics를 분석하고 high correlated output으로 neuron을 clustering 함으로써  , 최적의 neural network 가 만들어진다고 합니다. 그리고 , sparse matrix는 dense submatrix들로 approximate 될 수 있습니다. 위에서 언급했듯이, lower layer에서는 local region 에 high correlated output의 neuron들이 밀집되어 있습니다. 이를 , convolution 연산으로 clustering해준다고 합니다. (convolution 연산으로 neuron들이 연결되는 것을 clustering으로 표현한거 같습니다. ) .neuron의 cluster들이 좀 더 큰 영역에 퍼져있을 수 있기에 filter size를 3 x 3 , 5 x 5 까지로 한정합니다. pooling operation은 convolution neural network에서 중요하기에 pooling layer도 사용합니다.  
이러한 architecture는 sparse matrix를 근사할 수 있지만, 몇 번의 module을 통과후 computation이 많이 증가하게 됩니다. 

![image](https://1drv.ms/i/c/7e81bbcd99889380/IQOT10w8PbbuS7nJQez1hcwcAUbIgM86uE5jXVIUS5_UFu8?width=660)  
이를 해결하기 위해서 computation이 많이 증가하는 부분에서 dimension 감소와 projection을 적용하게 됩니다. 여기서 , dimension 감소를 먼저 하는 이유는 low-dimension embedding이 많은 정보를 가지고 있더라도 , low-dimension embedding은 압축되고, dense한 형태로 정보가 담겨있기에 model이 이를 학습하는 것은 어렵습니다. 따라서, dimension 감소를 먼저 적용하고 , convolution 연산을 적용합니다. 이렇게 함으로써, sparse matrix의 approximation을 유지하면서, computation 양을 감소시킬 수 있습니다. 
이렇게 함으로써, Inception Network는 computation 양을 조절하면서 layer를 깊게 쌓을 수 있습니다.   

Network의 구체적인 수치나 computation 감소량은 논문이나 다른 블로그를 찾아보시면 쉽게 알 수 있을 것입니다. 
## Separable Convolution
1 x 1 convolution은 computation양을 획기적으로 줄였지만, 다양한 hardware에서 적용하는 것은 어려웠습니다. smartphone같은 device에서 이를 사용하기 위해서는 좀 더 computation 양을 줄여야 할 필요가 있습니다. Separable Convolution이 바로 그 Idea입니다.

### Original Conv vs Seperable Conv
![image](https://1drv.ms/i/c/7e81bbcd99889380/IQPh-iZ-ZAXmQ5gOEJGzP9ZBAZWhYQx2Axyhl--pNCAW3Rs?width=660)
seprable convolution은 orginal convolution이 input의 channel을 전부 결합하여 feature map을 만드는 것과 달리 1개의 channel로 feature map을 만듭니다.seperable convolution은 original convolution에 비해 계산면에서 굉장히 효율적입니다.  하지만, 이를 결합하여 새로운 feature를 만들어 내지 않습니다. 따라서, feature map들을 결합하는 방법이 필요하게 됩니다. MobileNet에선  1 x 1 convolution을 통해  feature map의 각 channel들을 결합하여 새로운 feature를 만들게 됩니다. 
## BottleNeck Block
![image](https://1drv.ms/i/c/7e81bbcd99889380/IQMSOXEfK6_3TLmkUO3cS2dCASNUaVJzwzBsv3YV2_XCP4Q?width=1024)
1 x 1 convolution 과 separable convolution을 이용하여 MobileNetV2 paper에서는 bottleneck block이라는 새로운 convolution architecture를 소개한다. computation 측면에서 보면 channel을 늘렸다가 줄였다로 보이지만 , 여기에는 어떤 수학적인 이유가 숨겨져 있다. dimension의 변화는 다른 architecture에서도 적용되는 idea이기에 알아두면 유사한 idea를 이해하는데 도움이 될 것입니다.
### low-dimension embedding
![image](https://1drv.ms/i/c/7e81bbcd99889380/IQPb9H7oIxoJTJje1VgQB1ZcAZ9VfutmlV-Wyff-mYf1iCA?width=1024)  

Convolution Neural Network에선 activation tensor들은 height * width * dimension 의 형태를 가지고 있습니다. 이 tensor들은 "manifold of interest"를 형성하게 됩니다. neural network의 "manifold of interest"는 low-dimension으로 embedding 될 수 있다고 오랬동안 알려져 왔습니다. 즉, 어떤 임의의 channel을 가진 feature map에서 encoded 된 정보들은 어떤 manifold에 위치하는데  , 이 manifold는 feature map의 dimension 보다 낮은 dimension의 subspace입니다. 

하지만, 이러한 사실은 non-linear activation function에 의해 적용되지 않습니다.  
특수한 조건을 만족하게 된다면 low-dimension에 manifold가 존재할 수 있습니다.  

![image](https://1drv.ms/i/c/7e81bbcd99889380/IQNVdShjBRHrQYBj9tu78966AYumbZAEH7c0HwCTvdQkhzE?width=1024)  
input x에 대해  transformation B를 적용한후 , ReLU activation을 적용한 , ReLU(Bx) transformation의 output이 non-zero volume S를 가지게 된다면 , output은 여전히 linear transformation으로 제한됩니다.  위 그림은 dimension이 클수록, 2-D projection이 정보를 더 잘 보존함을 보여줍니다. ReLU를 적용하게 되면, 정보가 손실되는 것은 불가피합니다. 하지만, input의 channel이 충분히 크다면, 정보는 channel수가 적더라도 보존이 될 것입니다. 

### MobilenetV2
MobileNetV2에서는 activation function으로 ReLU6을 사용하였습니다. 이는 max값의 범위를 6으로 제한하는 ReLU function입니다. 왜냐하면 , on-device에서는 low-precision model을 사용하는데 , max값이 6으로 제한되어 low-precision에서 overflow가 발생하지 않고 robust하게 작동하기 때문입니다 . 

## EfficientNet
Convolution의 변형점 보다는 전체 ConvNet을 Peformance와 Speed의 tradeoff에 관한 내용이 EfficentNet입니다. 다른 딥러닝 architecture에서도 사용될 수 있는 Idea이기에 알아두면 좋을거 같습니다.  
EfficientNet은 network의 performance와 speed의 trade-off관계를 width , resolution, depth로 조절합니다. width,resolution,depth가 커지면 network의 performance가 향상되지만, speed는 떨어지게 됩니다. 따라서, 주어진 computation resource와 performance의 요구사항을 잘 고려하여 상황에 맞게 scaling을 하면 좋다고 주장합니다.

## Summary
오늘은 다음과 같은 내용들을 알아보았습니다.
- Network in Network 는 불필요한 feature를 detect하는 parameter를 학습하지 않고 non-linearty를 증가시켜 모델이 complex function을 학습하게 해준다.
- Inception Network는 NIN을 1 x 1 Conv로 변형해서 사용하고, 불필요한 feature의 representation까지 전부 한번에 고려한다.
- Separable Convolution은 Computation면에서 Origianl Convolution보다 상당한 감소를 보여준다.
- BottleNeck block은 high dimension의 feature에 low-embedding으로 표현될 수 있는 manifold가 존재한다 가정합니다. 
- Network의 Peformance,Speed는 서로 tradeoff 관계이다. 이는 depth,length,width로 조절할 수 있다 .
## References

[Network In Network](https://arxiv.org/abs/1312.4400)  

[DLS Conv](https://www.coursera.org/learn/convolutional-neural-networks/home/week/2)

[MobileNetV2](https://arxiv.org/abs/1801.04381)

[Sparse deep network](https://arxiv.org/pdf/1310.6343.pdf)