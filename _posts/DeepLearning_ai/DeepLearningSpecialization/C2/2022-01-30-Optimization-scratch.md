---
title : "Optimization Assignment from scratch"



excerpt: "DeepLearning.ai Dls Optimization "

categories:
  - DLS_C2
tags:
  - [Machine Learning,Coursera,deep learning ,C2]
# classes : wide
toc: true
toc_sticky: true
---
DLS의 과제는 뚤린 빈칸을 채워나가면서 , 전체적인 흐름이랑 키포인트들을 알아가는데 좋다고 생각합니다. 하지만, 같은 과제를 review를 할 때에는 , 모든 detail을 파악하는 것이 목적이기에 ,  TODO 로 주어진 빈칸 뿐만 아니라 모듈 전체를 scratch부터 구현하였습니다. 그 과정에서 , 여러가지를 느꼈습니다 .

# Gradient Descent

```python
X = data_input
Y = labels
parameters = initialize_parameters(layers_dims)
for i in range(0, num_iterations):
    # Forward propagation
    a, caches = forward_propagation(X, parameters)
    # Compute cost.
    cost += compute_cost(a, Y)
    # Backward propagation.
    grads = backward_propagation(a, caches, parameters)
    # Update parameters.
    parameters = update_parameters(parameters, grads)
```

Gradient Descent를 구현한 psuedo code입니다 . 저는 처음에 cache 를 사용하는 부분을 떠올리지 못했습니다.

## why cache?

왜 gradient  descent의 식을 떠올리면 chain rule을 이용해서 l layer의 derivative를 구하게 됩니다.  따라서 , loss 부터 재귀적으로 매번 call frame을 생성해야 합니다. 

```python
def backward_propagation(X, Y, cache):
    """
    Implement the backward propagation presented in figure 2.
    
    Arguments:
    X -- input dataset, of shape (input size, number of examples)
    Y -- true "label" vector (containing 0 if cat, 1 if non-cat)
    cache -- cache output from forward_propagation()
    
    Returns:
    gradients -- A dictionary with the gradients with respect to each parameter, activation and pre-activation variables
    """
    m = X.shape[1]
    (z1, a1, W1, b1, z2, a2, W2, b2, z3, a3, W3, b3) = cache
    
    dz3 = 1./m * (a3 - Y)
    dW3 = np.dot(dz3, a2.T)
    db3 = np.sum(dz3, axis=1, keepdims = True)
    
    da2 = np.dot(W3.T, dz3)
    dz2 = np.multiply(da2, np.int64(a2 > 0))
    dW2 = np.dot(dz2, a1.T)
    db2 = np.sum(dz2, axis=1, keepdims = True)
    
    da1 = np.dot(W2.T, dz2)
    dz1 = np.multiply(da1, np.int64(a1 > 0))
    dW1 = np.dot(dz1, X.T)
    db1 = np.sum(dz1, axis=1, keepdims = True)
    
    gradients = {"dz3": dz3, "dW3": dW3, "db3": db3,
                 "da2": da2, "dz2": dz2, "dW2": dW2, "db2": db2,
                 "da1": da1, "dz1": dz1, "dW1": dW1, "db1": db1}
    
    return gradients

```

DLS에서 제공해주는 backpropagation 코드입니다.  cache에서 한 번에 모든 layer의 output을 unpacking해서 chain rule로 derivative를 계산하지만, stack frame을 더 생성하지 않음을 알 수 있습니다. 

## 이 이외의 방법은?

OOP 프로그래밍 스타일을 따른다면 , recursive하게 짜게 될 것입니다 . stack frame을 많이 생성하는 만큼, resource allocation이 더 많아질 수 밖에 없습니다. layer 3개정도의 layer라면 단순하게 이렇게 구현해도 괜찮을 것이다 라고 생각이 들었습니다.(이 부분은 OOP style로 실제로 구현을 해보고 다시 추가해서 넣어보도록 하겠습니다.)

# Mini Batch

mini batch를 사용하게 되면 shuffle을 사용하게 됩니다. 단순히 shuffle하면 random한 순서로 학습할 수 있어서 더 일반적인 data를 학습할 수 있다고 말해도 되지만 , 이를 좀 더 분해해서 detail하게 알아보겠습니다.

