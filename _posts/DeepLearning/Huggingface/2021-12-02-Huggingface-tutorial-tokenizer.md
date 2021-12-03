---
title : "Huggingface tutorial: Tokenizer summary"



excerpt: "Tokenizer summary"

categories:
  - Huggingface
tags:
  - [Machine Learning,Huggingface,deep learning ]
# classes : wide
toc: true
toc_sticky: true
---

## Huggingface tutorial 시리즈 : tokenizer

Huggingface tutorial 시리즈중 tokenizer 편을 듣고 정리한 글입니다. [Summary of the tokenizers](https://huggingface.co/docs/transformers/tokenizer_summary)

## What is tokenizer

tokenizer란 sentence를 sub-word 혹은 word 단위로 쪼갠후 이를 look-up-table을 통해 input ids로 변환하는 프로그램입니다. Huggingface tutorial에서는 특히 transfomers 기반의 모델들에 사용되는 tokenizer를 살펴보게 됩니다.

![image](https://user-images.githubusercontent.com/50165842/144427223-af53b57a-f908-4b5c-b584-a425113a9cde.png)



## word-based tokenizer

word-level 단위로 tokenizing하는 tokenizer는 여러개가 있습니다.

space를 기준으로 tokenizing 하는 tokenizer 입니다. space를 기준으로 split하게 되면 아래와 같이 됩니다.

![image](https://user-images.githubusercontent.com/50165842/144427638-cb31e5ee-6438-484c-bb21-027ea69988e5.png)

문장부호를 기준으로 tokenizing 하는 rule을 만들 수도 있습니다.

![image](https://user-images.githubusercontent.com/50165842/144427823-345b4540-0a7b-410b-9146-5179cf3f0a96.png)



이를 look-up-table에서 input_ids 로 변환합니다. 

![image](https://user-images.githubusercontent.com/50165842/144427467-50990986-433e-4005-a1eb-70126e6d0e0e.png)

word-based의 문제점에 대해서 알아보도록 하겠습니다.

very large vocabulary size



![image](https://user-images.githubusercontent.com/50165842/144538921-1a218243-073e-4f1c-bcc2-aa80e0f3195c.png)

dog, dogs라는 단어가 있습니다. 이 두 단어는 비슷한 의미를 지닙니다. 하지만, split based tokenizer를 사용하게 되면 각각 다른 ids를 가진 단어로 look-up table에 mapping이 됩니다. 그러므로, 엄청난 크기의 vocabulary가 생기게 됩니다. 이 많은 단어를 embedding 하기 위해서 더 많은 parameters 들이 필요하게 됩니다.

![image](https://user-images.githubusercontent.com/50165842/144539705-dd0a62eb-9140-4ed3-8928-536bf6590804.png)

### fequently word

단어 수를 제한하기 위해서 출현 빈도가 높은 단어들을 위주로 tokenzing을 하는 것은 위의 문제를 해결할 수 있습니다.

![image](https://user-images.githubusercontent.com/50165842/144539738-7071ea88-ca0a-4eca-b644-138759e0a338.png)

하지만, 이렇게 될 경우 OOV(out of vocabulary)문제에서 자유롭지 못합니다. 

![image](https://user-images.githubusercontent.com/50165842/144540125-a26340ba-d0b1-429f-a58d-049264086b57.png)

단어장에 malapromisms라는 단어가 없기에 정보의 손실(loss of information) 이 발생합니다. Representation 능력이 떨어진다고 볼 수 있습니다.![image](https://user-images.githubusercontent.com/50165842/144540337-88941576-3459-4e24-8f8f-792999703c62.png)

UNKNOWN WORDS에 대해서 똑같은 representation을 가지므로 사람의 기준에서 봤을 때는 다르지만, input_ids로 변환을 한 경우 같은 표현을 가지는 여러 문장이 생길 수 있습니다. 다른 의미를 가지는 문장에 대해서 같은 representation을 가지므로 정보의 손실이 발생한다고 이해하시면 좋을 듯 합니다.

### punctation representation

위의 얘기와 비슷한 맥락이긴 하지만 다른 category라 생각하고 분류해봤습니다.

```python
"Don't you love 🤗 Transformers? We sure do."

["Don't", "you", "love", "🤗", "Transformers?", "We", "sure", "do."]
```

문장과 space-based split을 한 토큰들입니다. 여기서 주목해야 하는것은  "Transformers?"와 "do." 입니다. 이 두 개의 토큰 뒤에는 문장부호가 붙어있습니다. 위에서의 dog, dogs의 예시처럼 문장부호 단어의 조합은 많기 때문에 vocabulary의 조합을 증가시키게 됩니다. 이는, large size의 model로 이어지게 됩니다. 이를 해결하기 위해서 punctuation-based split을 적용할 수 있습니다.

```python
["Don", "'", "t", "you", "love", "🤗", "Transformers", "?", "We", "sure", "do", "."]
```

하지만, 이 방법에도 단점은 있습니다. "Don't"라는 토큰은 "Don" ,"'","t"로 토크나이징이 됩니다. "Don't" 는 "do not"을 의미합니다. 따라서, "Do" , "n' t" 로 토크나이징 되는 것이 바람직합니다. 여기서부터 case가 복잡해지기 시작합니다. 각 모델들이 고유한 tokenizer를 가지고 있는 이유이기도 합니다. 동일한 텍스트에 대해서 다른 규칙(tokenizer)을 적용하게 되면 다른 representation(token화된 text)이 결괏값으로 도출됩니다.PLM(Pretrained Language Model) 은 training data를 토큰화하는데 사용한 규칙을(Tokenizer) 사용하지 않으면 성능이 잘 나오지 않습니다.  

### summary

이러한 rule-based tokenizer의 유명한 라이브러리는[spaCy](https://spacy.io/)와 [Moses](http://www.statmt.org/moses/?n=Development.GetStarted)가 있습니다. 유명한 모델로는  [Transformer XL](https://huggingface.co/docs/transformers/master/en/model_doc/transformerxl) 가 있습니다. 이 모델의 vocabulary size는 무려 267,735입니다. 보통, big size vocabulary는 embedding layer의 size가 커지는 것을 야기하기 때문에 time complexity 와 memory 사용량을 증가시킵니다. 보통 , 단일언어모델은 50000개 보다 작은 vocabualry size 를 가집니다.  





## Character-level

문자 단위로 tokenizing을 하는 tokenizer입니다.  장점으로는 vocabulary size가 작다는 것에 있습니다. 



### small size vocabulary

![image](https://user-images.githubusercontent.com/50165842/144560709-91809e82-6e5d-4663-917d-51b7f9e09e37.png)

out-of-vocabulary issue가 상대적으로 덜 발생하게 됩니다.

![image](https://user-images.githubusercontent.com/50165842/144560588-ef680f88-3c17-4fe2-8b9c-571c9343a28e.png)



아까 위의 예시에서는 , malapromisms를 UNKNOWN 토큰으로 분류했지만, character-level tokenizer는 그 단어를 토큰화 합니다.

### poor representation

이에 따른 단점도 존재합니다. 우선, single character의 meaningful representation을 학습하는 것이 매우 어렵습니다. 예를 들면 , 'a'라는 character의 representation을 학습하는 것보다 'apple'이라는 단어의 representation을 학습하기가 더 쉽습니다. 또 다른 예로는, 위 사진에서 'l'이  많은 정보를 보유한다고 가정하는 것은 모든 상황에 대해서 사실이 아닐 수 있습니다. 한자 같은 표의 문자의 경우 문자 자체가 많은 의미를 담는 경우가 많습니다. 하지만,  로마자, 한글, 영어 같은 표음문자는 각 문자 자체가 많은 의미를 담고 있지는 않습니다.

### large size sequence

![image](https://user-images.githubusercontent.com/50165842/144560651-caa6d214-539c-432f-af95-47ab4bd65d0b.png)

토큰의 숫자가 많이 반환되기 때문에 , sequence의 길이가 길어집니다.대부분 transformer 계열들은 sequence의  length가 제한이 되었기에 truncation 이되고 token화 된 문장은 원래 문장의 정보를 다 담지 못하게 됩니다.



