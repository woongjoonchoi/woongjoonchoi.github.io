---
title : "Pytorch data api : Multiprocessing"



excerpt: "Pytorch multiprocess"

categories:
  - Pytorch
tags:
  - [Machine Learning,Pytorch,deep learning ,Pytorch data api]
# classes : wide
toc: true
toc_sticky: true
---
Pytorch에서 DataLoading을 할 때 DataLoader라는 class를 사용하는데 , 이 때 Data를 단일 Process내에서 loading 할수도 있고, parallelize해서 loading을 할 수도 있다.

# Default Option
DataLoader는 Single Process data loading이 default option입니다 .

Python Process는 GIL(Global Interpretatoin Lock)이 Python Code를 thread로 parallelize하는 것을 막습니다. 따라서, data loading에서 computation을 1개의 process가 blocking 하는것을 방지하기 위해서 ,Pytorch는 'num_workers' 라는 argument를 양의 정수로 설정하여 multi-process data loading으로 쉽게 전환 시킵니다. 


# Single Processing

data fetching이 같은 process내에서 이루어집니다. 즉, 1개의 process가 computing을 block하는 상태입니다. 
이 방법은 resource가 제한이 되거나 , 전체 memory 에 data를 올릴 수 있을만큼 작다면 , 선호되는 방법입니다.  
또한, 이 방법의 장점으로는 error tracing이 쉽다는 것입니다. 이에 대해서는, Multi Processing에서 자세히 알아보도록 하겠습니다.

# Multi Processing

Dataloader에서는 num_workers라는 argument를 설정할 수 있습니다. 

1. 'num_workers' = 0 
   + data loading이 main process에서 이루어집니다.
2. 'num_workers' > 0 
   + multi-process data loading 이 활성화되고 , 지정한 숫자만큼의 worker process가 생성이 됩니다 . 이 subprocess들이 data loading에 사용됩니다.


data를 loading할 때 에는 , enumerate(dataloader)를 call 하여 , 매번 DataLoader의 iterator를 생성합니다. 여기에서 , `num_workers' argument에 지정한 개수 만큼의 worker_process가 생성이 됩니다. 그리고 , 'dataset' , 'collate_fn', 'worker_init_fn'이 각  worker에 전달이 됩니다. worker가  initialize 된 후 ,   data가 fetch 되어집니다.   

즉 , Internal I/O , transformation('collate_fn' 포함)이  dataset access와 함께 , worker_process에서 실행되어짐을 의미합니다.


## Iterating DataLoader

실제로 , Data를 여러개의 worker에서 fetching 할 때에는 , torch.utils.data.get_worekr_info()라는 method를 사용합니다.   
이 function은 아래와 같은 항목을 return합니다.
+ worker_id
+ dataset replica
+ initial seed
+ .etc

main_process에서는 None을 return 하게 됩니다. Pytorch 개발자들은 이 function을 dataset code나 worker_init_fn에서 사용하여 code가 worker_process에서 실행중인지 아닌지를 판별하여 개별적인 dataset_replica를 configure합니다.   

특히, data sharding에 도움이 된다고 합니다.

### Map-style

map-style dataset에서 여러 subprocess들을 만들게 되면 , sampler를 사용해서 indicies를 만들게 되고 이를 각 worker에 전달하게 됩니다 . sampler에서의 shuffling 은 main process에서 수행이 되어집니다.   
즉, main process는 data loading에서 shuffle된 indicies를 worker에 할당하여 data를 loading하도록 합니다.   
아래의 코드 예시를 보도록 하겠습니다.
```python 
import torch

class MyMapDataset(torch.utils.data.Dataset) :
    def __init__(self , start , end) :
        super(MyMapDataset).__init__()
        assert end>start , "this example code only works with end> = start"

        self.start = start
        self.end = end 
        self.data = list(range(self.start,self.end))
    def __getitem__(self,idx) :
        
        worker_info  =torch.utils.data.get_worker_info()
        worker_id = worker_info.id
        print(f"worekr_id : {worker_id} data : {self.data[idx]}\n")

        return self.data[idx]
    def __len__(self) :
        return len(self.data)


