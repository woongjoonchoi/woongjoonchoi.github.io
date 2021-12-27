---
title : "Pretrained Language Model"



excerpt: "PLM"

categories:
  - NLP
tags:
  - [Machine Learning,NLP,deep learning ]
# classes : wide
toc: true
toc_sticky: true
---
## PLM(Pretrained Language Model)

Billion 단위의 Parameter로 이루어진 PLM(Pretrained Language model)이 오픈소스로서 공유되어 있습니다. Bert 등장이후, 언어의 representation을 학습하고 이를 다양한 downstream task에 맞게 fine-tuning의 시도가 계속 이어지고 있습니다. 그리고, 대형 연구기관(Ex. Google, DeepMind, OpenAI…. etc) 에서는 계속해서 대형 PLM 을 논문으로 발표하고 있습니다. 저도 예전에는 대형 PLM을 만들 줄 알아야 한다고 생각했지만, 요즘은 생각이 바뀌었습니다. 세상에는 이미, 다양한 PLM들이 오픈소스로 공유되어 있습니다. 이들의 Module은 비슷하지만, 각각이 훈련된 configuration 및 학습한 task들이 상이합니다. 그뿐만 아니라 PLM을 학습시키는 것은 엄청난 CO2 발생을 야기합니다. 지구의 탄소 문제가 가뜩이나 심각한데, AI 붐이 일어나면서 너도, 나도 Pretrained Model을 만들려 하면, 더욱더 악화할 것입니다. 이러한, 각 모델의 차이점을 잘 인지한다면 PLM을 학습하지 않아도 상황에 맞는 solution을 찾을 수 있습니다. (이 글은 네이버 BoostCamp AI Tech 2기의 강의자료를 사용합니다.)

2021-12-27 Bert, Roberta, GPT2 만 작성

## Bidirectional Encoder

![image](https://user-images.githubusercontent.com/50165842/147475191-30162c43-7a87-45cc-b26c-3fafe2ea11ea.png)

Bidirectional Encoder 은 기존의 Transformer의 구조에서 Encoder 부분만을 가져와서 사용한 것입니다.우선 가장 대표적인 Bert에 대해서 알아보도록 하겠습니다.

### Bert

![image](https://user-images.githubusercontent.com/50165842/147475435-ab04d211-a126-4227-b6ed-b8d192aa87eb.png)

Bert(Bidirectional Encoder Representations from Transformers)는 위에서 언급했듯이 Transfomer의 Encoder 구조를 이용해서 만든 모델입니다. Sentence1 , Sentence2 를 input으로 주어서 두 문장이 연속된 문장인지 맞추는 Next Sentence Prediction Task를 수행했습니다. 

![image](https://user-images.githubusercontent.com/50165842/147476098-25a718b0-faf7-40e9-aa7c-429585b050fb.png)

그리고, 토큰에 Mask를 넣어서 이를 예측하는 task를 수행합니다. 이 이미지는, BoostCamp 강의 자료에서 가져왔습니다. 이미지로 따지자면, 구멍을 뚫어 놓고 이를 복원을 하는 작업을 시키는 것입니다. 하지만, Bert의 Masked Token Prediction은 진정한 Bidirectional Encoder를 실현했다고 보기 어렵습니다. 왜냐하면, Mask 토큰을 예측하는 것은 독립적이기 때문입니다. 이를 설명하기 위해서 예시를 들어보겠습니다.

```python
A = ['일론' ,'머스크는'  ,'테슬라의' ,'Co-Founder이다' ]
Masked_A [ 'MASK' , 'MASk' ,'테슬라의' ,'Co-Founder이다' ]
```

이렇게 두 개를 MASK 처리 했을 때 이를 각각 독립적으로 `'일론'` `'머스크는'`  를 예측하게 됩니다. 실제로 , 앞 단어가 `'일론'` 이 나왔을 때 뒤의 단어가  `'머스크는'`  일 확률이 높을 것입니다. 즉, `'일론'`  ,   `'머스크는'`  는 독립적이지 않은데 이를 독립적으로 예측을 합니다. 그럼에도 불구하고, Bert가 처음 나왔을 당시 다양한 DownStream Task에서 좋은 성능을 보여주었기 때문에  sensational 한 모델이라고 말할 수 있습니다.  

```python
 A = ['일론' ,'주커버그는' '테슬라의' , 'Co-Founder이다' ]
```

또한, Bert는  토큰을 무작위 로 치환하기도 합니다. 이를 random replacing이라고 합니다.   

Bert는 input 시퀸스의 토큰중 15%를 무작위로 선택해서 80%는 mask로 치환하고  10%는 randomly replacing, 10%는 unchanged하게 두었습니다. 

즉 , 다시 말하자면 Bert는 Pretraining task로 Next Sentence Prediction , Masked LM task를 수행했습니다.

### Roberta

Roberta(A Robustly Optimized BERT Pretraining Approach) 는 Bert를 어떻게 학습할지에 대해서 고민한 모델입니다.

| Bert          | Roberta         |
| ------------- | --------------- |
| NSP           | WithoutNSP      |
| 128           | 512             |
| Fixed Masking | Dynamic Masking |
| 256           | 2K ,8K          |
| 16gb          | 160gb           |

- 우선 Roberta논문은 NSP task가 Bert의 성능에 중요한가에 대한 의문을 보여주는 논문에 근거하여 NSP task를 제거하고 pretraining을 했습니다. 
- 또한 , bert 는 90%의 data를 128 length 의 token sequence로 학습시킨 반면 roberta는 512 length의 token sequence로 학습을 시켰습니다. 
- Bert는 매 epoch마다 Fixed Masking으로 같은 Mask 토큰을 예측 하는 학습을 진행한 반면, Roberta는 매 epoch마다 Mask 위치를 다르게 하는 Dynamic Masking을 사용했습니다. 
- Bert는 256의 batch size를 사용한 반면 , roberta는 2K,8K의 batch size를 사용했습니다.
- Bert는 16gb 정도를 썼다고 하지만, Roberta는 160gb 의 corpus를 사용했습니다. 이는 더 많은데이터 , 더 많은 학습시간으로 이어집니다.

![image](https://user-images.githubusercontent.com/50165842/147479411-ac8c7489-ef64-41de-b0bb-6c1e9138c2a5.png)



위 와 같은 결과가 나왔다고 논문에 적혀 있습니다.

## Autoregressive Decoder



### GPT2



## Encoder-Decoder



### XLM



### T5