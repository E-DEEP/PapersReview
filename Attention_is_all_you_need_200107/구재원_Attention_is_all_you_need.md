## 핵심

NLP에서 가장 많이 사용되는 transformer network를 처음 제안한 논문

## Introduction
+ Problem: 
  1. Recurrent Model: 기존의 NLP에서는 recurrent model들인 RNN, LSTM, gated RNN이 주로 사용되었음. 하지만 이런 recurrent model들은 sequential하게 계산이 되기에, 긴 문장을 학습 시킬 때, 메모리 한계가 존재하였음.  
  2.  Attention mechanism:  attention은 input의 길이와 상관 없이 modeling을 할 수 있다는 장점이 있음. 하지만 recurrent network와 attention mechanism의 조합은 거의 연구되지 않았음

+ Contribution: 
  1. input과 output의 global dependencies를 구할 수 있는 attention과 recurrence를 융합한 새로운 모델인 transformer 제안

## Model Architecture

+ Encoder :

  - input sequence of symbol representation을 seqeunce of continuous representation으로 만들어줌. 

  - 2개의 sub-layer를 가진 총 6개의 동일한 layer로 구성되어 있음

       : multi-head self-attention mechanism과 position-wise fully connected feed-foward network로 구성되어 있음

+ Decoder:

  - output sequence를 만들어 주는데 한번에 하나의 element가 나옴

  - encoder의 구조에 하나의 sub-layer를 추가해준 구조로 되어있음

      :  multi-head attention을 담당함

+ Attention

  - query, key, value를 사용해 scaled dot-product attention을 하는 방식으로 이루어짐

+ Multi-head Attention

  - single attention이 아닌 다르게 h번 attention을 계산해주는 것



## Why Self-Attention

- self-attention을 사용하면 long-range dependency도 고려할 수 있게 됨, self-attention layer는 모든 position에 대해 고려를 해줌

-  computational complexity가 O(n)이기에  RNN보다 더 빠름. 