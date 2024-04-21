---
title : "Pytorch data api : sampler and dataset style"



excerpt: "Pytorch에서 dataset and data sampler"

categories:
  - Pytorch
tags:
  - [Machine Learning,Pytorch,deep learning ,Pytorch data api]
# classes : wide
toc: true
toc_sticky: true
---

23.12.01  Customsapmler code수정 : sampler는 indicies/key 를 넘겨야하기 때문에 dic-> dic.keys()로 수정


인공지능의 알고리즘이 점점 더 복잡해지지만, 딥러닝 1.0수준에서만 고려하면 , 이는 feature가 점점 더 복잡해짐을 의미합니다. 즉, data에 대한 hand engineering이 더 많아짐을 의미합니다. 이는 feature가 점점 더 복잡해지고 , data에 대한 hand engineering 비중이 더 커짐을 의미합니다. 

예를 들면 , object detction 의 경우 label 이외에도 , 여러가지 것들( bounding box , object, multi object label..etc) 같은 추가적인 feature engineering이 필요합니다. image recognition 에 비해서 좀 더 많은 비용이 들어갑니다. natural language processing의 경우  , 단어를 토큰화 해서 , lookup table을 만드는 feature engineering이 필요합니다. question answering의 경우 answer span에 대한 정보와 , passage가 길어서 잘렸을 때 , 이에 대한 offset을 계산하는등 추가적인 feature engineering이 필요합니다. text classification이 label 1개만 필요한 것과 비교하면 상대적으로 cost가 많이 들어갑니다. 

이는 딥러닝 1.0의 특성상 , 주어진 feature의 distribution을 학습하기에 feature를 어떻게 실험하는지가 중요합니다. 따라서, data feature engineering을 하는 법을 알아야하고, 이를 위해서는 , pytorch data api를 사용할 줄 알아야 합니다. 딥러닝 2.0으로 점점 넘어가는 추세가 될 것이지만, 아직 까지는 딥러닝 1.0이 대세이기에 이러한 data feature engieering은 중요한 craft라 볼 수 있습니다.



# Dataset 

딥러닝 모델을 훈련할려면 data를 사용해야 하는데, 이를 사용하기 위한 api specification을 dataset이라고 부릅니다. 이러한 api 를 Pytorch에서는 torch.utils.data.Dataset class로 implementation을 해 놓았습니다. 따라서, Pytorch에서 Dataset class를 사용하면 직접 implementation을 하지 않아도 됩니다. 하지만, 몇몇 method를 override 해놓도록 implementation이 되어 있기에 공부하는데 도움이 된다고 할 수 있습니다. 

## Map-style dataset

map-style dataset은 (non-integral)indicies or keys 로부터 data sample로의 map을 나타냅니다. 

map-style dataset은 아래와 같은 protocol(method)를 구현해야 합니다.

- `__getitem(idx)__ `
-  `__len__` 

이러한 map-style dataset에 대한 api를 제공해주는 것이 torch의 utils.data.Dataset class입니다. 이는 abstract class 인데 , 구현을 해야만 하는 method는 `__ getitem(idx)__` 이고 , optional 한 부분은 `__len()__` 입니다. 

`__getitem(idx)__` 는 어떤 key(idx) 가 주어지면 data sample을 fetching하도록 도와줍니다. 이 때 , key는 꼭 integer일 필요는 없습니다.

`__len__` method는 dataloader 를 iterate 할 때에는 무조건 필요합니다.  docs에는 많은 sampler implementation들이 daatloader의 default option으로 인해서 dataset size를 반환할 것이라 예상한닥 적혀있습니다.

즉 , default 값으로 sequentialsampler를 사용하는데 , 이 sequentialsampler는 `iter` method에서 dataset의 len() method를 필요로 하기에 , len method를 implementation을 해주어야 합니다. Python의 built-in iter method는 `__len__` method를 필요로 하지 않는데, 아마도 Pytorch에서 overriding을 한 거 같습니다. 

###  Using with Dataloader

sampler에 어떠한 argument를 넘기지 않았을 때에는, integer index를 yield하는 index sampler를 생성합니다.

