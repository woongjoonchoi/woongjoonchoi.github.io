---
title : "Convolution Assignment from scratch"



excerpt: "DeepLearning.ai Dls Convolution "

categories:
  - DLS_C2
tags:
  - [Machine Learning,Coursera,deep learning ,C4]
# classes : wide
toc: true
toc_sticky: true
---
DeepLearning Specialization에서의 Convolution operation 문제를 hint 없이 input,output docstring 만을 가지고 풀어보았습니다. convolution operatoin은 대부분의 deeplearning framework에서 지원을 해주지만, 스스로 implementation을 해보면서 많은 점을 느꼈습니다. 





# Convolution forward

```python
# GRADED FUNCTION: conv_forward

def conv_forward(A_prev, W, b, hparameters):
    """
    Implements the forward propagation for a convolution function
    
    Arguments:
    A_prev -- output activations of the previous layer, 
        numpy array of shape (m, n_H_prev, n_W_prev, n_C_prev)
    W -- Weights, numpy array of shape (f, f, n_C_prev, n_C)
    b -- Biases, numpy array of shape (1, 1, 1, n_C)
    hparameters -- python dictionary containing "stride" and "pad"
        
    Returns:
    Z -- conv output, numpy array of shape (m, n_H, n_W, n_C)
    cache -- cache of values needed for the conv_backward() function
    """
    # unpacking을 잘 이용못햇음
    n_H_prev = int(A_prev.shape[1])
    n_W_prev = int(A_prev.shape[2])
    n_C_prev = int(A_prev.shape[3])
    m = int(A_prev.shape[0])
    n_C =int(W.shape[-1])
    f = int(W.shape[0])
    p = hparameters['pad']
    s= hparameters['stride']
    A_pad  = zero_pad(A_prev , p)
    n_H  = int(np.floor((n_H_prev+2 * p - f) / s + 1 ))
    n_W = int(np.floor((n_W_prev +2 *p -f) /s + 1))
    Z  = np.zeros((m,n_H,n_W,n_C))

    # for문을 생각해낼 떄 반복적인 부분의 pattern을 조사해서 묶지 않앗음
    # 반복적인 부분의 pattern을 도출해내면 이 제 이 일반식을 적용하는 단계를 생각하지 않고 ( vert_start, vert_end,hori_start,hori_end 를 계산안함
        # ) 무의식적으로 바로 계산 (h*s, h*s + f .. 바로 도출)
    for i in range(m):
        for c in range(n_C):
            for h in range(n_H) :
                for w in range(n_W) :
                        Z[i,h,w,c] = conv_single_step(A_pad[i,h*s : h*s +f , w *s : w*s + f ,:].reshape((f,f,-1)) 
                            ,W[:,:,:,c].reshape(f,f,n_C_prev) , b[:,:,:,c].reshape(1,1,1))
    cache=(A_prev,W,b,hparameters)
    return Z ,cache
```

convolution layer 1개의 forward 연산을 scratch 부터 implementation 하는 과제입니다.  정답을 맞추기는 했지만, 정답을 도출하는 과정이 논리적이지 못했다고 생각이 들었습니다.

convolution layer의 forward 연산은 1개의 output pixel을 만드는 convolution operation을 무수히 반복을 하는 것입니다.  input과 filter의 channel 1개에 대응을 시켜서 볼 때 , stride라는 hyperparameter가 주어지면, 이 간격만큼 filter를 움직이면서 convolution operation을 수행해서  output pixel을 1개 만들어 냅니다. 즉 , stride만큼의 간격을 이동하면서 output을 만들어내는 반복적인 pattern이 존재하므로 for statement을 사용해서 iterative한 알고리즘을 사용한 것입니다. 반복적인 pattern이 존재하므로 filter의 위치를 일반화 시켜서 생각해야하는데 , 저는 반복적인 pattern을 찾은 것이 아니고 m * n_C * n_H * n_W 만큼을 반복하는 과정을 한번에 일반화 시켰습니다. 이는 어떻게 될지 결과를 대략적으로 알고 있었기에 , 사고의 논리적인 step이 비약되어서 나온 결과라고 생각이 듭니다. 

제가 생각하는 논리적인 과정은 이렇습니다. 