map_ds = MyMapDataset(3,100)


print(list(torch.utils.data.DataLoader(map_ds,num_workers=2)))

# worekr_id : 0 data : 3

# worekr_id : 1 data : 4
# worekr_id : 0 data : 5


# worekr_id : 1 data : 6
# worekr_id : 0 data : 7


# worekr_id : 1 data : 8
# worekr_id : 0 data : 9


# worekr_id : 1 data : 10
# worekr_id : 0 data : 11


# worekr_id : 1 data : 12
# worekr_id : 0 data : 13


# worekr_id : 1 data : 14
# worekr_id : 0 data : 15

# worekr_id : 1 data : 16


# worekr_id : 1 data : 18


#  ... 
# worekr_id : 0 data : 93
# worekr_id : 1 data : 96


# worekr_id : 0 data : 95
# worekr_id : 1 data : 98

# worekr_id : 0 data : 97
# worekr_id : 0 data : 99

# [tensor([3]), tensor([4]), tensor([5]), tensor([6]), tensor([7]), tensor([8]), tensor([9]), tensor([10]), tensor([11]), tensor([12]), tensor([13]), tensor([14]), tensor([15]), tensor([16]), tensor([17]), tensor([18]), tensor([19]), tensor([20]), tensor([21]), tensor([22]), tensor([23]), tensor([24]), tensor([25]), tensor([26]), tensor([27]), tensor([28]), tensor([29]), tensor([30]), tensor([31]), tensor([32]), tensor([33]), tensor([34]), tensor([35]), tensor([36]), tensor([37]), tensor([38]), tensor([39]), tensor([40]), tensor([41]), tensor([42]), tensor([43]), tensor([44]), tensor([45]), tensor([46]), tensor([47]), tensor([48]), tensor([49]), tensor([50]), tensor([51]), tensor([52]), tensor([53]), tensor([54]), tensor([55]), tensor([56]), tensor([57]), tensor([58]), tensor([59]), tensor([60]), tensor([61]), tensor([62]), tensor([63]), tensor([64]), tensor([65]), tensor([66]), tensor([67]), tensor([68]), tensor([69]), tensor([70]), tensor([71]), tensor([72]), tensor([73]), tensor([74]), tensor([75]), tensor([76]), tensor([77]), tensor([78]), tensor([79]), tensor([80]), tensor([81]), tensor([82]), tensor([83]), tensor([84]), tensor([85]), tensor([86]), tensor([87]), tensor([88]), tensor([89]), tensor([90]), tensor([91]), tensor([92]), tensor([93]), tensor([94]), tensor([95]), tensor([96]), tensor([97]), tensor([98]), tensor([99])]

```

보시는 바와 같이 각 indicies들이 worker에 전달되어 data를 loading함을 알 수 있습니다.

### Iterative-style
Iterable sytle의 dataset을 worker를 이용해서 loading할 때에는 data 중복을 주의해야 합니다.   
Iterable style의 dataset을 loading할 때 subprocess들을 만드는 경우 , 각 worker들이 dataset object의 replica를 얻게 됩니다. 그 다음에, 각 worker들이 dataset object를 iterating 함으로 써 , data의 중복이 발생하게 됩니다. 아래의 예시코드에서 확인해보도록 하겠습니다. 
```python

import torch

class MyIterableDataset(torch.utils.data.IterableDataset) :
    def __init__(self,start, end) :
        super(MyIterableDataset).__init__()
        assert end>start , "this example code only works with end >=start"

        self.start = start
        self.end = end
    def __iter__(self) :
        worker_info = torch.utils.data.get_worker_info()
        worker_id = worker_info.id
        print(range(self.start,self.end))
        print(worker_info)
        return iter(range(self.start,self.end))

ds = MyIterableDataset(start=3 ,end =100)