map-style dataset 특성상 non-integer index를 가질 수 있는데 , 이러한 경우에는 custom sampler가 필요합니다. (추후 Sampler part에서 자세히 설명하도록 하겠습니다.)

## Iterable-style dataset

IterableDataset의 subclass의 instance를 iterable-style dataset이라 합니다.

iterable-style dataset은 아래와 같은 protocol(method)를 구현해야 합니다.

- `__iter__()` 

iterable style은 data sample에 대한 iterable 입니다. `__getitem__` method를 implementation을 하지 않기 때문에 , random read가 비용이 크면서 불가능하고 ,batch size가 fetch 된 data에따라 다른데 적합합니다.

`iter`  method 특성상 stream을 return 하기에 한번에  메모리에 담을 수 없는 database, remote server, 실시간 log등에 적합합니다. 

### Using with Dataloader

map-style dataset과는 달리 dataset에서 각 item을 yield하는 iterator를 return합니다.

# Data loading in map-style dataset

Iterable dataset의 경우 아래와 같은 특징을 지닙니다.

- user-defined iterable에 따라 data loading 순서가 정해집니다.
- 임의의 chunk reading, dynamic batch size를 쉽게 구현을 할 수 있습니다.

Q . 그렇다면 Map-style dataset은 어떻게 해야 할까요?

A . map-style의 경우 sampler를 이용합니다.

## Sampler

`torch.utils.data.Sampler` 의 경우 아래와 같은 특징을 지닙니다.

- [indicies/or(key)] 의 sequence를 특정해주고 , 이 sequence를 data loading에 사용합니다.
- indicies에 대한 iterable object입니다. 

Dataloader의 sampler 와 관련된 argument에 따라 어떤 sampler가 사용될지 결정됩니다.

- Dataloader에서 shuffle argument 가 None이면 SequentialSampler , 아니라면 shuffled sampler가 default sampler로 assign됩니다. 

- sampler argument는 어떤 arugment를 pass하지 않아도 default로 SequentialSampler가 사용됩니다. 
- sampler arugment에 custom sampler 를 pass하면 custom sampler를 사용할 수 있습니다.
- batch sampler argument에 batch indicies를 yield하는 custom sampler를 사용할 수 있습니다.

## Integer Indicies Sampler

```python
class MySamplerTestDataset(torch.utils.data.Dataset) :

    def __init__(self , inp):
        self.inp  =inp

    def __getitem__(self , idx) :
        return self.inp[idx]
        
    def __len__(self):

        return len(self.inp)

    
    
```



#### default sampler



```python
a = np.arange(100)

IntegerIndiciesSamplerdataset = MySamplerTestDataset(a)

print(list(torch.utils.data.DataLoader(IntegerIndiciesSamplerdataset,sampler=None )))



```

```bash
[tensor([0]), tensor([1]), tensor([2]), tensor([3]), tensor([4]), tensor([5]), tensor([6]), tensor([7]), tensor([8]), tensor([9]), tensor([10]), tensor([11]), tensor([12]), tensor([13]), tensor([14]), tensor([15]), tensor([16]), tensor([17]), tensor([18]), tensor([19]), tensor([20]), tensor([21]), tensor([22]), tensor([23]), tensor([24]), tensor([25]), tensor([26]), tensor([27]), tensor([28]), tensor([29]), tensor([30]), tensor([31]), tensor([32]), tensor([33]), tensor([34]), tensor([35]), tensor([36]), tensor([37]), tensor([38]), tensor([39]), tensor([40]), tensor([41]), tensor([42]), tensor([43]), tensor([44]), tensor([45]), tensor([46]), tensor([47]), tensor([48]), tensor([49]), tensor([50]), tensor([51]), tensor([52]), tensor([53]), tensor([54]), tensor([55]), tensor([56]), tensor([57]), tensor([58]), tensor([59]), tensor([60]), tensor([61]), tensor([62]), tensor([63]), tensor([64]), tensor([65]), tensor([66]), tensor([67]), tensor([68]), tensor([69]), tensor([70]), tensor([71]), tensor([72]), tensor([73]), tensor([74]), tensor([75]), tensor([76]), tensor([77]), tensor([78]), tensor([79]), tensor([80]), tensor([81]), tensor([82]), tensor([83]), tensor([84]), tensor([85]), tensor([86]), tensor([87]), tensor([88]), tensor([89]), tensor([90]), tensor([91]), tensor([92]), tensor([93]), tensor([94]), tensor([95]), tensor([96]), tensor([97]), tensor([98]), tensor([99])]
```



