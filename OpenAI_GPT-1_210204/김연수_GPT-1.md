# GPT-1 : Improving Language Understanding by Generative Pre-Training

## Abstract


> We demonstrate that large gains on these tasks can be realized by **generative pre-training of a language model on a diverse corpus of unlabeled text**, followed by discriminative fine-tuning on each specific task. In contrast to previous approaches, we make **use of task-aware input transformations during fine-tuning to achieve effective transfer while requiring minimal changes to the model architecture.**


- LM을 다양하고 unlabeled text를 통해서 `generative pre-training` 시킨 후 `fine-tuning` 하는 방법이 큰 성능 향상을 이룸.
- fine-tuning 과정에서 모델은 최소한으로 변경, task-aware input transformations을 만들어 사용


## Introduction

- 이때까지의 연구들은 상당한 labeled data가 필요했고 이에 따라 applicability에 제약이 생김
- Information from Unlabeled text은 훌륭한 대안임.
    - 하지만 2가지 한계점이 있음.
    - 1.First, it is unclear what type of optimization objectives are most effective at learning text representations that are useful for transfer. ==> 어떤 text representations를 배우는 것이 transfer에 유용한 것인지 알 수 있는 목적함수가 분명하지 않음.
    - 2.Second, there is no consensus on the most effective way to transfer these learned representations to the target task. ==> 타겟으로 하는 태스크로 transfer 시키는 효과적인 방법에 대하여 일치된 의견이 없음.
- 본 논문에서는 `unsupervised pre-training`과 `supervised fine-tuning`을 사용하는 semi-supervised approach를 사용한다.
    - learn universal representation => adaptation to a wide range of tasks
- 2-stage training procedure
    - 1.뉴럴넷의 초기 파라미터를 학습하기 위해 unlabeled data에 대한 LM 목적 함수를 사용한다
    - 2.1번에서 학습한 파라미터를 가지고 타겟 태스크에 적용한다.

- transformer 사용


## Framework

1. learning a high-capacity language model on a large corpus of text
2. fine-tuning with labeled data


### Unsupervised pre-training

U = {u1, u2, ..., un}이 주어졌을 때 아래와 같은 Likelihood를 최대화하는 LM objective를 사용한다.

<img width="341" alt="스크린샷 2021-02-03 오후 11 35 28" src="https://user-images.githubusercontent.com/48315997/106761759-80538d00-6678-11eb-8a96-a5f511b967c4.png">
- k : size of the context window
- P : model param.


LM에는 Transformer를 살짝 변형한 multi-layer Transformer decoder를 사용함.
- input context token에 multi-headed self-attention 적용
- target token의 output distribution을 만들기 위해 position-wise feedforward layer

<img width="397" alt="스크린샷 2021-02-03 오후 11 37 36" src="https://user-images.githubusercontent.com/48315997/106762059-cd376380-6678-11eb-9aa6-a07cfd6d6e59.png">


### Supervised fine-tuning

위의 과정이 끝나면 target task에 맞춰 fine-tuning 한다.

<img width="542" alt="스크린샷 2021-02-03 오후 11 40 27" src="https://user-images.githubusercontent.com/48315997/106762460-3323eb00-6679-11eb-9f3c-7ee0cd6d49b0.png">

- C : labeled dataset
- x_1, ..., x_m : sequence of input tokens
- y : label
- 인풋 데이터는 pre-trained model에 넣어지고, transformer의 마지막 블록 activation 결과값은 y를 예측하기위해 linear output layer로 전달된다.



> We additionally found that including language modeling as an auxiliary objective to the fine-tuning helped learning by **(a) improving generalization of the supervised model, and (b) accelerating convergence.**

- Generalization
- 수렴 가속화



### Task-specific input transformers

![image](https://user-images.githubusercontent.com/48315997/106763293-03c1ae00-667a-11eb-8a22-afbcd8472ec0.png)

fine-tuning할 수 있는 target tasks
- Text Classification
- Textual entailment
- Similarity
- QA
