## You Only Look Once: Unified, Real-Time Object Detection

### Abstract
- Object detection을 regression problem으로 인식
- single neural network를 통해 bounding boxes와 class probabilities를 예측
- single network이기 때문에 eng-to-end 학습 가능
- 제안하는 모델인 YOLO, Fast YOLO는 매우 빠름
- 다른 SOTA 모델들과 비교하여 YOLO는 localization error를 더 많이 만들어내지만, background에 대한 false positive를 줄이는 것을 확인


### Introduction
- R-CNN과 같은 방법들은 region proposal 방법을 사용하여 bboxes를 예측하고 그 후 classifier를 통해 분류
- classification 후에 post-processing(refine the bboxes, eliminate duplicate detections, rescore the boexes...)진행
- 이러한 복잡한 pipelines은 매우 느리고 최적화하기 어려움
- 본 논문에서는 object detection을 single regression problem으로 인식하여 bboxes와 class probabilities를 한번에 예측

<img width="530" alt="image" src="https://user-images.githubusercontent.com/48814946/111060058-98bb9080-84dd-11eb-96d3-5bc390dcfa79.png">

- YOLO는 다음과 같은 장점이 있음
  - 첫째, YOLO는 매우 빠름 -> regression problem으로 복잡한 파이프라인이 필요없음
  - 둘째, YOLO는 prediction을 할 때 이미지 전체를 보고 판단 -> sliding window 혹은 region proposal 기반 방법과는 달리 YOLO는 전체 이미지를 보기때문에 contextual information을 암시적으로 사용함
  - 셋째, YOLO는 object의 일반화된 representation을 학습 -> 새로운 domain 혹은 unexpected input에 대해서도 일반화 가능


### Unified Detection
- YOLO는 input image를 SxS grid로 나눔
- 만약 object의 center가 하나의 grid cell 안에 속하게 되면, 그 grid cell은 해당 object를 detection해야 할 책임이 있음
- 각 grid cell은 B개의 bboxes와 그 boxes에 대한 confidence scores를 예측
- confidence scores는 모델이 그 box가 object를 포함하고 있는지에 대해 얼만큼 확신이 있는지, 그리고 그 예측에 대해 얼만큼 정확하다고 생각하는지를 반영함
- 만약 cell에 아무 object도 없으면 confidence score는 zero가 됨
- 각 grid cell은 또한 C 개의 conditional class probabilities를 예측
- 이 probabilities는 grid가 object를 포함하고 있을 확률을 의미

<img width="530" alt="image" src="https://user-images.githubusercontent.com/48814946/111060375-cacdf200-84df-11eb-9a35-088b496f042e.png">

- YOLO는 S = 7, B = 2, C = 20(PASCAL VOC -> 20 label), 따라서 final prediction = 7 x 7 x 30 tensor 

#### Network Design
- 본 논문에서 제안하는 방법론은 GoogLeNet으로부터 영감을 받음
- 24 convolutional layers, 2 fully connected layers로 이루어짐
- GoogLeNet과는 달리, 1x1 reduction layers를 사용
- Fast YOLO는 24개의 convolutional layers가 아닌, 9개의 convolutional layers 사용
- network의 size를 제외하고 YOLO와 Fast YOLO의 parameters는 모두 동일
<img width="1050" alt="image" src="https://user-images.githubusercontent.com/48814946/111060499-a58db380-84e0-11eb-9ca5-933623852974.png">

#### Training
- ImageNet 1000-class competition dataset을 사용하여 모델의 convolutional layers를 pretrain함
- pretraining동안은 처음 20개의 convolutional layers를 사용
- 약 일주일 정도 학습 진행, single crop top-5 accuracy 88% 달성
- pretraining 후 모델은 detection 을 위한 모델로 변환
- weight를 랜덤으로 초기화한 4개의 convolutional layers와 2개의 fully connected layer를 추가
- 모델의 마지막 layer에서 class probabilities와 bboxes를 예측
- 마지막 layer에서는 linear activation function 사용, 나머지 layers에서는 leaky rectified linear activation 사용
- sum-squared error사용 -> easy to optimize
- 하지만 이 error는 maximizing average precision이라는 모델의 목표에 완벽히 맞지는 않음
- 또한, 각 grid cell에는 어떤 object도 포함되어 있지 않을 수 있고 이는 그 cell의 confidence score가 zero가 되도록 함 -> 종종 overpowering the gradient
- 이는 모델을 불안정하게 만듬
- 이를 완화하기 위해, bboxes의 loss를 증가시키고, object를 포함하고 있지 않은 boxes의 confidence prediction에 대한 error를 감소시킴
- sum-squared error는 large boxes와 small boxes에 대한 에러를 equally 취급(?)함
- 이를 위해 본 논문에서는 bboxes width 와 height를 directly 예측하는 것이 아니라 square root를 예측
- Loss function 은 아래와 같음
<img width="512" alt="image" src="https://user-images.githubusercontent.com/48814946/111060823-16ce6600-84e3-11eb-9919-d221a6ae9ff5.png">

#### Inference
- training에서와 같이 test image에 대한 prediction도 one network만 필요
- YOLO는 single network evaluation만 필요로하기 때문에 매우 빠름

#### Limitations of YOLO
- strong spatial contraints -> 왜냐하면 각 grid cell은 오직 2개의 boxes만 예측하고 하나의 class만 갖기 때문 -> 이러한 spatial constraints는 우리의 모델이 예측할 수 있는 object의 수를 제한
- 우리의 모델은 데이터로부터 bboxes에 대한 예측을 배우기 때문에, 새로운 혹은 unusual한 aspect ratios 혹은 configurations에 대해 일반화 어려움
- 또한 모델이 bboxes를 예측하기 위해 상대적으로 "coarse features"을 사용
- small bboxes와 large bboxes의 error를 동일하게 취급

### Experiments
#### Comparison to Other Real-Time Systems
<img width="512" alt="image" src="https://user-images.githubusercontent.com/48814946/111061041-8d1f9800-84e4-11eb-84ef-d0ccdb81ffe4.png">

#### VOC 2007 Error Analysis
<img width="512" alt="image" src="https://user-images.githubusercontent.com/48814946/111061077-bccea000-84e4-11eb-9a93-22cd949d9f4b.png">

#### Combining Fast R-CNN and YOLO
<img width="512" alt="image" src="https://user-images.githubusercontent.com/48814946/111061092-dc65c880-84e4-11eb-81f6-8a39d1d1aceb.png">


