# Abstract

StyleGAN은 단어 그대뢔 Generative model인 GAN을 이용하여 style transfer를 하는 모델이다.

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

이 모듈은 인코딩된 벡터를 일종의 전달하는 역할을 하는데, 합성 네트워크의 단계들에 더해짐으로써 해당 단계에서의
시각적인 표현을 정의한다.

