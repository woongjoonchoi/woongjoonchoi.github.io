---
title : "Pytorch data api : worker, pinned memory, prefetch,  non-blocking?? 이 것들이 다 설정되면 어떻게 작동할까요?"



excerpt: "Data Transfer optimization and overlap"

categories:
  - Pytorch
tags:
  - [Machine Learning,Pytorch,deep learning ,DeepLearning General Method]
# classes : wide
toc: true
toc_sticky: true
---
# Pin-memory,worker,pre-fetch,gpu non-blocking을 다 합쳐서 생각해보자. 
pytorch의 docs를 보면서 worker라는 configuration을 지정해 주면 단순히 worker라는 게 생겨서 data를 loading 해준다고만만 알고 있었습니다. 이를, pin-memory,pre-fetch , GPU non-blocking과 결합을 하는 순간 어떻게 동작하는 것인지 전혀 이해를 못 하는 저 자신을 보게 되었습니다. Pytorch자체는 high-level API를 제공하는 framework이지만, 이 configuration은 deep learning model의 훈련을 low-level에서 설정해서 진행하도록 해주는 것임을 알게 되었습니다. low-level에서 훈련 configuration을 조정하는 것은 딥러닝 엔지니어로서 만이  아니라 소프트웨어 엔지니어로써의 역량향상에 도움이 될 것이라 여겨집니다.  

!! Asynchronous ,Sychronous가 무엇인자 모른다면 이 [post]()를 보시고 오기를 권장합니다. 
## worker
Pytorch의 DataLoader class를 보면 num_worker라는 configuration이 있습니다.num_worker에 대한 설명은 다음과 같습니다.
```
num_workers (int, optional) how many subprocesses to use for data loading. 0 means that the data will be loaded in the main process. (default: 0)
```

subprocess가 fork()로 생성되어(unix의 경우), batch_size * num_worker만큼의 data를 로딩하게 됩니다. 여기서, 주목해야 할 점은 호출하는 main process는 worker에 대해 asynchronous 하다는 점입니다. 즉, main process가 data를 GPU로 로딩하고 훈련하게 되면, num worker는 parallel 하게 data를 fetching 하게 됩니다.

num_worker configuration을 사용할 때 주의점은 CPU의 메모리를 얼마나 잡아먹는지를 고려하면서 사용해야 한다는 점입니다. parent process와 똑같은 양의 CPU 메모리를 worker process가 차지하게 되는데, 만약에 Datset Class가 매우 큰 List를 포함하고 있다면 `worker * parent process size`만큼의 메모리를 차지하게 될 것입니다. 

## Pin memory

Pin Memory에 대한 pytorch의 설명은 다음과 같습니다.
>pin_memory (bool, optional) If True, the data loader will copy Tensors into device/CUDA pinned memory before returning them. If your data elements are a custom type, or your collate_fn returns a batch that is a custom type, see the example below.

Tensor를 pinned memory에 복사한다는 것이 무슨 의미인지 알아보도록 하겠습니다. 

### nvidia