mini batch의 수학적 의미는 매 iteration 마다 loss function이 변한다는 것입니다 . batch gradient descent와 달리 data의 일부를 가지고 loss function을 계산하기에 달라질 수 밖에 없습니다. shuffle은 여기서 더 나아가서 매 epoch 마다 shuffle을 해줌으로 써 매 epoch마다 다른 loss function을 계산하게 하는 것입니다. 이렇게 하면, pattern을 더 학습하기 어려워져서 , local minima에서 탈출하고 global minima에 도달할 가능성이 높아지게 됩니다.

## Issue1 Shuffle을 어떻게 코드로 구현하는가?

### sol1

shuffle을 코드로 막상 코드로 구현을 할려고 하니까, 어떻게 해야 할지 고민이 되었습니다. 가장 기본적으로 해주어야 할 것은 X,Y의 pair가 shuffle해도 1대1 function 관계가 변하면 안된다는 것입니다. 

```python
np.random.permutation(X,axis=1)
np.random.permutation(Y,axis=1)
```

저는 처음에 이렇게 시도를 했습니다 .  `axis = 1` 로 옵션을 준 이유는 `axis=0` 가 feature의 dim 이고 `axis=1` 가 data size를 나타내기에 `axis=1` 을 기준으로 permuation을 하였습니다. 

하지만 , 여기서 처음에 seed값을 고정을 해도 한번 permutation을 하고 나면 seed가 다른 값으로 바뀌는 것을 확인했습니다. 따라서 , 아래와 같이 코드를 변경해야 했습니다.

```python
np.random.seed(0)
np.random.permutation(X,axis=1)
np.random.seed(0)
np.random.permutation(Y,axis=1)
```

### sol2

```
random_index = list(np.random.permutation(num_examples))
```

이번에는 data sample size만큼의 index를 만들어서 시도를 해보았습니다. 

```

    for i in range(iter_nums) :
      shuffled_X  =np.empty((nx,mini_batch_size))
      shuffled_Y = np.empty((1,mini_batch_size))
      for k,j in enumerate(random_index[i * mini_batch_size:(i+1) * mini_batch_size]) :
        
        shuffled_X[:,k] = X[:,j]
        shuffled_Y[:,k] = Y[:,j]
```

그리고 , 매번 data의 shape과 동일한 empty array를 생성한 다음에 random index에 있는 element로 data를 indexing해서 copy 한 다음에 다시 , emtpy array에 copy하는 방법을 사용했습니다. 

```
    for  k ,j in enumerate(random_index[iter_nums * mini_batch_size : ]) :
        # print(k)
        shuffled_X[:,k]  = X[:,j]
        shuffled_Y[:,k] =Y[:,j]
    
```

마지막 배치에서는 따로 base case로 지정해서 연산을 수행했습니다. 하지만, 이렇게 하면 copy , paste가 빈번히 일어나게 되는것을 확인 했습니다. 

### sol3

DLS의 solution은 저와 접근 방법은 비슷했지만, 코드를 더 효율적으로 짯습니다.

```
    # Step 1: Shuffle (X, Y)
    permutation = list(np.random.permutation(m))
    shuffled_X = X[:, permutation]
    shuffled_Y = Y[:, permutation].reshape((1, m))
    
```

저는 매번 단일 indexing으로 copy를 했습니다. 하지만, 이 solution에서는 임의의 random 한 순서의 index로 sequence 전체를 indexing 했습니다 .Numpy에서 이런 operation이 지원되는 것은 처음 알았습니다. Numpy 의 indexing은 `,`를 기준으로 행, 열 을 indexing 하는 것으로 이해하면 좋다고 생각이 들었습니다. 행,열에서도 broadcasting이 지원되는 것도 잊으면 안됩니다. 

```
    for k in range(0, num_complete_minibatches):
        # (approx. 2 lines)
        # mini_batch_X =  
        # mini_batch_Y =
        # YOUR CODE STARTS HERE
        mini_batch_X  = shuffled_X[:,k*mini_batch_size : (k+1) * mini_batch_size]
        mini_batch_Y = shuffled_Y[:,k*mini_batch_size : (k+1) * mini_batch_size]
        
```

따라서, mini_batch를 만들 때에도 비교적 indexing이 간단해지는 것을 확인할 수 있었습니다 .