default sampler의 경우  sequentialsampler를 사용하므로 위와 같이 순서대로 출력됨을 알 수 있습니다.

#### shuffle sampler

```python
Randomsampler = torch.utils.data.RandomSampler(IntegerIndiciesSamplerdataset)

print(list(torch.utils.data.DataLoader(IntegerIndiciesSamplerdataset , shuffle = True)))
```

```
[tensor([63]), tensor([32]), tensor([15]), tensor([25]), tensor([9]), tensor([31]), tensor([87]), tensor([68]), tensor([10]), tensor([42]), tensor([21]), tensor([72]), tensor([24]), tensor([56]), tensor([71]), tensor([70]), tensor([78]), tensor([14]), tensor([73]), tensor([83]), tensor([76]), tensor([16]), tensor([18]), tensor([5]), tensor([49]), tensor([29]), tensor([39]), tensor([77]), tensor([26]), tensor([45]), tensor([52]), tensor([8]), tensor([86]), tensor([17]), tensor([7]), tensor([88]), tensor([59]), tensor([55]), tensor([54]), tensor([35]), tensor([22]), tensor([0]), tensor([57]), tensor([92]), tensor([20]), tensor([64]), tensor([75]), tensor([43]), tensor([85]), tensor([1]), tensor([2]), tensor([89]), tensor([46]), tensor([28]), tensor([11]), tensor([82]), tensor([80]), tensor([91]), tensor([27]), tensor([6]), tensor([79]), tensor([44]), tensor([19]), tensor([58]), tensor([4]), tensor([47]), tensor([90]), tensor([51]), tensor([81]), tensor([33]), tensor([67]), tensor([50]), tensor([61]), tensor([3]), tensor([97]), tensor([93]), tensor([84]), tensor([48]), tensor([34]), tensor([66]), tensor([62]), tensor([41]), tensor([53]), tensor([23]), tensor([74]), tensor([60]), tensor([96]), tensor([30]), tensor([69]), tensor([12]), tensor([37]), tensor([65]), tensor([98]), tensor([13]), tensor([36]), tensor([38]), tensor([94]), tensor([95]), tensor([99]), tensor([40])]

```

shuffle argument를 true로 만드는 경우 , 위와 같이 shuffle이 되어서 나옴을 알 수 있습니다.



## Non-integral sampler

```
a = np.arange(100)

b = np.arange(100 , 200)

c = np.arange(200,300)

dic = {'a' : a, 'b' :b , 'c' : c}

class MySamplerTestDataset(torch.utils.data.Dataset) :

    def __init__(self , inp):
        self.inp  =inp

    def __getitem__(self , idx) :
        print(f"idx : {idx}")
        return self.inp[idx]
        ...
    def __len__(self):

        return len(self.inp)

    
```

```
class CustomSampler(torch.utils.data.Sampler) :
    def __init__(self, data_source) :
        self.data_source = data_source
    def __iter__(self) :
        print('hi')

        return iter(self.data_source)
        # return iter(['a' , 'b' , 'c'])
    def __len__(self) :
        return len(self.data_source)
```

```
NonintegerSamplerTestDataset = MySamplerTestDataset(dic)

nonintegersampler = CustomSampler(dic.keys())

print(list(torch.utils.data.DataLoader(NonintegerSamplerTestDataset , sampler = nonintegersampler)))
```

