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
