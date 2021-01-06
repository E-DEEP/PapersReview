## Introduction

RNN, LSTM, GRU 모델은 sequence modeling에서 좋은 성과를 보였다.
하지만 이러한 모델은 입력을 순차적으로 받기 때문에 병렬처리가 불가능하다.
본 논문에서는 attention 매커니즘에 의존한 transformer 모델을 제안한다.
transformer는 병렬처리가 가능하고 translation quality에서 SOTA를 달성하였다. 

## Model Architecture

encoder-decoder 구조

Encoder
- 6개의 encoder로 구성
- 각 층에는 2개의 sub-layer
  - 1) multi-head attention
  - 2) feed-forward
- 각 sub-layer는 residual connection을 사용 -> x + sub-layer(x)
- 또한 layer normalization

Decoder
- 6개의 decoder로 구성
- 3개의 sub-layer
- residual connection
- layer normalization
- masking을 통해 position i보다 작은 값들에만 의존하도로 self-attention 매커니즘을 수정

Scaled Dot-product attention
- Attention(Q,K,V) = softmax(QK^t / root(d_k))V
- Q = 디코더의 이전 레이어 hidden state
- K = 인코더의 output state
- V = 인코더의 output state
- self-attention의 경우 Q=K=V= 인코더의 output state

Multi-head attention
- scaled-dot product 계산시 한번에 계산하지 않고 h번으로 나누어서 계산
- 벡터들의 크기를 줄이고 병렬처리 가능

Position-wise feed-forward networkds
- fully-connected layer
- ReLU 사용

Positional Encoding
- transformer는 recurrent 모델이 아니기 때문에 입력의 위치 정보를 따로 알려주어야 함

Why self-attention
- 레이어 계산량 감소
- 병렬처리 가능
- attention을 통해 모든 부분 확인 -> rnn보다 훨씬 긴 시퀀스 학습 용이


참고
- https://wikidocs.net/31379
- https://hipgyung.tistory.com/12
