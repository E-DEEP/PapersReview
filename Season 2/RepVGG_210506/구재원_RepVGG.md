# RepVGG: Making VGG-style ConvNets Great Again
    
### Problem
 : ImageNet에서 image recognition에 좋은 성능을 보이는 ConvNet들이 많이 발표되었다. 하지만 이런 네트워크들은 다음과
같이 2가지의 문제점이 존재했다.
  1. 복잡한 multi-branch designs (e.g., ResNet에서 residualaddition, Inception에서 branch-concatenation)
     들은 모델을 구현하기 힘들게 만들고, inference시에 시간과 메모리를 많이 잡아먹음
      
  2. memory access cost를 증가시키고 그에 따라 사용할 수 있는 device에 종류를 제한시킴.


### Contribution

  : 위에 문제점들을 해결하는 새로운 모델인 RepVGG를 제안하였다. RepVGG는 다음과 같은 장점들을 갖고 있다. 

1. VGG와 같은 topology로 디자인 되어있으면서 branch가 존재하지 않는다. 즉, 한 layer의 input은 오직 전 layer에서만 온다. 
2. 3 × 3 conv와 ReLU만으로 구성되어 있다.
3. 전체 모델 중 무거운 부분이 존재하지 않는다. 

 Multi-branch의 장점은 train시간에만 존재하고, test 시에는 단점만이 존재한다. 따라서 RepVGG는 train시에는 multi-branch를
inference시에는 plain architecture가 되도록 re-parameterization을 시행한다. Re-parameterization이란 parameter를 
바꾸며 하나의 aritecture를 다른 aritecture로 만드는 것을 의미한다. 

아래 그림에서 보면, b가 train시에 multi-branch의 형태를 띄는 RepVGG이고, C는 test시의 변형된, plain architecure의 RepVgg이다.

![image](https://user-images.githubusercontent.com/34685762/117151170-c7762900-adf3-11eb-9c26-17d485a91df1.png)


Re-parameterization하는 과정은 다음과 같다. 

![image](https://user-images.githubusercontent.com/34685762/117151298-e83e7e80-adf3-11eb-8b82-53bd435cfacd.png)



    








