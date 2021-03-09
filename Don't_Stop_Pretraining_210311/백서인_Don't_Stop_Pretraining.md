## Don't Stop Pretraining: Adapt Language Models to Domains and Tasks

### Abstract
- target task의 도메인 데이터로 모델을 추가 pretraining하는 것이 효과적인지 확인하고자 함
- 4개의 도메인(biomedical, computer science publication, news, reviews)
- domain에 대한 second phase pretraining(domain-adaptive pretraining)이 high-and low resource settings에서 모두 performance gains을 가져옴을 확인 
- 또한, task의 unlabeling된 data로 pretraining(task-adaptive pretraining)을 하는 것도 성능을 향상시킴을 확인
- 마지막으로 domain-adaptive pretraining을 위한 데이터가 없을 때, 사용할 수 있는 simple data selection strategies를 제안

### Introduction
- 가장 최신 large pretrained model이 universally 잘 동작하는가? 혹은 특정 도메인에 대해 pretraining을 더 진행하는게 도움이 되는가?
- RoBERT_A 모델을 baseline으로 잡고 실험 진행
- Contributions
  - domain- and task- adaptive pretraining 진행
  - adapted LMs에 대해 cross domain 성능 확인
  - pretraining과 human-curated datasets의 중요성 확인, simple data selection strategy 제안

### Domain-Adaptive Pretraining
- RoBERT_A를 domain-specific text로 계속 pretraining함
- target domain과 RoBERT_A의 pretraining domain간 덜 similar할수록 DAPT의 효과가 클것으로 예상
- Experiment Results
  - 모든 domain에서 DAPT가 성능을 향상시킴
  - 관련없는 domain 데이터로 DAPT를 적용한 모델 (마지막 컬럼) -> 성능 저하, domain-relevant data로 학습하는 것이 중요함을 확인 
  ![image](https://user-images.githubusercontent.com/48814946/110467055-e15bfe00-8119-11eb-93fd-cefb983eb50d.png)
  
### Task-Adaptive Pretraining
- TAPT는 DAPT보다 더 narrowly-defined된 dataset 사용
- 실험 결과 TAPT이 less expensive하지만 종종 DAPT보다 outperform함을 확인
- Experiments Results
  - 모든 task에서 RoBERT_A보다 성능 향상
  ![image](https://user-images.githubusercontent.com/48814946/110467779-c50c9100-811a-11eb-922e-b40464d11e8c.png)
  
### Combined DAPT and TAPT
- Table5에서와 같이 DAPT와 TAPT를 combine하는 것이 모든 task에서 가장 좋은 성능을 보임
- DAPT followed by TAPT가 좋은 성능

### Cross-Task Transfer
- 동일 domain의 다른 task 에 대해 성능 평가(Transfer-TAPT)
- 실험결과 TAPT는 single task performance에 최적화됨을 확인(즉, 성능 저하됨)
![image](https://user-images.githubusercontent.com/48814946/110468548-cbe7d380-811b-11eb-82e0-603908524038.png)

### Augumenting Training Data for Task-Adaptive Pretraining
- TAPT의 성공에 영감을 받아 존재하는 task distribution으로부터 unlabeled data의 large pool을 가진 새로운 setting을 확인해봄(?)
- 첫째, 3개의 task(RCT, HYPERPARTISAN, and IMDB)에 대해 human-curated corpus의 unlabeled data로부터 생성된 large pool 사용(Human Cruated-TAPT)
- 둘째, extra human-curated data가 없을 때, 도메인의 large unlabeled data로부터 연관있는 unlabeled data를 추출하는 방법 제안(Automated Data Selection for TAPT)

#### Human Curated-TAPT
- target task의 labeled data와 같은 distribution의 unlabeled target task corpus (by human)
- 모든 task에서 DAPT 후 Curated-TAPT을 적용한 경우 가장 좋은 성능을 보임
![image](https://user-images.githubusercontent.com/48814946/110470745-94c6f180-811e-11eb-9ad5-4ffa2a022f71.png)

#### Automated Data Selection for TAPT
- task setup 당시에 large unlabeled corpus 조차 풀리지 않는 경우, 자동으로 관련있는 데이터를 찾아서 이를 기반으로 학습하게 되면 어떨지를 실험
- large in-domain corpus로부터 관련있는 unlabeled text를 추출하는 간단한 unsupervised 방법
  - nearest neighbors selection (knn-TAPT)
  - random (RAND-TAPT)
- Results
  - 모든 경우에서 knn-TAPT이 TAPT보다 outperform함을 확인
  - RAND-TAPT은 일반적으로 knn-TAPT보다 성능이 낮음
  - knn-TAPT에서 k의 수를 증가시킬수록 성능도 비례하여 향상됨
  ![image](https://user-images.githubusercontent.com/48814946/110471465-7f9e9280-811f-11eb-8dec-c773ea53d9a2.png)

### References
- https://inmoonlight.github.io/2020/11/30/Don-t-stop-pretraining/
