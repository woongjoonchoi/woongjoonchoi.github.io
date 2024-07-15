---
title : "Encoder-Decoder(RNN,CNN,attention,self-attention)"



excerpt: "Encoder decoder Types"

categories:
  - DLArchitecture
tags:
  - [Machine Learning,Transformer,attention,deep learning ]
# classes : wide
toc: true
toc_sticky: true
---


수많은 Generative AI 모델이 여러 분야에서 응용되고 있는게 2024년도 AI의 Trend라 볼 수 있습니다. 단순히 , image,text,speech 같은 1가지 type의 input을 넘어서서 여러 type의 input을 받는 multimodal이 점점 대세가 되고 있습니다. 
하지만, 모든 응용사례를 전부 알기에는 시간이 너무 부족합니다. 이를 abstraction 해서 생각할 수 있다면 나중에 실제로 필요한 사례들의 data type만 고려한다면 쉽게 자신의 연구분야나 업무에 적용할 수 있습니다. generative AI를 가장 상위 개념으로 abstraction 한다면 Encoder-Decoder로 볼 수 있다고 생각합니다. Encoder-Deocder의 여러 유형에 대해 알아보겠습니다.

## Encoder-Decoder?

Encoder-Decoder 모델은 input에서 어떠한 정보를 추출한 다음에 deocder의 input으로 넘겨주는 model이라 볼 수 있습니다. 
기존의 ML은 $$ P(Y | X) $$ 의 problem을 푸는 과정이라 볼 수 있습니다. 하지만, Encoder-Decoder 문제는 아래와 같이 문제가 번형이 됩니다.

$$ P(Y | X) \rightarrow P(Y | H) $$

 standard한 ML model은 input X에서 output을 도출하지만, Encoder-Decoder는 input X를 어떤 continuous 한 vector space의 vector H에 mapping 한 다음, 이 vector H가 주어졌을 때 probability distribution을 구하게 됩니다.  따라서, 여기서 2가지 function이 필요합니다.

 *  $$  f : X \rightarrow H $$
 *  $$  g : H \rightarrow Y $$ 





## CNN Encoder - CNN Decoder
CNN encoder , Cnn decoder의 경우 가장 고전적인 예제중 하나는 바로 Image Segmentation 입니다. 

