# ALBERT REVIEW

## Main Idea
- previous limitations : 모델이 매우 heavy --> memory limitation, long training time
- Main Idea
  - Factorized embedding parameterization : 인풋 레이어의 파라미터 수를 줄임으로써 모델의 크기를 줄였음.
  - Cross-layer parameter sharing : 트랜스포머에서 각 레이어 간 같은 파라미터를 사용함
  - Sentence order prediction : NSP대신 두 문장을 바꿔 맞추는 방식 사용함

## Experiments
- Training time : xlarge의 경우 BERT보다 2배 빠름
- Model degradation
  - BERT의 경우 커질 경우 성능이 더 떨어졌었는데, ALBERT는 커질 떄 성능이 더 높아졌음. --> various downstream task에 가능할 것이다.


