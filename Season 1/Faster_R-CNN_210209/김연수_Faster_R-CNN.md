
# Faster R-CNN : Towards Real-Time Object Detection with Region Proposal Networks


* 흐름 : R-CNN -> (SPP-net) -> Fast R-CNN -> Faster R-CNN


1. R-CNN : Region with CNN features. RoI 들을 뽑아내고 CNN에 각각 집어넣음.
    - Region Proposal – Selective Search : 어떻게 바운딩 박스(bbox)를 뽑아내는가
    - Training 
    - Pre-train AlexNet
    - SVM, bounding-box regressor (CNN에 학습이 안된다는 단점이 있음)


2. Fast R-CNN
    - RoI projection -> 매 bounding box마다 RoI pooling
    - 어떤 RoI가 나와도 똑같은 size가 나오도록 max pooling => RoI pooling
    - Fixed-length feature vector from RoI가 됨.
    - FC 레이어에 넘기면서 classification(K+1 class) + bounding box location 동시에 계산
    - Problems of Fast R-CNN : Out-of-network region proposals are the test-time computational bottleneck


3. Faster R-CNN
    - Notion : Region Proposal을 Selective Search(CPU에서 했음)을 하지 말고 실제 네트워크 안에서 같이 해보자(GPU로 계산 가능)
    - **Key Point : Region Proposal Network(RPN) + Fast R-CNN**
    


    - CNN을 share하는 것을 목표로, 즉 네트워크가 하나인 것처럼 해보자!
      
      <img width="400" alt="그림1" src="https://user-images.githubusercontent.com/48315997/88275099-c61db900-cd17-11ea-9e61-b0f44172936a.png">


    - RPN 
    ![IMG_5BDD1471DB01-1](https://user-images.githubusercontent.com/48315997/88275179-e6e60e80-cd17-11ea-9f04-8a258dc4778d.jpeg)  
    ![image](https://user-images.githubusercontent.com/48315997/107228852-78bd2b00-6a60-11eb-9b16-fa5b23d2a1a4.png)
  
        -  각 위치의 object bounds와 objectness score를 동시에 예측하는 fully convolutional network
        - 슬라이딩 윈도우가 찍은 지점마다 Region Proposal(anchor) 예측
        - Anchor
            - **sliding window의 각 위치에서 Bounding Box의 후보로 사용되는 상자**
            
    
    - Loss function (사진)
    
        ![IMG_7BFAB2EF8A93-1](https://user-images.githubusercontent.com/48315997/88275199-f2d1d080-cd17-11ea-8a88-53902efa4790.jpeg)
        
    - 4-step Alternation Training 

        



## Experiments

<img width="500" alt="스크린샷 2020-07-23 오후 7 10 42" src="https://user-images.githubusercontent.com/48315997/88275353-39bfc600-cd18-11ea-967f-4799ddcbba59.png">



Proposal time 매우 줄어듦!
