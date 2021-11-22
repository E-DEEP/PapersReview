# Mask R-CNN

~~~
[ 정리 ]
 - Object Instance Segmentation을 하는 simple, flexible 그리고 fast한 모델을 제안한 논문.
 - Segmentation mask를 예측하는 branch를 parallel하게 만들었음. 
 - spatial location을 보존하는 RoIAlign을 제안함.
~~~


## 1. Introduction

### Contribution:


   기존의 object detection은 bounding box만을 결과로 보여주었음. 즉, 물체가 어디에 있는지만 알려줄 뿐, 그 안에 몇 개의 물체가 있는지는 알려주지 못하였음.
   본 논문에서는 pixel 하나하나의 class를 알려주는, 즉 물체가 무엇인지 알려주는 semantic segmentation과 object detection을 한번에 수행하는 instance segmentation에 대해 다룸.
   ** 참고로 semantic segmentation은 pixel 단위로 수행되는 task이고 object detection은 bounding box로 수행됨.

   두 task를 동시에 수행해야하기에 복잡한 구조가 필요하다고 생각이 들 수 있으나, 본 논문에서 제안하는 mask R-CNN은 간단하며 빠른 구조를 갖고 있음. 각각의 RoI(Region of Interest)마다
   segmentation mask를 예측하는 branch를 하나 더 추가해줌. 이때 이 branch는 bounding box regression model과 parallel하게 동작함.

   또한, 이 논문에서는 Faster R-CNN을 사용하였는데, Faster R-CNN은 pixel-to-pixel alignment를 하기에 적합하지 않게 되어있음. Faster R-CNN은 본래 목적이 object detction이기때문임.
   따라서 저자는 RoIAlign이라는 제안하여 이런 feature들의 misalignment를 해결하였음.

    기존의 모든 COCO instance segmentation task 모델들보다 성능이 좋음. 학습하는데 빠름. human pose estimation에도 잘 동작함.

## 2. Mask R-CNN

 - Faster R-CNN의 output: class label + bounding box offset
 - Mask R-CNN의 output  : class label + bounding box offset + object mask

 -> object mask를 얻기 위해서는 물체의 finer spatial layout을 얻어야하기에 Pixel-to-pixel alignment를 추가해줌.


#### 1) Mask R-CNN
    - Faster R-CNN의 two stage를 그대로 사용함. 첫번째 stage는 동일하게 사용하되, 두번째 stage에서 각각의 RoI마다 binary mask를 나오게 만들어줌. 
    - L = L_cls + L_box + L_box로 이루어져있음. L_cls와 L_box는 Faster F-CNN과 동일한 식을 사용함. L_box는 binary cross-entropy loss를 사용하였음. 
    mask branch는 RoI마다 Km^2의 output을 갖고 있음( K: class 수, m X m: resolution). Grountruth class인 k에만 mask가 정의되어 있기에 다른 output은 최종 loss에
    기여를 하지 않게 만들었음. 이처럼 per-pixel sigmoid와 binary loss를 사용하여 다른 class와 compete하는 loss처럼 만들지 않음. 이렇게 만들었기에 instance segmentation에서 좋은
    성능을 보여주었음. 

#### 2) Mask Representation
    - Mask는 input objectdml spatial layout을 encode시킴. 따라서 일반적인 FC layer에 넣으면 spatial layout이 전부 사라짐. 따라서 일반적으로 RoI에서 FCN을 사용하여 mask prediction을
    하면 안됨.
    - 따라서 per-pixel spatial correspondence를 잘 보존할 RoIAlign을 제안함 

#### 3) RoIAlign
    - RoIPool: RoI에서 조그만한(7x7) feature map을 추출해내는 것 
    - RoIPool에서의 harch quantization을 없애 위한 RoIAlign을 제안함. RoI boundary에 관련한 모든 quantization을 안함. bilinear interpolation을 사용하여 결과를 aggregate함.
    





