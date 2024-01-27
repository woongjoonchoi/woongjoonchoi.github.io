---
title : "Synchronize,Asynchronize  vs  Non-blocking,Blocking  : Synchronize와 Blocking은 같은거 아닌가?"

excerpt: "I/O , CPu overlaping"

categories:
  - Development
tags:
  - [SoftwareEngineering , Resource Management]
# classes : wide
toc: true
toc_sticky: true
---

# Synchronize,Asynchronize,Non-blocking,Blocking??
요새 딥러닝 프레임워크인 pytorch docs를 읽다가 blocking이라는 단어를 보게 되었습니다. blocking이라는 단어가 별 뜻이 없는 줄 알았는데, 관련 내용을 forum에서 찾아보다가 NVIDIA의 패트릭 형님이 깊은 이해를 위한 링크를 달아주신 걸 보았습니다. NVIDIA의 post였는데 blocking, non-blocking, synchronize,asynchronize가 수십번은 반복되는데, 내용이 전혀 이해되지 않았습니다. blocking, non-blocking을 검색하면서 이 개념은 프로세스의 동작 제어와 관련한한 내용임을 알았습니다. synchronize, asynchronize와 blocking, non-blocking은 다른 개념이라는 사실을 처음 알게 되었습니다. 관련 포스트를 보면서, synchronize, asynchronize를 세부적으로 이해하지 못하고 있음을 알았습니다. 엔지니어로서 동작 제어권은 프로세스를 설계하는 데 중요하므로 꼭 알아야 한다는 생각이 듭니다. 
## Sync vs Async