![image](https://github.com/user-attachments/assets/10782b20-ac12-4503-942a-106ab88227cf)  


Image Segmentation의 예제의 경우 input image를 fixed size vector로 압축하게 됩니다 . 그리고 압축된 vector를 통해서 원래의 이미지로 복원하게 됩니다. 

원래의 이미지로 복원할 떄 Transpose Convolution이라는 것을 사용하게 됩니다. 이는 input의 scale을 키우는 convolution 연산입니다. 

중요한점은 , 이때 압축된 벡터는 high level의 semantic information을 담고 있지만 , low level의 spatial information을 담고 있습니다. 
따라서, Residual Connection을 사용해서 같은 scale의 image를 concatenation 해줍니다. 이 image는 transpose를 거친 벡터보다 high level의 spatial information , low level의 semantic information을 담고 있습니다.  

이미지의 경우 , pixel이 어디에 위치해 있는지가 중요하기에 spatail information을 residual connection으로 concat 해줍니다. channel 방향으로 concat한다음에 이 channel들을 전부 사용하여 새로운 feature를 계산하게 됩니다.

마지막엔 1 by 1 convolution을 통해서 각 pixel 마다 probability distribution을 계산해줍니다 . 



## RNN Encoder - RNN decoder

![image](https://github.com/user-attachments/assets/58b3ff13-b735-4541-821f-d00cb8a1a700)  

RNN의 경우 Input X를 sequence로 표현한 input $$ ( x_1, x_2, \ldots, x_n ) $$ 으로부터 fixed size vector를 계산하게 됩니다. 이 때 , input 의 order를 고려하기에 casual strucuture 의 형태를 띄게 됩니다 . input으로 fixed size vector H를 계산하고 , 이를 Decoder에 input으로 넣어주게 됩니다. 
Decoder의 경우 Output을 계속 generation 하기 위해서 Auto-regressive Model이 됩니다. 여기에 추가적으로 , hidden state vector H가 주어지게 됩니다. 
따라서, 최종적으로  $$ P(Y \mid H) = \prod_{t=1}^{n} P(y_t \mid y_{1:t-1}, H) $$ 이와 같은 모델을 만들게 됩니다 . 
## CNN Encoder - RNN decoder


![image](https://github.com/user-attachments/assets/cad7feef-a119-4d85-99f1-b0ff47c532a7)  


이의 대표적인 예시로는 image captioning model 이 있습니다. RNN Encoder 에서는 sequence를 압축한 fixed size vector를 decoder의 input으로 주었다면 여기에선 CNN으로 압축한 fixed size vector를 Decoder의 Input으로 주게 됩니다. CNN으로 sequence를 압축할 수 있지만, 위의 예시와 차별점으로 두기 위해서 image로 생각해보겠습니다.

image의 input을 받게 된다면 , CNN을 통해서 어떠한 vector가 나오게 되고 , 이를 fully connected layer를 거쳐서 classification이나 object detection을 했습니다. 따라서, 이 vector는 image의 어떠한 high level information을 담고 있다고 볼 수 있습니다. 따라서, 이 vector를 decoder에 vector H로 input으로 주게 된다면 , 이 이미지에 해당하는 text를 generation하게 됩니다. 

## fixed size encoding의 한계점

fixed size encoding은 임의의 input을 항상 똑같은 size로 encoding하게 됨으로서 , information loss가 발생할 수 있다는 단점이 있습니다. 

![image](https://github.com/user-attachments/assets/1cf8f147-ad5f-470c-b74f-a799b462a1ee)   

위 그림은 RNN Encoder - RNN decoder로 Machine Translation을 시도했을 때 , sequence가 길어지면 길어질수록 번역 정확도가 떨어짐을 보여주는 그래프입니다. input sentence의 길이가 길고, output sentence의 길이가 점점 길어지면서 input sentence의 information 이 loss가 일어나게 됩니다. 결과적으로 , 말은 되지만 input과 관련이 없는 setence를 점점 generate 하게 됩니다. 

## token size encoding

이에 대한 해결방법중 하나로 fixed size가 아닌 input에 비례하는 size의 vector를 decoder에 input으로 준다가 있습니다. input size에 비례하는 vector를 decoder에 input으로 주게 된다면 상대적으로 information loss가 덜 할 것입니다. input sequence의 length, 즉 token 의 개수만큼 vector를 decoder에 input으로 주게 됩니다.

## RNN(attention) Encoder - RNN(attention)  decoder


![image](https://github.com/user-attachments/assets/bbeb7377-f585-40db-a259-8cb435bcc61b)    

여기에서 attention이라는 mechanism을 사용하게 됩니다. attention mechanism은 input seqeunce에 대해 weighted factor를 계산하게 됩니다. weighted factor를 매번 계산해서 각 decoder의 time step마다 다른 weighted factor를 사용하게 됩니다. 
그리고 , token size의 vector의 input을 통해 새로운 vector를 계산할 때 attention 을 사용하면 attention layer의 수는 token 개수에 independent하게 됩니다. 이론상으로 1개의 layer만으로 token 개수 만큼 information을 반영할 수 있습니다. 

각 token의 representation이 decoder에 input으로 주어지지만, 이 때 token은 sequence 전체의 의미를 담고 있는 representation이 됩니다. 

이 attention model을 수식으로 표현하면 아래와 같습니다. h는 각 token의 hidden state, s는 decoder의 hidden state를 의미하게 됩니다 .

$$  e_{ij} = a(s_{i-1}, h_j)  $$ 

$$ \alpha_{ij} = \frac{\exp(e_{ij})}{\sum_{k=1}^{T_x} \exp(e_{ik})}  $$


$$  c_{i} = \sum_{j=1}^{T_x} \alpha_{ij} h_j  $$

$$ s_i = f(s_{i-1}, y_{i-1}, c_i). $$  


## Transformer Encoder - Transformer decoder


![image](https://github.com/user-attachments/assets/9de83ee7-d0ab-4f95-84d6-a9d82f90dc34)  

기존 RNN 의 단점으로는 다음 step의 output을 계산하는데 이전 input의 output을 사용해서 parallelize 하기 어렵다는 단점이 있습니다 . 따라서 , 이러한 attention mechanism은 유지한 채, pallelize의 이점을 살리기 위한 architecture가 self-attention입니다.  이 self-attention을 이용해서 block을 만든 것을 transformer라고 합니다. 

self-attention 도 attention mechanism이지만 rnn attention의 형태와 사뭇 다릅니다. 우선  , Q,K,V라는 것이 등장하는데 , 이것을 query, key ,value로 생각하는 것보다 기존의 rnn attention과의 공통점, 차이점을 비교하는게 이해하기 더 쉽습니다. 


$$ q^t \cdot (k^i)^T $$


$$ \alpha_{ij} = \frac{\exp(q^i \cdot (k^j)^T)}{\sum_{t^{'}} \exp(q^i \cdot (k^{t^{'}})^T)} $$

$$ c_i = \sum_{j=1}^{T_x} \alpha_{ij} v_j $$  


q가 decoder의 hidden state가 되고 k,v가 input sequence의 hidden state가 됩니다. 다만, k는 weighted factor를 계산하는데 사용되어지고 , v는 input sequence 전체의 의미를 담고있는 token representation 이 됩니다. 
이러한 summation들은 matrix multiplication으로 표현할 수 있습니다. 따라서, 아래와 같은 유명한 식이 나오게 됩니다.   

$$\text{Attention}(Q, K, V) = \text{softmax} \left( \frac{QK^T}{\sqrt{d_k}} \right) V $$

self-attention은 sequence의 수식에서 보면 summation이기에 , sequence의 order가 고려되지 않습니다. 즉, Continuous bag of words와 같습니다. 따라서, 구현시에 여러가지 position을 고려해주는 방법들을 적용하는데, 그 중하나가 positional encoding 입니다.   

이 식을 Deocder 관점에서 해석하면 Decoder Model은 $$  P(Y \mid H) = \prod_{t=1}^{n} P(y_t \mid y_{1:t-1}, H) $$ 를 구하는 것입니다. 즉 , Q의 matrix는 매번 size가 달라지게 됩니다. 반면, K,V의 matrix의 size는 decoder 관점에서 매번 token의 개수만큼으로 고정이 됩니다.  

그리고, 여기까지만 봤을때 이상한 점이 하나 있습니다. 저희 Decoder Model은 $$  P(Y \mid H) = \prod_{t=1}^{n} P(y_t \mid y_{1:t-1}, H) $$ 을 풀어야 하는데 decoder의 hidden state q를 계산할 때에는 이전 q와 independent하게 연산이 되고 있습니다. 

![image](https://github.com/user-attachments/assets/f763679b-8444-4885-802c-25b09791c2e4)  

Transformer architecture를 보면 Decoder에서 전체 decdoer 의 hidden state를 input에서 먼저 multi-head-attention을 연산을 하게 됩니다. 따라서, $$( y1,y2 .. y^{t-1} )$$ 에 대해서  output의 hidden state을 계산해주고 , 그 다음에 source token representation으로 k,v를 계산한 다음에 decoder의 output을 계산합니다. 즉 ,$$  P(Y \mid H) = \prod_{t=1}^{n} P(y_t \mid y_{1:t-1}, H) $$ 의 문제를 풀고 있음을 알 수 있습니다. 

Decoder에서도 기존RNN 에서는 fixed size vector로 압축했지만, transformer에선 output의 token 개수 , 즉 infinite하게 token의 representation을 보관하여 output의 information loss가 발생하지 않음을 알 수 있습니다. 





## Vision Transformer Encoder - Transformer Decoder

![image](https://github.com/user-attachments/assets/b930a6c8-20c2-4127-a120-56349c0067dc)  

이 부분은 Encoder 부분만 보고 넘어 가겠습니다. Transformer에 어떻게 Image Input을 어떻게 넣을 수 있을까요? transformer가 input으로 인식할 수 있게 변환해주면 됩니다. token을 word embedding으로 변환하고 word embedding의 sequence를 input으로 받는게 transformer이지만, 사실 $$ d_{model} $$ 의 vector의 sequence를 input으로 받는 것입니다.  따라서, 어떠한 function으로 image를 $$ d_{model} $$ 의 vector로 mapping 해준 다음에 transformer의 input으로 사용하게 됩니다. 

위의 예시는, 비교적 초기 모델인 simple visual language model입니다. 
이 model의 경우 $$ \mathbf{x} \in \mathbb{R}^{H \times W \times C} $$ 의 image를 $$ \mathbf{x}_p \in \mathbb{R}^{T_i \times D} $$ 로 변환합니다. 이 때 , $$ T_i = \frac{HW}{P^2} $$ 입니다. 
## Reference


https://www.coursera.org/learn/nlp-sequence-models/home/week/3

https://www.boostcourse.org/ai331/joinLectures/195045

https://arxiv.org/abs/1505.04597   

https://arxiv.org/abs/1706.03762 

https://arxiv.org/abs/1409.0473  

