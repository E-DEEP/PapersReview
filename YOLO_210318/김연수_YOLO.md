# You Only Look Once: Unified, Real-Time Object Detection

## Abstract

- prior work : repurposes classifiers to preform detection
- YOLO : object detection problem -> **regression problem** about bounding boxeds and class probabilities
    - Neural Nets --> predicts bbox and class proba. **directly from images in one evaluation**
    - `end-to-end`
- YOLO has less 'background error'
- YOLO learns general representations


## Introduction

Prior Work
- R-CNN : region proposal --> generates multiple of bboxes --> classifier --> post-processing to refine the bboxes
    - slow, hard to optimize
- **YOLO**
    - object detection as a single regression problem
    > "You Only Look Once" at an image to predict what objects are present and where they are.


YOLO
- convolutional net simultaneously predicts *multiple bboxes and class probabilities*
- trains on full images and directly optimizes detection performance


Pros
- FAST
- reasons GLOBALLY (<-> sliding window, region proposal)
    - less background error
- learn General Representation
    - new domain, unexpected input is OK.

## Unified Detection
1. input image into SxS grid
2. each grid cells predicts bounding boxes and confidence score
    - $ confidence = Pr(object) * IOU^{truth}_pred $
    - bounding box has 5 predictions : (x,y,w,h,confidence)
    - also predicts Conditional class probabilities
        - $ conditional_class_probability = Pr(class_i|Object) * Pr(Object) * IOU $


### Network Design

![image](https://user-images.githubusercontent.com/48315997/111483644-5661b000-8778-11eb-8f74-975cf69db03d.png)


- inspired by GoogLeNet
- 24 convolutional layers + 2 FC
- alternating 1x1 reduction layer (to reduce the feature space)


### Training

- pretrain on ImageNet 1000-class competition dataset
- optimize for sum-squared error
    - this method eqaully weights errors in large boxes and small boxes.
    - **weights localization error equally**
- To remedy this, Use $ gamma_{coord}, gamma_{noobj} $
    - increase the loss from bbox coord. predictions
    - decrease the loss from condfidence prediction for NO OBJECT
- One predictor to be "responsible" for predicting an object
    - (?) 잘 모르겠다
- loss function
![image](https://user-images.githubusercontent.com/48315997/111482691-7e044880-8777-11eb-9ab3-a74c00341d39.png)


### Limitations
- didn't work well with group of small objects
- didn't work well with new or unusual ratio of bounding box
- treats errors the same in small bboxes vs. large bboxes


## Experiments

- Error Analysis

![image](https://user-images.githubusercontent.com/48315997/111483199-ef43fb80-8777-11eb-944f-e649e1f4e1ec.png)
YOLO makes much fewer Background error!

- Generalization results on Picasso and People-Art datasets.

![image](https://user-images.githubusercontent.com/48315997/111483516-303c1000-8778-11eb-8cb2-b7b9a89a6484.png)
