## 0. Abastract

- Instance Segmentation 
- 다른 task에도 쉽게 사용할 수 있다. 

-----------

## 1. Introduction 

* Mask R-CNN이 Faster R-CNN과 비교해 달라진 점.

1) Mask branch:　Fast R-CNN의 classification, localization branch에 새롭게 mask branch가 추가됐다.
2) FPN: RPN 전에 FPN이 추가됐다.
3) RoI align: Image segmentation의 masking을 위해 RoI align이 RoI pooling을 대신하게 됐다.
+) 이 외에는 Faster R-CNN과  크게 다르지 않는 구조를 갖고 있다.

-----------

## 2. Related Work

* 이전 모델

R-CNN
Fast R-CNN
Faster R-CNN
-> Mask R-CNN

-----------
## 3. Mask R-CNN

* Mask R-CNN Structure 

Input:　(NxN 사이즈의 이미지) 

-> 이미지를 800~1024 사이로 resize (bilinear interpolation)

	- 800~1024 사이일 때 ResNet의 성능이 좋다고 알려져 있다.	
	- 보간법으로 bilinear inpolation 방법을 채택

-> 1024 x 1024 로 resize (padding)

	- 네트워크의 input size는 1024x1024이다.
	- zero padding

-> ResNet-101을 통해 각 layer에서 feature map 생성(c1,c2,c3,c4,c5) 

	- Backbone으로 ResNet 모델을 사용
  ![3 ResNet](https://user-images.githubusercontent.com/50253860/113870130-7b33cb00-97ec-11eb-8a77-1cd45424d39d.png)


-> FPN을 통해 feature map 생성(p2,p3,p4,p5,p6)

	- FPN = Feature Pyramid Network
	- 기존 Faster R-CNN에서의 비효율성을 극복.
	- FPN에서는 마지막 layer의 feature map에서 점점 이전의 중간 feature map 들을 더해 이전 정보까지 유지할 수 있도록 한다. 
	- 모두 동일한 scale의 anchor를 생성. (비효율적으로 여러 개 만들 필요가 x)
	- ResNet을 통해 C1,C2,C3,C4,C5 feature map을 생성 => F5,F4,F3,F2 생성 => 3x3 convolution 
	- F6은 F5로부터 추가적으로 생성됨. (maxpooling)
  ![2  FPN](https://user-images.githubusercontent.com/50253860/113870084-6ce5af00-97ec-11eb-9719-2a28ee2dfd2f.png)


-> 최종적으로 생성된 feature map에 각각 RPN을 적용, classification, bbox regression output 값을 도출

	- 생성된 F2,F3,F4,F5,F5을 각각 RPN모델에 전달
	- 각 feature map에서 1 scale*3 ratio= 3 anchor를 생성. 
	- classification 값, bbox regression 값으로 output.

-> bbox regression을 원래 이미지로 projection, anchor box 생성

	- bbox regression 값 -> delta
	- delta -> anchor 정보를 연산
	- anchor bounding box 좌표값으로 바뀜

-> 가장 score가 높은 anchor box를 선택 (Non-max-suppression)

	- 이미지에 anchor 좌표를 대응시킨 후에 각각 normalized coordinate로 대응.
	- classification score가 높은 anchor를 선택.
	- NMS 알고리즘을 통해 anchor의 개수를 줄인다.
	- bbox와 IoU를 계산.
	- IoU가 해당 bbox와 0.7이 넘어가면 두 bbox는 동일 object를 detect한 것.
	- 각 객체마다 score가 가장 큰 box만 남게 됨.

-> ROI align을 통해 anchor box들의 size를 맞춤.

	- 기존의 Faster R-CNN와는 달리, bilinear interpolation을 이용해서 위치 정보를 담는 RoI align을 이용.
  
  <img width="251" alt="1 ROI" src="https://user-images.githubusercontent.com/50253860/113869894-36a82f80-97ec-11eb-93d4-9b3f34475f70.png">


-> Mask branch에 anchor box 값을 통과

---------------
## 5. Conclusion

다양한 task가 가능하다. 
Instance Segmentation을 중점으로 진행.