```
hi
idx : a
idx : b
idx : c
[tensor([[ 0,  1,  2,  3,  4,  5,  6,  7,  8,  9, 10, 11, 12, 13, 14, 15, 16, 17,
         18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30, 31, 32, 33, 34, 35,
         36, 37, 38, 39, 40, 41, 42, 43, 44, 45, 46, 47, 48, 49, 50, 51, 52, 53,
         54, 55, 56, 57, 58, 59, 60, 61, 62, 63, 64, 65, 66, 67, 68, 69, 70, 71,
         72, 73, 74, 75, 76, 77, 78, 79, 80, 81, 82, 83, 84, 85, 86, 87, 88, 89,
         90, 91, 92, 93, 94, 95, 96, 97, 98, 99]]), tensor([[100, 101, 102, 103, 104, 105, 106, 107, 108, 109, 110, 111, 112, 113,
         114, 115, 116, 117, 118, 119, 120, 121, 122, 123, 124, 125, 126, 127,
         128, 129, 130, 131, 132, 133, 134, 135, 136, 137, 138, 139, 140, 141,
         142, 143, 144, 145, 146, 147, 148, 149, 150, 151, 152, 153, 154, 155,
         156, 157, 158, 159, 160, 161, 162, 163, 164, 165, 166, 167, 168, 169,
         170, 171, 172, 173, 174, 175, 176, 177, 178, 179, 180, 181, 182, 183,
         184, 185, 186, 187, 188, 189, 190, 191, 192, 193, 194, 195, 196, 197,
         198, 199]]), tensor([[200, 201, 202, 203, 204, 205, 206, 207, 208, 209, 210, 211, 212, 213,
         214, 215, 216, 217, 218, 219, 220, 221, 222, 223, 224, 225, 226, 227,
         228, 229, 230, 231, 232, 233, 234, 235, 236, 237, 238, 239, 240, 241,
         242, 243, 244, 245, 246, 247, 248, 249, 250, 251, 252, 253, 254, 255,
         256, 257, 258, 259, 260, 261, 262, 263, 264, 265, 266, 267, 268, 269,
         270, 271, 272, 273, 274, 275, 276, 277, 278, 279, 280, 281, 282, 283,
         284, 285, 286, 287, 288, 289, 290, 291, 292, 293, 294, 295, 296, 297,
         298, 299]])]
```



이번에는 non-integral indicies에 대한 example을 보도록 하겠습니다. 

위의 예제에서는 numpy array a , b , c를 item으로 하고 , 각 key를 a , b, c로 설정하였습니다.  

`iter` method를  override할 때 , hi라는 단어를 출력하도록 하였는데 , sampler 에 대한 iterator를 한번만 assign하는 것을 볼 수 있습니다. iterator를 한번만 호출하고 next method로 data sample을 yield하기 때문입니다.

## Custom batch Sampler

위의 예제에서는 sampler가 전체 indicies를 yield하도록 해서 , 한번에 각 data sample을 for loop으로 iterate하는 형식이였습니다. 하지만 , custom batch sampler를 이용해서 data sample을 batch단위로 iterate할 수 있게 해줍니다. 

```python
class MySamplerTestDataset(torch.utils.data.Dataset) :

    def __init__(self , inp):
        self.inp  =inp

    def __getitem__(self , idx) :
        return self.inp[idx]
        ...
    def __len__(self):

        return len(self.inp)

    
class CustomSampler(torch.utils.data.Sampler) :
    def __init__(self, data_source) :
        self.data_source = data_source
    def __iter__(self) :
        print('hi')

        return iter(np.split(self.data_source,10))
    def __len__(self) :
        return len(self.data_source)
a = np.arange(100)
dds  = MySamplerTestDataset(a)
customsampler = CustomSampler(a)
```

dataset을 정의할 때 이번에는 data set을 10개의 group으로 나누어서 이에대한 iterator를 return하도록 하였습니다. 

### using default sampler argument

```
# 10개의 element를 가진 sequence를 2개의 batch 로 묶어서 tensor로 만들어서 return한다.


#for indices in batch_sampler:
    # yield collate_fn([dataset[i] for i in indices])
ddd = list(torch.utils.data.DataLoader(dds,sampler = customsampler , batch_size = 1 ))
for dd in ddd :
    print(dd)
```



