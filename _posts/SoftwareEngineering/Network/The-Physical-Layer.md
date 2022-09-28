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

simplex는 정보를 한 방향으로만 전송하는 

![image](https://user-images.githubusercontent.com/50165842/192907219-b76b3246-9525-473f-a3f5-52338320cef1.png)

simplex는 

# Network Ports and panel


# References

[Google-IT-support][Google-It-support]

[Google-It-support]: "https://www.coursera.org/learn/computer-networking/lecture/Nihjd/moving-bits-across-the-wire" 

[Twisted-pairwiki][Twisted-pairwiki]

[Twisted-pairwiki]: "https://en.wikipedia.org/wiki/Twisted_pair"


[Simplex][Simplex]

[Duplex][Duplex]

[Duplex]: "https://en.wikipedia.org/wiki/Duplex_(telecommunications)"