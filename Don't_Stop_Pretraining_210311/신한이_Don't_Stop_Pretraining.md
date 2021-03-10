
# Don't Stop Pretraining: Adapt Language Models to Domains and Tasks(ACL2020)

--------------------------

## 0.Abstract

+ 오늘날의 NLP 모델은 수많은 다양한 소스로부터 사전학습됨. ex)BERT, GPT

+ 모델이 사전학습 과정에서 훈련되는 텍스트의 도메인과,  
실제 task를 수행하기 위한 fine-tuning 과정에서 훈련되는 텍스트의 도메인 사이에
어느 정도의 차이(discrepency)가 발생한다. 

=> 이러한 도메인 사이의 간극에 의해 성능 하락이 존재하는 지 검증하고 이 문제를 해결할 수 있는 방법을 조사한다.

## 1.Introduction

+ 본 논문에서 RoBERTa를 베이스라인 모델로 함
+ 4개의 서로 다른 도메인: Biomedical, CS, News, Review에 대해 8개의 classification task를 설정.

![figure1](https://user-images.githubusercontent.com/50253860/110647651-581cf800-81fb-11eb-864a-ad9a8175a2e6.png)

=> 결론: 사전학습된 RoBERTa가 아직 세부적인 downstream task의 도메인을 잘 표현하지 못한다.
=> 이에 대해 DAPT, TAPT 방법을 제안. 상대적으로 크기가 작은 TAPT의 단점을 보완하기 위한 데이터 수집 방법을 제안.

## 2.Background: Pretraining

+ NLP에서 Trasfer Learning의 과정:
 대규모의 Unlabeled Text data로부터 pre-training 뒤, 
 실제 목표로 하는 downstream task에 대해 fine-tuning 한다.

> 그러하면, 사전학습된 모델을 특정 task에 바로 fine-tuning하는 것이 충분한가?
=> 해당 데이터셋의 문장들로 사전 학습을 진행하면 성능 향상이 있다고 함.

사전 학습된 언어모델은 모든 도메인에 대해 범용적으로 돌아간다.
목표로 하는 task에 대한 domain의 데이터를 활용하여, 
> 추가적으로 사전학습할 경우, 과연 성능이 향상될까?

## 3.Domain-Adaptive Pretraining(DAPT)

+ 논문에서 첫 번째로 실험하는 내용: DAPT
사전 학습된 RoBERTa를 목표하는 downstream task의 도메인에 해당하는
대량의 데이터로 한 번 더 사전학습을 하는 방법이다. 

+ 실험 테이블) 
RoBERTa를 바로 평가한 지표보다, DAPT를 거친 후 평가가 더 낮은 loss를 보인다.
=> DAPT의 유효성 검증

![figure2](https://user-images.githubusercontent.com/50253860/110648871-720b0a80-81fc-11eb-8bf6-6d3034f23e43.png)

+ figure)
사전학습과 각 도메인 데이터 사이의 frequent unigram 분포.
사전학습 과정에서 BioMed나 CS 도메인을 자주 만나지 못함.
=> 따라서 더욱 좋은 성능 향상을 기대

+ 각 task에 대해 다음 두 과정을 비교.
RoBERTa 사전학습 -> 바로 finetuning
RoBERTa 사전학습 -> DAPT -> 바로 finetuning
=> 대부분의 task에서 DAPT를 거친 모델이 좋은 성능을 보였다. (특히 BioMed, CS)

=> 단순히 많은 데이터에 노출(x)
도메인과 연관성 많은 데이터 노출(o)이 성능 향상에 더 좋았다.


## 4.Task-Adaptive Pretraining(TAPT)

+ DAPT: 별다른 레이블 데이터가 필요하지 않음. 성능 향상이 가능.
단, 데이터가 많아 소스와 계산량이 많이 필요.
=> 이에 대체하기 위해 downstream task의 데이터셋을 활용한 사전학습 실험.
DAPT보다 작은 크기+쉽고+빠름+task와 직접적인 연관성 있음.

+ TAPT에서도 성능 향상이 존재.
DAPT를 거친후 TAPT 를 거치는 것이 성능에 좋았다.

+ 같은 도메인 내의 서로 다른 task 데이터 사이의 TAPT에 대해서 실험
->성능 하락.
-> 같은 도메인의 task더라도 각각 너무 좁은 분포를 가지기 때문에
효과를 보지 못했다.



## 5.Augmenting Training Data for Task-Adaptive Pretraining

+ TAPT는 상대적으로 적은 데이터 크기로도 어느 정도 좋은 성능을 보임.
큰 크기의 도메인 데이터 중,
task와 관련이 높은 데이터에 대해 사전학습을 진행하면,
=> 연산량을 줄이고+더 좋은 성능을 보일 것이라 기대

+ TAPT에 사용된 데이터와 가까이 있는 DAPT 데이터를
k-NN 알고리즘에 돌려 사전 학습에 사용하는 방법을 제안
실제 target task와 가까운 공간에 있는 domain data를 많이 사용할수록 높은 성능.



## 7. Conclusion

+ 사전학습에 한계: 
범용적인 활용은 가능하지만 모든 도메인에 대한 적용은 어렵다.
> task와 관련된 도메인의 데이터로도 성능 향상이 가능.

