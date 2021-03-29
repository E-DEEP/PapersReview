# Faster R-CNN

------------
2016년 1월 , 논문 제목: Faster R-CNN: Towards Real-Time Object Detection with Region Proposal Networks

## 0. Abstract

- 참고:
Fast R-CNN은 이전 R-CNN의 한계점을 극복함.

- R-CNN의 한계점: 
1. ROI 마다 CNN 연산을 해야 되서 속도가 느림
2. 모델을 한 번에 학습시키지 못함.

- Faster R-CNN은 RPN + Fast R-CNN이다.
Fast R-CNN의 구조에서 conv feature map과 RoI Pooling사이에 ROI를 생성하는 Region Proposal Network가 추가됨.
RPN 네트워크에서 사용할 CNN과 Fast R-CNN에서 사용한 CNN 네트워크를 공유하려는 개념.


------------

## 1.Introduction

R-CNN의 경우)
Region proposal 추출 -> 각 region별로 CNN 연산 -> classification -> bounding box regression

Fast R-CNN의 경우)
Region proposal 추출 -> 전체 image CNN 연산 -> ROI Projection, ROI Pooling -> classification, bounding box regression
단, 단점: selective search를 수행하는 Region proposal 부분이 외부에 있기 때문에 느리다.

Faster R-CNN의 경우)
+  detection에서 쓰인 Feature를 RPN에서도 공유해서 ROI생성을 CNN 단계에서 수행한다 -> region proposal도 CNN안에서 수행한다 -> 속도가 빨라진다.
결론: RPN 자체를 학습하여 selective search 없이 학습.


------------

## 2.Related Work


------------

## 3.Faster R-CNN
### 3.1.Region Proposal Networks(RPN)
- RPN의 input 값은 이전 CNN 모델에서 뽑아낸 feature map이다. 
- RPN의 output은 object proposal들의 sample


- ancohor)
9개의 anchor box를 이용해->classification과 box regression을 구한다
+1x1 convolution을 이용한다.
Bounding box regression에는 4개의 좌표값을 사용한다. 
anchor 개념: 미리 정의된 bounding box. IoU가 0.7이상인 anchor->positive label
IoU가 0.3이상인 anchor->negative label

-최종 Loss = Classification Loss + Regression Loss


------------



### 3.2Sharing Features for RPN and Fast R-CNN
모델의 학습과정
CNN의 output으로 RPN만을 학습한다 
=> RP를 이용하여 Faster R-CNN 네트워크를 학습
=> shared CNN, fc layers, detector 부분 학습
=> shared CNN부분은 프리징하고 다시 학습
=> 모델의 RP로 다시 한 번 모델을 학습



------------

### 5. Conclusion
Real time Detector로는 아직 부족.


