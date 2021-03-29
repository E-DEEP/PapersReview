# Stargan
-------------

## 0. Abstract
최근 image-to-image translation은 눈에 띄는 발전을 이루었다.
하지만, 기존의 접근법들은 scalability가 부족하고 2개 이상의 도메인에서는 다루기가 어렵다는 단점이 있다. 



## 1. Introduction
각 도메인에 대응하는 모델이 각각 하나씩은 필요하다는 뜻이다.  
-> k 개의 도메인이 있다면, k(k-1)개의 모델이 필요하다.
> ![사진1](https://user-images.githubusercontent.com/50253860/106028006-c788cd80-610e-11eb-8bff-98198d925d2d.png)


=> Star따라서 GAN을 제안한다, 하나의 모델로 다중의 도메인을 다룰 수 있다. 
ex. Male/female, long/short hair 등등
> ![사진2](https://user-images.githubusercontent.com/50253860/106028146-f0a95e00-610e-11eb-9345-e018481452b1.png)


## 2. Related Work
- GAN
전형적인 gan모델. 베이스가 되었다. 
- Conditional GANs
Conditional 도메인 정보를 제공
- Image-to-Image Translation
오직 두개의 도메인만을 분류.
-> 하나의 모델만으로 다중 도메인을 분류할 수 있음을 보여준다.


## 3.Star GAN

### 3.1.Multi-Domain Image-to-Image Translation
Single generator G로 다수의 도메인들 사이의 mapping을 학습하게 만든다. 

> ![사진3](https://user-images.githubusercontent.com/50253860/106028414-4120bb80-610f-11eb-90af-4b03bc0936a5.png)

G는 인풋 이미지 x와 도메인 라벨 c를 받아서 output image y로 변환한다. 
D는 이미지의 소스와(진짜이미지 or G가 만든 이미지) 도메인 라벨에 대한 확률 분포를 만들어야 한다. 
단계는 다음과 같다. 

+ a D 는 진짜 이미지와 G가 만든 이미지를 구별하고, 진짜 이미지일 때는 domain을 알아내는 것을 학습한다.
+ b G는 인풋으로 이미지와 타겟 도메인 라벨을 받고 fake image를 생성한다.
+ c G는 도메인 라벨을 받고 가짜 이미지를 진짜 이미지로 재구성한다. 
+ d G는 진짜 이미지 같고 D에 의해 도메인이 분류 가능한 이미지를 생성하려고 한다. 

> Adversarial loss
> ![사진4](https://user-images.githubusercontent.com/50253860/106028524-67def200-610f-11eb-9fa7-305eb46bf539.png)
>> G 는 y=G(x,c)라는 이미지 아웃풋을 내고, 
>> D는 진짜 이미지와 가짜 이미지를 구분하려고 노력한다. 
>> GAN의 원리와 같다. 
>> D가 진짜이미지라고 판별을 내리면 출력은 1에 가깝고, 가짜라고 판별을 내리면 출력은 -에 가깝다. 
>> 1을 가지도록 학습을 하면 loss는 최소가 될 것.

> Domain Classification Loss
>> 주어진 x와 c에 대해 y로 변환했을 때, c도메인으로 분류되는 것이 목적이다. 
>> D와 G를 최적화할 때 쓰인다. 

> Reconstruction loss
> ![사진5](https://user-images.githubusercontent.com/50253860/106028701-9d83db00-610f-11eb-88dd-1d8e8d615e45.png)
>> 인풋 이미지의 본래 형태를 잘 보존하기 위해, G에 loss를 하나 더 추가.

> Full objective 
> ![사진6](https://user-images.githubusercontent.com/50253860/106028768-b2f90500-610f-11eb-9350-648e9b0b49a8.png)
>> 최종적으로 G,D에 대한 objective func.이 나온다. 

### 3.2.Training with Multiple Datasets

Dataset 이 다른 도메인을 가지고 있어야 한다. 
ex. 빨간 머리 주근깨 소녀 이미지->빨간머리/주근깨/female 등의 다중 도메인

- Mask vector m :
명시되지 않은 라벨은 무시하고 명시된 라벨에 집중.
n차원의 one-hot벡터를 사용한다. 
Mask vector에 어떤 dataset인지를 명시해 주어서 해당 dataset의 특징에 관련된 label에만 집중한다. 
> ![사진7](https://user-images.githubusercontent.com/50253860/106028813-c3a97b00-610f-11eb-85b2-be37b2793e14.png)

-> CelebAdhk RaFD dataset으로 학습할 때 자료.


## 5.Experiments
### 5.1.Baseline Models
> 2개의 도메인 image to image translation 에서 좋은 성과를 보임. 
그래서 매 도메인마다 다수 학습.
-> DIAT, CycleGAN
> cGAN모델로 인코더. 
-> IcGAN

### 5.2.Datasets CelebA.:7 domains
RaFD.: 8개 표정, 3개 각도, 3개 방향

## 6.Conclusion
다중 도메인에서 훌륭한 image to image transformation 성능을 보인다.
고품질 이미지를 출력한다.
mask vector가 간단하다.


