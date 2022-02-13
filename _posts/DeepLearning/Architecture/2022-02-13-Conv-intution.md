---
title : "Convolution Neural Network : intuition"



excerpt: "Convolution Intuition"

categories:
  - DLArchitecture
tags:
  - [Machine Learning,Convolution,deep learning ]
# classes : wide
toc: true
toc_sticky: true
---

딥러닝으로 인해 많은 발전을 이룬 분야중 하나는 바로 computer vision 입니다 .computer vision 분야는 딥러닝을 접목시키기 시작하면서 , 다양한 발전들을 이루어 왔습니다. Computer vison에서 딥러닝을 하면 Convolution Neural Network를 떠올리게 됩니다.Convolution Neural Network는 이미 다양한 딥러닝 프레임워크에서 구현이 되어있습니다. Convolution Neural Network가 인간의 logic programming에서 무엇을 대체하고자 했고 , 어떤 intution이 담겨 있는지를 아는것 과 모르는 것은 큰 차이가 있다고 생각합니다. 

# History of Visual Processing

![9: Experimental setup for study of the visual cortex of cats (HUBEL;... |  Download Scientific Diagram](https://www.researchgate.net/profile/David-Macedo-4/publication/330838896/figure/fig10/AS:722098146263043@1549173022871/Experimental-setup-for-study-of-the-visual-cortex-of-cats-HUBEL-WIESEL-1959.jpg)

Convolution에 대한 역사에 대해 잠시 알아보도록 하겠습니다.  Wiesel,Hubel이라는 사람들이 고양이 뇌세포의 망막에 빛을 비추어 어떤 자극에 반응을 하는지 실험하였습니다. 그들은 시각피질의 세포들을 복잡한 세포와 간단한 세포로 분류하였고 , 간단한 세포에서의 시각자극이 어떻게 되는지 실험하였습니다.

![1: Seminal results of Hubel and Wiesel, figure adopted from (Hubel and Wiesel, 1968), Left) orientation selectivity in V1 cells, the recorded cell shows the most sensitivity to the orientation presented in the middle of the column. The sensitivity of cells to a range of orientations is varied smoothly, as shown by the curve. Right) direction selectivity in V1 cells, the recorded cell has the maximum sensitivity to the stimulus presented in the middle of the column. ](https://www.researchgate.net/profile/Mina-A-Khoei/publication/280736145/figure/fig1/AS:614166909947916@1523440210603/Seminal-results-of-Hubel-and-Wiesel-figure-adopted-from-Hubel-and-Wiesel-1968.png)

그 결과 특정 방향의 edge에 반응함을 알아내었습니다. 이 결과가 의미하는 것은 단순한 세포에서는 특정 방향의 edge같은 단순한 특징들을 포착하다가 더욱더 복잡한 세포 ,  즉 hypercomplex cell는 edge가 어떤 endpoint를 향해 다가가는지와 같은 좀 더 복잡한 특징들을 포착한다는 것입니다. 

일반적으로 말하자면, 생물학관점에서 visual processing은 simple cell에서 hycomplex cell로 갈수록 좀 더 많은 정보를 갖고 처리를 한다는 것입니다. 

# What is Convolution ?

기존에 쓰던 Multilayer neural network와 Convolution의 다른 점에 대해서 언급해보도록 하겠습니다.

![image](https://user-images.githubusercontent.com/50165842/153734655-d71360f8-74a2-4a75-97ac-42b8b42f8a10.png)

이러한 고양이 이미지를 multilayer perceptron에 input으로 준다고 가정해보겠습니다. 고양이 이미지의 크기가 128 * 128 이고 다음 hidden layer의 unit수를 300 정도라 보면 대략 3 million 정도의 parameters가 필요할 것입니다. 만약의 , 고양이 이미지의 크기가 1000*1000이면 대략 300million 정도의 parameters가 필요하게 됩니다. 

![Image convolution with an input image of size 7 × 7 and a filter kernel...  | Download Scientific Diagram](https://www.researchgate.net/profile/Chaim-Baskin/publication/318849314/figure/fig1/AS:614287726870532@1523469015098/Image-convolution-with-an-input-image-of-size-7-7-and-a-filter-kernel-of-size-3-3.png)

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