1. 1개의 input 과 filter를 생각한다.
2. filter는 수직으로 , 수평으로 이동하면서 convolution operation을 실행한다.
3. 수직으로 , 수평으로 operation을 반복하는데 , 수직방향을 고정시키고 수평방향의 operation을 생각해보겠다.
4. 수평방향의 간격은 stride이다. 따라서, hori_start = ws , hori_end = ws +f  가 된다. 반복적인 pattern이 존재하기 때문에, for statement을 사용해서 iterative하게 알고리즘을 구성하였다.
5. 수직방향의 간격도 역시 stride이다. 따라서, vert_start = hs , vert_end = vs + f 가 된다. 반복적인 pattern이 존재 하기 때문에, for statement을 사용해서 iterative하게 알고리즘을 구성하였다. 
6. 4,5의 과정을 통해서 1개의 input과 filter에 대한 convolution layer의 forward propagation을 정의할 수 있다. 
7. 이를 n_C개의 channel수만큼 반복하고, m개의 batch에 대해서 반복하게 된다. n_C개만큼 forward propagatoin을 진행하고 이를 m개의 batch에 대해서 반복하게 된다. 

저는 여기서 모든 논리과정을 생략하고 결과 만을 도출하였습니다. for expression을 사용해야 할거 같아서 for statement을 사용한것이기에 , 그저 coding을 한 것입니다. 문제를 생각하고 이를 번역해내는 과정이 없었기에 완전히 알고 있다고 볼 수 없었습니다. 

또한, convolution과는 별개로, python의 unpacking기능을 잘 활용하지 못하였습니다.  shape 를 unpacking을 하면 손쉽게 coding 할 수 있었는데, 여러 line이 더 비효율적으로 사용되었습니다. 이를 이상적으로 고치면 아래와 같이 고칠 수 있을거 같습니다.



```python
# GRADED FUNCTION: conv_forward

def conv_forward(A_prev, W, b, hparameters):
    """
    Implements the forward propagation for a convolution function
    
    Arguments:
    A_prev -- output activations of the previous layer, 
        numpy array of shape (m, n_H_prev, n_W_prev, n_C_prev)
    W -- Weights, numpy array of shape (f, f, n_C_prev, n_C)
    b -- Biases, numpy array of shape (1, 1, 1, n_C)
    hparameters -- python dictionary containing "stride" and "pad"
        
    Returns:
    Z -- conv output, numpy array of shape (m, n_H, n_W, n_C)
    cache -- cache of values needed for the conv_backward() function
    """
    # unpacking을 잘 이용못햇음
    n_H_prev , n_W_prev , n_C_prev ,m =  A_prev.shape
    f , n_C = W.shape
    p = hparameters['pad']
    s= hparameters['stride']
    A_pad  = zero_pad(A_prev , p)
    n_H  = int(np.floor((n_H_prev+2 * p - f) / s + 1 ))
    n_W = int(np.floor((n_W_prev +2 *p -f) /s + 1))
    Z  = np.zeros((m,n_H,n_W,n_C))

    # for문을 생각해낼 떄 반복적인 부분의 pattern을 조사해서 묶지 않앗음
    # 반복적인 부분의 pattern을 도출해내면 이 제 이 일반식을 적용하는 단계를 생각하지 않고 ( vert_start, vert_end,hori_start,hori_end 를 계산안함
        # ) 무의식적으로 바로 계산 (h*s, h*s + f .. 바로 도출)
    for i in range(m):
        for c in range(n_C):
            
            for h in range(n_H) :
            	vert_start = h * s
                vert_end= h*s +f
            	for w in range(n_W) :
                    	hori_start = w *s
                        hori_end = w*s +f
                        Z[i,h,w,c] = conv_single_step(A_pad[i,vert_start : vert_end , hori_start : hori_end ,:].reshape((f,f,-1)) 
                            ,W[:,:,:,c].reshape(f,f,n_C_prev) , b[:,:,:,c].reshape(1,1,1))
    cache=(A_prev,W,b,hparameters)
    return Z ,cache
```





# Conv Pooling Forward

pooling layer도 기본적으로 conv layer와 format은 비슷하다. 하지만, 여기서 쓰인 idea중 하나 차별되는 것은 pooling operation를 function object로 취급했다는 것입니다. 단순하게 생각했을 때 , mode에 따라 pooling operation을 다르게 사용해야 하기에 , if statement을 이용한 flow를 제어하는 code를  for statement 안에서 제어해야 했습니다. 하지만, 이 자체는 layer의 operation을 하는 것이기에 pooling opeartion을 function object로 return하고 for statement안에서는 max인지 avearage인지 관계없이 pooling operation을 하도록 abstraction이 되었습니다 . 

