## Faster R-CNN: Towards Real-Time Object Detection with Region Proposal Networks

### Abstract
- 본 논문에서는 Region Proposal Network (RPN) 을 제안, 이는 full-image convolutional features를 detection network 와 공유
- cost-free region propsals 가능
- RPN은 fully comvolutional network로 object bound와 objectness scores를 동시에 예측
- 본 논문에서는 RPN과 Fast R-CNN을 attention mechanism을 사용하여 merge

### Introduction
- Region proposal method
  - inexpensive features와 economical inference schemes에 의존
  - Selective search(가장 인기 있는 방법론) 는 superpixels을 low-level features에 기반하여 merge
  - 하지만 다른 효과적인 detection networks와 비교했을때, selective search는 느림
- Region-based CNN
  - GPU 의 advantage
  - 하지만 re-implementation은 down-stream detection network를 무시, 따라서 sharing computation 기회를 놓침
- 본 논문에서는 거의 cost-free한 알고리즘 제안
- Region Proposal Networks (RPNs)를 제안하는데 이는 SOTA object detection networks와 convolutional layers를 공유
- 본 논문에서는 region-based detectors에서 사용하는 convolutional feature maps이 region proposals을 생성하는데에도 사용될 수 있다는 것을 발견
- pyramids of images or pyramids of filters을 사용하는 기존 방법들과 달리 본 논문에서는 novel "ahchor" boxes를 도입
- RPNs를 Fast R-CNN과 통합하기 위해, 본 논문에서는 training scheme을 도입하는데, 이는 region proposa을 위한 fine-tuning과 object detection에 대한 fine-tuning을 번갈아가면서 함


### Related Work
- Object Proposals
  - 가장 널리 사용되는 object proposal method는 selective search, CPMC, MCG과 같은 grouping super-pixels에 기반한 방법론과
  - objectness in windows, EdgeBoxes와 같은 sliding windows에 기반한 방법론임
  - Object proposal method는 detector와 독립적적으로 external 모듈로 적용
- Deep Networks for Object Detection
  - R-CNN방법론은 Cproposal regions를 object categories나 background로 분류하기 위해 CNNs를 end-to-end로 학습
  - R-CNN은 주로 classifier로서 역할을 함
  - 몇몇 연구에서 deep networks를 object bounding boxes를 예측하기 위해 사용하는 방법을 제안
  
### Faster R-CNN
- Faster R-CNN은 2개의 모듈로 구성
- 첫번째 모듈은 regions을 propose하는 deep fully convolutional network
- 두번째 모듈은 제안된 regions을 사용하는 Fast R-CNN detector
- 'Attention' mechanism을 사용하여 RPN 모듈은 Fast R-CNN 모듈한테 어디를 보아야 하는지 말해줌
- 1) Region Proposal Networks
  - Region Proposal Network(RPN)은 이미지를 input으로 받고 objectness score와 함께 object proposals 구역(사각형)을 output으로 줌
  - 본 논문에서는 이를 fully convolutional network로 구현
  - 궁극적인 목표는 Fast R-CNN object detection network과 computation을 공유하는 것이기 때문에, 두 network가 같은 convolutional layers를 공유한다고 가정
  - region proposal을 하기 위해, 컨볼루션 레이어의 마지막 층의 컨볼루션 특징 맵 위로 작은 네트워크를 슬라이딩 함
  - 1. Anchors
    - 각 sliding-window location에서 multiple region proposals을 동시에 예측
    - 각 location에서 가능한 maximum proposals은 k로 나타냄
    - reg layer에서는 4k 개의 output, cls layer에서는 2k scores output
    - k 개의 proposals은 k reference boxes와 관련하여 파라미터화되고, 이를 anchors라 부름
    
    - 1. Translation-Invariant Anchors
       - 본 논문에서 제안하는 방법론의 중요한 속성은 translation invariant 임
       - 이는 anchor와 anchor에 상대적인 proposal을 제안하는 함수 측면에서 모두 translation-invariant하다는 것
    - 2. Multi-Scale Anchors as Regression References
       - multi-scale prediction을 위한 2가지 인기 있는 방법이 있음
       - 첫번째 방법은 image/feature pyramids에 기반한 방법
       - 이 방법은 useful하지만 time-consuming
       - 두번째 방법은 feature map에 multiple scales의 sliding windows를 사용하는 것
       - 두번째 방법은 보통 첫번째 방법과 함께 적용됨
       - 본 논문에서 제안하는 anchor-based method는 pyramid of anchors 위에 built 됨, 이는 more cost-efficient함
       
 - 2. Training RPNs
  - RPN은 backpropagation과 SGD를 사용하여 end-to-end로 학습
  - image-centric sampling strategy를 사용
  - 랜덤으로 256개의 anchors를 샘플링, 샘플은 positive 와 negative의 비율이 1:1
 
 - 2) Sharing Features for RPN and Fast R-CNN
 

  
