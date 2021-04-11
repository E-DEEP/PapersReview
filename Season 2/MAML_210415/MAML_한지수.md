# Model-Agonistic Meta-Learning for Fast Adaptation of Deep Networks

(시험 기간이라(내일 시험..ㅠ) 간단하게만 작성됨. 시험 끝나면 더 자세하게 업로드 예정)

- 모르는 용어 정리
  meta-learning: goal is to train a model on a variety of learning tasks
  model-agnostic: applicable to a variety of different learning problem

## 1. Introduction

    propose a meta-learning algorithm that is general and model-agnostic.
    -> to train the model's initial parameter such that the model has maximal performance on a new task after the parameters have been updated through one or more gradient steps computed with a small amount of data from that new task.

## 2. Model-Agnostic Meta-Learning

### 2.1 Meta-Learning Problem Set-up

Point: train a model that can quickly adapt to a new task using only a few datapoints and training iterations.
-> tha model or learner is trained during a meta-learning phase.
![](https://www.google.com/url?sa=i&url=https%3A%2F%2Fmedium.com%2F%40saketdingliwal97%2Fmodel-agnostic-meta-learning-maml-an-intuitive-way-f7539e043c0b&psig=AOvVaw15vM-b5Zd76Di36cA-_w_v&ust=1618196217726000&source=images&cd=vfe&ved=0CAIQjRxqFwoTCNjB_-eY9e8CFQAAAAAdAAAAABAY)
: Few datapoints를 이용해서 adaptation을 진행한다.

### 2.2 A Model-Agnostic Meta-Learning Algorithm

a model via meta-learning in such a way as to prepare that model for fast adaptation.

![](https://www.cellstrat.com/wp-content/uploads/2020/08/Algo-1.png)
대략적으로 이해한 순서도
1. new task에 맞기 위해 theta loss 를 이용하여 gradient descent 처리한다.
2. theta가 task에 적합한지 loss를 구해서 theta를 업데이트한다.

## 3. Species of MAML

MAML을 supervised learning과 강화학습에 적용해보면 다음과 같다.

### 3.1 Supervised Regression and Classification

![](https://jiminsun.github.io/assets/images/maml.png)
: Datapoint에 대한 업데이트를 진행한다.

### 3.2 Reinforcement Learning

![](https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcRWLJ4y12EsjnmqNTdjd4X2bDsIP5xmYo4ZGmwwqE4vK0UeU6UphNZQUlGnzhXuHk49U3g&usqp=CAU).  

: trajectories 에 대한 수정을 진행한다.

## 5. Experimental Evaluation

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FmjZWY%2FbtqzIzmPV54%2F8Wp3CtYfdJAxDN54QmbRYK%2Fimg.png)
pretrained 된 모델보다 MAML 모델이 더 빠르게 학습되는것을 볼 수 있다. task에 관한 학습을 진행하다보니 1-step 에 따라 굉장히 급격하게 빠르게 변해서 흥미로움.
