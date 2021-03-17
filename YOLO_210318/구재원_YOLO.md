# You Only Look Once: Unified, Real-Time Object Detection

~~~
[ 정리 ]
Probelm: 기존의 object detection method들은 classifer을 사용하였음.
Contribution: Regrssion Problem으로 바꿈, 단순한 end to end 구조, 전체 이미지를 보며 학습함, 빠르고 generalize 잘됨.
~~~

## 1. Introduction

### Problem:


   기존의 object detection algorithm은 대부분 classifier를 가지고 다양한 지점에서 이미지를 봄. 즉 전체적으로 보는 것이 아님.

   ex1. Deformable parts models(DPM): sliding window 방식 사용.

   ex2. R-CNN: Region proposal method(RPN) -> classifier -> posrt-processing
               -> 각각 학습시켜야하기에 느리고 optimize하기 어려


### Contribution

  : Single Regression Problem으로 바꿈. 하나의 loss function으로 학습 가능옴

  : 매우 간단한 convolution 구조로 bounding box와 class probability 나옴

  이런 차이점은 다음과 같이 3가지 장점이 존재함

1. 매우 빠름
: regression problem 이기에 복잡한 pipeline이 필요 없음.
2. 사진 전체를 보며 학습함.
: 기존 모델은 부분적으로 물체를 보았다면, YOLO는 이미지를 전체적으로 보기에 큰 물체들과 배경을 잘 인식함.
3. Generalize가 잘됨
: 처음 보여진 이미지에도 잘 작동함.

하지만 조그만한 물체들 인식에서는 정확도가 떨어진다는 단점이 존재함. 하지만 이것은 빠른 속도와 trade-off.


## 2. Unified Detection

- 기존의 R-CNN처럼 3개로 나뉘어진 부분들을 하나의 network로 만듬. 이것은 다음과 같은 의미를 지님.

  1. 학습 시에 이미지 전체의 feature을 사용.
  2. 모든 class에 대한 모든 bounding box를 찾는게 동시에 이루어짐.

  -> Network가 이미지 전체와 모든 물체를 고려해서 object detection이 이루어진다.

- S X S Grid로 구성되어 있음.
- 각각의 Grid는 B개의 bounding box와 confidence score를 찾음

  1. confidence - box안의 물체가 얼마나 올바르게 예측되었는지 나타냄.

       confidence = Pr(Object) * IOU
      ** 안에 물체 없으면 0

  2. bounding box - x, y, w, h, confidence로 구성되어 있음.

       (x, y): box의 중심을 상대적 위치로 나타냄

       width, height: 전체 이미지에 대한 상대적 크기.

       confidence: IOU between predicted box & GT

  3. C 개의 Conditional class probability

     : Pr(Class | Object)






