---
title : "what is vector and operating on vectors:Computer Science perspective"



excerpt: "What  does on vectors?"

categories:
  - Linear Algebra
tags:
  - [Mathmathics,Machine Learning,Coursera,Linear algebra ,vectors]
# classes : wide
toc: true
toc_sticky: true
---


Vector란 흔히들 방향이 있는 무언가라고 알고 있다. 물리학에서 자동차의 속도를 vector라고 표현을 하면 , 실제로 , 앞 , 위 , 옆 방향이 있기에 받아들이기 쉽다. Machine Learning에서 Input을 Vector라고도 칭하는데, 이때 부터 알고있는 기존의 개념과 충돌이 오는듯한 경향이 있다. 예를 들어 , 어떤 집단의 평균이 input에 포함되어 있을 때 , 평균에 방향이 어떻게 있는지 잘 와닿지가 않는다. Machine Learning에서 말하는 vector가 무엇이며 , 이 vector들에 대해 알아야 할 것들을 간단하게 정리해보고자 한다.



# What is vector?

vector란 무엇일까? 물리학이나 공학 관점에서의 벡터는 공간상에서 움직이는 object 입니다 .여기서 공간은 물리적인 의미의 공간이 됩니다. 예를 들면, 어떤 자동차가 공간상에서 움직일 때 , 이를 vector라 볼 수 있습니다.

Computer Science에서 vector의 의미는 약간 달라집니다.  computer science에서 vector는 1 dimensional array data structure 입니다 .물리학이나 공학관점에서 벡터는 physical space에서 움직이는 object 였다면, Compuer Science 관점에서는 data space를 움직이는 object입니다. 따라서 , vector를 list of attribute라고 부르기도 합니다. 예를 들면, 한 가구를 , 소득, 재산, 구성원 수 등의 attribute로 구성된 list , 즉 vector로 표현할 수 있습니다 .

물리학이나 공학에서 사용하던 vector의 이론을 list of attribute를 적용할려고 합니다.

# Vector Operation

vector는 addition , multiply by scalar 이 두가지 rule에 근거하여 계산할 수 있습니다.

## addition