```
hi
tensor([[0, 1, 2, 3, 4, 5, 6, 7, 8, 9]])
tensor([[10, 11, 12, 13, 14, 15, 16, 17, 18, 19]])
tensor([[20, 21, 22, 23, 24, 25, 26, 27, 28, 29]])
tensor([[30, 31, 32, 33, 34, 35, 36, 37, 38, 39]])
tensor([[40, 41, 42, 43, 44, 45, 46, 47, 48, 49]])
tensor([[50, 51, 52, 53, 54, 55, 56, 57, 58, 59]])
tensor([[60, 61, 62, 63, 64, 65, 66, 67, 68, 69]])
tensor([[70, 71, 72, 73, 74, 75, 76, 77, 78, 79]])
tensor([[80, 81, 82, 83, 84, 85, 86, 87, 88, 89]])
tensor([[90, 91, 92, 93, 94, 95, 96, 97, 98, 99]])
```



default sampler agument를 사용하면 list의 list가 결과로 나옴을 알 수 있습니다. 

dataloader에서 batch_size, drop_last argument로 개별 sample 대신에 batched sample을 yield하게 됩니다 . 따라서, 위의 예제의 경우 각 sample을 10개의 group으로 사용하였기에 , 10개의 group이 individual batch_sample이 되고 batch_size만큼의 individual batch sample을 list로 collate_fn의 argument로 passing해줍니다. 따라서, list의 list가 결과값으로 나오게 됩니다.

```python
# 10개의 element를 가진 sequence를 2개의 batch 로 묶어서 tensor로 만들어서 return한다.

#for index in sampler:
    # yield collate_fn(dataset[index])

ddd = list(torch.utils.data.DataLoader(dds,sampler = customsampler , batch_size = 2 ))
for dd in ddd :
    print(dd)
    
hi
tensor([[ 0,  1,  2,  3,  4,  5,  6,  7,  8,  9],
        [10, 11, 12, 13, 14, 15, 16, 17, 18, 19]])
tensor([[20, 21, 22, 23, 24, 25, 26, 27, 28, 29],
        [30, 31, 32, 33, 34, 35, 36, 37, 38, 39]])
tensor([[40, 41, 42, 43, 44, 45, 46, 47, 48, 49],
        [50, 51, 52, 53, 54, 55, 56, 57, 58, 59]])
tensor([[60, 61, 62, 63, 64, 65, 66, 67, 68, 69],
        [70, 71, 72, 73, 74, 75, 76, 77, 78, 79]])
tensor([[80, 81, 82, 83, 84, 85, 86, 87, 88, 89],
        [90, 91, 92, 93, 94, 95, 96, 97, 98, 99]])
```

만약에 batch_size를 2로 설정한다면 위와 같이 결과값이 나오게 됩니다.



### using batch sampler argument



```python
ddd = list(torch.utils.data.DataLoader(dds,batch_sampler = customsampler  ))

#for indices in batch_sampler:
    # yield collate_fn([dataset[i] for i in indices])
for dd in ddd :
    print(dd)
```

```
hi
tensor([0, 1, 2, 3, 4, 5, 6, 7, 8, 9])
tensor([10, 11, 12, 13, 14, 15, 16, 17, 18, 19])
tensor([20, 21, 22, 23, 24, 25, 26, 27, 28, 29])
tensor([30, 31, 32, 33, 34, 35, 36, 37, 38, 39])
tensor([40, 41, 42, 43, 44, 45, 46, 47, 48, 49])
tensor([50, 51, 52, 53, 54, 55, 56, 57, 58, 59])
tensor([60, 61, 62, 63, 64, 65, 66, 67, 68, 69])
tensor([70, 71, 72, 73, 74, 75, 76, 77, 78, 79])
tensor([80, 81, 82, 83, 84, 85, 86, 87, 88, 89])
tensor([90, 91, 92, 93, 94, 95, 96, 97, 98, 99])
```



만약에 , batch_sampler argument를 사용한다면 어떻게 될까요?

batch_sampler를 사용하게 된다면 , batch_sampler를 iterate해서 10개의 indicies를 얻게 되고 , 이를 list comprehension으로 list로 만들어서 collate_fn을 적용하게 됩니다. 따라서, 결과값을 list가 나오게 됩니다. 