![image](https://onedrive.live.com/embed?resid=7E81BBCD99889380%217840&authkey=%21APnBHuRo6P7JPu0&width=435&height=250)  
위 그림은 Synchronous, Asynchronous 를 나타내는 그림입니다. 그림을 보면 프로세스 A가 프로세스 B의 동작을 기다리는지가 차이점입니다. 이를 일반적으로 정의해보겠습니다. 
 1.  synchronization is the task of coordinating multiple of processes to join up or handshake at a certain point, in order to reach an agreement or commit to a certain sequence of action.
 2.  Asynchrony, in computer programming, refers to the occurrence of events independent of the main program flow and ways to deal with such events. 
   
Sync ,Async 는 앞에 철자 a가 붙어있기 때문에 반대되는 개념이라고 예측이됩니다. 하지만, 설명이 반대되는 내용이 아닌거 같습니다. 아래의 reference에서 본 설명이 이해가 잘 되었기에 그대로 적어보도록 하겠습니다.

1.  synchronization은 B process를 호출한 process A가 B process의 처리결과를 신경써서 기다리는 것이다.
2.  asynchronization은 B process를 호출한 process A 가 B process의 처리결과를 신경쓰지 않고 기다리지 않고 자신의 동작을 수행한다. 

호출한 process A가 특정 부분에서 다른 process를 호출하고 동작의 순서가 특정한 순서가 되도록 하는 것은 process A가 나머지 process들의 처리결과를 신경쓴다고 볼 수 있습니다.  
main program ,즉 process A와 특정 이벤트를 처리하는 방법, 즉 process B과 서로 신경쓰지 않고  process B가 발생합니다. process A가 process B의 처리결과를 신경쓰지 않고 자신의 동작을 수행함과 같은 의미입니다. 
## Block vs Nonblock
blocking,non blocking은 I/O api에서 사용되는 개념입니다. data를 read하거나 write하는 등의 작업이 I/O 작업입니다. 
1. Blocking : approach to I/O would be to start the access and then wait for it to complete.
2. Non-locking : a form of input/output processing that permits other processing to continue before the I/O operation has finished. 

앞의 sync,async에 비해 설명이 명시적으로 반대의 성질을 갖고 있습니다. blocking은 호출되면 제어권을 호출한 process에게 넘겨주지 않고 , non-blocking은 호출한 process에게 제어권을 넘겨줍니다. 이를 간단하게 정의하면 다음과 같습니다. 이번에도, reference의 블로그의 설명을 그대로 차용했습니다.

1. blocking : 호출된 함수가 호출한 함수에게 자신의 동작이 끝날때 까지 제어권을 넘겨주지 않는다.
2. non-blocking : 호출된 함수가 자신의 동작이 끝나지 않았음에도 호출한 함수에게 바로 제어권을 넘겨줍니다. 

## Four patterns 
![image](https://onedrive.live.com/embed?resid=7E81BBCD99889380%217846&authkey=%21AIFoSWztVEf8__U&width=280&height=230)  
이번에는 sync,async 그리고 blocking,non blocking을 결합한 모든 경우 4가지를 살펴보도록 하겠습니다. 아래의 예시들은 IBM의 블로그에서 가져왔습니다. 
### Synchronous blocking I/O
![image](https://onedrive.live.com/embed?resid=7E81BBCD99889380%217845&authkey=%21ACDJFjlBed31WXw&width=768&height=493)  
user application이 system call을하고 system call이 완료될 때 까지 system call이 application을 block합니다. 
여기서 cpu를 차지하지 않고 system call의 response를 기다리고 있기에 효율적이라 볼 수 있다고 합니다.  
read system call이 호출되면 , system call이 application을 block하고 context는 kernel 전환되어집니다. 

### Synchronous non-blocking I/O
![image](https://onedrive.live.com/embed?resid=7E81BBCD99889380%217849&authkey=%21APD7PUTY2InbhPo&width=546&height=392)  
이 case에서는 user application이 system call을 요청하는데 , read는 error를 return하고 call이 수행되지 않습니다. 대부분의 case에서 user application은 data를 읽어야 다음 작업을 수행할 수 있기에 무수히 많은 system call을 read가 수행될 떄 까지 하게 됩니다.  
data가 kerenl에서 접근이 가능해지고 , user 가 호출한 read가 다시 return 되기 까지의 시간이 길어지기에  , IO에서 latency가 증가되고, 따라서 전체적인 처리량이 감소하게 됩니다.  

### Asynchronous blocking I/O
![image](https://onedrive.live.com/embed?resid=7E81BBCD99889380%217849&authkey=%21APD7PUTY2InbhPo&width=546&height=392)  
이 case에서는 user application이 select라는 system call을 요청하는 case입니다. select는 file descriptor가 쓰기 가능한지, 읽기 가능한지 에 대한 것을 알려줍니다. 하나의 file descriptor만이 아니라 1024 이하의 file descriptor에 대해 알려주게 됩니다. 여기서, user application은 다음 작업을 하러 가도 됩니다. 즉, asynchronous합니다. 하지만,select가 user application을 blocking 하기에 기다리게 됩니다.  
이 select는 비효율적이기에 고성능 I/O에서 사용되는 것은 권장되지 않는다고 합니다. 

### Asynchronous non-blocking I/O (AIO)
![image](https://onedrive.live.com/embed?resid=7E81BBCD99889380%217848&authkey=%21AKSg7EXa9R4YtLk&width=559&height=388)  
asynchronous non-blocking I/O 모델은 호출한 process와 I/O의 작업을 overlap하여 실행하게 됩니다. user application이 aio_read라는 system call을 하게되면 read가 성공했다고 즉시 return을 받게됩니다. user application은 다른 process를 수행하면서, 동시에 background에서는 I/O가 read를 수행하고 있습니다.  read의 response가 user application에 도착하면 , signal이나 callback이 발생하여 data가 kernel에서 user application으로 전송되게 됩니다. 
여러 I/O 요청에 대해 단일 프로세스에서의 계산과 I/O처리를 겹치는 것은 프로세스의 처리속도와 I/O의 처리속도의 차이를 최대한 활용합니다. 하나 이상의 느린 I/O요청이 처리되는 동안 CPU는 프로세스의 다른 작업을 처리할 수 있고, I/O가 처리가 되면 CPU는 이에 대해 동작을 수행하고 동시에 새로운 I/O요청이 수행됩니다.  
## Conclusion 

Program을 코드로 작성할 때 원하는 동작을 하는지에 대해만 관심이 있었을 뿐 , device가 computer의 resource를 얼마나 효율적으로 활용하는지에 대해서는 크게 신경을 안쓰고 있었다는 것을 깨달았습니다. Operating System수업을 학부시절에 들었지만, 이를 실제로 제 Program에 어떻게 접목시킬지는 크게 고민을 안해봤습니다.지금부터라도 , Operating System에서 배웠던 지식들을 어떻게 제 Program에 접목시킬지를 고민해보고 추가적으로 Operating System을 더 깊이있게 공부해야 함을 깨달았습니다.

## References

https://musma.github.io/2019/04/17/blocking-and-synchronous.html

https://developer.ibm.com/articles/l-async/

https://en.wikipedia.org/wiki/Asynchronous_I/O

https://en.wikipedia.org/wiki/Asynchrony_(computer_programming)

https://en.wikipedia.org/wiki/Synchronization_(computer_science)