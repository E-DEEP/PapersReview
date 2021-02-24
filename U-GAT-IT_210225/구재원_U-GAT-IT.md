# U-GAT-IT

Created: Feb 24, 2021 6:56 PM
Created By: 재원 구
Last Edited Time: Feb 24, 2021 10:09 PM
e-deep: E-DEEP

# U-GAT-IT: Unsupervised Generative Attentional Networks with Adaptive Layer-Instance Normalization for Image-to-Image Translation

## 1. Introduction

- attention module과 learnable normalization function을 이용한 새로운 Image-to-Image Translation 방법을 제안한 논문
- 기존의 attention based model들은 도메인의 geometric변화를 잘 handle하지 못하였음. 본 논문에서는 커다란 모양의 변화가 있는 두 이미지에서도 잘 동작함.
- 본 논문에서 제안한 AdaLIN을 사용하면 모양과 texture의 변화 정도를 학습해서 image to Image translation시에 변화 정도를 control할 수 있음.

    ### Problem:

    1. I2I translation을 하려는 두 도메인 간의 shape와 texture의 차이가 크면 translation이 잘 안된다는 한계가 존재함.

        ex. real image → van gogh풍의 이미지로 만드는 task에서는 성능이 잘 나옴  but... 고양이 → 강아지로 만드는 domain의 차이가 있는 task에서는 잘 안됨.

    2. 기존 논문들은 도메인의 특성이 달라지면 모델의 구조를 변경하거나 하이퍼파라미터를 변경해야함. 예를 들어 shape이 본존되어야하는 horse2zebra I2I translation network와 shape이 변경되어야하는 cat2dog의 모델은 달라야함. 

    ### Contribution

    1. attention module을 제안함: translation시에 어느 부분에 초점을 맞춰서 translation시켜야할지 알려주는 역할을 함.

        : generator→ 다른 domain과 차별화되는 부분에 집중

        : discriminator→ target같은 이미지를 generate하는데 중요한 영역에 집중하도록 Generator를 규제하는 역할

    2. adaLN이라는 새로운 normalization기법을 제안함. attention guided model에서도 shape랑 texture을 유연하게 변형해주는 역할을 함.

## 2. Model: Generator

- $x \in {X_s,X_t}$  : sample from source and target domain
- $E_x, G_x:$ Generator는 Encoder와 Decoder로 구성되어 있고, 각각을 의미함
- $\eta_s(x)$: x가 $X_s$에 속할 확률
- $E^k_s(x)$: $E_x$의 k번째 activation map

1. Auxiliary Classifier $\eta_s$는 각 feature map의 weight를 학습함. 즉, source domain의 중요한 부분만을 배우도록 학습함.  

2. AdaIN과 LN의 장점을 결합한 AdaLIN을 사용함 

AdaIN의 장점: 눈코입 등 source domain의 각 요소들의 정보에 강함

LN의 장점: domain의 style 정보에 강함 

 

## 3. Model: Discriminator

- $E_D, G_D:$ discriminator의 encoder와 decoder를 의미함
- $\eta_s(x)$: x가 $X_s$에 속할 확률
- x가 실제 target image인지, generate된 이미지인지 구분하게 학습

    → $C_D(aD_t(x))$

---