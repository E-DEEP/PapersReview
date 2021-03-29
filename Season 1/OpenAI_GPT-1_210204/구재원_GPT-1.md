# GPT-1

Improving Language Understanding by Generative Pre-training

## Introduction

- Labeling된 텍스트 데이터를 사용해 학습을 하기에는 데이터를 구하기 힘들다보니 제약이 존재한다. 즉, 학습에 사용된 데이터와 다른 데이터가 들어온다면 모델의 정확도가 떨어지는 문제가 발생한다. 따라서 labeling이 되지 않은 데이터를 가지고 비지도학습 방법으로 학습을 시키는 것이 지도학습 방법에 대한 의존성을 낮출뿐만 아니라 더 좋은 성능을 보여주기도 한다. (ex. Pretrained word embedding)
- unlable이 된 text을 이용해 word-level 이상의 정보를 얻기에는 다음과 같은 문제들이 존재한다.
    1. 어떤 optimization objective가 transfer에 효과적인 text representation을 학습하는지 모른다.  
    2. 학습된 representation들을 target task에  transfer(전이)시킬 수 있는 가장 효과적인 방법이 없다. 
- 해당 논문에서는 unsupervised pre-training과 supervised fine-tuning을 이용한 semi-supervised방식의 language understanding task를 제안하였다.

         → training은 2 단계로 되어있음

               1. (pre-training) large corpus of unlabeled text을 사용하여 먼저 학습

         2. (fine-tuning) 학습된 parameter를 target task에 알맞는 loss function과 데이터를 사      

              용하여 학습

    → Transformer를 사용하였음

    → transfer 시에 pretrained된 model의 구조를 최소로 바꾸어 학습하는 task-specific input adaptation 사용함. 이 방법은 traversal-style 접근법에서 나온 방법으로 text input을 하나의 contiguous sequence of token으로 받음. 

## Framework

1. Unsupervised pre-training 
    - 다음과 같이 token이 주어졌다고 하면,
    
        ![Untitled](https://user-images.githubusercontent.com/34685762/106765863-9bc09700-667c-11eb-912e-c764d6b1db3e.png)

        목적함수는 다음과 같다. 

        ![Untitled 2](https://user-images.githubusercontent.com/34685762/106765824-8e0b1180-667c-11eb-807f-ffae0e4bcca6.png)

        : 주어진 토큰이 $u_i$일때, i번째 이전까지의 token을 보며 그 다음에 올 토큰인 $u_i$을 예측하도록하는 목적함수이다. 

    - transformer decoder를 사용한다. (이것이 BERT와의 차이점!! BERT는 encoder를 사용하지만, GPT는 decoder만을 사용)

         deconder를 여러개 이어서 사용한다. 

        ![Untitled 1](https://user-images.githubusercontent.com/34685762/106765663-6320bd80-667c-11eb-9a93-86783d1e6489.png)

        이와 같이 처음 $h_0$는 context vector token U와 token embedding matrix $W_e$와 position embedding matrix $W_p$로 만들어지고, 이것이 transformer decoder block에 들어감. 마지막 decoder에서 나온 값은 softmax로 다음 token 예측

2. Supervised finetuning
    - 마지막 token을 넣었을 때 마지막 hidden layer의 output 값에 softmax를 적용해 확률을 계산한다. 이 확률이 최대가 되도록 학습한다.
    - finetuning시에 pretraining에 쓰인 목적함수를 같이 사용하여 학습 시키면 더 성능이 향상됨
