# Abstract

StyleGAN은 단어 그대 Generative model인 GAN을 이용하여 style transfer를 하는 모델이다.

StyleGAN을 통해 super resolution뿐만 아니라 input을 수정하여 이미지 내의 semantic한 특징들을 뽑아내여 조절할 수도 있다.

StyleGAN의 기본이 되는 모델은 PGGAN으로 layers를 쌓아가며 super resolution한다는 특징이 있다.
이러한 특징을 통해 Output 정보를 잘 이해할 수 있게되며 다른 Gnerative model들보다 더 진짜같은 고해상도 이미지 아웃풋을 만들어낸다.

![image](https://user-images.githubusercontent.com/73181519/115556720-ecdf3f00-a2eb-11eb-8e53-a898916f3ca7.png)


# Background

GAN은 모두 잘알다싶이 Generator와 Discriminitor가 번갈아가며 적대적으로 학습하여 진짜같은 이미지를 만들어내는 생성모델이다. 
이전에 Super resolution을 한 ProGAN이라는 모델은 high quality의 이미지를 생성해냈지만 이미지 내의 특징을 조절하는 측면에서는 한계가 있었다.
즉 특징들을 독립적으로 조정하는 것이 어려웠다.

이와 달리 StyleGAN은 Generator에 초점을 맞춰서 ProGAN보다 개선된 성능을 보여준다.


# How StyleGAN works

![image](https://user-images.githubusercontent.com/73181519/115558365-a25ec200-a2ed-11eb-8d3e-529161aed6e6.png)

이 논문의 저자는 ProGAN에서의 progressive layer를 활용하여 특징들을 조절하는 것이 가능할 것임을 밝혀내어 이를 사용했다.

StyleGAN 저자들은 또한 특징을 세 가지로 구분했는데,

1. Coarse : 8 해상도 까지의 특징 ex. hair style, face shape...
2. Middle : 16~32 해상도까지의 특징 ex. face details, eyes(closed/opened)
3. Fine : 64~1024해상도까지의 특징 ex. eyes, skin 등의 colors

## Mapping Network

이 네트워크의 목표는 input vector를 일종의 intermediate vector로 encoding하는 것이다. 
즉 이미지내의 다른 특징들을 조절하기 위해 encoding을 하는 것이다.

기존의 GAN에서는 특징들이 얽혀있어서(?) 하나를 조정하면 다른 특징이 변했었는데 이 Mapping Network를 통해 이 문제를 해결할 수 있다.

예를 들어, 사용한 Dataset에서 검은색 머리를 가진 사람의 이미지가 많다면 input이 이런 특징에 매핑이 되어 모델이 일종의 bias를 가질 수 있는데,
이 네트워크를 통해서 학습 Dataset의 분포에 관계없이 특징을 뽑아내서 특징간의 독립성을 높이게 된다.

Mapping Network는 8개의 fully connected 레이어를 가지고 output은 input layer와 같은 사이즈인 512X1를 가진다.


## Style Modules(AdaIN)

이 모듈은 인코딩된 벡터를 레어이에 일종의 전달하는 역할을 하는데, 합성 네트워크의 단계들에 더해짐으로써 해당 단계에서의 시각적인 표현을 정의한다.

## Removing traditional input

보통 GAN에서 Generator는 처음에 random값으로 셋팅되었었는데 이 논문 저자들은 이러한 인풋을 생략하고 상수값으로 대체했다.
(왜일까? 의미가 있나?)

## Stochastic variation

예를 들어 인간의 이미지라 하면 주근깨나 주름과 같은 작은 부분들이 있다. 
GAN에서 이러한 특징을 위해서 input를 random vector를 사용했다고 하는데 이 논문 저자들은 위에서 언급한 특징들끼리 얽힌 현상으로 인해서 이렇게 random vector를 통해 노이즈를 주는 것이 까다롭다고 한다.

StyleGAN에서 이러한 노이즈는 AdaIN과 비슷한 방식으로 추가되는데, scaled noise가 AdaIN모듈 이전에 각 채널에 더해짐으로써 visual representation에 약간의 노이즈를 추가해 변화를 준다고 생각하면 된다.


## Style mixing

StyleGAN에서 generator는 각 단계마다 중간 vector를 이용하는데 이러한 상관관계(?)를 줄이기위해 StyleGAN은 랜덤하게 두 개의 input vector 선택 후 각각에 대한 중간 vector w를 생성한다고 한다. 

또한, 초기에는 첫번째 vector로 학습을 진행하고 특정 시점 (랜덤으로 정함)이후에는 두번째 vector로 학습을 진행한다. 이를 통해 네트워크가 라벨간의 관계를 학습하게 되고 일종의 독립성또한 배우게 된다.

## Truncation trick in W

Generative model은 이전에서도 언급했듯이 training data에서 잘 나오지 않은 특징을 다루는 것이 중요한 과제이다. Training data에 없는 특징은 학습하기가 어렵기 때문이다. 

StyleGAN은 중간 vector를 truncate하여 평균의 중간벡터로 가도록 한다. 이를 w_arg라 하면 이 벡터는 많은 input vector로부터 생성되는데, mapping network를 통해 중간벡터를 생성한 뒤 이 벡터들의 평균을 구하는 식으로 생성된다. 이를 통해 각 특징이 평균 특징(?)으로부터 얼마나 떨어져있는지 결정할 수 있다는 흥미로운 사실또한 밝혀냈다.


## Fine-tuning

StyleGAN은 위에서 언급한 ProGAN을 일종의 튜닝을 통해 모델을 개선했다고도 볼 수 있는데, ProGAN에서의 hyperparameter를 업데이트하고 scaling을 bilinear sampling으로 대체했다.


# Results

정량적인 지표를 살펴보자.

![image](https://user-images.githubusercontent.com/73181519/115559827-046bf700-a2ef-11eb-813a-27009b5e0ee4.png)

![image](https://user-images.githubusercontent.com/73181519/115559758-f3bb8100-a2ee-11eb-847c-529bce6010b9.png)

정성적인 결과는 다음과 같다.

보통 Generative model이 얼굴 이미지 중심인 것에 비해 StyleGAN은 일반 물체에 대한 결과도 다루었는데
아래 사진에서 확인할 수 있듯이 성능이 좋다.

![image](https://user-images.githubusercontent.com/73181519/115559939-1b124e00-a2ef-11eb-8c8c-dedb8352f2ab.png)

![image](https://user-images.githubusercontent.com/73181519/115559973-236a8900-a2ef-11eb-8e0e-59febd6b0079.png)


참고 : [link](https://towardsdatascience.com/explained-a-style-based-generator-architecture-for-gans-generating-and-tuning-realistic-6cb2be0f431)
