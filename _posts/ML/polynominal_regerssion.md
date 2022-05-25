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

여기서 주의할 점은 모든 feature를사용하는 것이 아니라 , 필요한 만큼의 feature를 사용한다는 것입니다.

![image](https://user-images.githubusercontent.com/50165842/170036215-31ef848c-168f-4f1d-9f17-e7c39509d458.png)

예를 들어, 위와 같이 데이터가 주어진 경우에는 feature를 1개만 사용하면 충분합니다.

![image](https://user-images.githubusercontent.com/50165842/170036527-36e28dc6-2dea-4dab-8e9e-affe47055672.png)

이렇게 데이터가 주어진 경우 , feature를 2개 (x, x*x) 를 사용하게 된다면 , 빨간색과 같은  model을 얻게 될 것입니다. 이는 데이터를 잘 표현하지 못하는 model입니다. 

![image](https://user-images.githubusercontent.com/50165842/170037696-19d8cc0f-6b60-406e-b677-682a83f1e860.png)



만약에 , feature를 3개(x,x*x,x*x*x) 사용하게 된다면, 3차 함수가 되어서 , 위와 같은  model을 만들게 될 것입니다.



![image](https://user-images.githubusercontent.com/50165842/170037425-e9b1f125-6509-4e66-ae16-905a7445b020.png)

하지만 , feature를 다르게 사용한다면(x,x*x x*(1/2) (root x) ) 위와같이 점진적으로 증가하는 model이 되어서 데이터를 잘 표현하는 model이 될 것입니다. 



위의 데이터의 경우 , feature가 x1 ,x2... xn 만큼 주어졋다하더라도, 임의의 feature xk만을 사용하여 표현할 수 있고 , 이 feature의 지수를 어떻게 설정하느냐에 따라 data를 저 잘 표현할 수 있습니다.



## Learning Polynomial Regression 

model을 설정하였으면 ,  model을 학습해야 합니다. Polynomial Regression model 역시 Gradient Descent를 이용하여 학습을 진행합니다.

### Gradient Descent in Multiple Variables

우선 기존의 linear regression에서의 Gradient Descent를 equation으로 적으면 아래와 같습니다.
$$
\begin{align}
\theta_{0} := \theta_{1} - \alpha * {\partial J(\theta) \over \partial\theta_{0}} \\
\theta_{1} :=  \theta_{1} - \alpha * {\partial J(\theta) \over \partial\theta_{1}}

\end{align}
$$
Polynomial Regression의 경우에는 variable의 개수가 늘어나므로 여러번 update를 해주면 됩니다.
$$
\begin{align}
\theta_{0} &:= \theta_{1} - \alpha * {\partial J(\theta) \over \partial\theta_{0}} \\
\theta_{1} &:=  \theta_{1} - \alpha * {\partial J(\theta) \over \partial\theta_{1}} \\
\theta_{2} &:=  \theta_{2} - \alpha * {\partial J(\theta) \over \partial\theta_{2}} \\
\theta_{3} &:=  \theta_{3} - \alpha * {\partial J(\theta) \over \partial\theta_{3}} \\
&...


\end{align}
$$
이를 일반화시키면 아래와 같은 eqation을 얻을 수 있습니다.
$$
\begin{align}

\theta_{k} &:=  \theta_{k} - \alpha * {\partial J(\theta) \over \partial\theta_{k}} \\

(k &= 1 ... n)

\end{align}
$$


### Convergence Speed

Polynomial Regression model을 학습할 방법을 알아봤습니다. 바로 학습에 들어가도 되지만, model 빠르게 convergence할수록 model을 학습하는 시간을 줄일 수 있습니다. 여러 hyperparameter들에 의해서 이 model이 convergence하는 시점이 빨라질 수 있습니다. 

#### Scaling Variables

![image](https://user-images.githubusercontent.com/50165842/170268003-1fd87952-68ed-492f-9ba4-e0169e7117ae.png)

Variable간의 scale 차이가 크다면 Loss Function이  위와 같은 형태를 보일 것입니다.  scale이 큰 variable의  parameter를 update하면 loss function의 값이 상대적으로 크게 변하고 , scale이 작은 variable의 parameter를 update하면 loss function의 값이 상대적으로 작게  변하기 때문에 위와같이 찌그러진 모양이 나오게 됩니다. 

#### Learning Rate



#### 







