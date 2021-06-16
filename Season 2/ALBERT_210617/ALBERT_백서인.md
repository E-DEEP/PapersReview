ALBERT: A Lite BERT For Self-Supervised Learning Of Language Representations

## Abstract
- 모델 사이즈를 증가시키느 것은 downstream task에서의 성능 개선 결과를 가져옴
- 하지만 모델 사이즈를 증가시킬수로 GPU/TPU 메모리가 부족해지고, 학습 시간도 더 오래걸림
- 이러한 문제를 해결하기 위해, 2가지의 parameter reduction techniques 제안
- 또한, inter-sentence coherence를 모델링하는데 집중하는 self-supervised loss를 사용하고, 이는 multi-sentence inputs이 있는downstream task에서 도움이 됨을 확인
- 실험 결과, 제안 모델이 GLUE, RACE, SQuAD 데이터셋에서 SOTA 달성

## ALBERT
### 1. Factorized embedding parameterization
- Input layer의 parameter 수를 줄임
- BERT에서는 Input token embedding 사이즈와 Hidden 사이즈가 동일
- ALBERT에서는 Input token embedding 사이즈를 Hidden 사이즈보다 작게 설정하여 parameter를 줄임

### 2. Cross-layer parameter sharing
- Transformer의 Layer간 parameter 공유

### 3. Inter-sentence coherence loss
- Sentence Order Prediction
- Next Sentence Prediction 대신 두 문장이 주어질 때 순서를 맞히도록 학습

## References
- https://y-rok.github.io/nlp/2019/10/23/albert.html