```python
ddd = list(torch.utils.data.DataLoader(dds,batch_sampler = customsampler ,batch_size= 3 ))

#for indices in batch_sampler:
    # yield collate_fn([dataset[i] for i in indices])
for dd in ddd :
    print(dd)
    
    
```

```
---------------------------------------------------------------------------
ValueError                                Traceback (most recent call last)
<ipython-input-16-05e1dcc877a7> in <module>()
----> 1 ddd = list(torch.utils.data.DataLoader(dds,batch_sampler = customsampler ,batch_size= 3 ))
      2 
      3 #for indices in batch_sampler:
      4     # yield collate_fn([dataset[i] for i in indices])
      5 for dd in ddd :

/usr/local/lib/python3.7/dist-packages/torch/utils/data/dataloader.py in __init__(self, dataset, batch_size, shuffle, sampler, batch_sampler, num_workers, collate_fn, pin_memory, drop_last, timeout, worker_init_fn, multiprocessing_context, generator, prefetch_factor, persistent_workers)
    249             # auto_collation with custom batch_sampler
    250             if batch_size != 1 or shuffle or sampler is not None or drop_last:
--> 251                 raise ValueError('batch_sampler option is mutually exclusive '
    252                                  'with batch_size, shuffle, sampler, and '
    253                                  'drop_last')

ValueError: batch_sampler option is mutually exclusive with batch_size, shuffle, sampler, and drop_last
```

만약에 , batch_size와 drop_last를 batch_sampler argument가 None이 아닐때 같이 사용하게 된다면, batch_sampler는 batch_szie,shuffle,sampler 그리고 drop_last argument와는 mutually exclusive하기 때문에 같이 사용할 수 없다는 것을 아셔야 합니다.

## Iterative with Sampler not allowed



```
a = np.arange(100)

b = np.arange(100 , 200)

c = np.arange(200,300)

dic = {'a' : a, 'b' :b , 'c' : c}

class MySamplerTestIterableDatset(torch.utils.data.IterableDataset) :
    def __init__(self,inp) :
        self.inp =inp 
    def __iter__(self) :
        return iter(self.inp)
    def __len__(self) :
        return len(self.inp)

itercustomsamplerdataset = MySamplerTestIterableDatset(dic)

```

```
itersampler  = torch.utils.data.RandomSampler(itercustomsamplerdataset)

# iterable dataset에  sampler, batch_sampler ,batch_size를 적용하면 안된다.

print(list(torch.utils.data.DataLoader(itercustomsamplerdataset ,batch_size = 4, batch_sampler = itersampler)))
```

```
---------------------------------------------------------------------------
ValueError                                Traceback (most recent call last)
<ipython-input-36-5a86c478aaf9> in <module>()
      1 # iterable dataset에  sampler, batch_sampler ,batch_size를 적용하면 안된다.
      2 
----> 3 print(list(torch.utils.data.DataLoader(itercustomsamplerdataset ,batch_size = 4, batch_sampler = itersampler)))

/usr/local/lib/python3.7/dist-packages/torch/utils/data/dataloader.py in __init__(self, dataset, batch_size, shuffle, sampler, batch_sampler, num_workers, collate_fn, pin_memory, drop_last, timeout, worker_init_fn, multiprocessing_context, generator, prefetch_factor, persistent_workers)
    238                 raise ValueError(
    239                     "DataLoader with IterableDataset: expected unspecified "
--> 240                     "batch_sampler option, but got batch_sampler={}".format(batch_sampler))
    241         else:
    242             self._dataset_kind = _DatasetKind.Map

ValueError: DataLoader with IterableDataset: expected unspecified batch_sampler option, but got batch_sampler=<torch.utils.data.sampler.RandomSampler object at 0x7f5511cf1690>
```

Sampler는 indicies를 yield하기 때문에, iterator를 이용해 iterate하는 Iterable Dataset과는 같이 사용될 수 없습니다.

즉, batch_sampler , sampler argument를 Iterable Dataset에서는 사용할 수 없습니다.