## Issue2 mini-batch를 PYthon list로 만들어주면 안되는가?

mini-batch 를 python list로 만들어 주면 안되는가 문득 생각이 들었습니다. 이 의문은 처음에 list로 시도를 하다가 numpy array가 아니라는 test error를 받아서 다시 numpy array로 바꾸면서 생겼습니다. 이에 대한 답은 반드시 numpy array로 해야만 한다는 것입니다.

왜냐하면 , python의 list는 연산을 iteration으로 수행합니다. 하지만, neural net에서는 이를 matrix화해서 병렬 연산을 하게 됩니다. 즉, vectorization을 해서 parallell 하게 연산을 수행하는 것입니다.   numpy array는 matrix 연산을 지원하지만, python list는 matrix 연산을 지원하지 않습니다. 따라서 ,numpy array를 사용해야 합니다. 

## Issue3. mini_batch를 만들 때 append연산을 이용하면 안되는가?

numpy_list를 빈 리스트로 만들고 np.append() 를 했는데, shape이 기댓값이랑 다르게 나왔습니다. 그 이유는 , np.append를 axis를 지정해주지 않으면 input을 flatten하게 된다고 docs에 적혀 있었습니다.

## Issue4. axis를 지정하고 np.append를 하는데, dim에러 발생

np.append를 하는데, dim error가 발생하였습니다. 그 이유는, empty list의 dimension 설정이 되지 않기 때문입니다. 따라서, mini-batch의 shape과 같은 shape의 matrix를 만들고 assignment를 통해 mini-batch data를 만드는 방법밖에 없다고 생각했습니다. 

# Mini batch unit test

```

assert n_batches == math.ceil(m / mini_batch_size), f"Wrong number of mini batches. {n_batches} != {math.ceil(m / mini_batch_size)}"
for k in range(n_batches - 1):
    assert mini_batches[k][0].shape == (nx, mini_batch_size), f"Wrong shape in {k} mini batch for X  {mini_batches[k][0].shape}"
    assert mini_batches[k][1].shape == (1, mini_batch_size), f"Wrong shape in {k} mini batch for Y {mini_batches[k][1].shape}"
    assert np.sum(np.sum(mini_batches[k][0] - mini_batches[k][0][0], axis=0)) == ((nx * (nx - 1) / 2 ) * mini_batch_size), "Wrong values. It happens if the order of X rows(features) changes"
    
if ( m % mini_batch_size > 0):
    assert mini_batches[n_batches - 1][0].shape == (nx, m % mini_batch_size), f"Wrong shape in the last minibatch. {mini_batches[n_batches - 1][0].shape} != {(nx, m % mini_batch_size)}"

```

mini-batch dataset을 test할 때 저는 마지막 batch 가 있는지 없는지 확인할 때 m - n_batches * mini_batch_size 가 0보다 큰 지 계산을 했습니다. 그리고 , 마지막 batch size의 sample도 저렇게 계산을 하였는데, 모듈러 연산으로 계산을 하는게 더 깔끔하다는 것을 알게되었습니다.

# Update parameter with Adam