print(list(torch.utils.data.DataLoader(ds,num_workers=2)))

# range(3, 100)
# WorkerInfo(id=0, num_workers=2, seed=4911920692807402111, dataset=<__main__.MyIterableDataset object at 0x7f5d2ef69910>)range(3, 100)

# WorkerInfo(id=1, num_workers=2, seed=4911920692807402112, dataset=<__main__.MyIterableDataset object at 0x7f5d2ef69910>)
# [tensor([3]), tensor([3]), tensor([4]), tensor([4]), tensor([5]), tensor([5]), tensor([6]), tensor([6]), tensor([7]), tensor([7]), tensor([8]), tensor([8]), tensor([9]), tensor([9]), tensor([10]), tensor([10]), tensor([11]), tensor([11]), tensor([12]), tensor([12]), tensor([13]), tensor([13]), tensor([14]), tensor([14]), tensor([15]), tensor([15]), tensor([16]), tensor([16]), tensor([17]), tensor([17]), tensor([18]), tensor([18]), tensor([19]), tensor([19]), tensor([20]), tensor([20]), tensor([21]), tensor([21]), tensor([22]), tensor([22]), tensor([23]), tensor([23]), tensor([24]), tensor([24]), tensor([25]), tensor([25]), tensor([26]), tensor([26]), tensor([27]), tensor([27]), tensor([28]), tensor([28]), tensor([29]), tensor([29]), tensor([30]), tensor([30]), tensor([31]), tensor([31]), tensor([32]), tensor([32]), tensor([33]), tensor([33]), tensor([34]), tensor([34]), tensor([35]), tensor([35]), tensor([36]), tensor([36]), tensor([37]), tensor([37]), tensor([38]), tensor([38]), tensor([39]), tensor([39]), tensor([40]), tensor([40]), tensor([41]), tensor([41]), tensor([42]), tensor([42]), tensor([43]), tensor([43]), tensor([44]), tensor([44]), tensor([45]), tensor([45]), tensor([46]), tensor([46]), tensor([47]), tensor([47]), tensor([48]), tensor([48]), tensor([49]), tensor([49]), tensor([50]), tensor([50]), tensor([51]), tensor([51]), tensor([52]), tensor([52]), tensor([53]), tensor([53]), tensor([54]), tensor([54]), tensor([55]), tensor([55]), tensor([56]), tensor([56]), tensor([57]), tensor([57]), tensor([58]), tensor([58]), tensor([59]), tensor([59]), tensor([60]), tensor([60]), tensor([61]), tensor([61]), tensor([62]), tensor([62]), tensor([63]), tensor([63]), tensor([64]), tensor([64]), tensor([65]), tensor([65]), tensor([66]), tensor([66]), tensor([67]), tensor([67]), tensor([68]), tensor([68]), tensor([69]), tensor([69]), tensor([70]), tensor([70]), tensor([71]), tensor([71]), tensor([72]), tensor([72]), tensor([73]), tensor([73]), tensor([74]), tensor([74]), tensor([75]), tensor([75]), tensor([76]), tensor([76]), tensor([77]), tensor([77]), tensor([78]), tensor([78]), tensor([79]), tensor([79]), tensor([80]), tensor([80]), tensor([81]), tensor([81]), tensor([82]), tensor([82]), tensor([83]), tensor([83]), tensor([84]), tensor([84]), tensor([85]), tensor([85]), tensor([86]), tensor([86]), tensor([87]), tensor([87]), tensor([88]), tensor([88]), tensor([89]), tensor([89]), tensor([90]), tensor([90]), tensor([91]), tensor([91]), tensor([92]), tensor([92]), tensor([93]), tensor([93]), tensor([94]), tensor([94]), tensor([95]), tensor([95]), tensor([96]), tensor([96]), tensor([97]), tensor([97]), tensor([98]), tensor([98]), tensor([99]), tensor([99])]

