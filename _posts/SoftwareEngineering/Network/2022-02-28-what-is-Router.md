---
title : "Network layer device :Rotuer"

excerpt: "Network System device at Network Layer"

categories:
  - Network
tags:
  - [SoftwareEngineering , Network ,Network Device]
# classes : wide
toc: true
toc_sticky: true
---
single network로 컴퓨터를 연결할 때 , 즉 , LAN을 만들 때 , switch hub를 사용하게 된다. 하지만, Network를 연결할 때에는 어떻게 할까요?

# Router란?

Router는 network 간에 data를 어떻게 전송할지 결정하는 device 입니다. 2 layer에서는 switch, 1 layer에서는 hub를 사용하지만 , 3 layer에서는 router를 사용하게 됩니다.

switch는 어디로 data를 보낼지 결정하기 위해서 , ethernet frame의 destination mac address를 검사하는 것처럼, router는 ip address를 검사합니다.

Router 내부에는 table이 담겨있는데, 전 세계 다른 network에 어떻게 traffic을 routing 할지가 담겨 있습니다.



# Core Router vs home/small router

home이나 작은 회사에서 사용하는 router는 routing table이 담겨있지 않습니다. 이 router의 목적은 lan으로 부터 traffic을 받아서 isp를 따라서 전진하게 하는 것입니다. 따라서, 세부적인 routing table이 없습니다.

traffic이 ISP로 오게되면, 더 복잡한  router에 도달하게 됩니다. Core Router는 Internet에서 traffic이 어떻게  전송될지 혹은 Internet으로부터 어떤 data를 수신할지 책임을 집니다.  

Core Router와 home/small router의 다른 점은 core router는 더 많은 data를 전송하고 , 어디로 보낼지에 대해서 더 복잡한 결정을 하게 됩니다.

# BGTP

router에서는 bgtp라는 protocol을 사용하는데, 이는 traffic을 전송하기 위해서 optimal한 path를 배우게 합니다. 