```python
def pool_forward(A_prev, hparameters, mode = "max"):
    """
    Implements the forward pass of the pooling layer
    
    Arguments:
    A_prev -- Input data, numpy array of shape (m, n_H_prev, n_W_prev, n_C_prev)
    hparameters -- python dictionary containing "f" and "stride"
    mode -- the pooling mode you would like to use, defined as a string ("max" or "average")
    
    Returns:
    A -- output of the pool layer, a numpy array of shape (m, n_H, n_W, n_C)
    cache -- cache used in the backward pass of the pooling layer, contains the input and hparameters 
    """
    def cus_pool(mode):
        if mode =="max" :
            return np.amax
        else :
            return np.ma.average
    pooling = cus_pool(mode)
    # print(pooling)
    (m , n_H_prev, n_W_prev , n_C_prev) = A_prev.shape
    s , f  = [ v for k,v in hparameters.items()]
    n_H = int(np.floor((n_H_prev-f) /s + 1 ))
    n_W  = int(np.floor( (n_W_prev - f) /s + 1 ))
    n_C = n_C_prev
    A = np.zeros((m,n_H,n_W,n_C))
    for i in range(m):
        for c in range(n_C) :
            for h in range(n_H) :
                vert_start = h*s 
                vert_end = h*s + f
                for w in range(n_W) :
                    hori_start = w *s 
                    hori_end = w*s + f
                    A[i,h,w,c] = pooling(A_prev[i,vert_start:vert_end, hori_start:hori_end , c])

    # print(f'f : {f}  s L {stride}')
    # cache = []
    cache = (A_prev , hparameters)
    return A, cache
```









# Conv_Backward

Convolution backward가 가장 오랫동안 고민했던 부분이였던 것 같습니다. Back propagation을 그저 single node graph간의 scalar form으로만 혹은 그저 공식 결과만을 외우고 있었던 것이 큰 원인이였던것 같습니다.  이를 일반화해서 어떻게 matrix form으로 도출하는지를 몰랐었습니다.

몇몇 분들이 , linear algebra 에서 linear transformation을 보일때 , matrix에서 transformed 된 coordinate를 도출할려 하는데, 이는 linear algebra에 대해 전혀 이해하지 못하고 있다고 할 수 있습니다. linear transform 이라는 operation을 정의한 다음에, 이에 대한 representation 중 하나가 matrix multiplication일 뿐입니다.  

back propagtion도 computation graph의 multiplication 연산을 matrix로 representation 하는 것이라 생각합니다.  back propagation이라는 것은 computation graph에서 연결된 노드로 partial derivative 값을 전달하는 것입니다.


$$
\Sigma \Sigma \Sigma F[m,c,w,h] * A[m,c,w' , h'] = Z[m,c,W,H]
$$
 

F를 kernel , A를 input, Z를 output이라 하면 1개의 pixel을 연산하는 과정은 위와 같이 됩니다. 

convolution 은 fully connected layer가 아니기에  matrix의 transpose를 이용하는 방식이 적용되지 않습니다 . computational graph 관점에서 보면,  output pixel 1개에 여러개의 input pixel이 connected 되어 있습니다. convolution 연산처럼 마찬가지로 1개의 pixel 값 즉, scalar에 대해서 Wegiht, bias, activation에 대한 derivative를 구해서 accumulation을 해나가면 됩니다 . 

굳이 공식을 설명하자면 , m번째 training sample의 channel c에서 h,w 위치에 있는 pixel은 numpy syntax로 Z[m,hori_start:hori_end,vert_start:vert_end,c] 의 pixel과 connection이 있습니다. 

