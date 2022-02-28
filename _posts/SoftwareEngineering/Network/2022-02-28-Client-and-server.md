---
title : "Transport Layer Device : Client and Server"

excerpt: "Network System device at Transport Layer"

categories:
  - Network
tags:
  - [SoftwareEngineering , Network ,Network Device]
# classes : wide
toc: true
toc_sticky: true
---

Transport Layer에서 자주 사용되는 Client와 Server라는 개념이 있다. 이를 Google IT support에서는 networking device라고 부른다. device가 맞지만, 이 device안에 수많은 client와 sever가 동시에 실행될 수 있다. transport layer에서 사용되는 protocol에서 어떤 client와 어떤 server인지 확정을 지어주는데, 이번에는 이 client와 sever라는 것에 대해 알아볼려 한다. 

# Client and Server



![image](https://user-images.githubusercontent.com/50165842/155990435-1780af59-bfa4-4502-88f3-3550110dc47c.png)



간단하게 생각하면, Client는 data를 요청하고 수신하는 역할이고 , Server는 요청받은 data를 다시 보내는 역할이다. 

각 device를 node라고 부를 때 , node는 client 혹은 server 둘 다 될 수 있다.  

간략하게 말하면, node는 socket이라는 것을 만들어서  port라는것을 여러개 열 수 있는데 , 각 port가 server 혹은 client의 역할을 할 수 있다.(자세한 사항은 나중에 transport layer를 다룰 때 설명하도록 하겠다.)

![image](https://user-images.githubusercontent.com/50165842/155991286-bb5e84fe-a8d2-45e1-990b-65af46e370f8.png)

예를 들면 , mail server는 client에게 응답하는 port를 열어서 server가 될 수도  있고 ,  DNS server에 요청하는 Client가 될 수도 있다.



Server,Client라는 표현은 역할을 나타낼 수도 있고, 주된 역할을 나타내기도 한다. 

