---
title : "Convolution Neural Network : intuition"



excerpt: "Convolution Intuition"

categories:
  - DLArchitecture
tags:
  - [Machine Learning,Convolution,deep learning ,DLSC4]
# classes : wide
toc: true
toc_sticky: true
---

딥러닝으로 인해 많은 발전을 이룬 분야중 하나는 바로 computer vision 입니다 .computer vision 분야는 딥러닝을 접목시키기 시작하면서 , 다양한 발전들을 이루어 왔습니다. Computer vison에서 딥러닝을 하면 Convolution Neural Network를 떠올리게 됩니다.Convolution Neural Network는 이미 다양한 딥러닝 프레임워크에서 구현이 되어있습니다. Convolution Neural Network가 인간의 logic programming에서 무엇을 대체하고자 했고 , 어떤 intution이 담겨 있는지를 아는것 과 모르는 것은 큰 차이가 있다고 생각합니다. 

# History of Visual Processing

![Convolutional Networks II](data:image/jpeg;base64,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)

Convolution에 대한 역사에 대해 잠시 알아보도록 하겠습니다.  Wiesel,Hubel이라는 사람들이 고양이 뇌세포의 망막에 빛을 비추어 어떤 자극에 반응을 하는지 실험하였습니다. 그들은 시각피질의 세포들을 복잡한 세포와 간단한 세포로 분류하였고 , 간단한 세포에서의 시각자극이 어떻게 되는지 실험하였습니다.

