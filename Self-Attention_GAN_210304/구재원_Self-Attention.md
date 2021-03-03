# Self-Attention Generative Adversarial Networks

##1. Introduction

- Problem:  
  
    Image synthesis 분야는 GAN을 주축으로 활발하게 연구가 되었다. 하지만 아직 문제점들이 남아있는데, 그 중 multi-class dataset을 conditional GAN에 
  학습한 경우 특정 image class에 동작이 잘 안되는 것을 발견함.  주로 structual constraint가 적은 데이터에 대해서 더 잘 이미지를 생성하는 경향이 
  존재함. 하늘, 바다와 같이 texture에 더 의존하는 데이터에서 훨씬 더 잘 작동함. 이러한 이유는 기존의 GAN은 convolution을 사용하였고, convolution은
  receptive field가 local하기에  long range dependency가 잘 고려되지 않았기 때문임. 
  
- Contribution:
    
    본 논문에서는 문제점을 해결하기 위해 self-attention을 GAN에 적용하였음. self-attention은 long range, multi-level dependency에 효과적이라는 특징이
  있음. Self-attention을 사용함으로 generator는 커다란 영역에 걸쳐서 나타나는 상세한 디테일을 가진 이미지를 생성해낼 수 있음. Discriminator 또한
  이미지 전체적으로 나타나는 복잡한 geometric 구조들을 정확하게 판별할 수 있게됨. 추가적으로 spectral normalization technique를 discriminator
  뿐만 아니라 generator에도 적용하였음.
                
  
##2. Self-Attention Generative Adversarial Networks 
: 어떻게 Self-Attention을 GAN에 적용하였는지에 대한 설명

[ 모델 사진]

- x: 이전 hidden layer에서 온 image feature

- x 를 attention 계산을 위해 feature space f와 g로 바뀜. (각자 1x1 convolution을 해준 것과 동일)
- s_ij: f와 g의 similarity 게산한 것
- b_i,j: j번째 region에 synthesizing할 때 i번째 region에 집중하는 정도, 즉 j번째 region과 i번째 region이 상관하는 정도를 나타냄
- o_j: 최종 attention layer의 output, j번째 region이 전체 region과 관계있는 정도 




##3. Techniques to Stabilize the Training of GANS
: GAN학습 시에 안정화시키기위한 두가지 방법을 제안함

1. spectral normalization을 generator와 dicriminator 모두에 적용함 
2. TTUR이 효과적이라는 것을 입증하여 discriminator에 적용함 