![image](https://onedrive.live.com/embed?resid=7E81BBCD99889380%217834&authkey=%21ALlP7XyB6BzlyAo&width=717&height=379)

Cuda 에서 host에서 device(GPU)로 data를 전송하기 위해서는 임시로 lock 된 page, pinned memory에 data를 전송해야만 합니다. 그다음에, device로 data를 전송하게 됩니다. git의 staging area와 비슷한 개념이라 볼 수 있습니다. git에서 commit 객체로 변경 사항을 이동시키기 위해서는 staging area를 거치는 것과 유사한 개념입니다.
Pinned memory를 고정해 놓음으로써 host의 pageable memory와 pinned memory 사이의 전송 cost를 없앨 수 있습니다.  

## prefetch
prefetch의 pytorch docs의 정의는 다음과 같습니다.  

>prefetch_factor (int, optional, keyword-only arg) Number of batches loaded in advance by each worker. 2 means there will be a total of 2 * num_workers batches prefetched across all workers. (default value depends on the set value for num_workers. If value of num_workers=0 default is None. Otherwise, if value of num_workers > 0 default is 2).  

모든 worker가 prefetch * batch_size만큼의 data를 미리 loading 하게 됩니다. 따라서, Disk와 host 간의 통신이 줄어들게 되어 overhead를 많이 줄일 수 있게 됩니다. 하지만, 이 역시 CPU의 spec을 고려해서 잘 설정해야 합니다.  

## non_blocking
non block의 pytorch docs의 정의는 다음과 같습니다. 

> Returns a Tensor with the specified device and (optional) dtype. If dtype is None it is inferred to be self.dtype. When non_blocking, tries to convert asynchronously with respect to the host if possible, e.g., converting a CPU Tensor with pinned memory to a CUDA Tensor. When copy is set, a new Tensor is created even when the Tensor already matches the desired conversion.

이 option은 pinned_memory 가 true 설정되어 있다면, 즉 pinned memory가 고정되어 있을 때 사용할 수 있는 옵션입니다. host는 tensor의 GPU로의 전송에 대해 asynchronous 하게 됩니다. 즉, tensor를 GPU로 전송하는 함수를 host는 신경 쓰지 않고 다음 동작을 수행하게 됩니다. 이에 대해, 내부적으로 어떻게 작동하는지 NVIDIA의 docs를 통해 알아보도록 하겠습니다.


### nvidia
![image](https://onedrive.live.com/embed?resid=7E81BBCD99889380%217835&authkey=%21AK_rXcRO4cr-r1s&width=1139&height=727)  

이 그림에서 h2d는 device에서 host로의 data 전송, kernel은 병렬 computation,d2h는 device에서의 hardware로의 data 전송을 의미합니다.
NVIDIA의 Cuda 에서는 data를 작은 chunk들로 쪼갠 다음에, data transfer와 computation을 scheduling 하여 bottleneck을 줄인다고 합니다 .
여기서, sequential 버전의 경우 ,h2d 와 관련된 stream(함수)은 host에 non-block이면서, host는 h2d stream에 대하여 asynchronous 합니다. 제어권이 다시 host에 돌아오기 때문에, computation을 병렬적으로 진행되게 됩니다.

### 주의점

 염두에 둬야 할 것이 있습니다. non-blocking configuration을 설정하는 것 자체는 program에 나쁜 점은 없습니다. 하지만, 실제로 non-blocking을 사용하는 것과 사용하지 않은 것의 차이가 없을 수 있습니다. 병렬적으로 computation을 하지 못하고 모든 small chunk data를 기다려야 하는 지점, 즉 sync point 때문입니다. 예를 들어, tensor를 transformation 하는 것은 small chunk에 대해 각각 적용되어도 최종 결과에는 차이가 없습니다. 하지만, 특정 dimension에 대해 normalize를 하는 경우, 예를 들어 batch normalization, batch 전체에 대한 statistic을 계산해야 하기에 hots 는 batch norm 함수에 대해 synchronous여야 합니다.

 즉, model의 computation에 대한 수학적인 이해가 바탕이 되지 않는다면 성능에 대한 profiling을 할 수 없을 것입니다. 

## All together
worker, pinned memory, prefetch, non blocking을 전부 합쳐서 data fetching 과정을 설명해보도록 하겠습니다.  
![image](https://onedrive.live.com/embed?resid=7E81BBCD99889380%217836&authkey=%21AO0XjU31chGSrI8&width=730&height=414)  
pin memory configuration이 설정되어있고 num_worker는 2 로 설정되었습니다. 따라서, main process(host)는 batch * 2 만큼의 data를 pinned memory에 loading 하게 됩니다.  
![image](https://onedrive.live.com/embed?resid=7E81BBCD99889380%217837&authkey=%21AKiwiniQwNuDN2o&width=696&height=463)  
data가 device(gpu)로 전송이 되면 , host는 data 전송에 대해 asynchronous 하기 때문에 dataloader는 다음 batch data를 pinned memory에 로딩하게 됩니다.  
![image](https://onedrive.live.com/embed?resid=7E81BBCD99889380%217838&authkey=%21AE0hAnAwRJMia-4&width=750&height=451)  
prefetch factor가 설정된다면,  prefetch factor*worker*batch만큼의 data를 loading하게 됩니다. 나머지 과정은 위와 동일하게 진행이 될 것입니다.  

![image](https://onedrive.live.com/embed?resid=7E81BBCD99889380%217839&authkey=%21AJHSTuTRWcQ3g2s&width=1091&height=622)  
non-blocking이 설정이 된다면, 내부적으로 async version 2와 같이 data transfer와 computation이 overlap 될 것입니다. d2h transfer가 일어나는 때는 ,output을 cpu로 다시 전송하거나 model의 parameter를 update하면서 checkpoint를 host에 연결된 디스크에 저장하거나 같은 상황입니다.   

## Conclusion 

Data transfer를 computation과 overlap 하는 것을 추상화된 high-level API 관점에서만 사용할 수도 있습니다. 하지만, 저는 이를 좀 더 low-level에서 보았고, overlap이나 optimize가 어떻게 일어나는지 알게 되었습니다. high-level API에만 의존하고 내부 동작을 모르게 되면, 특정 librarary에 종속되어 customize나 debugging이 어려워집니다. low-level에서 어떻게 작동하는지를 고민하게 되면, customize와 debugging이 용이하게 되고, 동작에 대한 이해도가 더 깊어집니다. 또한, Computer Science에 대한 이해도가 깊어지고, 문제해결 능력이 향상되고, 뿐만 아니라 새로운 기술을 더 쉽게 배우는 데 도움이 됩니다. 아직 Junior의 입장이고, 정년은 더욱더 늘어나는 시대이기에 low-level로 생각하는 연습을 계속할 것입니다.

## References

https://d2.naver.com/helloworld/3773258


https://developer.nvidia.com/blog/how-optimize-data-transfers-cuda-cc/

https://discuss.pytorch.org/t/when-does-a-pytorch-dataset-worker-pull-new-data/153286


https://discuss.pytorch.org/t/how-to-maximize-cpu-gpu-memory-transfer-speeds/173855/4


https://docs.nvidia.com/cuda/cuda-runtime-api/api-sync-behavior.html#api-sync-behavior__memcpy-async


https://discuss.pytorch.org/t/non-blocking-memory-transfer-to-gpu/188941


https://discuss.pytorch.org/t/should-we-set-non-blocking-to-true/38234/24


https://discuss.pytorch.org/t/doing-data-augmnetation-in-parallel/90685/2


https://pytorch.org/tutorials/recipes/recipes/tuning_guide.html