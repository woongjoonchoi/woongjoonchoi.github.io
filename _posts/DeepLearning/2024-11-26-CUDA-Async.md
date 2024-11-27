---
title : "Asynchronous in CUDA "



excerpt: "What is "Async" in CUDA ? "

# categories:
#   - 
tags:
  - [deep learning]
# classes : wide
toc: true
toc_sticky: true
---
연구실에서 박사님과 미팅을 하던 도중 한가지 의문점이 생겼습니다. 
> 과연 Nvidia가 말하는 Asynchronous는 Computer Science의 Asynchronous와 같은 개념인가?  

Nvidia의 Document에서는 Aysnchronous라는 단어가 자주 등장합니다. Asynchronous programming model은 concurrent programming을 위한 것입니다. Nvidia의 GPU는 parallel processing에 특화되어 있으므로 Asynchronous 라는 단어를 자주 사용하는 것은 자연스러운 흐름입니다.  
**하지만, 우리는 Asynchronous 라는 단어의 명확한 의미를 고려해 볼 필요가 있습니다.**

## What is Asynchronous in computer programming?
[Asynchronous 블로그 링크](https://woongjoonchoi.github.io/development/Sync-vs-Async-Block-vs-Nonblock/)  
위 링크는 제가 예전에 Asynchronous관련해서 정리한 블로그 링크입니다. 읽어보시면 도움이 될거라 생각합니다.  
Asynchronous Programming의 정의는 wikipedia에 다음과 같이 적혀있습니다.
> Asynchrony, in computer programming, refers to the occurrence of events independent of the main program flow and ways to deal with such events. 

제 개인적으로 이를 좀 더 구체화 하면 다음과 같다고 생각합니다. 
> Asynchronous는 thread가 실행 중에 External Event가 발생할 때 선택할 수 있는 option이다.

program은 process안에 isolated 된채로 Memory에 load되어 실행이 되어집니다 . 실제 program을 실행하는 최소 unit은 thread가 됩니다. thread는 실행이 되면서 Internal events(ex.yield) 나 external events(ex.Interrupt)를 통해 processor의 통제권을 다른 thread나 process에게 넘기고 ready queue나 wait queue에 들어가서 다시 scheduler에 의해 processor의 통제권을 얻고 실행을 하는 과정을 반복하게 됩니다. Asynchronous는 Interrupt를 통해 wait을 하는 상황에서 CPU의 통제권을 즉시 넘겨받고 Internal Events가 발생할 때까지 실행을 하게 해주는 option입니다.  

이러한 개념을 토대로, Asynchronous Programming을 CUDA로 확장을 해볼려 합니다.

## Terminology
우선, technical terminology에 대해 알 필요가 있습니다.
**Cuda Thread** 는 GPU의 SM(streaming multiprocessor)에서 Operation을 실행할 때 실행의 최소 unit입니다. 즉, GPU안에서의 thread라 보면 됩니다.  
**Kernel**은 GPU에서 C++ extension으로 작성된 user-defined function입니다.  
**Hetreogeneous Programming** 은 CUDA에서 Host 와 Device의 실행이 Parallel하게 된다는 것을 의미합니다. Host는 cuda의 api를 호출하는 computer, device는 GPU를 의미합니다. device와 computer는 서로 분리되어 있기에 Parallel하게 실행이 될 수 있습니다. 
**Cuda Context**는 CPU에서의 Process와 유사한 개념입니다. **Cuda Thread**에게 격리된 환경을 제공하여 Protection을 제공해줍니다.
**parallel programming** 복수의 program을 한 순간에 동시에 실행 시키는 것입니다.  
**concurrent programming** 은 복수의 program을 1개의 core에서 interleaving을 통해 실행을 시키는 것입니다.
**Streams**는 cuda command의 sequence를 의미합니다. 
## Asynchronous in CUDA 
Asynchronous는 CUDA에서 다음과 같이 정의할 수 있다고 생각합니다.
> Host thread와 Cuda thread 에서 External Event가 발생할 때 선택할 수 있는 option이다. 

기존의 Asynchronous는 CPU에서 실행되는 thread 만을 고려했다면, CUDA에서의 Asynchronous는 GPU에서 실행되는 thread로 개념을 확장했다고 볼 수 있습니다 .

## Concurrent Programming in CUDA

Nvidia는 C++ CUDA Programming Guide라는 600pg 짜리 document를 작성했습니다. 이 Docs에는 Concurrent Programming의 여러 Scenario가 설명이 되어있습니다. 이를 통해서 어떤 경우에 Asynchronous라는 용어가 사용됬는지를 알아보겠습니다.  

### Concurrent Execution between Host & Device 
Host와 Device의 Programming Model은 **Hetereogeneous Programming** 입니다. 따라서 , Host가 Cuda API를 호출 하고 GPU Device의 thread가 끝날때 까지 기다릴 수도 아니면 Host의 operation을 수행하러 갈 수도 있습니다.CUDA Device의 operation은 외부 device의 operation이기에 , Host Thread 입장에선  External Events가 발생하는 것입니다.  processor의 utilization을 높이기 위해서 Asynchronous Programming을 사용하여 Host와 Device의 Operation을 Parallel하게 수행하게 됩니다.

### Concurrent Kernel Exectuion
Cuda에서 한 Kernel을 실행시키면 Cuda Context가 설정이 되고 여러 Cuda Thread가 이를 Interleaving을 통하여 실행을 하게 됩니다.최신의 몇몇 GPU들은 같은 cuda context내의 다른 kernel들을 concurrent programming을 활용하여 실행시킬 수 있습니다. Default Option으로는 다른 Cuda Context의 Kernel을 Concurrent Programming을 통해 실행 시킬수 없습니다.하지만, MPS(multi processes service)라는 것을 활용하여 cocurrent하게 실행시킬 수 있습니다.   
여기서는 , asynchronous라는 용어를 사용하지 않았습니다. Cuda에서 Kernel이 실행될 때에는 ,  External Event가 발생하지 않습니다. Internal Events 만으로 Interleaving을 하기에 Asynschronous라는 용어를 사용하지 않았다고 생각합니다.

### Overlap of Data Transfer and Kernel Execution

Data Transfer 와 Kernel Execution을 Overlap할 수 있습니다. Cuda Thread나 Host Thread에서 asynchronous memory copy를 호출하고 바로 kernel을 실행할 수 있다고 적혀있습니다. Data Transfer는 thread에서 호출을 하지만 실질적인 Data transfer는 메모리 컨트롤러나 DMA(Direct Memory Acess) 컨트롤러가 담당하게 됩니다.thread는 이러한 External Events가 발생하면 asynchronous programming을 통해 processor의 control을 thread가 다시 돌려받아 Kernel을 실행할 수 있게 됩니다 .이는 Device 간의 Transfer 뿐만 아니라 Device안의 Cache와 Main Memory 그리고 register간의 transfer에도 적용될 수 있습니다 .

### Overlap of Data Transfers

복수의 Data Transfer를 overlap 할 수도 있습니다. Data Transfer들은 메모리의 컨트롤러에 의해서 설정이 됩니다. Data Transfer들은 thread에서 실행중인 program이 아니기에 asynchronous라는 용어를 사용하지 않습니다.


## Conclusion

**Asynchronous**라는 개념을 일반적인 thread와 일반적인 External Events로 확장해서 좀 더 깊은 이해를 하게되었습니다. 앞으로도, 여러 Operating System이나 Network 개념을 GPU나 NPU로 확장하여 생각할 때 오늘의 글이 도움이 될 거 같습니다.



## References


[cuda-c-programming-guide/programming-models](https://docs.nvidia.com/cuda/cuda-c-programming-guide/#programming-model)
[cuda-c-programming-guide/cuda-runtime](https://docs.nvidia.com/cuda/cuda-c-programming-guide/#programming-model)