### Abstract
- 최근 두 도메인간 image-to-image translation에 대한 많은 성공적인 연구가 있었다.
- 하지만 기존 방법론은 각 도메인별로 모델을 따로 학습해야 했기 때문에 scalability 와 robustness에서 한계가 있었다.
- 이러한 문제를 해결하기 위해 본 논문에서는 StarGAN 을 제안한다.
- StarGAN은 여러 도메인에 대해 하나의 모델로 학습이 가능하다.

### Introduction
- image-to-image translation 은 이미지의 특정 aspect을 다른 것으로 바꾸는 것이다. 
- ex) changing the facial expression of a person from smiling to frowning, changing hair color, 
  reconstructing photos from edge maps, changing the seasons of scenery images
- 본 논문에서는 attribute를 hair color, gender, age와 같은 의미있는 feature를 나타내는 단어로, 
  attribute value를 hair color의 black/blond/brown과 같이 attribute의 특정 값을 나타내는 단어로
  domain을 동일 attribute value을 공유하는 이미지의 집합을 나타내는 단어로 사용한다.
 
- 기존 방법론들은 multi-domain image translation task를 수행할 때 효율적이지 못하다.
- k개의 도메인 학습을 위해서는 k(k-1)개의 generators 학습이 필요하다.
- 또한, 모든 도메인의 데이터로부터 학습될 수 있는 global한 feature에 대해 각 generator들은 이를 전부 활용하지 못한다.
- 또한, 서로 다른 데이터셋이 함께 학습될 수 없는데 이는 각 데이터셋이 "partially labeled" 되었기 때문이다 
   -> 어떤 데이터셋에는 happy, angry 등의 라벨링이 되어있고 다른 데이터셋은 black/brwon 등 hair에 대한 라벨링이 되어 있는 경우가 해당된다.

- 기존 방법론들의 문제를 해결하기 위해 본 논문에서는 StarGAN을 제안한다.
- StarGAN은 multi domain의 데이터로부터 학습이 되고 하나의 generator만으로 모든 가능한 도메인간의 맵핑이 가능하다.
- 아이디어느 다음과 같다.
- fixed translation을 학습하는 대신, StarGAN은 image와 domain information에 대한 것을 input을 사용한다.
- 그리고 flexible하게 도메인간 tranlate을 할 수 있도록 학습한다.
- domain information에 대한 정보는 binary 혹은 one-hot-vector와 같이 라벨링하여 나타낸다.
- 또한, domain 라벨에 mask vector를 도입하여 서로 다른 데이터셋이 함께 학습될 수 있는 효과적인 방법을 제안한다.

### Star Generative Adversarial Networks
- StarGAN의 목표는 single generator G를 multiple domains으로 맵핑하기 위해 학습시키는 것이다.
- 이를 위해 generator G는 input image x를 target domain이 c인 output image y로 바꾸도록 학습된다. G(x,c) -> y
- 본 논문에서는 domain label c를 랜덤으로 생성하여 G가 input image에 대해 flexible하게 translate할 수 있도록 한다.
- 또한, auxiliary classifier를 도입하는데, 이는 single discriminator가 multi domain을 컨트롤할 수 있도록 한다.

### Reconstruction loss
- G는 adversarial 과 classification loss를 minimize하도록 학습된다.
- 하지만 기존 loss function은 input image의 content를 잘 보존하는 것을 보장하지는 않는다.
- 이를 해결하기 위해 cycle consistency loss를 도입하였다.

### Training with Multiple Datasets
- StarGAN은 동시에 서로 다른 라벨링 타입을 가진 multiple datasets을 학습할 수 있다.
- 이를 위해 Mask Vector를 도입하였는데, 이 mask vector m은 unspecified labels를 무시하고, explicitly known label에만 집중한다. 
- StarGAN에서는 n-dimensional on-hot vector, m을 사용하였다.

### Conclusion
- StarGAN은 하나의 generator와 discriminator를 사용하여 multi domain에 대한 학습이 가능하도록 한 모델이다.
- 모델의 scalability에서 장점이 있을 뿐만 아니라 StarGAN은 생성된 이미지에 있어서도 다른 모델들과 비교하여 우수한 quality를 보여주었다.


