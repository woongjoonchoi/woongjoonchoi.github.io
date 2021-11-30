---
title : "Pytorch SubSampling : Dataloaer & SubSampler, ranodm_split , Subset"



excerpt: "Pytorch에서 subset 만들기"

categories:
  - Pytorch
tags:
  - [Machine Learning,Pytorch,deep learning ]
# classes : wide
toc: true
toc_sticky: true
---
## Pytorch Subsampling

파이토치로 model optimization 을 하다보면 많은 양의 실험을 진행해야 합니다. 실제로, 학습을 해서 사용하기 보다는 POC(proof of correctness) 가 목적이라고 볼 수 있습니다. 진짜로 학습을 하기 보다는  최적의 Architecture , Augmentation 조합, hyperparameter를 찾는 것이기 때문에 탐색이 주 목적입니다. 따라서 , tiny set에 대하여 experiment를 진행하는 것이 더 좋다고 생각이 들었습니다. Pytorch에서 subsampling을 어떻게 하는지 여러 방법들을 알아 보도록 하겠습니다.



## random_split

![image](https://user-images.githubusercontent.com/50165842/144055253-5711b84c-3f45-4257-ae14-6f515df7d5b1.png)

Dataset class의 instance와 어떤 비율로 나눌것 인지에 대한 sequence 값을 argument 값으로 넘겨주면 됩니다.  

```python
        torch.manual_seed(0)
        
        ....
        train_length = int(len(train_dataset)* 0.8)
        train_dataset, val_dataset = random_split(
            train_dataset, [train_length, len(train_dataset) - train_length]
        )
        test_length = int(len(test_dataset) * 0.8) 
        test_dataset, test_abandon_dataset = random_split(
            test_dataset, [test_length, len(test_dataset) - test_length]
        )        
```

매번 말하지만, pseudocode이기 때문에 문법이 안맞습니다. 이 method의 주 용도는 train, valid를 나눌 때 사용합니다. 이 경우  , train ,valid를 나눈 다음에 valid는 버리고 train에 대해서만 search 하는 방법을 취하면 나머지는 버리고 사용할 수 있습니다.  sequence의 sum이 dataset length가 아닐경우 아래와 같은 Error를 마주하게 될 것입니다.

```bash
ValueError: Sum of input lengths does not equal the length of the input dataset!
```









## Dataloader & Sampler

Pytorch 1.x 의 Dataset type은 Map style dataset , Iterable style dataset 2가지를 지원합니다.  Map-style datset일 경우 , Sampler는 데이터 로딩에 사용되는 인덱스/키를 지정해줍니다. 파이토치 docs에 나와있는 정의인데 , 대충 해석하면 얼마나 뽑을지 sampler로 정해준다는 의미입니다.

### RandomSampler

![image](https://user-images.githubusercontent.com/50165842/144062452-45375147-1cbf-4286-b33b-1e36ad5396ca.png)

```python
        tdss = torch.utils.data.RandomSampler(train_dataset,replacement=True,num_samples=train_len / 5)
        print(list(tdss))
        print(tdss)
        [21604, 11797, 47723, 35783]
        <torch.utils.data.sampler.RandomSampler object at 0x7f5887366f10>
        
        
        ..
        
        train_loader = DataLoader(dataset=train_dataset, shuffle=False, batch_size=4, sampler=train_sampler)
```

이런식으로 출력을 해보면 index값이 출력이 된다. 말 그대로 index를 지정해준다. 이를 적절히 DataLoader의 batchsize와 결합해서 사용하면 된다.  replacement=True 로 argument를 넘겨주면 전체 에대해서 random sampling을 하는것 이 아닌 sample 개수를 정할 수 있다.

아직 제대로 사용안해봐서 이렇게 하면 DataLoader에서 정상적으로 작동하는지는 모른다. 필자가 실험해보고 다시 update하겠다. 일단은 된다는 전제하에 글을 적겠다.

### WeightedRandomSampler

![image](https://user-images.githubusercontent.com/50165842/144062286-2ac63ef0-70ac-432d-b448-2383be54fb06.png)

위의 RandomSampler와 같다. random이 아닌 가중치를 부여해서 sampling을 한다는 점이 다른 것이다.

### SubsetSampler

![image](https://user-images.githubusercontent.com/50165842/144059211-3ba533a1-20be-4705-ab44-21453b5d7176.png)

```python
np.random.seed(0)

...


dataset_size = len(train_dataset)
dataset_indices = list(range(dataset_size))
np.random.shuffle(dataset_indices)
...


train_idx, val_idx = dataset_indices[val_split_index:], dataset_indices[:val_split_index]
val_split_index = int(np.floor(0.8 * dataset_size))

train_idx, val_idx = dataset_indices[val_split_index:], dataset_indices[:val_split_index]

train_sampler = SubsetRandomSampler(train_idx)
val_sampler = SubsetRandomSampler(val_idx)

..

    train_loader = DataLoader(
        dataset=train_dataset,
        pin_memory=(torch.cuda.is_available()),
        shuffle=True,
        batch_size=batch_size,
        num_workers=10,
        sampler=train_sampler,
        drop_last=True
    )
    valid_loader = DataLoader(
        dataset=val_dataset,
        pin_memory=(torch.cuda.is_available()),
        shuffle=False,
        batch_size=batch_size,
        sampler=val_sampler,
        num_workers=5
    )
```

위 Random_split과 비슷하다.대신 , 얘는 numpy로 indices를 생성해서 shuffle후 그 indices에서 random 하게 sampling 한다. 위의 2개와는 다르게 , replacement argument가 없다. 



## 필자가 생각하는 단점

위의 sampler 방식은 필자가 생각하기에 큰 단점이 있다고 봅니다. tiny dataset을  만들기 위해 orginal dataset을 메모리에 올려두고 있는건 메모리 낭비라 생각합니다. dataset 자체에 대해서 subset을 뽑는 방식은 없을까 고민하고 torch에서 해결책을 찾았습니다.

## Subset

![image](https://user-images.githubusercontent.com/50165842/144064664-d63f89e0-e6a5-4e0d-913a-ccde930026ce.png)

dataset 과 indicies를 넘겨주면 subdataset을 return 합니다. 

```
        np.random.seed(0)
        ...
        def get_random_indicies(length : int) -> List[int] :
            indicies = list(range(0,length,5))
            np.random.shuffle(indicies)
            return indicies 
        ...
        train_indicies = get_random_indicies(len((train_dataset)))
        train_length = len(train_dataset)
        train_dataset, val_dataset = random_split(
            train_dataset, [train_length, len(train_dataset) - train_length ]
        )
        train_dataset = torch.utils.data.Subset(train_dataset , train_indicies)
        
        test_dataset = Dataset(
            root=data_path, train=False, download=False, transform=transform_test
        )
        
        test_indicies= get_random_indicies(len(test_dataset))
        test_dataset = torch.utils.data.Subset(test_dataset, test_indicies)
```

## subset vs random_split

그럼 도대체 이 둘은 뭐가 다른 것일 까요?



![image](https://user-images.githubusercontent.com/50165842/144065417-53500da2-3828-447f-855e-828b808f5387.png)

좀더 low-level에서 보면 Subset을 호출하는 부분은 같다고 볼 수 있다. randperm  부분은 똑같이 해주면 차이가 없어진다. 차이점은 stack frame을 한번더 생성하는 것이다. 이게 성능의 큰 차이가 날지는 모르겠지만, 직접 subset을 호출하는게 더 빠를 것이라 필자는 생각한다.

## Reference

[PyTorch [Basics] — Sampling Samplers  by Akshaj Verma  Towards Data Science](https://towardsdatascience.com/pytorch-basics-sampling-samplers-2a0f29f0bf2a)

[torch.utils.data — PyTorch 1.10.0 documentation](https://pytorch.org/docs/stable/data.html#data-loading-order-and-sampler)

