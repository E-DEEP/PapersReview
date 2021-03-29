# Don't Stop Pretraining: Adapt Language Models to Domains and Tasks

### Abstract & Introduction

- Trend : 대용량 텍스트로부터 Pre-trained된 LM에 Fine-tuning 하여 다양한 도메인/태스크에 적용했었음
  - 이 방식이 정말 다양한 태스크에 잘 적용되는 게 맞나? Fine-tuning할 때의 도메인 간극이 성능을 하락시키는 것은 아닌가?
- DAPT : Domain-Adaptive Pretraining
- TAPT : Task-Adaptive Pretraining

## DAPT

- 이미 Pre-trained된 RoBERTa를 다양한 Target Domain(Biomed, CS, News, Reviews)에 해당되는 충분한 코퍼스로 이어서 Pretraining을 함.

  ![image](https://user-images.githubusercontent.com/48315997/110636251-649b5380-81ef-11eb-949e-546873653433.png)

  - RoBERTa vs. DAPT : NEWS 도메인 제외 더 좋아짐

- ![image](https://user-images.githubusercontent.com/48315997/110636832-1470c100-81f0-11eb-85fb-44ecb1f94add.png)
  - DAPT -> Fine-tuning 했을 때 / DAPT -> 관련 없는 도메인에 Fine-tuning 했을 때
  - 결과 : 대다수는 DAPT 적용한 게 좋았지만, 관련없는 도메인에 대해서는 떨어지기도 했음. 
    - 즉, 많은 데이터를 학습시키는 것보다는 Domain Relevance가 더 중요하다.



## TAPT

- Pre-trained 이후 태스크에 해당하는 데이터를 pre-training에 활용함
  - 데이터 수는 DAPT보다 적지만, task-relevant한 학습 가능
- ![image](https://user-images.githubusercontent.com/48315997/110639748-69620680-81f3-11eb-9dba-70800bb6687d.png)
  - DAPT + TAPT 하는 것이 가장 성능이 좋음

### Cross-Task Transfer

- 같은 도메인에 서로 다른 task에 대해서도 TAPT 실험을 했을 때 성능이 하락하게 됨.
  - ![image](https://user-images.githubusercontent.com/48315997/110640262-f311d400-81f3-11eb-8c99-df5256ccf302.png)
  - 같인 도메인이더라도 각각이 다른 distribution을 가지기 때문에...



## Augmenting Training Data for TAPT

- KNN을 사용해 DAPT 데이터를 select
- 