```

dataset의 replica가 각 worker에 전달되어 중복되게 fetching함을 알 수 있습니다.   
이를 해결하기 위해서는 여러가지 방법이 있습니다. 

#### Using get_worker_info()

위에서 언급했듯이 , get_worker_info() 는 main_process의 경우 None을 return하고 , subprocess의 경우에는 id,replica ,seed 등등을 return합니다. worker_id를 이용해서 각 worker마다 fetching을 configuration을 할 수 있습니다.
```python
class MyIterableDataset(torch.utils.data.IterableDataset) :
    def __init__(self, start, end) :
        super(MyIterableDataset).__init__()
        assert end > start , "this example code only works with end >= start"

        self.start = start
        self.end = end
    
    def __iter__(self) :
        worker_info= torch.utils.data.get_worker_info()
        if worker_info is None :
            iter_start = self.start
            iter_end = self.end
        else :
            per_worker = int(math.ceil((self.end-self.start) ) /
                             float(worker_info.num_workers))
            worker_id = worker_info.id
            iter_start = self.start + worker_id * per_worker
            iter_end =  min(iter_start + per_worker , self.end)
            print(f'worker_id : {worker_id} \n iter_start : {iter_start}  iter_end : {iter_end}\n')
        return iter(range(iter_start, iter_end))


ds = MyIterableDataset(start = 3 ,end = 100) 


print(list(torch.utils.data.DataLoader(ds,num_workers=3)))


# worker_id : 0 
#  iter_start : 3  iter_end : 35
# worker_id : 1 
#  iter_start : 35  iter_end : 67


# worker_id : 2 
#  iter_start : 67  iter_end : 99

# [tensor([3]), tensor([35]), tensor([67]), tensor([4]), tensor([36]), tensor([68]), tensor([5]), tensor([37]), tensor([69]), tensor([6]), tensor([38]), tensor([70]), tensor([7]), tensor([39]), tensor([71]), tensor([8]), tensor([40]), tensor([72]), tensor([9]), tensor([41]), tensor([73]), tensor([10]), tensor([42]), tensor([74]), tensor([11]), tensor([43]), tensor([75]), tensor([12]), tensor([44]), tensor([76]), tensor([13]), tensor([45]), tensor([77]), tensor([14]), tensor([46]), tensor([78]), tensor([15]), tensor([47]), tensor([79]), tensor([16]), tensor([48]), tensor([80]), tensor([17]), tensor([49]), tensor([81]), tensor([18]), tensor([50]), tensor([82]), tensor([19]), tensor([51]), tensor([83]), tensor([20]), tensor([52]), tensor([84]), tensor([21]), tensor([53]), tensor([85]), tensor([22]), tensor([54]), tensor([86]), tensor([23]), tensor([55]), tensor([87]), tensor([24]), tensor([56]), tensor([88]), tensor([25]), tensor([57]), tensor([89]), tensor([26]), tensor([58]), tensor([90]), tensor([27]), tensor([59]), tensor([91]), tensor([28]), tensor([60]), tensor([92]), tensor([29]), tensor([61]), tensor([93]), tensor([30]), tensor([62]), tensor([94]), tensor([31]), tensor([63]), tensor([95]), tensor([32]), tensor([64]), tensor([96]), tensor([33]), tensor([65]), tensor([97]), tensor([34]), tensor([66]), tensor([98])]


```

위의 code에서 iter method는 subprocess가 설정되면 , num_workers만큼 data를 나누어서 worker_id를 기준으로 fetching하도록 configuration 합니다.



#### Using worker_init_fn(worker_id)

pytorch dataloader의 argument에는 worker_init_fn을 설정할 수 있습니다. worker_init_fn은 worker_id를 argument로 받아서, 각 dataset의 replica를 개별적으로 설정합니다.
```python
class MyIterableDataset(torch.utils.data.IterableDataset) :
    def __init__(self,start, end) :
        super(MyIterableDataset).__init__()
        assert end>start , "this example code only works with end >=start"

        self.start = start
        self.end = end
    def __iter__(self) :
        worker_info = torch.utils.data.get_worker_info()
        worker_id = worker_info.id
        print(f'worker_id : {worker_id} \n iter_start : {self.start}  iter_end : {self.end}\n')
        return iter(range(self.start,self.end))


