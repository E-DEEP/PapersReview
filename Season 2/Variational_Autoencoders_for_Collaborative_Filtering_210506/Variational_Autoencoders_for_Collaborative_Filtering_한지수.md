# Variational Autoencoders for Collaborative Filtering
작성자: 한지수
#Multi-VAE #RecommenderSystem #GenerativeModel
### 용어 정리
- Variational Autoencoder: 데이터의 확률분포를 학습하기 위해 latent variable z를 생성하는 Autoencoder 모델    
<img src="https://i.imgur.com/PhHb2aF.jpg" width = "300">
- Multinomial variable다항 변수: binomial distribution이항 분포에서 확장하여 여러 개의 class 중 분류하는 문제
- Bayesian inference베이즈 추론: 추론 대상의 사전 확률(p(\theta))와 관계된 관측 X의 확률 분포를 통해 해당 대상의 사후 확률(p(\theta | X))를 구하기는 과정
<img src = "https://wikimedia.org/api/rest_v1/media/math/render/svg/a9deb72fb98d404146f831e7ba2aa03100cbfc8e" width = "250">  
- Collaborative filtering: similarity pattern -> 뭘 이용할지 predict

## Introduction 
- Recommender Systems 기존의 방식
    - Collaborative Filtering: inherently linear, limits their modeling capacity >> explore non-linear probabilistic latent-variable model
    - Top-N ranking loss: difficult to optimize directly 

Neural Generative model with multinomial likelihood and use Bayesian inference for parameter estimation  

## Method 
### 1. Model
- Generative Process
Deep latent Gaussian Model -> Multinomial likelihood

### 2. Variational Inference
to estimate \theta, need to approximate the intractable posterior distribution p(z_u|x_u) -> Use Variational inference
- Amortized inference and the variational autoencoder
  : replace individual variational parameters with a data-dependent function(inference model)
- Alternative interpretation of ELBO
   : ELBO(the evidence lower bound) 의 수식을 통해 loss를 최소화 -> KL term의 trade-off를 조절하기 위해 파라미터 beta 도입: KL annealing   
  
**Multi-VAE** : multinomial likelihood 기반의 partially regularized 된 VAE
### 3. A taxonomy of autoencoders
일반 AutoEncoder와 Denoising Autoencoder, Variational Autoencoder와 이번에 기술된 Multinomial VAE, DAE 비교
<img src = "https://user-images.githubusercontent.com/47301926/115750988-b1b83b00-a3d3-11eb-812c-744721cf5b11.png" width="300">
DAE는 AE에서 dropout을 적용하여 overfitting을 방지하였는데, Multi-VAE에 이어 DAE를 적용하여 Multinomial Likelihood기반의 DAE인 Mult-DAE 도 있다.

추천시스템쪽 논문은 처음이라 읽는데 통계 분포 관련하여 어려움이 있었다.완전히 흡수 하지 못해서 방학 때 다시 볼 것 같다. 어려운 논문 만나서 좋다!
