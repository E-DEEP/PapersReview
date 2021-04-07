### Variational Autoencoders for Collaborative Filtering

#### Abstract
- variational autoencoders를 implicit feedback을 사용한 협업 필터링에 적용한 연구
- 이러한 non-linear probabilistic model은 여전히 collaborative filtering 연구에서 가장 많은 비율을 차지하고 있는 linear 한 모델의 한계를 뛰어 넘음
- 본 논문에서는 multinomial likelihood를 사용한 generative model을 제시하고 parameter estimation을 위해 Bayesian inference를 사용함
- 본 논문에서는 학습을 하기 위한 새로운 regularization parameter를 제안 -> 이는 competitive performance를 달성하기 위해 매우 중요함을 증명
- 제안하는 방법론이 state-of-the-art 방법론과 비교하여 outperform 함을 확인
- 또한, multinomial likelihood와 다른 likelihood function을 비교하였고 favorable한 결과를 확인

#### Introduction
- 정보의 양이 많아짐에 따라 좋은 추천시스템을 사용하여 사용자가 좋아할만한 아이템을 추천하는 것이 매우 중요해짐
- 협업 필터링(Collaborative filtering)은 추천시스템에서 가장 널리 사용되는 방법론
- Latent factor model이 여전히 collaborative filtering의 많은 부분을 차지하고 있음
- 하지만 이 모델은 linear하다는 한계가 있음
- 한 연구에서는 carefully crafted non-linear feature를 linear lantet model에 추가하는 것이 성능 향상을 가져옴을 확인
- 최근 neural network을 collaborative filtering에 적용하는 것이 매우 promising 한 결과를 가져온다는 연구가 진행됨
- 본 연구에서는 variational autoencoders(VAEs)를 사용한 collaborative filtering을 제안
