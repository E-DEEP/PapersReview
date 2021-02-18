## Self-Attention Generative Adversarial Networks

### Abstract
- 본 논문에서는 Self-Attention Generative Adversarial Networks(SAGAN) 제안
- 이는 이미지 생성 task에서 attention 에 기반하여 long-range dependency modeling 가능
- SAGAN에서는 cues를 사용하여 모든 feature location으로부터 details을 생성
- 또한, discriminator는 이미지의 먼 부분에서 매우 상세한 특징들이 서로 일치하는지 확인 가능
- 최근 연구는 generator conditioning이 GAN의 성능에 영향을 준다는 것을 보임
- 이에 기반하여 본 논문에서는 spectral normalization을 GAN의 generator에 적용하였고, 이것이 training dynamics를 향상시킴을 확인

### Introduction
- Deep convolutional networks에 기반한 GAN 모델이 크게 성공해왔으나, 여전히 몇몇 문제가 남아있음
- convolutional GANs은 multi-class datasets을 학습할 때 다른 모델에 비해 더 어려움이 있음
- 예를 들어, ImageNet GAN 모델은 구조적 제약 조건이 거의 없는 이미지 클래스(예: 기하학보다 질감에 의해 구별되는 해양, 하늘 및 풍경 클래스)를 합성하는 데 뛰어나지만, 일부 클래스에서 지속적으로 발생하는 기하학적 또는 구조 패턴을 포착하지 못함 (예를 들어, 개는 사실적인 질감의 털을 갖고 있지만 명확하게 보이는 발은 없음)
- 이에 대한 하나의 가능성있는 설명으로는 이전의 모델들이 convolution에만 너무 크게 의존하였다는 것임
- convolution operator는 local receptive field를 가지고 있기 때문에, long range dependencies는 오직 몇몇 convolutional layers를 통과한 후에만 처리될 수 있음
- 이는 long tern dependency를 학습하는 것을 방해함
- 반면 Self-Attention은 long-range dependencies와 computational & statistical efficiency 사이에서 더 나은 balance를 보임
- 본 논문에서는 Self-Attention GAN을 제안, 이는 Self-Attention 모듈을 convolutional GANs 에 적용한 것임
- Self-Attention은 convolutions과 보완적이며, long range, multi-level dependencies을 모델링하는데 도움이 됨
- 또한, spectral normalization technique를 generator에 적용하여 generator의 상태를 좋게(?) 함

### Self-Attention Generative Adversarial Networks
- 이미지 생성을 위한 대부분의 GAN 모델은 convolutional layers를 사용하여 구축됨
- Convolution은 local neighborhood 의 information을 처리함
- 하지만 convolutional layer만 사용하는 것은 이미지의 long range dependencies를 모델링 하기 위해서는 computationally inefficient함
- 따라서 Self-Attention을 GAN에 적용한 모델을 제안
- 이는 generator와 discriminator 모두 효과적으로 widely separated spatial regions 간의 관계를 효과적으로 모델링하도록 함
- hinge version의 adversarial loss 사용

![image](https://user-images.githubusercontent.com/48814946/108315835-5c0eb900-71ff-11eb-96ae-1cc11d74f6e3.png)

### Techniques to Stabilize the Training of GANs
- GAN 학습을 안정화하기 위해 두 가지 테크닉 사용
- 첫째, discriminator 뿐만 아니라 generator에도 spectral norm 사용
- 둘째, two timescale update rule(TTUR)이 효과적임을 확인하고, 정규화된 dicriminator의 느린 학습을 해결하기 위해 이를 사용

#### Spectral normalization for both generator and discriminator
- 다른 normaliztion 기법과 비교하여 spectral norm은 추가적인 하이퍼 파라미터 튜닝을 필요로하지 않음
- 또한, computational cost도 상대적으로 낮음
- 본 논문에서는 generator에도 spectral norm을 적용하는 것이 GAN의 성능 향상에 도움이 된다고 주장
- generator의 spectral norm은 매개 변수 크기의 증가를 방지하고 unusual gradient를 방지
- 본 논문에서는 경험적으로 generator와 discriminator에 대한 spectral norm 적용이 discriminator가 generator에 비해 덜 update하도록 하는 것을 발견
- 이는 computatinal cost를 매우 줄임
- 또한 이러한 방법은 학습을 매우 안정적으로 하게 함

### References
https://ml-dnn.tistory.com/7