```python
# GRADED FUNCTION: update_parameters_with_adam

def update_parameters_with_adam(parameters, grads, v, s, t, learning_rate = 0.01,
                                beta1 = 0.9, beta2 = 0.999,  epsilon = 1e-8):
    """
    Update parameters using Adam
    
    Arguments:
    parameters -- python dictionary containing your parameters:
                    parameters['W' + str(l)] = Wl
                    parameters['b' + str(l)] = bl
    grads -- python dictionary containing your gradients for each parameters:
                    grads['dW' + str(l)] = dWl
                    grads['db' + str(l)] = dbl
    v -- Adam variable, moving average of the first gradient, python dictionary
    s -- Adam variable, moving average of the squared gradient, python dictionary
    t -- Adam variable, counts the number of taken steps
    learning_rate -- the learning rate, scalar.
    beta1 -- Exponential decay hyperparameter for the first moment estimates 
    beta2 -- Exponential decay hyperparameter for the second moment estimates 
    epsilon -- hyperparameter preventing division by zero in Adam updates

    Returns:
    parameters -- python dictionary containing your updated parameters 
    v -- Adam variable, moving average of the first gradient, python dictionary
    s -- Adam variable, moving average of the squared gradient, python dictionary
    v_corrected = {}                         # Initializing first moment estimate, python dictionary
    s_corrected = {}                           # Initializing second moment estimate, python dictionary
    """
    corrected_v={}
    corrected_s={}
    for l in range(1, len(parameters)//2 + 1 ) :

        v['dW' + str(l)] = beta1 * v['dW'+str(l)] + (1-beta1) * grads['dW'+str(l)]
        v['db' + str(l)] = beta1 * v['db' + str(l)] + (1-beta1)  * grads['db' + str(l)]
        corrected_v['dW'+str(l)] = v['dW'+str(l)] / (1 - math.pow(beta1 , t))
        corrected_v['db'+ str(l)] = v['db' + str(l)]  / (1 - math.pow(beta1,t))


        s['dW' + str(l)]  = beta2 * s['dW' + str(l)] + (1-beta2) * (grads['dW' +str(l)]) * (grads['dW' +str(l)])
        s['db' + str(l)]  =beta2 * s['db' + str(l)] + (1-beta2) * (grads['db' + str(l)]) * (grads['db' +str(l)])


        corrected_s['dW'+str(l)] =  s['dW' + str(l)] / (1-math.pow(beta2,t)) 
        corrected_s['db' + str(l)]  = s['db' + str(l)] / (1-math.pow(beta2,t))

        parameters['W' + str(l)] -= learning_rate * corrected_v['dW' + str(l)] \
                              /( np.sqrt(corrected_s['dW' + str(l)]   ) +epsilon ) 
        parameters['b' + str(l)] -= learning_rate * corrected_v['db' + str(l)] \
                            /( np.sqrt(corrected_s['db' + str(l)] ) + epsilon )  
    return parameters , v, s , corrected_v  , corrected_s
```

## Issue1 Adam Scaling term

Now, implement the parameters update with Adam. Recall the general update rule is, for $l = 1, ..., L$: 



$$\begin{cases}

v_{dW^{[l]}} = \beta*_1 v_*{dW^{[l]}} + (1 - \beta_1) \frac{\partial \mathcal{J} }{ \partial W^{[l]} } \\

v^{corrected}*_{dW^{[l]}} = \frac{v_*{dW^{[l]}}}{1 - (\beta_1)^t} \\

s_{dW^{[l]}} = \beta*_2 s_*{dW^{[l]}} + (1 - \beta_2) (\frac{\partial \mathcal{J} }{\partial W^{[l]} })^2 \\

s^{corrected}*_{dW^{[l]}} = \frac{s_*{dW^{[l]}}}{1 - (\beta_2)^t} \\

W^{[l]} = W^{[l]} - \alpha \frac{v^{corrected}*_{dW^{[l]}}}{\sqrt{s^{corrected}_*{dW^{[l]}}} + \varepsilon}

\end{cases}$$



Adam의 수식은 위와 같습니다. V,S를 scaling해줄 때 beta1,beta2 의 exponential을 scaling term으로 사용하게 됩니다. 저는 이 때 , 여기서 t대신 l을 사용해야 하는지 햇갈렸습니다 . time step이라는 것이 layer를 거치는 횟수라 표현 되는 다른 network도 있기에 개념의 혼동이 왔던거 같습니다. 하지만, 여기서 time step은 gradient 를 update한 횟수를 의미하게 됩니다.

## Issue2 grad element-wise

또한 , S를 구할 때 grad의 element-wise를 구해야 하는데 , np.exp2()로 계산을 시도해서 오류가 났습니다. element-wise를 구할때 , np.exp2() 로 계산이 되는지는 모르겠지만, element-wise를 구할려면 , 그저 곱셉으로 계산을 하는게 났다고 생각이 들었습니다.



## Issue3 Update Parameters

처음에 update expression을 작성할 때 , `np.sqrt(corrected_s['db' + str(l)]  + epsilon )` 작성을 했었는데 , `np.sqrt(corrected_s['db' + str(l)] ) + epsilon` 로 작성을 해야 오류가 나지 않음을 확인했습니다. 이 부분이 실제로 학습을 할 때 큰 영향을 끼치는지는 잘 모르겠습니다 .