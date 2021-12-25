---
title : "Huggingface:Datasets"



excerpt: "Huggingface Datasets"

categories:
  - Huggingface
tags:
  - [Machine Learning,Huggingface,deep learning ,Dataset]
# classes : wide
toc: true
toc_sticky: true
---
## Huggingface Datasets

Huggingface에서는 Datasets라는 Module을 제공해줍니다. 이번 글에서는  Huggingface 의 Datasets에 대한 소개와 제가 자주 쓰는 간단한 method 및 attribute들을 소개해볼려 합니다. 

## Datasets Arrow

Huggingface Datasets는 dataset을 external filesystem으로 부터 loading을 하게 되면 local 에 arrow로 caching합니다. Arrow는 대용량의 data를 빠르게 processing 하기 위해 고안되었습니다. Arrow는 특히 column orientated data에 특화되어있습니다.

추가 특성은 자세히 알게 되면 다시 업데이트 하도록 하겠습니다.

## load_dataset

![image](https://user-images.githubusercontent.com/50165842/147385759-663a26d3-a631-4bf5-a3c0-c96d70276d8a.png)

Huggingface Datasets는 csv,txt , json, parquet format으로 부터 Datasets 클래스를 생성할 수 있도록 지원합니다. load_datasets은 Dataset dict를 반환하며 key를 지정해주지 않으면 default로 'train' 이라는 key에 mapping이 됩니다.

### txt

```python
load_dataset('txt' , data_files='my_file.txt')
```

txt파일을 로딩할려면 data_files에 path와 txt 타입임을 명시해주면 됩니다. text file은 line by line으로 data를 읽어서 datasets dict를 만듭니다.

### csv

```python
load_dataset('csv' , data_files='my_file.csv')
```

csv 파일을 로딩할려면 data_files에 path와 csv 타입임을 명시해주면 됩니다.csv file도 마찬가지로 line by line으로 data를 읽어서 datasets dict를 만듭니다.

### json

```python
from datasets import  load_dataset

load_dataset('json' , data_files='my_file.json')
```

json파일을 로딩할 때는 2가지 유형으로 구분할 수 있습니다. 첫번째는 파일의 모든 row들이 각각 json object인 경우입니다. 이 경우에는 data_files argument에 file의 path를 넘겨주면 됩니다.

```python
load_dataset('json' , data_files = 'my_file.json' , field='data')
```

2번째 경우는 json file이 여러 field로 nested 하게 구성이 되어있습니다. 제 개인적인 생각으로는 하나의 큰 datasets안에 여러개의 table이 있는데 , 필요한 table만 loading하는 것과 비슷하다고 생각합니다. 이 때에는, field 라는 argument에 어떤 field를 loading 할것 인지 지정해줘야 합니다.

~~~python
load_dataset('json' , data_files = {'train' : 'my_train.json' , 'validation' : 'my_valid.json'} ,field = 'data')

```
    DatasetDict({
        train: Dataset({
            features: ['id', 'context', 'question', 'answers', 'class_type'],
            num_rows: 226887
        })
        validation: Dataset({
            features: ['id', 'context', 'question', 'answers', 'class_type'],
            num_rows: 18116
        })
    })
```
~~~

2개 이상의 file을 datasets dict에서 지정하는 key값에 mapping할 수 도 있습니다. 



### summary

요약하면, txt,csv,json 은 3개 모두 line by line으로 data를 읽어서 loading 할 수 있습니다. 하지만, json의 경우 nested한 구조이면 , field를 정해서 읽을 수 있습니다.



## slice,shuffle

```python
load_dataset('json' , data_files = 'my_file.json' , field='data').select(1000)
```

모든 data를 전부 loading을 할 필요가 없다면 select method를 이용해서 일부 만을 loading 할 수 있습니다. select는 Datasets dict를 반환합니다. 위의 코드는 1000개의 data만을 loading 합니다. 하지만, select는 순차적으로 subset을 만들게 됩니다.

```python
indicies = [10,20,30,40]
load_dataset('json' , data_files = 'my_file.json' , field='data').select(indicies)
```

원하는 index list를 만들어서 subset을 뽑을 수도 있습니다.

```python
load_dataset('json' , data_files = 'my_file.json' , field = 'data').shuffle(seed=1).select(1000)
```

만약, random하게 subset을 만들고 싶으면 shuffle을 먼저 적용한후 select를 하면 만들 수 있습니다. 매번, 동일하게 실험을 할려면, seed를 항상 같은 값으로 고정을 해두는 것이 좋습니다.

## filter,map

```python
dataset.filter(lambda x  : x["title"].startswith('L'))
```

filter method를 사용하면 특정 조건을 만족하는 dataset dict을 return할 수 있습니다.

```python
dataset.map(tolower)
```

dataset은 map method를 제공합니다. 이는 python의 map method와 같은 역할을 합니다. 주로 , row별로 preprocessing이 필요할 때 map method를 사용하게 됩니다.

```python
dataset.map(tokenizing_word , batched= True, batch_size = 5000)
```

map method는 또한 batch 로 dataset의 row를 함수에 한번에 넘겨주는것을 허용합니다. 이는 특히 , tokenizing을 할 때 사용하게 됩니다. multithreading으로 여러개의 batch를 한번에 processing 하게 됩니다.

## remove_columns , rename_columns , flatten

```python
datasets.rename_columns("context" , "passage")
```

위 코드는 "context"라는 column을 "passage"라는 column으로 바꾸는 코드입니다. 위의 코드들과 다르게 rename_columns는 datasets dict 객체를 바꾸는 method입니다. 따라서, dataset dict를 return하지 않습니다.

```python
datasets.remove_columns(["context" ,"passage"])
```

위 코드는 "context" , "passage" 라는 column을 삭제하는 코드입니다. 

```python
datasets.flatten(["answers"])
```

flatten은 nested column을 denormalize하는 method입니다. 

## to_json,to_csv,..etc

![image](https://user-images.githubusercontent.com/50165842/147386907-cd1f584f-b964-4ac8-b878-66261f507f40.png)

마지막으로 , 이렇게 반환한 dataset 을 다른 format으로 export할 수 있습니다.