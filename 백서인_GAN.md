## Generative Adversarial Nets

### Abstract
- adversarial process를 사용하는 새로운 형태의 generative model 제안
- G: generative model, captures the data distribution
- D: discriminative model, estimates the probability that a sample came from the training data rather than G

### Introduction
- 제안하는 adversarial nets framework는 다음과 같음
- discriminative model은 sample 데이터가 model distribution에서 왔는지 혹은 data distribution에서 왔는지를 판단하도록 학습
- generative model은 fake data를 real data처럼 생성하도록 학습
- 제안하는 모델은 backpropagation 과 dropout 알고리즘을 사용하여 학습 가능, approximate inference 나 Markov chains은 필요하지 않음

### Adversarial nets - [참고](https://wegonnamakeit.tistory.com/54)
- 예시) 경찰과 위조지폐범: 
  - 위조지폐범(G)은 가짜 지폐를 진짜 지폐처럼 만들도록 학습
  - 경찰(D)는 실제 지폐와 위조지폐범이 만들어낸 가짜 지폐를 잘 구별해낼 수 있도록 학습

  <img width="736" alt="스크린샷 2021-01-13 오후 7 51 18" src="https://user-images.githubusercontent.com/48814946/104442778-e77da480-55d8-11eb-8ea3-417e6ac38c40.png">
  
- 학습 과정
  - 파란 점선 : discriminator distribution
  - 녹색 선 : generative distribution 
  - 검은색 점선 : data generating distribution

  <img width="736" alt="스크린샷 2021-01-13 오후 7 56 54" src="https://user-images.githubusercontent.com/48814946/104443236-81dde800-55d9-11eb-809c-c43659fae3c3.png">

### Two-player minimax game
- D는 V(D,G)를 maximize
- G는 V(D,G)를 minimize

  <img width="736" alt="스크린샷 2021-01-13 오후 7 58 59" src="https://user-images.githubusercontent.com/48814946/104443483-d41f0900-55d9-11eb-8596-06ebdbcdf657.png">

### Advantages and disadvantages
- advantages: 
  1. Markov chains are never needed, only backprop is used to obtain gradients, 
  2. no inferences is needed during learning
  3. wide variety of functions can be incorporated into the model
  4. computation
  5. can represent very shap, even degenerate distributions
- disadvantages: no explicit expresentation of p_g(x), and D must be synchronized well with G during training
