# Faster R-cnn

## Introduction

- R-CNN, Fast R-CNN에 이은 논문인 Faster R-CNN
- 처음 R-CNN은 3가지의 모듈(region proposal, classification, bounding box)로 구성되어 있었음. Fast R-CNN에서는 classification과 bounding box regression을 neural network로 구성하였음. 즉, Region proposal단계는 네트워크 밖에서 cpu로 계산되기에 시간이 오래 걸린다는 단점이 존재하였음. Faster R-CNN은 region proposal도 neural network로 연산하는 방법을 제안함. 진정한 end to end 방식의 object detection을 제안한 논문임.
- Faster R-CNN은 RPN + Fast R-CNN임. 즉, Fast R-CNN구조에서 conv feature map과 RoI Pooling 사이에 RoI를 생성하는 Region Proposal Network가 추가되어있음.

## Faster R-CNN

: 2개의 모듈로 구성됨 1. deep fully conv. network : region 제시 2. Fast R-CNN detector

: end-to-end로 되어짐

: feature map 추출 → RPN을 통해 RoI 계산 → RoI pooling → classification을 통한 object detection

## Regional Proposal Networks

- input: image (any size) / output: objectness score가 적혀진 직사각형 모양의 object proposal
    1. feature map을 input으로 받음. 이 feature map에 3x3 conv를 통해서 intermediate layer(두 번째 feature map)을 만듬
    2. intermediate feature map을 이용해 classification과 bounding box regression 수행. 이때 1x1 conv를 사용함.
    3. Classification에서는 1x1 conv를 통해서 k개의 anchor box들이 object인지 아닌지에 대한 예측을 함. 이 결과를 softmax를 적용하면 anchor가 object일지 아닐지가 확률의 값으로 나옴.
    4. Bounding box regression에서는 1x1 conv를 통해 구함.
    5. Classification의 결과값을 이용해 물체일 확률 값들을 정렬 후, 높은 순으로 k개의 anchor를 골라서, 각각에 대해 bounding box regression을 적용해줌. 그 후 non-maximum suppression을 적용헤 RoI를 구함.
    6. 뒷 부분은 Fast R-CNN

## Loss Function

- Classification과 Bounding box regression을 위한 loss function으로 구성됨.
- Classification: log loss를 사용
- Bounding box regression: smooth L1 loss 사용

## Training

- RPN이 학습이 제대로 되지 않은 상태에서는 뒤에 classification layer가 제대로 학습을 할 수 없는 상황이 발생됨. 따라서 논문에서는 alternating training 기법을 제안함
    1. ImageNet에 pretrained된 모델을 불러와 RPN 학습함
    2. 학습된 RPN에서 region proposal을 가져와 Fast R-CNN학습
    3. 앞에서 학습된 RPN과 Fast R-CNN을 불러와 RPN에 해당하는 부분만 finetuning, 나머지는 고정
    4. Fast R-CNN에 해당되는 부분만 fine-tuning, 나머지는 고정

    [참조] [https://yeomko.tistory.com/17](https://yeomko.tistory.com/17)