![image](https://user-images.githubusercontent.com/50165842/156884955-1e02a119-0fac-40b4-af69-e2f8c3c70e01.png)

1. Vector를 원점에서 생각하는 geometric object라 생각을 하고, 임의의 vector r을 만듭니다.

![image](https://user-images.githubusercontent.com/50165842/156885010-a9bc856c-6892-46bc-b770-149cf7840729.png)

2. 다른 vector s를 가져옵니다.

![image](https://user-images.githubusercontent.com/50165842/156885062-b620782b-6e16-4e6a-9533-d2c1ffd83be5.png)

3. s를 r의 끝에 이동을 시킵니다.

![image](https://user-images.githubusercontent.com/50165842/156885096-6a7ccaef-a0bd-4995-baaa-903ac6150af0.png)

4.  r 다음에 s를 따라가는 sum 을 얻습니다. 이를 r + s라고 부릅니다.



![image](https://user-images.githubusercontent.com/50165842/156945221-7cd648e9-814a-47b9-aa60-2b71d6cc46e9.png)

5. 다른 순서로 1~4 의 과정을 반복하여 s 다음에 r을 따라가는 vector를 만듭니다. 이는 s +r 이라 부릅니다.
6. 여기서, 우리는 r+ s , s+r의 결과가 같음을 알 수 있게 됩니다. 따라서, r+s = s+r이 됩니다.

여기서, 알 수 있는 것은 어떤 순서로 더하든 같은 결과값을 얻게 됩니다.

vector의 addition에서 왼쪽 operand가 기준이고 오른쪽 operand를 이동해서 이 성질을 증명해보았습니다.



## Multiply  by scalar

![image](https://user-images.githubusercontent.com/50165842/156945397-22b89bef-8a3b-4165-ba15-732b7fe9f545.png)



어떤 임의의 vector가 r이 있습니다. 이를 , 2개 더 이어붙이도록 하겠습니다. 그렇게 되면, 3r 이라는 벡터가 나오게 됩니다. 

![image](https://user-images.githubusercontent.com/50165842/156945497-876b8e60-801a-4f0b-bdbb-94fb2359a39e.png)



이 vector를 직선이 되도록 반대방향을 보게 하면 이 vetor를 -r이라고 부릅니다. 

# Define Coordinate System

## coordinate

위의 두 operation을 정의하게 되면, 두 개의 vector로 coordinate system을 정의할 수 잇게 됩니다.

![image](https://user-images.githubusercontent.com/50165842/156945777-17928489-ebbd-4873-92da-1ff7596cb3a1.png)

1. 2개의 unit lenght를 가지는 vector i , j를 정의합니다.

   

![image](https://user-images.githubusercontent.com/50165842/156945883-409ce933-916c-4e9a-b103-af2c35ffa784.png)



2. 3이라는 것은 3i 가 되고 , 2라는 것은 2j가 됩니다.
3. 3i라는 것은 i에 scalar 3을 곱한 것이고 , 2j라는 것은 j에 scalar 2를 곱한 것입니다. 
4. r = 3i + 2j 로 vector sum을 이용해서 표현하게 됩니다.

## sum in coordinate system

![image](https://user-images.githubusercontent.com/50165842/156946142-17578cef-4086-49cf-99a3-7cf0542380bd.png)

 다른 벡터 s = -1 * i + 2 * j 를 정의 하고 ,r+s를 정의해보도록 하겠습니다.

1. 우선 vector s를 r의 끝으로 옮기게 됩니다.
2. r+s vector를 vector sum의 rule로 구할 수 있게 됩니다.
3. r+s = (-1+3) *i + (2+2) j가 됩니다.
4. r +s = 2*i + 4 * j = [2,4] 가 됩니다.

여기서 알 수 있는 것은 coordinate system에서 vector addition을 할 때 , 그저 component를 더하면 됩니다.

component를 더하는 연산은 그저 덧셈의 연산이므로 associative 성질이 이미 증명이 되어 있습니다. 따라서, vector sum은 associative합니다.

(r+s) +t = r + (s+t)

## mutliply by scalar in coordinate system

![image](https://user-images.githubusercontent.com/50165842/156945883-409ce933-916c-4e9a-b103-af2c35ffa784.png)

1. scalar 2를 vector r에 곱할려고 합니다.
2. 2 * r 은 2x3i , 2 x 2j 입니다.
3. 즉 component를 2배하게 되므로 , 2r = [2x3,2x2] = [6,4] 가 됩니다. 



1. 반대로 scalar -1을 vector r에 곱할려고 하면, -1 * r 은 =[-3,-2] 가 될 것입니다.



## substraction in coordinate system

1. r+ (-r) = [3-3,2-2] =[0,0] 가 됩니다.

이를 vector substraction 이라고 합니다.  vector substraction은 -1 scalar를 곱한것을 더하는 것입니다.

![image](https://user-images.githubusercontent.com/50165842/156946917-7e2e9fd6-ebb1-4514-ad90-157a86957692.png)

r-s 라는 vector는 vector sum이므로 component들을 더하면 됩니다.

3i+i = 4 , 2j -2j =0 라는 결과가 나오게 됩니다. 



# Application in Computer Science vector

예를들어 , house라는 vector가 있다고 하겠습니다. 이 vector는 넓이 , 침대수 , 욕실수, 가격이라는 attribute로  구성된 list입니다.

한 집이 , [120,2,1,150]이라고 했을 때 , 똑같은 2개의 집은 이 vector를 더하는 것이고 , 혹은 scalar 2를 곱하게 되는 것입니다.

2개의 집은 240 제곱미터, 침실 4개, 욕실 2개 가격 300 이라는 attirbute를 가지게 됩니다.

이는 3개 혹은 4개의집을 구할 때도 적용할 수 있으며, 모양이 다른 집을 더할 때도 적용할 수 잇게 됩니다. 

즉, vector에 대해 정의한 operation을 house object에 적용하게 되는 것입니다. 





#  Reference

[Mathematics for Machine Learning: Linear Algebra - 홈 Coursera](https://www.coursera.org/learn/linear-algebra-machine-learning/home/week/1)

