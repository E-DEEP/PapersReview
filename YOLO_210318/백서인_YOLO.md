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
- 
  