def worker_init_fn(worker_id) :
    worker_info = torch.utils.data.get_worker_info()
    dataset = worker_info.dataset
    overall_start = dataset.start
    overall_end = dataset.end

    per_worker = int(math.ceil((overall_end-overall_start) ) /
                     float(worker_info.num_workers))
    worker_id = worker_info.id
    dataset.start = overall_start + worker_id * per_worker
    dataset.end = min(dataset.start + per_worker , overall_end)
    # print(f'worker_id : {worker_id} , worker_start : {dataset.start}  ,  worekr_end : {dataset.end}')


ds = MyIterableDataset(start = 3 ,end = 100) 


print(list(torch.utils.data.DataLoader(ds,num_workers=10,worker_init_fn=worker_init_fn)))


# /usr/local/lib/python3.7/dist-packages/torch/utils/data/dataloader.py:481: UserWarning: This DataLoader will create 10 worker processes in total. Our suggested max number of worker in current system is 2, which is smaller than what this DataLoader is going to create. Please be aware that excessive worker creation might get DataLoader running slow or even freeze, lower the worker number to avoid potential slowness/freeze if necessary.
#   cpuset_checked))
# worker_id : 0 
#  iter_start : 3  iter_end : 12

# worker_id : 1 
#  iter_start : 12  iter_end : 21
# worker_id : 2 
#  iter_start : 21  iter_end : 30

# worker_id : 3 
#  iter_start : 30  iter_end : 39


# worker_id : 4 
#  iter_start : 39  iter_end : 48

# worker_id : 5 
#  iter_start : 48  iter_end : 57

# worker_id : 6 
#  iter_start : 57  iter_end : 66

# worker_id : 7 
#  iter_start : 66  iter_end : 75

# worker_id : 8 
#  iter_start : 75  iter_end : 84

# worker_id : 9 
#  iter_start : 84  iter_end : 93

