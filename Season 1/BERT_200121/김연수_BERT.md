# BERT : Pre-training of Deep Bidirectional Transformers for Language Understanding

## Abstract

```
BERT is designed to pre-train deep bidirectional representations from unlabeled text by jointly conditioning on both left and right context in all layers.
```
- **bidirectional**이라는 점이 이전 연구들과의 가장 큰 차이점이다.

```
As a result, the pre-trained BERT model can be fine-tuned with just one additional output layer to create SOTA ~...
```
- fine-tuning이 매우 용이한 모델이다


여러 가지 task에서 SOTA를 기록했음.


## Introduction

Applying pre-trained language representations to downstream tasks

1. `feature-based`
    - ELMo
2. `fine-tuning`
    - GPT

### Limitation of Unidirectional approach

두 접근법 모두 **unidirectional** 방법을 사용함.
- But, unidirectional 방법은 한계점이 뚜렷함. 
- GPT(1)처럼 left-to-right architecture를 사용하게 되면, 모든 token들은 이전의 token들에만 self-attention할 수 있게 된다.
    - 이는 token-level task (예를 들어 QA)들에 부적합하다. 이러한 태스크들은 양 방향을 봐서 **context**를 읽어야하기 때문.

### Masked Langauge Model(MLM)

BERT에서는 pre-training 할 때, 위에서 언급한 unidirectionality constraint를 `Masked Language Model(MLM)` 으로 완화(?)했음.

- MLM은 input 토큰들로부터 **randomly mask**함.
    - 이것의 목적은, **오직 context만 가지고** mask된 original vocab.을 맞추는 것.
- Left-to-right LM pre-training과 달리, MLM의 목적 함수는 양 방향의 representations를 이해할 수 있다.
    ```
    which allows us to pre-train a deep bidirectional Transformer
    ```

### Next Sentence Prediction

jointly pre-train text-pair representation


## BERT

<img width="766" alt="스크린샷 2021-01-25 오후 9 09 09" src="https://user-images.githubusercontent.com/48315997/105704076-91eab580-5f51-11eb-8b52-16c48146bd50.png">


### Pre-training

```
During pre-training, the model is trained on unlabeled data over different pre-training tasks.
```

Pre-training 단계에서 모델은 **unlabeled data**를 가지고 학습된다.



**TWO Unsupervised task를 사용함.**


#### Task #1. Masked LM

deep bidirectional representation을 학습시키기 위해, input token에서 랜덤으로 몇 %정도를 masking하고 이 masked token들을 predict하도록 학습시킴.

|[MASK]|Random|Unchanged|
|:---:|:---:|:---:|
|80%|10%|10%|


#### Task #2. Next Sentence Prediction (NSP)

**두 문장 사이의 관계(relationship)를 이해**하기 위해 학습하는 task이다.

`IsNext`인지 아닌지(`NotNext`) 분류하는 binary classification 문제임.



### Fine-tuning BERT

적절한 Input과 Output을 넣으면, single text나 text pair 모두 가능.


```
BERT instead uses the self-attention mechanism to unify these two stages, as encoding a concatenated text pair with self-attention effectively includes bidirectional cross attention between two sentences.
```

