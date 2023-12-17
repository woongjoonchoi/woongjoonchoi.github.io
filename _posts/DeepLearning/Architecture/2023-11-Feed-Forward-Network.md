작성중-23.11.13


## Why we need understand Neural Network in Mathmatical view?
NeuralNetwork를 인간이 이해할 수 있는 logic으로 표현하는 방법중 하나는 바로 수식입니다. 수식으로 표현하는 방법을 알아야만 하는 이유로 크게 몇가지 있다고 생각합니다.
첫번째로 , Neural Network를 공부하는 이유는 이 neural Network를 컴퓨터로 구현하여 사용하기 위함입니다. 컴퓨터로 구현한다는 것은  머릿속의 로직을 코딩함을 의미합니다. 
## Term Definition
여기서의 Term에 관한 Symbol이나 Notation 방식은 Andrew 교수님의 DeepLearning 강의를 참고하였습니다.

|기호|설명|
|-------------------------------|-----------------------------------------------------------------------------------|
|l| $$l $$번째  layer  . $$ l $$ =   0, ... , L  은 weights와 bias를 가지는  layer의 번호를 의미한다.|
|$$n^{[l]}| $$ l $$ 번째 layer의 node의 개수를 의미합니다.|
|$$j$$|$$j  = 0,..., n^{[l]}$$|
|$$k$$|$$k = 0, .... n^{[l-1]} $$|
|$$m$$|$$m$$ 은 training step에서의 batch size 입니다.|
|$$i$$|$$i = 0,..... m$$|
|$$w_{j,k}^{[l]}$$| $$l $$ 번째 layer의 weight $$ W^{[l]} $$ , $$ W^{[l]} \in \mathbb{R}^{n^{[l]} \times n^{[l-1]} } $$ . $$ W^{[l]} $$ 의 $$(j,k) $$ 원소를 의미한다.|
|$$z_{j,i}^{[l]}$$| $$l$$ 번째 layer의 bias를 더한 output의 $$(i,j)$$ 성분입니다 . |
|$$a_{j,i}^{[l]}$$|$$l$$ 번째 layer의 activation의 $$(i,j) $$성분입니다 .|
|$$b^{[l]}|$$l$$ 번쨰 layer의 bias입니다.$$ b^{[l]} \in \mathbb{R}^{n^{[l]}  } $$ |
|$$g_{j}^{[l]}| $$l$$ 번째 layer activation function $$g_{j}^{[l]} : \mathbb{R}^{n^{[l]}} \rightarrow \mathbb{R}^{n^{[l]}} $$  , $$g_{j}^{[l]} \in \mathbb{R}^{n^{[l]}  } $$|
## Explanation in Mathmatical View

$$w_{j,k}^{[l]}$$
$$l 번째$$
$$ W^{[l]} $$
$$ W^{[l]} \in \mathbb{R}^{n^{[l]} \times n^{[l-1]} } $$
$$j  = 0,..., n^{[l]}  \quad k = 0, .... n^{[l-1]} $$
$$(i,j) $$
$$g^{[l]} : \mathbb{R}^{n^{[l]}} \rightarrow \mathbb{R}^{n^{[l]}} $$ 
$$g_{j}^{[l]} \in \mathbb{R}^{n^{[l]}  } $$
## Reference

feedforward-neural-networks-part-1/journalsim From Jonas Lalin  
1. [Link](https://jonaslalin.com/2021/12/10/feedforward-neural-networks-part-1/)  
