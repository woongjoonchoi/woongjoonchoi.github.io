---
title : "The Phyiscal Layer"

excerpt: "TCP 5 layer model-phyiscal"

categories:
  - Network
tags:
  - [SoftwareEngineering , Network ,TCPIP]
# classes : wide
toc: true
toc_sticky: true
---
예전 Post에서 TCP-IP Model에 대해 설명을 한 적이 있습니다. TCP-IP Model은 5개의 layer로 이루어져 있는데, 오늘은 가장 아래 layer인 Physical Layer Model에 대해서 설명해보도록 하겠습니다. 

# Physical Layer?

![image](https://user-images.githubusercontent.com/50165842/192401951-1c3511fd-0043-4d22-84a4-ccca355c97fc.png)

Physical Layer란 여러 device에서 다른 Network로 Bit라는 정보를 보내게 됩니다. Bit는 computer가 이해할 수 있는 data 의 representation입니다. 0,1의 값을 가지고 있습니다.   



구리 네트워크 케이블에 Modulation이라는 과정을 거쳐서 bit를 만들어 보내게 됩니다. Modulation이란 cable을 따라 움직이는 전하의 전압을 다르게 하는것을 의미합니다. 
![image](https://user-images.githubusercontent.com/50165842/192905671-59927d83-99a7-4192-a317-c08583b6b058.png)


Computer Networking에서 이러한 Modulation을 line coding이라 부릅니다. 현대 networking에서는 10 기가비트 네트워크라는 단어가 많이 등장하는데 이는 100억개의 bit(0,1)을 각 cable마다 1초에 처리한다는 의미입니다. 


# Twisted Pair Cabling

![image](https://user-images.githubusercontent.com/50165842/192906277-22635c2f-4c75-4f5f-9ba2-06b0fc052f38.png)

twisted pair란 구리선이 꼬아져있음을 의미합니다. 꼬아져 있는 cable pair로 이루어져 있어서 twisted pair cable이라 합니다. 이렇게 cable을 꼬아두면 , 전자기간섭 , 즉 crosstalk을 줄여줍니다.  
예를 들어, cat6 cable은 8개의 wire,즉 4 pair를 사용합니다. 전송기술에 따라서  몇개의 pair를 사용할 지 결정합니다.
# Duplexing

근대의 네트워킹에서 cable은 duplex communication이나 simplex communication을 사용합니다.    
![image](https://user-images.githubusercontent.com/50165842/192907374-7139d5a2-fa5c-4ab9-9c73-eb429b4f7593.png)  

simplex는 정보를 한 방향으로만 전송하는 communication 방식을 의미합니다.그림과 같이 한 쪽의 device가 일방적으로 수신하거나 송신하는것을 볼 수 있습니다. 대표적인 예시로는 , 라디오나 TV가 있습니다. 방송국에서 전파를 송신하고 , 청자는 그 전파를 수신하기만 합니다.  
![image](https://user-images.githubusercontent.com/50165842/193703834-b4a8bc16-a6b1-45cb-93ed-7761ef8dc4c8.png)  

Duplex는 Client와 Server가 양방향으로 communication을 할 수 있는 방식입니다. 그렇다면 , 이것이 어떻게 가능한 것일까요?  
cable은 여러개의 pair로 이루어져 있는데 ,각 pair를 한 방향으로만 미리 예약을 해두는 것입니다. 정확히 동시에 양방향 communication이 가능하다면 , 이를 full duplex라고 부릅니다.

![image](https://user-images.githubusercontent.com/50165842/192907219-b76b3246-9525-473f-a3f5-52338320cef1.png)

만약에 Network에 문제가 생긴다면, Network는 link가 degrade 되고 , half-duplex communication을 한다고 보고합니다.  
이 때, Network는 양방향으로 communication이 가능하지만, 한번에 한방향으로만 가능합니다. 

# Network Ports and panel

Twisted Pair network cable에는 끝에 Plug가 있습니다. 

![image](https://user-images.githubusercontent.com/50165842/193705135-31a0b36a-c130-4664-861c-a2d8071ecb6e.png)  

가장 대중적인 plug는 RJ45 plug(Registered Jack 45)가 있습니다.  
![image](https://user-images.githubusercontent.com/50165842/193705565-1e8ffa88-ec48-40a4-9dbb-c64140880ae7.png)  
![image](https://user-images.githubusercontent.com/50165842/193705655-9bafd997-c574-4eb7-ba11-c963cbcda4dc.png)  
저번에 설명했던, Switch에는 여러개의 port가 있는반면, Server나 Desktop에는 1 개나 2개의 port가 달려있습니다.  
![image](https://user-images.githubusercontent.com/50165842/193705381-c4cb44c5-1763-43dd-80cb-555e3f94757c.png)  


모든 네트워크 port는 link led, activity led라는 2개의 port를 가지고 있습니다. LinkLed는 두 Device가 잘  연결되면 전원이 켜집니다. activityLed는 data가 활발하게 전송되면 ,  LED가 깜빡입니다.   
요즘은, 컴퓨터 네트워크가 워낙 빨라서 ,Activity Led는 실제로 traffic이 있는지 없는지 외에는 의미하지 않습니다.  

![image](https://user-images.githubusercontent.com/50165842/193705887-78fbb6cf-3513-46a8-a44d-f4fd93120f73.png)
가끔씩, 책상 아래에 network port가 붙어있는 경우가 있습니다. 이 포트들은 네트워크 케이블을 통해서 , 벽을 넘어가서 patch-panel에 도달하게 됩니다. patch-panel에는 많은 port들이 잇지만, 그저 port의 container일 뿐입니다. 이 patch panel로 부터 나온 cable은 switch나 router나 computer로 향하게 됩니다.

# References

[Google-IT-support][Google-It-support]

[Google-It-support]: "https://www.coursera.org/learn/computer-networking/lecture/Nihjd/moving-bits-across-the-wire" 

[Twisted-pairwiki][Twisted-pairwiki]

[Twisted-pairwiki]: "https://en.wikipedia.org/wiki/Twisted_pair"


[Simplex][Simplex]

[Simplex]: "https://en.wikipedia.org/wiki/Simplex_communication"

[Duplex][Duplex]

[Duplex]: "https://en.wikipedia.org/wiki/Duplex_(telecommunications)"