![1: Seminal results of Hubel and Wiesel, figure adopted from (Hubel and Wiesel, 1968), Left) orientation selectivity in V1 cells, the recorded cell shows the most sensitivity to the orientation presented in the middle of the column. The sensitivity of cells to a range of orientations is varied smoothly, as shown by the curve. Right) direction selectivity in V1 cells, the recorded cell has the maximum sensitivity to the stimulus presented in the middle of the column. ](https://www.researchgate.net/profile/Mina-A-Khoei/publication/280736145/figure/fig1/AS:614166909947916@1523440210603/Seminal-results-of-Hubel-and-Wiesel-figure-adopted-from-Hubel-and-Wiesel-1968.png)

그 결과 특정 방향의 edge에 반응함을 알아내었습니다. 이 결과가 의미하는 것은 단순한 세포에서는 특정 방향의 edge같은 단순한 특징들을 포착하다가 더욱더 복잡한 세포 ,  즉 hypercomplex cell는 edge가 어떤 endpoint를 향해 다가가는지와 같은 좀 더 복잡한 특징들을 포착한다는 것입니다. 

일반적으로 말하자면, 생물학관점에서 visual processing은 simple cell에서 hycomplex cell로 갈수록 좀 더 많은 정보를 갖고 처리를 한다는 것입니다. 

# What is Convolution ?

기존에 쓰던 Multilayer neural network와 Convolution의 다른 점에 대해서 언급해보도록 하겠습니다.

![image](https://user-images.githubusercontent.com/50165842/153734655-d71360f8-74a2-4a75-97ac-42b8b42f8a10.png)

이러한 고양이 이미지를 multilayer perceptron에 input으로 준다고 가정해보겠습니다. 고양이 이미지의 크기가 128 * 128 이고 다음 hidden layer의 unit수를 300 정도라 보면 대략 3 million 정도의 parameters가 필요할 것입니다. 만약의 , 고양이 이미지의 크기가 1000*1000이면 대략 300million 정도의 parameters가 필요하게 됩니다. 

![In a convolutional neural network (CNN), when convolving the image, is the  operation used the dot product or the sum of element-wise multiplication? -  Cross Validated](https://i.stack.imgur.com/MkFSC.png)

Convolution Neural network는 filter라는 것을 사용하여 filter와 filter 크기 만큼의 input image의 영역을 element-wise (혹은 dot product) 해서 계산된 결과를 output image의 pixel로 사용하는 것입니다 . MLP와 달리 Filter만큼의 parameters만 사용하면 되기에 상대적으로 적은 paramters를 사용하게 됩니다. 

# What is filter?

## Filter Example(vertical edge)

![image](https://user-images.githubusercontent.com/50165842/153734814-d8802fb5-c305-4401-b4f8-968433034dac.png)

(위 사진은 Andrew 교수님의 강의에서 가져온 사진입니다.)



어떤 이미지가 주어지면 pixel 값의 차이에 따라 이미지 상에서 object의 위치가 정해지게 됩니다.  여기서, vertical edge를 분류를 해야한다면 사진에서 주어진 3*3 filter를 사용하면 됩니다. 저 filter sliding해서 다음 output pixel을 계산하는 과정을 거치게 되면  , pixel값이 높은 곳에서는 output pixel이 0으로 유지되다가, 경계선에 도달하면 output pixel값이 높아지면서 , pixel 값이 낮은 곳에 가게되면 다시 pixel값이 0으로 유지되는 것을 확인할 수 있습니다. 

pixel값이 높으면 밝고 , 낮으면 어둡게 컴퓨터 화면상에서 표현이 됩니다.  따라서, 원본 image는 왼쪽이 밝고 오른쪽 부분이 어둡게 보여집니다.filter는 백, 회색, 검은색 순으로 보여지고, output은 가운데가 두껍게 보입니다. output image가 두껍게 보이는 이유는 이 이미지의 size가 6 * 6 으로 작기 때문입니다. 한 , 1000 *1000쯤 되면 두꺼워 보이는 것 없이 잘 나올 것입니다.

## Another Filter

![image](https://user-images.githubusercontent.com/50165842/153736235-911a12c9-d147-454a-98a8-9203f2b02b3d.png)

(Andrew 교수님 강의에서 가져온 사진입니다.)



위 vertical edge detector는  밝은 pixel에서 어두운 pixel의 방향으로 filter를 sliding 해나가면서 edge를 detect 하게 됩니다 .하지만, 어두운 pixel에서 밝은 pixel로 slidng을 해나가면 , 양옆이 회색 pixel , 가운데가 검은 pixel이 되는 output activation이 나오게 될 것입니다. 

여기서 어두운 pixel에서 밝은 pixel로 가는것과 밝은 pixel에서 어두운 pixel로 가는 것이 중요하지 않다면, 절대값을 사용해서 vertical edge만을 detecting 하게 할 수 있을 것입니다 .

![image](https://user-images.githubusercontent.com/50165842/153736518-a8d9f971-9e6c-4b80-9682-83dacd9ba2c6.png)

vertical edge 를 감지하는 filter말고도 다양한 filter들이 있습니다. vertical 과 대조되는 horizontal filter가 그 중 하나의 예시라 볼 수 있습니다. 

horizontal filter는 vetrical filter를 회전하면 됩니다. 위의 vertical filter와 마찬가지로 경계선에 도달하면 output pixel값이 높아져서 horizontal line이 보이게 됩니다. 위의 예시는 체크 패턴에 horizontal filter를 sliding 하는 상황입니다 .기존의 vertical edge와는 다르게 30에서 10 으로 떨어지는 부분이 있는것을 확인할 수 있습니다 .이는 왼쪽에는 positive 오른쪽에는 negative pixel이 있음을 알려줍니다. 왜냐하면, 두 값을 섞게 되면 중간값이 되기 때문입니다. 

vertical edge detctor와 마찬가지로 6*6 size의 이미지이기 때문에 , 전환영역이 눈에 띄게 됩니다. 만약에 1000 * 1000 이미지라면 전환영역은 눈에 띄지 않을 것입니다 .



## Learning Edge detector

![image](https://user-images.githubusercontent.com/50165842/153736894-172992d5-e0dc-4b5e-be84-ec46ed7b41ce.png)

이 외에도 , 45도 , 70도 line같은 filter도 만들 수 있습니다. 45도 flter는 식을 구할 수 있지만, 70도 같은 특수한 filter는 식을 구하기가 어렵습니다. 그리고 , 컴퓨터 비젼 과학자들이 만든 sobel filter, scharr filter라는 것들이 있습니다. 이전에는, 과학자들이 이러한 filter 값들을 실험을 통해서 찾았지만, 딥러닝은 비전 과학자들이 이를 선택하게 하지 않고 , 이 filter를 backpropagtion을 통해서 학습을 해나가게 합니다.

. 따라서, convolution filter를 learnable paramter로 설정하여 이러한 filter들을 학습해 나가는 것이 바로 convolution neural network 입니다. 즉 , neural net이 edge같은 low level feature를 배워나가게 하는 것입니다 .

# Convolution Layer

위에서는 , visual processing과 convolution filter가 무엇이며 딥러닝에서 어떤 부분을 사람 대신하는지 와 같은 여러 convolution layer에 대한 motivation을 알아보았습니다. 그렇다면, neural net에서 convolution layer를 사용할려면 forward operation을 정의해야 하는데, 정의할 때 무엇을 고려해야 하는지 알아보도록 하겠습니다 .

## Convolution forward

![image](https://user-images.githubusercontent.com/50165842/153737095-2755523b-05c6-4bf0-9303-faafbfd4d72d.png)

(Standford cs231n lecture 5 pdf의 image를 가져왔습니다.)

우선 convolution 연산이 무엇인지 부터 알아보도록 하겠습니다.  Convolution은 filter와 image pixel을 dot-product 해서 actvation map의 output pixel로 사용하는 것입니다 . output image size가 28로 작아졋음을 확인할 수 있습니다. 이미지 사이즈는 (N-F+1) * (N-F+1)이라는 공식을 따르게 됩니다. (N:이미지 한변의 길이 , F:filter의 한변의 길이)

 input size를 넘어가는 범위에 대해서 dot-product를 수행하면 안되므로  전체 input size에서 filter size만큼을 뺀 pixel의 개수 만큼을 반영합니다. 양 쪽 끝 구간을 반영해야하므로 계산상 +1을 해줍니다. 

## Padding

![image](https://user-images.githubusercontent.com/50165842/153737599-333b0cf7-17f7-4a9a-8179-f7e3022953bc.png)

Convolution 연산의 단점으로는 여러가지가 있습니다.

1. 매 layer를 거칠 때 마다 image의 size가 작아진다.
2. corner 나 edge 쪽의 pixel은 output에 한번만 반영이 되어진다.

이러한 단점을 해결하기 위해서 padding이라는 idea가 제시되었습니다.



image의 외각에 어떤 pixel들을 붙이게 됩니다. 보통, 0을 padding하는데, 이를 zero-padding이라고 부릅니다. 만약에 , zero padding대신에 다른 value로 padding을 할 수 있느냐라고 물을 수 있는데, 답은 사용할 수 있습니다 입니다. 하지만, 보통 일반적으로는 0이 잘된다고 알려져 있기에 0을 사용하게 됩니다. 

여기서, padding을 하게 된다면 이미지의 size는 (N-F+1+2p) * (N-F+1+2p) 가 됩니다. 

## Stride

여태까지는 , filter를 1칸씩 만 이동을 했었는데 , 반드시 filter를 1칸씩 만 이동을 해야하는 것은 아닙니다. filter를 몇 칸 이동할지 정하는 것을 stride라고 합니다. 

이 stride를 고려하게 된다면 , output 의 size는  $$ (\lfloor {(N+2p-f) \over S} \rfloor + 1 )(\lfloor {(N+2p-f) \over S }\rfloor + 1)$$ 가 됩니다. 

이를 말로 설명하자면, filter의 (0,0) position의 element와 dot-product를 하는 input pixel이 있는데, input pixel의 간격이 S가 되므로  S로 나누는 것과 같게 됩니다. 아까 설명했던 것처럼 양쪽 끝 구간을 포함해야 하므로 +1을 수행합니다. (수직선 에서 <= 범위를 생각하면 편합니다. )

stirde의 값을 1이상으로 주다 보면 , 반영이 되지 않는 부분이 생길 수 있습니다.이러한 경우, output 이미지가 모든 input pixel을 symmetric하게 반영하지 못하므로 asymmetric 하다 합니다. 이러한 , 경우의 stride 값은  거의 쓰지 않는다고 합니다 .

## Convolution over Volume

![image](https://user-images.githubusercontent.com/50165842/153737095-2755523b-05c6-4bf0-9303-faafbfd4d72d.png)

아까 설명을 보면 input image가 2d인 것처럼 가정하고 설명을 하였습니다. 하지만, 실제로 존재하는 이미지들은 대부분 rgb 이미지, 즉 3-d 이미지가 많습니다 .

![image](https://user-images.githubusercontent.com/50165842/153738165-66ef0891-cae4-47b9-8ff3-434c10a7f841.png)



좀 더 detail하게 보면 , 2d image가 여러개가 있고, 2d filter가 여러개가 있는 것입니다.이러한 새로운 dimension을 depth 혹은 channel이라고 합니다. 이전에 보았던 convolution 연산처럼 연산을 진행하는데, 같은 depth의 image와 filter에 대해서 convolution 연산을  수행해줍니다 .

위의 예시의 경우 , Red channel에서만의 vertical edge를 detect하는 것을 확인할 수 있습니다.

![image](https://user-images.githubusercontent.com/50165842/153738289-1fdb7362-877c-4640-bb16-7df2aafa9f01.png)

여태까지의 convolution 연산은 output 이 2-d image였습니다. 하지만, 같은 shape 의 다른 filter를 사용함으로써 3-d output image를 만들어 낼 수 있습니다 . 이 때 , 다른 filter를 거치게 되므로 각 channel의 output은 달라진 다는 것을 기억해야 합니다. 

## Putting it together

이것들을 전부 합쳐본다음에 필요한 parameters 수와 hyperparameters 를 정확히 인지하는 것이 중요합니다.

![image](https://user-images.githubusercontent.com/50165842/153738536-f038eeab-4646-4698-9c2e-a0aaae5f06f6.png)

이러한 예시의 경우 첫번째 layer에서 2번째 layer로 갈 때 parameters수는 (5 * 5 * 3 + 1)  * 6  가 될 것입니다. 여기서 +1 은 bias를 더해주는 것을 의미합니다. 

일반적인 l번째 conv layer에 대한 정의를 해보겠습니다.

- f[l] = filter size, p[l] = padding size , s[l] = stride size
- Input : n_h[l-1] * n_w[l-1] * n_c[l-1]
- output : n_h[l] * n_w[l] * n_c[l]
- activation_size  : n_h[l] * n_w[l] * n_c[l]
- filter size: f[l] * f[l] * n_c[l-1] * n_c[l]
- bias : 1 * 1 * 1* n_c[l]
- n_h,w[l] = $$ (\lfloor {(n_h,w[l-1+2p[l]-f[l]) \over s[l]} \rfloor + 1 ) $$
- A[l] : m * n_h[l] * n_w[l] * n_c[l] 

A[l]은 m개의 mini batch에 대한 output 결과를 의미합니다. Andrew 교수님은 bias의 implementation이 4 dimension이면 편하다고 하셧는데 , position이 weight와 같은 dimension으로 access가 되어서 logic상 이해하기 쉽다고 하신 의미같다고 개인적으로 생각하고 있습니다. cs231n assignment 를 보면 1 dimension으로 처리를 했는데 , 코딩 스타일의 차이라 생각이 되어집니다 .



# Other Layer

Convolution Neural Net에서는 Conv layer만 존재하지 않습니다. 다른 layer도 존재합니다. 그 중 하나인 pooling layer에 대해서 알아보도록 해보겠습니다.

## Max Pooling Layer

![image](https://user-images.githubusercontent.com/50165842/153739642-d4a195a7-e574-4c44-b800-2195dae22362.png)

max pooling layer는 filter 를 stride 하면서 영역에서 최대값을 뽑는 operation 입니다 . 1box(파란색)에서는 pixel값이 큰 데 , 이는 여러 다양한 feature들이 발견이 되었다는 뜻입니다.(ex.고양이의 눈 , 코, 입) . 2 box(하늘색)는 pixel값이 상대적으로 작은데 , 이는 feature들이 발견되지 않았다는 것입니다. 

보통 , max pooling 을 image size를 줄인다고 설명을 하는 경우가 많지만, 여기에는 숨겨진 직관이 있습니다  . max pooling의 역할은 feature들이 최대한 많이 발견되게 하는 것입니다. filter가 stride를 해나가면서 pixel 값이 높으면, 즉 feature가 있으면 max operation으로 보존이 됩니다. feature가 발견되지 않는다면 max operation의 값은 작을 것입니다. 

즉, 이미지를 downsampling을 하는데 , feature는 max operation으로 최대한 많이 발견되면서 보존이 되게 해서 , 작은 사이즈의 image로 비슷한 효과를 내는 것입니다. 

Standford 강의를 듣다보면 , pooling 과 stride의 차이점에 대해서 질문을 하는 학생이 있습니다.  거기서 serena 교수님은 (2017년 기준 ) 최신 architecture들은 pooling 대신 stride로 대체하는 architecture들이 꽤 있다고 답변을 해줍니다. 하지만, 실제로는 pooling이 더 좋은 결과를 낸다고 추가적인 코멘트를 달아주십니다. Pooling의 intuition을 생각하면 stride convolution과는 완전히 다른 역할을 하는 연산이므로 대체가 될 수 없다고 개인적으로 생각합니다 .

이 외에도, average pooling도 존재하는데 , 주로 max pooling을 쓴다고 합니다. 

그림을 보시면서 아시겠지만, 이 pooling operation은 channel 에 대해 독립적으로 진행이 됩니다. 즉, 2-d input에 대하여 진행이 됩니다 .

여기서, 중요한 것은 pooling은 parameters가 없다는 것입니다. pooling의 operation에 필요한 것은 max, average function이므로 learnable parameters가 필요 없습니다. 

#  Why Convolution?

글 서두에서 Convolution이 MLP 보다 parameters를 줄일 수 있다고 하였습니다. 그 이유와 Convolution을 사용하는 다른 이유를 자세히 알아보도록 하겠습니다. 

## Parameter sharing

Convolution은 activation map을 만드는데 모든 pixel에 대하여 같은 parameter를 사용하게 됩니다. 같은 parameter를 사용하므로 , 이미지의 다른 position에서도 같은 feature를 발견할 수 있게 됩니다. 예를 들면, 3 x 3 filter로 vertical edge를 detection을 한다고 했을때 , 이미지의 왼쪽과 이미지의 오른쪽의 vertical edge를 detection 하기 위해서 다른 parameter를 필요로 하지 않습니다.다시 말해서, Multilayer perceptron보다 더 적은 parameter로 같은 feature를 추출할 수 있게 됩니다. 

## Sparse Connection

Activation map은 Fully Connected Layer와 달리 각 pixel의 input의 모든 pixel과 연결이 되어있지 않습니다. 따라서, 이미지가 조금 손상이 되어도 조금 shift 되도 전체적인 feature를 추출하는데 큰 영향을 받지 않습니다. 이를 translation invariance를 잘 capture한다고 합니다. 

# Reference

[Lecture 5 | Convolutional Neural Networks - YouTube](https://www.youtube.com/watch?v=bNb2fEVKeEo&t=3164s)

[Convolutions Over Volume | Coursera](https://www.coursera.org/learn/convolutional-neural-networks/lecture/ctQZz/convolutions-over-volume)

