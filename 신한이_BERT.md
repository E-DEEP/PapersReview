BERT 리뷰
------------
## 0. Abstract 

BERT라는 새로운 언어 표현 모델은 최근의 언어 모델과는 달리,
양방향 표현을 사전 학습한다. (Pre-train deep two-way representations in) 
결과적으로, 사전 훈련된 BERT 모델은 출력 계층을 하나만 추가하는 것으로도 미세하게 조정할 수 있어서 다양한 분야의 최첨단 모델을 만들 수 있다. 
-> 모든 자연어 처리 분야에서 좋은 성능을 내는 범용 language model이다.
BERT는 개념적으로, 경험적으로도 단순하다. 

## 1. Introduction

언어 모델의 사전 교육은 많은 자연어 개선에 효과적이다. 
다운스트림 작업에 사전 훈련된 언어 표현을 적용하기 위한 기존의 방법은 2가지:
feature-based(ex.ELMo)과 fine-tuning(ex.GPT)이다.
이 두 가지 방식은 단방향 언어 모델을 사용한다.
토큰 수준 작업에서 미세 조정 접근 방식을 적용할 경우 비효율적이다. 
본 논문은 미세 조정 방식을 기반으로 개선한다. 
양방향 트랜스포머를 사전 학습할 수 있다.
masked 언어 모델을 사용하고, 최초의 미세 조정 기반 표현을 달성한 모델이다

## 2. Related works

### 2.1 Unsupervised Feature-based Approaches
feature-based는 특정 task를 해결하기 위한 architecture를 구성하며, 
pre-trained representations를 부가적인 feature로 사용한다.

### 2.2 Unsupervised Fine-tuning Approaches
Fine-tuning approach는 어떤 특정 task에 대해 parameter를 최소화하여 범용적인 pre-trained 모델을 사용한다. 
특정 task를 수행할 때 그에 맞추어 fine-tuning을 한다. 

## 3. BERT
대용량의 unlabeled data로 사전 학습을 하고 transfer learning을 한다. 
2가지의 unsupervised learning을 통해 사전학습한다. 
BERT는 Masked Language Model(MLM)을 활용하여 이전과 이후 정보를 모두 활용하여 양방향 사전학습을 하는 모델이다. 
Next Sentence Prediction(NSP) 학습 방법을 통해 문장 간의 연관성을 사전학습한다. 
transformer의 구조 중 인코더만을 사용했다.

### 3.1 Pre-training BERT
unlabeled data사용
기존의 모델은 left to right 또는 right to left로 단방향으로 사전 학습을 했다. 
예시로 GPT는 left to right transformer를 사용했고, ELMo는 독립적인 단방향 LSTM을 결합하여 사용했다. 
BERT는 모든 레이어에서 bidirectional한 방식으로 학습한다. 


#### MLM) 
deep한 양방향 표현을 학습하기 위해 input 토큰의 일부분을 랜덤하게 마스킹한다. 
mask 토큰의 마지막 hidden vector는 softmax로 들어가 어떤 단어를 출력하는 방식이다. 
Mask 토큰은 랜덤하게 만들어지고 비율은 전체의 15%이다. 
문장 전체를 예측하지 않고 masking된 단어만 예측하는 token-level 학습이다.

> + 80%: ex. My dog is hairy -> my dog is [mask] 
> + 10%: ex. My dog is hairy -> my dog is apple
> + 10%: ex. My dog is hairy -> my dog is hairy

MLM을 통해 사전학습된 BERT는 문맥 정보를 활용할 수 있는 모델이 된다.

#### NSP)
BERT는 두 문장이 연결되어 있던 문장인지 예측하는 NLP를 학습하여 문장 간의 relationship을 파악할 수 있도록 사전학습한다. 
> + 50%: 연결된 문장 
> + 50%: 랜덤하게 뽑힌 문장

토큰에 대해 WordPiece Embedding을 한다.
두 개의 쌍 문장이 하나의 시퀀스를 이룬다. 


### 3.2 Fine-tuning BET
특정 task에 대해 labeled supervised learning
pre-training과 거의 동일한 하이퍼파라미터를 가진다. 
Batch size와 learning rate, epoch이 다르고 dropout은항상 0.1 이다.
분류 문제에서 CLS 토큰이 사용된다. 
QA task는 SEQ 이후에서 start/end span을 찾도록 한다. 

## 6. Conclusion 

BERT는 범용적인 언어모델을 만들기 위해 unlabeled data를 활용해 사전 학습했다. 
특정 task에 대한 fine-tuning을 했고 11개의 task에서 sota를 달성했다. 
특정 task에 맞추어 tuning하는 것이 중요.
