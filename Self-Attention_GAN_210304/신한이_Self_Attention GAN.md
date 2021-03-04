## Self Attention GAN
-------------------------

## 0. Abstract 
Self-Attention Generative Adversarial Networks(SAGAN)제안.
SAGAN은 모든 특징의 위치로부터 힌트를 사용하여 데이터를 생성.
Spectral Normalization(sn)을 GAN의 generator에 적용하였꼬, 이것이 traning dynamics를 향상

## 1. Introduction 
### 1.1. GAN
- GAN기반의 모델은 성공적이었지만 일부 클래스에 문제가 있다. 
    > geometry가 필요하지 않은 조건(ex.텍스처 위주)은 잘 생성하지만, geometry가 중요한 클래스에서 한계가 있음
    > geometric or structural patterns 표현 부족.
    > 동물의 texture는 실제 같음.

한계의 이유)
- GAN에서는 이미지의 다양한 위치에 대한 dependency를 모델링하기 위해 convolution에 많이 의지한다.
- 이때, convolution의 receptive field의 범위는 local로 제한.
- 거리가 먼 region 사이의 dependency 모델은 convolution layer를 몇 차례 거친 후에 가능
  > 이러한 점에서 leng-term dependency를 학습하는 데 어려움이 있다. 

> - small model: not able to represnet dependency
> - optimzation: 여러 conv layer를 거쳐 dependency를 잘 표현하는 parameter를 찾아내는데 어려움이 있을 수 있다.
> - paraemterization이 통계적으로 기반이 약하고, unseen input에 대해서 성능이 떨어질 수 있다.
> - kerenl size 증가: 모델의 capacity가 커져서 dependency를 표현하기 쉬워질 수 있지만, convolution의 근본 목적인 계산량 감소와 통계적 효율성을 포기 해야한다.

### 1.2. Self-Attention
long range dependency와 computational cost 사이의 balance가 좋음
self attention 모듈은 적은 코스트로 각 픽셀의 전체 영역에 대한 attention vector를 만들 수 있다.

### 1.3. Propose
> Self-Attention Generative Adversarial Networks)
- self attention 메카니즘을 기존 GAN에 도입함
- self attention 기존 convolution의 단점을 보완할 수 있고, 이미지에서 긴 범위와 여러 단계의 의존에 대해 모델링 할 수 있음

> GAN의 성능 향상과 관련된 최근의 insight 도입)
- spectral normalization technique
- 기존에는 discrimnator에만 도입되었지만 generator에 도입함


## 3. SAGAN

대부분의 이미지 생성을 위한 GAN model은 conv layer가 존재
conv layer는 local neighbor에 대한 정보를 제공
따라서 conv 만으로 long-range dependency를 모델링 하는 것은 비효율적

=> Self-Attention을 GAN에 적용한 모델을 제안, non-local network 모델을 이용할 것




## 4. Techniques to stabilize GAN training
1)spectral normalization,
2)two-timescale update rule

### 4.1. Spectral normalization for both generator and discriminator
constrains the Lipschitz constant of the discriminator by restricting the spectral norm of each layer
Spectral normalization 기법을 generator에도 적용
Generator에 적용했을 경우 emprical하게 잘 됨을 확인

### 4.2. Imbalanced learning rate for generator and discriminator updates
regularized discriminator는 학습이 느림
TTUR(Two time-scale update rule for training GAN)을 사용
Wall clock time 기준으로 더 나은 효율을 보임
