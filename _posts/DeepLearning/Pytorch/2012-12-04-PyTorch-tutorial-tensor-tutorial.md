## PyTorch tutorial-tensor tutorial

Pytorch의 기본적인 코드들을 고민하지 않고 의식하지 않고 사용하기 위해 반복연습이 중요하다 느꼈습니다. 따라서, torch 튜토리얼을 진행하게 되었습니다.Pytorch 공식 튜토리얼을 진행하면서 추가적으로 알게된 사실들을 적습니다 .

### Tensor

torch의 Tensor는 3가지 방식으로 만들 수 있습니다.

1. 직접 data를 입력

   ```python
   data = torch.tensor([[1,2] , [3,4]])
   print(data)
   
   data = torch.tensor(np.array([[1,2] , [3,4]]))
   ```

   2.numpy 배열로 부터 생성

   ```python
   np_array= np.array([[1,2] , [3,4]])
   data = torch.from_numpy(np_array)
   
   
   ```

   tensor로부터 numpy array를 생성도 가능합니다.

   ```python
   data = torch.tensor([[1,2] , [3,4]])
   print(data)
   
   data = data.numpy()
   print(data)
   ```

   

### torch.from_numpy vs torch.Tensor

이 2개다 tensor를 만드는 것에는 차이가 없습니다. 하지만, from_numpy는 numpy array와 메모리를 공유하는 특징이 있습니다.

***<u>torch.Tensor로  만들면 새로운 메모리를 할당하지만, torch.Tensor는 새로 메모리를 할당해줍니다.</u>***