```python
def conv_backward(dZ, cache):
    """
    Implement the backward propagation for a convolution function
    
    Arguments:
    dZ -- gradient of the cost with respect to the output of the conv layer (Z), numpy array of shape (m, n_H, n_W, n_C)
    cache -- cache of values needed for the conv_backward(), output of conv_forward()
    
    Returns:
    dA_prev -- gradient of the cost with respect to the input of the conv layer (A_prev),
               numpy array of shape (m, n_H_prev, n_W_prev, n_C_prev)
    dW -- gradient of the cost with respect to the weights of the conv layer (W)
          numpy array of shape (f, f, n_C_prev, n_C)
    db -- gradient of the cost with respect to the biases of the conv layer (b)
          numpy array of shape (1, 1, 1, n_C)
    """   
    A_prev, W,b,hparameters = cache
    m , n_H_prev , n_W_prev, n_C_prev= A_prev.shape
    _,n_H,n_W,n_C = dZ.shape
    s , p  = hparameters['stride'] , hparameters['pad']
    f,_,_,_ = W.shape
    dA_prev_padded = np.zeros((m,n_H_prev+2*p , n_W_prev + 2*p , n_C_prev))
    dW = np.zeros((f,f,n_C_prev, n_C))
    db = np.zeros((1,1,1,n_C))
    A_prev_padded  = zero_pad(A_prev,p)
    for i in range(m):
        for c in range(n_C) :
            for h in range(n_H) :
                vert_start = h * s 
                vert_end = h*s +f
                for w in range(n_W) :
                    hori_start = w *s 
                    hori_end = w*s + f
                
                    dA_prev_padded[i,vert_start : vert_end  ,hori_start : hori_end ,: ] += dZ[i,h,w,c] * W[:,:,:,c]
                    dW[:,:,:,c] +=  dZ[i,h,w,c] * A_prev_padded[i,vert_start:vert_end , hori_start:hori_end, :]
                    db[:,:,:,c] += dZ[i,h,w,c]
    dA_prev = dA_prev_padded[:,p:-p , p:-p,: ]
    assert(dA_prev.shape == (m, n_H_prev, n_W_prev, n_C_prev))
    return dA_prev, dW , db
```

# Conv_Pooling_Backward

Pooling Layer를 Backward propagtion은 Convolution Layer와 달리 주의를 해야 합니다. 

max operation는 여러개의 node중 1개의 node만을 보존시키는 operation 입니다.  즉, 1개의 node만이 computation graph에서 edge로 연결이 되어있다고 볼 수 있습니다. 즉 , derivative가 이 node에만 전달이 된다는 뜻입니다. 

average operation은 여러개의 node의 mean 값입니다. 즉 , activation을 accumulation 한 다음에 activation 수 , 즉 filter의 parameter 수만 큼 나누어주는 operation입니다. backward propagation을 할 때 이 사실을 반영해주어야 합니다.

max operation의 matrix operation 의 이점을 이용해서 mask matrix를 만들어서 사용하였습니다.  max node의 position을 찾은 다음에 , 그 position 만을 update하는 것도 괜찮아보이지만, mask operation 자체가 O(1) 의 constant time이기 때문에 , 무시해도 될만한 overhead라 생각했고, 계산상 편의 때문에 mask operation이 좋다고 생각이 들었습니다. 

```python

def create_mask_from_window(x):
    """
    Creates a mask from an input matrix x, to identify the max entry of x.
    
    Arguments:
    x -- Array of shape (f, f)
    
    Returns:
    mask -
    """
    mask = (x == np.max(x))    
    return mask

def distribute_value(dz, shape):
    """
    Distributes the input value in the matrix of dimension shape
    
    Arguments:
    dz -- input scalar
    shape -- the shape (n_H, n_W) of the output matrix for which we want to distribute the value of dz
    
    Returns:
    a -- Array of size (n_H, n_W) for which we distributed the value of dz
    """    
    n_H, n_W  = shape
    a = np.ones((n_H,n_W))
    a[:,:] = dz / (n_H*n_W)
    return a




def pool_backward(dA, cache, mode = "max"):
    """
    Implements the backward pass of the pooling layer
    
    Arguments:
    dA -- gradient of cost with respect to the output of the pooling layer, same shape as A
    cache -- cache output from the forward pass of the pooling layer, contains the layer's input and hparameters 
    mode -- the pooling mode you would like to use, defined as a string ("max" or "average")
    
    Returns:
    dA_prev -- gradient of cost with respect to the input of the pooling layer, same shape as A_prev
    """

    
    m , n_H,n_W,n_C = dA.shape

    A_prev , hparameters = cache
    s , f  = hparameters['stride'] , hparameters['f']
    dA_prev = np.zeros(A_prev.shape)
    for i in range(m):
        for c in range(n_C) :
            for h in range(n_H) :
                vert_start = h * s 
                vert_end = h*s + f
                for w in range(n_W) :
                        hori_start  = w * s
                        hori_end = w*s + f
                        A_slice = A_prev[i,vert_start:vert_end,hori_start:hori_end,c]

                        ## computational graph 관점으로 생각해서 식적기
                        ## chain rule 관점에서 생각해보기 
                        if mode == "max" :
                            masking = create_mask_from_window(A_slice)
                            dA_prev[i,vert_start:vert_end,hori_start:hori_end,c] += dA[i,h,w,c]  * masking
                        elif mode=="average" :
                            masking = distribute_value(dA[i,h,w,c],(f,f))
                            dA_prev[i,vert_start:vert_end,hori_start:hori_end,c] +=  masking
                        
    return dA_prev
```

