---
title : "What is Attention?Why softmax in hidden layer output?"



excerpt: "Attention and Explicit Memory"

categories:
  - DLArchitecture
tags:
  - [Machine Learning,Attention,deep learning ]
# classes : wide
toc: true
toc_sticky: true
---

2017년 Attention is all you need 논문이 나온후 2019년 Bert가 나오면서 Deep Learning에서 Natural Language domain이 급부상했다고 말해도 과언이 아닙니다.  Transformer라는 architecture가 등장하면서, NLP분야에서도 VISION 처럼 Fine-tuning이 가능해졌고 , 다양한 downstream task를 진행할 수 있게 되었습니다. Transformer라는 architecture는 여러개의 multihead attention으로 구성이 되어있고 , multihead attention은 attention 이라는 module로 구성이 되어있습니다. Attention module은 다른 deeplearning module과 아주 큰 차이점이 하나 있습니다. 바로 hidden layer임에도 불구하고 activation function으로 softmax function이 쓰이는 것입니다. softmax function은 input들을 probability space로 변환시킵니다.  통계기반모델은 probability을 계산해야 하기에 output layer에서 probability를 구하는데 사용되었습니다. 이에  대해, 여러 커뮤니티에 질문했지만 이에 대해 잘 아는 사람은 없었습니다 . 저도 포기하던 와중에 우연히 [Deep Learning](https://www.deeplearningbook.org/)(by Ian GoodFellow) book에 이에 대해 5~6pg 정도 할당해서 기술해놓은걸 확인할 수 있었습니다. (모르면 책을 찾아 보세요!)  .그리고 ,[Hands-On Machine Learning with Scikit-Learn, Keras, and TensorFlow: Concepts, Tools, and Techniques to Build Intelligent Systems: ](https://www.amazon.com/Hands-Machine-Learning-Scikit-Learn-TensorFlow/dp/1492032646)(by [Aurelien Geron](https://tensorflow.blog/tag/aurelien-geron/))   에서 찾아보면서 Attention 개념에 대한 더 detail한 이해를 하게 되었습니다. 오늘은 이에 대해서 다루어 볼까 합니다.(최대한 수식을 제외하고 정리하였습니다.)(본 글은 Transformer Architecture보다 Attention 모듈에 집중을 하기에 transformer architecutre에 대한 설명은 의도적으로 뺐습니다.)



2022-02-21 alignement modifed



## What is Attention ?

![image](https://user-images.githubusercontent.com/50165842/148772559-4c347b53-56e5-45d1-8cbe-5d1ebf479273.png)



Attention Mechanism을 이해하기 위해서는 Attention 이 나오기 이전에 Encoder-Decoder Model(Seq2Seq Model)이 어떻게 output을 계산했었는지 알아야 합니다.  Attention 이전에는 rnn 계열 모델이 linear transformation 후 directly 하게 output을 계산했습니다 . 왼쪽이 attention 이전에  sequence output을 계산하는 과정입니다. 위의 예시는 , 영어 문장 `milk drink I` 에서 불어 `je bois du lait` 를 번역하는 과정입니다. `milk` 에서 부터 `lait` 까지 굉장히 오래 걸림을 알 수 있습니다.  Attention 알고리즘은 `milk` 라는 단어의 context가 어떻게 하면 `lait` 까지 짧게 전달되게 할까를 해결하는 알고리즘입니다. Attention 알고리즘은 [1409.0473.pdf (arxiv.org)](https://arxiv.org/pdf/1409.0473.pdf) by[Dzmitry Bahdanau](https://arxiv.org/search/cs?searchtype=author&query=Bahdanau%2C+D), , [Kyunghyun Cho](https://arxiv.org/search/cs?searchtype=author&query=Cho%2C+K), [Yoshua Bengio](https://arxiv.org/search/cs?searchtype=author&query=Bengio%2C+Y). 에서 소개된 알고리즘 입니다.  . 오른쪽 그림에서 보면 , Decoder가 특정한 input 단어에 attention 하도록 model의 architecuture를 만들자는 것이 그들이 소개한 알고리즘 입니다.  hidden state $$h(i)$$ 가 weight  $$\alpha_{(t,i)}$$ 와의 operation 을 통해서 output이 계산이 됨을 볼 수 있습니다.  weight  $$\alpha_{(t,i)}$$ 는  `i-th` encoder의  `t-th` decoder 에서의 weight입니다. 예를 들어 , $$\alpha_{(3,2)}$$ 는 $$\alpha_{(3,0)}  , \alpha_{(3,0)} $$ 보다 attention을 상대적으로 더 하게 될 것입니다. 그리고 , 매 `t-th` 마다 decoder에서 encoder의 output과 hidden state를 concatenate(additive)를 할 것입니다.  Bahdanau  Attention는 TimeDistributed Layer를 거치기 때문에 , input sequenec가 n이라고 하면 $$ O(n**2) $$ 의 complexity 계산이 됩니다 . output sequence가 매우 길다면 quadratic order는 critical한데 , output이 천단위로 계산할만하기에 큰 영향은 없습니다.

이제 $$\alpha_{(t,i)}$$ 를 어떻게 계산하는지 알아보도록 하겠습니다. 예를들어 , $$\alpha_{(t,i)}$$ 를 계산할 때 , hidden state $$h(t-1)$$ 과 $$\alpha_{(t,i)}$$ 를 concatante 하게 됩니다. 그 다음에 , TimeDistributed(Dense Layer)(Time Distributed라는게 matrix로 병렬 연산을 의미하는듯)를 거치면서 $$e_{(t,i)}$$  를 계산하게 됩니다.  $$e_{(t,i)}$$   는 이전의 hidden state 와 얼마나 alignment 하는지 나타내는 value입니다. ( alignment score는 두 sequence 가 있을 때 , 두 sequence가 어느 정도로 연속적인지를 나타내는 점수정도로 생각하시면 편합니다. [Alignment and Alignment Score (titech.ac.jp)](https://www.gsic.titech.ac.jp/supercon/supercon2004-e/alignmentE.html)) 그 다음에 , 어느 $$y_{(i)}$$ 에 좀 더 attention을 할지 가중치를 두기 위해서 weighted sum을 계산하게 됩니다. 이 때  softmax를 이용해서 weighted sum을  계산합니다.메인 저자 Bandanau 의 이름을 따서 , Bahdanau Attention 혹은 concatenate(additive) attention 이라고도  불리기도 합니다 .

이 외에도 [[1508.04025\] Effective Approaches to Attention-based Neural Machine Translation (arxiv.org)](https://arxiv.org/abs/1508.04025) 에 논문에서 발표된  Luong eAttention 도 있습니다.  Luong Attention은 simliarity를 구하는게 목적이였기에 , TimeDistributed Layer(Dense) 대신에 dotproduct를 이용해서 similarity를 계산하였 습니다. 이는Luong Attention , 혹은 multiplicative attention이라고도 불립니다.  그들은 simliarity score를 계산하고 이를 Bahdanau Attention과 마찬가지로 softmax layer를 통해서 $$\alpha_{(t,i)}$$  를 계산하게 됩니다. 이들의 특이한 점은 decoder의 hidden state를 $$h(t-1)$$ 를 사용하는 것이 아닌  $$h(t)$$ 를 사용하였습니다. 그들은 dotproduct 방식 이외에 TimeDistributed layer를 통과시킨 general 방식을 제안했습니다. 그리고 , 이들은 이 방법을 concatanate 방식과 performance를 비교하였는데, dotproduct, general 방식이 concatanate 방식보다 performance가 더 좋음을 보였습니다. 따라서 , 현재는 concatanate방식은 잘 사용되지 않습니다. 실제로 , Transformer도 dotproduct방식을 사용합니다. 이 3가지를 수식으로 요약하면 아래와 같습니다._

$$
\begin{align}
\tilde {h(t)} &= \sum_{i=1} {y_i \alpha_{(i,t)}} \\
\alpha_{(i,t)} &= {\frac{exp{( e_{(i,t)})} }{\sum_{i=1}{exp{ (e_{(i,t)}) } }}} \\
e_{(i,t)} &= \begin{cases}  h_{(t)} y_{(i)} & \qquad dot  \\ h_{(t)}W y_{(i)} & \qquad general \\ v^TW_a[h_{(t-1)};y_{(i)}] & \qquad concatanate \end{cases}
\end{align}
$$




### Self-Attention



![image](https://user-images.githubusercontent.com/50165842/148861399-92bcde71-7df9-4ef0-bb30-8ed70f6f5eaa.png)



multiheadAttention 은 AttenionisAllyouneed 논문에서 소개된 transformers 모듈을 이루는 모듈입니다.  .Attention에 대한 정의를 하기 앞서 , Attention이 어떠한 식으로 score를 계산하는지 예시를 들어보겠습니다. 예를 들어 ,  {"주어" : "최웅준" , "동사" : "밥을 먹었다." , "어디에서" : "집에서"} 이러한 dictionary 가 있다고 가정을 하겠습니다. `최웅준은 밥을 먹었다.` 라는 문장이 나오면 다음에는 `어디에서` 라는 정보가 나와야 할 것입니다. 이를 알기 위해서 , 우리는 dictionary 에서 `어디에서` 라는 keyword를 찾을 것이고  , 거기에서 `집에서`라는 value를 얻을 것입니다. 그리고, `최웅준은 밥을 먹엇다 집에서` 라는 문장을 완성할 것입니다 .  여기에서 , 이 dictioary 의  key, value 그리고 , 문맥을 고려하는 query 를 이용하는 것이  multihead attention은 이루는 self-attention 모듈입니다. 

하지만, 이를 바로 구현하는데 사용하기에는 무리가 있습니다. 왜냐하면, attention 내부는 vector representation이기에 , `주어` , `동사` ,`어디에서` 라는 key중에서 `어디에서`라는 key를 선택해서 활용할 수 없습니다. 그렇다면, 이 vector representation을 어떻게 활용해야 할까요? 여기서 , 문맥적 정보인 `query` 와 `key`값의 유사도를 구하는 것으로 어떤 `key`  값에서 좀 더 가중치(attention)을 둘지 결정합니다. 유사도를 계산하기 위해서 dot-product를 이용합니다. 그 다음에  , softmax 함수를 이용해서 이를 sum 이 1인 가중치로 변환합니다. 이를 이용해서 , value값을 얻게 됩니다. 여기에서는 `어디에서` 라는  key를 표현하는 vector의 weight가 1에 가깝게 될 것이고 , 나머지는 `주어` , `동사` 를 표현하는 vector의 weight는 0에 가깝게 될 것입니다. 이에 대한 weighted sum을 계산하게 되면, `어디에서`  의 value값인 `집에서` 를 나타내는 vector값에 근사하게 될 것입니다.  이를 수식으로 표현하면 아래와 같습니다.



$$Attention(Q,K,V) =  softmax({ {QK^T} \over { \sqrt{d_{keys}} } } )    \cdot V$$

attention 수식에 대해서 좀 더 자세하게 코멘트를 달아보겠습니다.



- Q는 $$[n_{queries}, d_{keys}]$$ 의 shape를 갖는 matrix입니다.
- K는 $$[n_{keys} ,d_{keys}] $$ 의 shape를 갖는 matrix입니다.
- V는 $$[n_{keys} , d_{values}] $$ 의 shape를 갖는matrix입니다.
- matrix $$QK^T$$ 의 shape은 $$[n_{queries} :n_{keys} ] $$ 입니다.  그리고 , softmax function의 output도 같은 shape을 가지게 됩니다. 그리고 , 모든 row가 sum이 1을 가지는 weighted sum의 형태입니다.  $$QK^T$$ 의 shape은 $$[n_{queries} :d_{values} ] $$ 가 됩니다. 각 row가 query 1개에 대한 result라 봐도  됩니다.
- scaling factor $$\sqrt{d_{keys}}$$ 는 softmax 가 커지는 것을 방지하여 작은 graident 값을 계산해서 update할 수 있도록 해준다고 합니다.(왜 꼭 gradient의 크기를 낮춰야하는지는 잘 모르겠습니다.)
- 실제로 , MaskedMultiheadAttention은 구현할 때 , decoder에 있는 각 self attention에서 아주 큰 negative 값을 더해주는 technique를 사용하는데 이는 꽤 유용합니다.



그리고 , transformers에서 보면 encoder, decoder 파트가 있습니다. 여기에서 , encoder는 query, key ,value 가 모두 같은 input sentence 로부터 계산을 하게 됩니다.또한 , decoder 도 target sentence 로 query,key,value를 계산하게 됩니다. 하지만, decoder의 경우 $$ P(y_{t} \vert y_{<t}) $$ 를 계산해야 하기에  , 위에서 언급했듯이 , 아주 큰 negtaive 값을 이용해서 softmax 값을 계산할 때 뒤의 값들의 가중치를 0에 가깝게 만들어줍니다. 그리고 ,Decoder에서 MultiheadAttention Layer가 나오는데 , K,V는 encoder에서 만들어진 vector representation(dictionary) 를 사용하고 , Q는 maskedmulitheadattention에서 계산한 vector 를 사용합니다.

### Multihead-attention

![image](https://user-images.githubusercontent.com/50165842/148869343-08d9b859-0a3d-4438-9015-4002213dfc61.png)

그럼 왜 , attention을 1개가 아닌 여러개의 attention을 사용할까요? 예를 들어서 , 설명해보도록 하겠습니다. `최웅준은 집에서 집중이 안되서 카페에 공부하러 갔다 . ` 이라는 문장이 있습니다. 이 때 ,   {"주어" : "최웅준" , "동사" : "공부하러 갔다." , "어디에서" : "카페에"}   이러한 dictionary가 만들어 질 것입니다. `어디에서` 라는 key값에 대해서 `카페에` 라는 value값을 얻을 수 있습니다. 하지만, `카페에 왜 갔어` 라는 key값에 대해서는 기존의 dictionary로 답을 할 수가 없습니다. 문장에는 이 뿐만 아니라 여러개의 맥락적인 정보들이 담겨있습니다. 1개의 attention 만으로는 1가지 측면의 정보만을 담을 수 밖에 없을 것입니다. 따라서, 여러개의 attention으로 여러개의 vector space에 projection을 해서 sequence의 다양한 정보들을 표현하고자 합니다.

## Why Attention Weighted Sum? -> Detailed

Attention이 왜 weighted sum을 계산하는지에 대해서는 아직 설명하지 않았습니다.  연구자분들이 만들때 그냥 해보고 좋아서 만들어진 것이 아닙니다. 이에 대해 알기 위해서는 사람의 뇌가 어떻게 기억을 하는지에 대해서 알아야합니다. 

사람은 애매모호하고 , 무의식의 영역에 있으면서, 굉장히 복잡한 구조의 memory를 가지고 있습니다. 예를 들어, `사람이 걷는 방법` , `사람이 재킷을 입는 과정` 이 있습니다. 이 memory들은 우리가 일상생활을 하면서 자연스럽게 하는 무의식인 행동들입니다. 하지만, 이 memory들을 깊게보면 굉장히 복잡하기 때문에 , 애매모호한 기억들입니다. 사람이 의식하면서 기억하지 못하기에  implicit memory라 불립니다. 반면에 ,  굉장히 뚜렷하고 , 명시적인 기억들이 있습니다. 예를들어 , `콜라` ,`사과` 는 명확한 이미지를 가지고 있습니다. 그리고, `아침 8시에 약을 먹어야한다` 도 사람이 의식적으로 기억을 하면서 하는 행동입니다.사람이 의식을 하면서 기억할 수 있기에 explicit memory라 불립니다.(implicit memory , explicit memory 에 대한 정확한 설명은 아니지만 concept만을 잡기위해 대략적인 차이점만 언급을 합니다.)

Neural Network는 implicit memory를 저장하는데 특화되어 있습니다. 하지만, explicit memory를 저장하는데는 많이 부족했습니다 . Stochastic Gradient Descent는 input을 feedforward로 계산하고 이 내용이 바로 저장되는 것이 아니라 여러 layer를 거친다음에 update가 됩니다. 이 input들은 hidden layer를 거치면서 다른 hidden layer의 input으로 들어가기에 , 원래의 input과 달라지고 , 나중에 gradient descent를 계산해서 update할 때는 그 순간의 input정보가 update 되지는 않을 것입니다.(DeepLearning Book의 구절을 제가 나름대로해석해봤는데 맞는지는 모르겟네요.원문에서 10.12 418pg 에 해당하는 구절을 제가 해석해봤습니다.) . [Nueral Turing Machine](https://arxiv.org/pdf/1410.5401.pdf)  에서는 이를 사람과 같은 working memory의 부재로 생기는 문제점이라 주장합니다. 

### Working Memory?

![A Blackboard Full of Mathematical Formulas. Educational Concept Background  Stock Image - Image of complicated, education: 174283309](https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcRurJWypZIrq1oBcsypUfLwEMJwk813BrjcsA&usqp=CAU)

Working Memory는 사람이 학습을 하게되면 일시적으로 기억이 저장되는 장소입니다.제가 칠판 그림을 가져왔는데 , Working Board는 BlackBoard와 유사한 성질을 가지고 있습니다. 선생님이 수업을 가르칠 때 , 어떤 내용을 칠판에 적어서 학생들한테 설명을 합니다. 그리고,시간이 지나면서 다른 내용을 적게됩니다. 그 이전 내용은 , 선생님이 지우개로 지울것입니다.  사람이 공부를 할 때도 , 그 순간에 공부한 것은 기억이 나지만, 시간이 지나면 지날 수록 까먹게 됩니다. 

사람은 그 순간의 working memory를 이용해서 다음 행동을 어떻게 할지 결정할 수 있습니다. working memory에서 어떤 정보를 가져올지 , working memory에 어떠한 새로운 정보를 기록할지 매 순간 결정합니다. 사람이 input이 들어오면 자동적으로 반응하는게 아닌, 추론을 하고 행동하는 능력이 있습니다. neural net에게 이러한 능력을 줄 수 없을까 하고 나온게 Memory Cell입니다.



### Memory Cell 



![image](https://user-images.githubusercontent.com/50165842/148873967-48e6136a-6c62-4bcd-9693-7a64bcd72568.png)



그림을 보시면, Neural Net이 있고 , Neural Net이 어떤 task를 수행합니다. 그러면서 , 외부 input이 들어오면 controller(nueral net) 이 memory에  쓰기도 하고 , output을 내야할때 memory를 읽고 output을 내기도 합니다. Ian Goodfellow가 동영상에서 설명하는부분이 있는데 좋은 예제인거 같아서 이 예시도 설명하겠습니다. 예를 들어 ,제가 내일 오전 8시 30분에 매주 있는 정기적인 독서모임을 나가기로 했습니다. 지금 , Ian의 meorycell에서  "내일 오전 8시 30분 독서모임" 에 gating function(actviation function)이 1로 활성화가 되어 있습니다. 갑자기, 오늘 오후 9시에 나의 boss한테서 연락이 옵니다. "Ian, 내일 투자 설명회에 참가해야하니까 오전 9시에" , "oh my god", 그러면서, Ian의 memory cell의 "내일 오전 8시 30분 독서모임"  에 해당하는 부분에서 gating function(actviation function)이 0으로가까워지고 , "내일 오전 9시 회의" 에 해당하는 부분의 gating function(activation function) 이 1로 가까워지게 됩니다. 즉, memory cell에서 input으로 들어오면서 writing을 하는데, input 에 따라 activation이 1에 가까워지기도 0에 가까워지기도 합니다. external output이 필요할 떄는 memory를 읽습니다. 이 task network는 어떻게 memory를 `control` 하는지를 `학습` 합니다. 어떤 memory를 활성화 시키고, 다른 memory에 따라 다른 기억들이 비활성화 될 수도 있어야하기에 softmax를 사용했다고 생각합니다.

처음에 이러한 시도를 Memory Network라는 architecture으로 시도를 하였습니다.  하지만 Memory Network는 이러한 memory를 어떻게 쓸지에 대해서 supervised Learning으로 학습을 해야 했습니다. 따라서, 이를 보완해서 neural turing machine이라는 architecture가 나왔습니다. neural turing machine은 memory cell에대한 supervised signal 없이 end-to-end로 학습이 가능하였습니다. 이 neural turing machine 은 content-based soft-attention mechainism을 사용했습니다. 이러한 soft-attention mechanism은 gradient descent optimization은 허용했기에 2015년 대에부터 standard 한 architecuture가 되었습니다.

이 Memory Cell은 GRU, LSTM architecture의  cell state가 vector로 확장되었다고 생각하면 된다. GRU, LSTM도 vector인데 , vector로 확장되었다는 표현이 이상하게 들릴 수 있다.cell state에서 는 scalar의 set이지만, Memory Cell은 vector의 set이다. GRU,LSTM과 Memory cell의 차이점은 memory 에  read and write를 무엇을 할지 결정을 하냐 안하냐의 차이입니다. 이는 , 마치 computer 가 memory address값을 받고 memory에 access하는 것과 마찬가지 입니다. 그렇다면, 왜 도대체 cell state에서 더 큰 용량의 vector를 사용하게 된 것일까?

### Why Memory Cell use vector?

#### soft-alignment

![image](https://user-images.githubusercontent.com/50165842/154868602-0d267e51-2917-4cb7-92f0-bd13984b85d4.png)

Memory Cell은 address Location  방식으로 참조를 하게 되는데 , 이 때 기존의 방식으로는 read 와 write를 할 integer address를 정확하게 근사(approximation) 하기가 어려웠습니다. 따라서 , Memory Cell에서 read , write를 동시에 하는 방법으로 이를 해결하고자 했습니다. 동시에 ,모든 memory address를 읽고 가장 가능성이 높은 address에 access를 하는 것입니다. 즉, Read를 하기 위해서 weighted average으로 memory에서 값을 읽고  각 memory에 다른 값을 write 했습니다. memory 에 operation을 하기 위한 coefficient로 작은 수의 memory에 집중을 하게 해주었습니다. 이 때  , softmax function을 사용하게 되었습니다. 이러한 , non-zero deriative를 통해서 graident descent를 통해서 optimization을 하게 됩니다. coefficient의 gradient 는 어떤 weight를 키울지 ,줄일지를 정하지만 ,  coefficient가 큰 값에 해당하는 row는 gradient가 커지기에 , 이에 영향을 받습니다.

이를 좀더 detail 하게 설명하면 hard-alignment 와 soft-alignment의 차이라 할 수 있습니다. 왼쪽은 hard-alignment방식을 사용하는 방식이고 , 오른쪽은 soft-alignment를 사용하는 방식으로 attention score를 계산하는 module입니다. 두 방식 모두 alignment vector를 softmax로 계산하는 공통점이 있습니다. 하지만, hard-alginment 방식을 사용하게 어떤 단어의encoding state를 해줘야 할지 hard-coding으로 labeling을 해줘야 합니다. 하지만 , soft-alignment 방식은 weighted-sum을 구하기에 hard-coding으로 labeling(alignment)를 해줘야할 필요가 없게 됩니다.

여기서 , 주목할 점은 alignment vector를 직접 hard-coding으로 desired한 output을 지정하지 않는다는 의미는 이를 더이상 latent variable로써 고려하지 않는다는 것입니다. 즉, 쉽게 말하자면 별개의 parameter를 가진 확률분포로써 encoder-decoder model과 별개로 고려하는게 아니라 , 전체 모델과 함께 gradient descent로 train이 가능해졌음을 나타냅니다.

#### computation cost reduced



위의 얘기가 Memory Cell이vector를 사용하는것과 무슨 상관인가 라고 생각이 드실 수 있습니다. Memory Cell을 vector의 set으로 사용한 첫번째 이유는 이미 동시에 read하도록(weighted average) computation cost를 늘렸기 때문입니다. 이미 , computation cost가 약 $$O(N^2) $$ 로 늘어났기에 , vector를 read함으로써 이 비용을 상쇄시킨다고 합니다. (이 ~~부분이 좀 애매했습니다. 시간을 들여서 고민을 해봤는데 , neural turing machine , NTM에서는 shape (N,M) 특정 위치를 approximation 하지 않고 , 동시에 read해서 weighted avearge를 구하는 방식이였습니다. 앞에서 , 문제점으로 언급한게 정확한 integer address를 특정하는 것이였습니다.정확하게 특정하는 부분이 sequential  연산이고 , vector값으로 읽으면 병렬 연산으로 matrix multiplication을 하기에 계산상의 비용을 좀더 상쇄시켰다는 말 같습니다.~~ ) 

original paper[1]의 A.1.2 항목을 보면 이에 대한 설명이 나와있습니다.  
$$\begin{align} a(s_{i-1} , h) = e_{ij} = v_a^T \tanh (W_as_{i-1} + U_ah_{j}) \end{align}$$

보통은 decoder의 hidden state s 와 encoder의 hidden state h를 concatenate해서 계산을 한다 하는데 , 논문에서는 encoder 의 hidden state 를 U라는 matrix를 이용해서 미리 precomputation을 한다고 합니다. 왜냐하면, timestep i에 dependent하기 때문입니다. 모든 timestep에 대해서 미리 계산을 해놓고 사용하므로 compute cost를 줄인다는 의미라고 생각합니다.(책의 저자가 yousha bengio이기 때문에 논문에서 근거를 찾았습니다.) . computation cost를 상쇄시킨다는 의미는 아마도 기존의 hard-alignment 방식과 대조해서 설명한거 같습니다. 기존의 hard-alignment는 매 time-step마다 fully-connected layer에 concatenated 해서 input으로 주어서 output을 계산해야 하므로 , timestep에 dependent하기에 시간이 상대적으로 더 오래걸린다고 설명하는거 같습니다 .

#### content-based 

두번째로는 ,content-based reading을 하기 위해서입니다.  기존의  LSTM, GRU `cell state`는 모든 문서의 정보에서 어떤 특정한 pattern에 일치하는 문서를 생성하게 됩니다. sigmoid를 통해 그저 값이 크면 가중치를 올리고 값이 낮으면 가중치를 낮춥니다. 하지만, memory network는  어떤 pattern에 부합하는 정보를 찾을지 선택하기 때문에(coefficient , weighted average) 몇몇 문서만을 조회해서 정보를 찾습니다.(key,value dictionary).  예를 들어 , `코로나` 라는 단어가 들어간 문서를 찾기 위해서는 `코로나` 라는 keyword에 그루핑 되어 있는 문서들만을 검색 할 것입니다. 반대로, location based는 content 를 참조하지 않습니다. 이것은 , `문서함 347번째에 있는 문서를 가져와라` 라는 것과 유사하게 볼 수 있습니다.

이를 Transformers에서 MultiheadAttention에서 비유하면 Decoder에서 multiheadattetntion이 
$$softmax(QK^T)$$ 
를 계산하는 것과 유사합니다. Transformer에서도 Decoder가 time step t에서 어떤 정보를 갖고있을지를 vector로 나타냅니다.Transformer에서 Memory cell에 대응되는 것이 V라 볼수 있고 , 이에 대한 weighted average를 구하기 위한 matrix가 
$$softmax(QK^T)$$ 
라 볼 수 있습니다. Transformer에서도 V에서 가장 관련이 높은 content의 address를 찾는 것이 아닌 weighted average를 구함을 볼 수 있습니다.

## Attention in optimization view

attention이 무엇이고 , attention에서 왜 hidden layer output으로 softmax 를 사용하게 되었는지 그리고 weighted sum을 왜계산하는지, 그리고 어째서 $$Attention(Q,K,V) =  softmax({  {QK^T} \over {\sqrt{d_{keys}}}  })    \cdot V$$ 
의 형태로 QK , V를 matrix multiplication을 했는지 알아 보았습니다. 이번에는, gradient optimization 관점에서 기존의 LSTM,GRU보다 어떤 이점이 있는지 알아보도록 하겠습니다. Explicit memory가  LSTM,GRU와는 다르게 상대적으로 propagation이 잘 되고, backpropagation이 잘 됩니다. hidden layer를 거치면서 cell state가 LSTM, GRU 기반의 seq2seq 에서는 많은 layer를 통과하기에 gradient가 explode 되거나 vanishing 될 확률이 높습니다. 하지만,Explicit memory는 attention mechanism을 적용해서 각  output에 대하여 gradient 계산이 가능하기에 , context를 weighted average로 계산하지 않고 directly 하게 계산하는 GRU, LSTM seq2seq과는 다르게 ,gradient descent 를 이용해서 학습을 할 수 있습니다.(respectively long duration )



# Reference

[NEURAL MACHINE TRANSLATION BY JOINTLY LEARNING TO ALIGN AND TRANSLATE Dzmitry Bahdanau Jacobs University Bremen, Germany KyungHyun Cho Yoshua Bengio∗](https://arxiv.org/pdf/1409.0473.pdf)

[Deep Learning (deeplearningbook.org)](https://www.deeplearningbook.org/)

[Hands-On Machine Learning with Scikit-Learn, Keras, and TensorFlow: Concepts, Tools, and Techniques to Build Intelligent Systems: Géron, Aurélien: 9781492032649: Amazon.com: Books](https://www.amazon.com/Hands-Machine-Learning-Scikit-Learn-TensorFlow/dp/1492032646)

[Learning Deep Learning: Theory and Practice of Neural Networks, Computer Vision, Natural Language Processing, and Transformers Using TensorFlow: Ekman, Magnus: 9780137470358: Amazon.com: Books](https://www.amazon.com/Learning-Deep-Processing-Transformers-TensorFlow/dp/0137470355)

