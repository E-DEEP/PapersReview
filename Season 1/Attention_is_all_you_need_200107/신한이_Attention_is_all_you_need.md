Attention is all you need Review
=============

[Submitted on 12 Jun 2017 (v1), last revised 6 Dec 2017 (this version, v5)]
-------------

Introduction
-------------

sequence 모델링과 언어모델 주로 채택되는 모델은 RNN, LSTM, GRU 등이 있다.

Recurrent 모델은 시계열 데이터에 알맞게 고안되어, recurrent 벡터를 사용하여 정보를 전달한다. 
Recurrent 모델은 병렬처리를 배제하고, 메모리 제한으로 인해 입력 데이터가 커질수록 출력 시퀀스의 정확도가 떨어지는 근본적 단점이 있다.

어텐션은 decoder에서 출력을 예측하는 해당 시점마다 
encoder에서 해당 시점과 연관이 있는 부분을 attention하여 전체 입력을 다시 참고한다.

이전의 attention mechanism은 recurrent network와 함께 사용되는 경우가 많았다. 

이 논문에서는 Transformer 모델 구조를 제안한다.

Attention mechanimsm만을 사용한 모델 구조로, 입력과 출력 간의 의존성을 학습할 수 있다. 

병렬화를 가능하게 했고 state of the art 결과를 얻을 수 있다.





Model Architecture
-------------

![스크린샷 2021-01-07 오전 12 33 57](https://user-images.githubusercontent.com/50253860/103786756-231bea00-5080-11eb-8ac5-06be266042c6.png)

논문에 참고된 이미지이다.
처음으로 Attention으로만 구성된 모델로, 크게 보았을 때 encoder과 decoder 구조를 가지고 있다.

Transformer는 encoder와 decoder 모두에서 쌓은 self-attention과 point-wise FC layer를 사용한다.




### Encoder

- 6개의 동일한 층으로 구성.
- 각 층에 2개의 sub-layers가 있다. (Multi-head+feed forward)
- Residual connection 을 채택
- Layer normalization 진행
- 모델의 모든 레이어는 512 차원으로 임베딩


### Decoder

- 6개의 동일한 층으로 구성.
- 각 층에 2개의 sub-layers가 있다. (Multi-head+feed forward)
- Residual connection 을 채택
- Layer normalization 진행
- 3번째 sub-layer 있다.
- Decoder 순차적으로 결과를 만들어야 함. 따라서 미리 알고 있는 output에 의존한다.



Attention
-------------

Attention 함수에서 Output은 queries, Key, Values의 가중합으로 계산되고, 

가중치는 query와 연관된 key의 compatibility function에 의해 계산된다.






> #### Scaled Dot-Product Attention
Attention을 계산할 때 dot-product를 쓰고, 그 결과에 scale 조정을 한다. 

모든 내적을 계산하여 각각 차원의 제곱근으로 나누고, value의 가중치를 얻기 위해 softmax함수를 적용한다. 

Quries를 동시에 계산하기 위해 이를 행렬 Q로 묶고 하나의 식으로 표현할 수 있다.






> #### Multi-Head Attention

단일 attention보다 Key, Values, Queries를 다차원으로 계산함으로써 벡터의 크기를 줄이고 병렬처리가 가능하다.    
(dk, dk, dv)






> #### Position-wise Feed-Forward Networks

ReLU를 활성함수로 사용하여 linear transformations로 구성된다.







> #### Positional Encoding

시퀀스의 위치에 관한 정보가 필요를 주입.
인코더, 디코더 스택 하단에 positional enconding를 추가한다.







Why Self-Attention?
-------------

> 우선, 레이어당 계산량이 줄어든다. 

> 병렬처리가 가능한 계산이 늘어난다. recurrence에서 순차적으로 계산하는 특징과 차이를 보인다.

> 결론적으로, Rnn의 고질적인 문제였던 장거리 학습을 더 효율적으로 할 수 있게 되었다.





### Training & Results

논문에 기재된 참고 표와 이미지, 에서 정확히 확인할 수 있다. 







