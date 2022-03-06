인공지능의 알고리즘이 점점 더 복잡해지지만, 딥러닝 1.0수준에서만 고려하면 , 이는 feature가 점점 더 복잡해짐을 의미합니다. 즉, data에 대한 hand engineering이 더 많아짐을 의미합니다. 이는 feature가 점점 더 복잡해지고 , data에 대한 hand engineering 비중이 더 커짐을 의미합니다. 

예를 들면 , object detction 의 경우 label 이외에도 , 여러가지 것들( bounding box , object, multi object label..etc) 같은 추가적인 feature engineering이 필요합니다. image recognition 에 비해서 좀 더 많은 비용이 들어갑니다. natural language processing의 경우  , 단어를 토큰화 해서 , lookup table을 만드는 feature engineering이 필요합니다. question answering의 경우 answer span에 대한 정보와 , passage가 길어서 잘렸을 때 , 이에 대한 offset을 계산하는등 추가적인 feature engineering이 필요합니다. text classification이 label 1개만 필요한 것과 비교하면 상대적으로 cost가 많이 들어갑니다. 

이는 딥러닝 1.0의 특성상 , 주어진 feature의 distribution을 학습하기에 feature를 어떻게 실험하는지가 중요합니다. 따라서, data feature engineering을 하는 법을 알아야하고, 이를 위해서는 , pytorch data api를 사용할 줄 알아야 합니다. 딥러닝 2.0으로 점점 넘어가는 추세가 될 것이지만, 아직 까지는 딥러닝 1.0이 대세이기에 이러한 data feature engieering은 중요한 craft라 볼 수 있습니다.



# Dataset 

딥러닝 모델을 훈련할려면 data를 사용해야 하는데, 이를 사용하기 위한 api specification을 dataset이라고 부릅니다. 이러한 api 를 Pytorch에서는 torch.utils.data.Dataset class로 implementation을 해 놓았습니다. 따라서, Pytorch에서 Dataset class를 사용하면 직접 implementation을 하지 않아도 됩니다. 하지만, 몇몇 method를 override 해놓도록 implementation이 되어 있기에 공부하는데 도움이 된다고 할 수 있습니다. 

## Map-style dataset



## Iterable-style dataset





# DataLoader