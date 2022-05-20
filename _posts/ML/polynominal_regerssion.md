# Polynomial Regression in Multiple Features

Linear Regression 모델을 1개의 독립변수 x와 1개의 의존변수 y로 나타내는 것을 Simple Linear Regression이라 한다. 이를 , 수식으로 표현하면  
$$
y = \theta_{1} x + \theta_{0}
$$
로 표현할 수 있다. 이는 단순하게 x,y 축을 가진 그래프로 표현할 수 있기에 쉽게 이해할 수 있습니다. 하지만, 의존변수 y를 예측해야하는 Linear Regression 모델을 만들때 , 독립변수는 1개가 아닐 수 있습니다. 이때 , 사용하는 LInear Regerssion 모델은 다른 equation입니다. 

## What is Polynomial Regression

Polynomial Regression이란 독립변수가 1개가 아닌 여러개인 경우의 Linear Regression 모델입니다.  

이를 식으로 표현하면  아래와 같습니다.


$$
\begin{align}
y &= \theta_{0} + \theta_{1}x_{1} + \theta_{2}x_{2} + ... + \theta_{n}x_{n} \\
y &= \sum_{i=1}^{n} \theta_{i}x_{i} + \theta_{0} \\
\end{align}
$$





## Learning Polynomial Regression 



### Gradient Descent in Multiple Variables



### Convergence Speed



#### Scaling Variables



#### Learning Rate



#### 







