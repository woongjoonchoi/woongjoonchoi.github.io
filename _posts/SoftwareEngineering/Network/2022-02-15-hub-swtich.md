---
title : "Hub and Switch"

excerpt: "Network System device"

categories:
  - Network
tags:
  - [SoftwareEngineering , Network ,Network Device]
# classes : wide
toc: true
toc_sticky: true
---
![image](https://user-images.githubusercontent.com/50165842/154070819-176a5078-c66d-4c77-ac30-cae03f26169f.png)

cable이 있으면 point-to-point 연결이 가능하다. 하지만, 이는 더 많은 유저들을 연결할 때 Point-to-Point 만으로는 한계가 있다. 이를 가능하게 해주는 것이 Hub,Switch이다.



# Hub

![image](https://user-images.githubusercontent.com/50165842/154070931-6bd1b5d0-68ba-4d49-8221-85c69d41c304.png)



Hub 는 여러 Node들을 연결하는 Physical Layer의 Device이다 .

![image](https://user-images.githubusercontent.com/50165842/154071101-99b05343-532c-40f3-af59-a2a1081b2fe3.png)

하지만, Hub는  data를 전송하면 모든 node가 이 data를 수신하게 된다. 따라서, 한번에 여러 node들이 data를 전송하게 되면 이 data가 corrupt된다. 이를 Collision domain이라고 한다.  

복수의 system이 data를 전송할려 하면 전기 pulse가 cable에 영향을 끼친다. Collision domain이 발생하게 되면 , data를 재전송해야 하므로 재전송하는동안 기다려야 한다. 

이것이 Hub가 잘 쓰이지 않는 이유입니다 .



# Switch

![image](https://user-images.githubusercontent.com/50165842/154071211-836c7c2f-6542-46ed-ab2a-fa260a2088d7.png)

Switch는 DataLink Layer의 device이다. 

Switch는 Ethernet protocol을  검사할 수 있다.  따라서,이 data가 어떤 source로 부터 왔으며 어떤 destination으로 가는지 알 수 있다. 따라서, 모든 system에 전송하지 않고 destination에만 전송하게 된다. 



collsion domain이 줄어들어 재전송률이 줄어든다. 



# Reference

[Hubs and Switches  Coursera](https://www.coursera.org/learn/computer-networking/lecture/8rMWU/hubs-and-switches)