# [tensor([3]), tensor([12]), tensor([21]), tensor([30]), tensor([39]), tensor([48]), tensor([57]), tensor([66]), tensor([75]), tensor([84]), tensor([4]), tensor([13]), tensor([22]), tensor([31]), tensor([40]), tensor([49]), tensor([58]), tensor([67]), tensor([76]), tensor([85]), tensor([5]), tensor([14]), tensor([23]), tensor([32]), tensor([41]), tensor([50]), tensor([59]), tensor([68]), tensor([77]), tensor([86]), tensor([6]), tensor([15]), tensor([24]), tensor([33]), tensor([42]), tensor([51]), tensor([60]), tensor([69]), tensor([78]), tensor([87]), tensor([7]), tensor([16]), tensor([25]), tensor([34]), tensor([43]), tensor([52]), tensor([61]), tensor([70]), tensor([79]), tensor([88]), tensor([8]), tensor([17]), tensor([26]), tensor([35]), tensor([44]), tensor([53]), tensor([62]), tensor([71]), tensor([80]), tensor([89]), tensor([9]), tensor([18]), tensor([27]), tensor([36]), tensor([45]), tensor([54]), tensor([63]), tensor([72]), tensor([81]), tensor([90]), tensor([10]), tensor([19]), tensor([28]), tensor([37]), tensor([46]), tensor([55]), tensor([64]), tensor([73]), tensor([82]), tensor([91]), tensor([11]), tensor([20]), tensor([29]), tensor([38]), tensor([47]), tensor([56]), tensor([65]), tensor([74]), tensor([83]), tensor([92])]
```


### Warning

Pytorch Docs에서는 CUDA Tensor를 multi-processing loading 에서 return하는 것을 추천하지 않는다고 한다.  CUDA Tensor를 공유하거나 CUDA를 사용하는 대신에 , automatic memory pinning(pin_memory = True)을 이용해서  사용하는 것을 추천한다고 합니다. 이는 CUDA가 사용가능한 GPO로 빠른 data 전송을 하게 한다고 합니다. 


## Platform Specific

Python Multiprocessing을 사용하게 되면, OS에 따라서 , worker launch behavior가 달라집니다. 

1. UNIX
    + fork()가  multiprocessing을 시작하는 default method입니다. fork()를 사용하면 , child worker들은 복제된 address space를 통해 dataset과 Python functions에 직접 access 할 수 있습니다. 
  
2. Windows, MAC 
   + spawn()이 multiprocessing을 시작하는 default method입니다. spawn()을 사용해서 , 다른 interpereter들이 실행되면서 , main script를 실행합니다. 그 다음에 , pickle serialization을 통해 dataset, collate_fn , 그리고 다른 argument를 serialization 하고 , internal worker function을 실행합니다. serialization을 사용한다는건 multiprocess data load를 사용하는동안에 Windows와 호환이 되는지 확인하는 2단계를 실행해야 함을 의미합니다.
        1. main script code를 `if __name__  =='__main__'` block으로 둘러쌉니다. 
           + why? 각 worker process가 실행될 때 , 다시는 main script code가 실행되지 않도록 하기 위해서입니다. main script code에 Dataset,Datalodaer instance 생성 코드를 포함시켜 , worker에서 다시 실행되지 않도록 합니다.
        2. custom collate_fn ,worker_init_fn , 그리고 dataset code가 top level definitions에서 ,즉 `__main__` 을 check하는 code 바깥에서  define되도록 합니다.
            + why? fucntions들이 bytecode가 아닌 reference로써 pickled 되기 때문입니다.

## Randomness in multiprocessing data loading

각 worker는 seed를  (base_seed + worker_id)로 설정합니다. base_seed는 main_process에 의해서 생성이 되는데 , 이 때 RNG(Random Number Generator) 나 지정한 generator를 이용하게 됩니다. 하지만, 다른 라이브러리의 seed가 중복이 될 수 있습니다. 따라서 ,  worker 가 initialized 될 때 , 각 worker가 동일한 random number를 return할 수 있습니다.   
worker_init_fn에서 torch.utils.data.get_worker_info().seed 나 torch.initial.seed를 사용하면 각 worker에 대한 Pytorch seed set에 access하고 , 이를 사용하여  , data 를 loading하기 전에 다른 라이브러리로 seed를 전달할 수 있습니다. 

## Issues
+ Problem
  + iteration을 몇 번 반복하고 나면 , loader worker process가 parent process에 있는 모든 Python Objects(worker process에서 access가능)에 대해 같은 양의 CPU Memory를 점유하고 있습니다.만약에 , Dataset이 엄청나게 큰 data(ex.매우 큰 filename lsit)를 포함하거나 수많은 worker를 사용한다면 문제가 발생할 것입니다.
+ Why?
  + 임의의 Python Objects를  shared memmory에 저장하는 것은 copy-on-write problem을 발생시킵니다. 이 object들을 read할 때마다 , reference count를 증가시킵니다. reference count의 변화로 인해 fork된 python process의 copy-on-acess problem이 발생하게 되는것입니다. (Memory-leak 문제가 아닙니다.)
+ Sol 
  + 기본적인 Python Objects(list,dict) 대신에 pandas,numpy,pyarrow 같은 objects를 사용합니다. 이들은 reference count가 1입니다.  
  + String을 저장할 때에는 , ASCII code로 numpy array를 사용하여 저장할 수 있습니다. 아니면, ByteCode나 Custom Datatype을 사용할 수 있습니다.
# References

[Pytorch-data-docs][Pytorch-data-api]

[Pytorch-data-api]: "https://pytorch.org/docs/stable/data.html#single-and-multi-process-data-loading"




