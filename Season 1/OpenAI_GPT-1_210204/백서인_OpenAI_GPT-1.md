## Improving Language Understanding by Generative Pre-training

### Abstract
- Natural language understanding은 textual entailment, question answering, semantic similarity assessment, document classification 등 다양한 범위의 일을 할 수 있음
- 라벨링되지 않은 다양한 텍스트 corpora가 있지만, "specific task"를 위한 라벨링된 데이터는 매우 희소하고, 이는 모델을 잘 학습시키기 어렵게 함
- 우리는 "generative pre-training" 언어 모델을 사용하여 라벨링되지 않은 텍스트로부터 "large gain"을 얻을 수 있다는 것을 확인함
- 이는 각 task에 "discriminative fine-tuning"을 사용하여 가능
- 이전 연구들과는 달리, 우리는 fine tuning 동안 task-aware input transformations을 사용하는데, 이를 통해 모델 아키텍쳐는 최소한으로 바뀌면서 효과적인 transfer 가 가능
- 많은 natural language understanding 문제에서 본 논문이 제안하는 모델이 효과적임을 증명

### Introduction
- 대부분의 딥러닝 방법들은 상당한 양의 라벨링된 데이터를 필요로 함
- 많은 리소스의 부족을 겪고 있는 분야에서는 하기 어려운 작업임
- 이러한 상황에서 라벨링되지 않은 데이터로부터 언어적 정보를 학습하는 모델은 valuable함
- 하지만 라벨링되지 않은 텍스트로부터 word-level information 이상을 활용하는 것은 두 가지 주요 challenge가 있음
  - 어떤 optimization objectives가 가장 효과적인지 clear하지 않음
  - 학습된 representation을 target task로 transfer하는 가장 효과적인 방법에 대한 일치가 없음
- 본 논문에서는 NLU task에 적용할 수 있는 semi-supervised approach를 제안
- 이는 unsupervised pre-training 과 supervised fine-tuning 방법을 combination함
- 모델 학습의 목표는 약간의 변형(?)으로 다양한 task에 적용할 수 있는 universal representation을 학습하는 것임
- 제안하는 모델은 target task의 도메인이 pre-training시 사용한 라벨링되지 않은 데이터의 도메인과 일치하지 않아도 됨
- 모델은 two-stage로 나뉨
  - 라벨링되지 않은 데이터로부터 neural network model의 초기 파라미터를 학습하기 위해 language modeling objective를 사용
  - supervised objective를 사용하여 학습된 파라미터를 target task에 적용
- 모델 아키텍쳐는 Transformer 모델을 사용
- Transformer 모델을 사용하는 것은 long-term dependencies를 핸들링하기 용이하게 함
- Transfer를 할 땐, task-specific input adaptation을 활용
- 이러한 adaptation은 pre-trained model의 아키텍쳐를 조금만 바꾸어도 효과적인 fine-tuning이 가능하도록 함

### Related Work
#### Semi-supervised learning for NLP
- 본 논문은 semi-supervised learning의 범위 안에 속한다고 할 수 있음
- 이 패러다임은 sequence labeling이나 text classification과 같은 task의 응용과 함께 상당항 흥미를 끌었음
- 초기 단계의 접근법은 라벨링되지 않은 데이터를 word-level 혹은 phase-level statistics을 계산하기 위해 사용하는 것이었음 (이는 supervised model의 feature 로서 사용됨)
- 몇년 후, 연구자들은 word embedding을 사용하는 것의 장점을 증명하였음
- 하지만 이러한 방법들은 주로 word-level information을 transfer함
- 본 논문에서는 좀 더 higher-level semantics를 캡쳐하는 것을 목표로 함
- 최근 연구들은 라벨링되지 않은 데이터로부터 word-level 이상의 information을 활용하는 방법에 대한 것임
- 라벨링되지 않은 데이터로부터 학습 가능한 Phase-level 또는 sentence-level embedding은 다양한 task에서 text를 적절한 vector representation으로 인코딩하기 위해 사용되어 왔음

#### Unsupervised pre-training
- Unsupervised pre-training은 semi-supervised learning의 special case임
- 목표는 supervised learning objective를 수정하는 것이 아니라 good initialization point를 찾는 것임
- 초기 연구들은 이 technique를 이미지 classification 이나 regression task에 활용해왔음
- 많은 연구들이 pre-training이 마치 deep neural network를 잘 일반화할 수 있도록 하는 regularization scheme과 같은 역할을 한다는 것을 증명해왔음
- 최근 연구에서는 이 방법이 다양한 task에서 deep neural network의 학습을 돕기 위해 사용되고 있음

#### Auxiliary training objectives
- auxiliary unsupervised training objectives를 추가하는 것은 semi-supervised learning의 대안 형태임
- 본 논문에서도 auxiliary objectives를 사용하지만, unsupervised pre-training은 이미 target task에 관련된 several linguistic aspects를 학습한 상태임

### Framework
- Training procedure은 two stage로 나뉨
- 첫번째 단계는 high-capacity language model을 학습하는 것
- 두번째 단계는 라벨링된 데이터를 사용하여 학습된 pre training model을 각 task에 적용하는 것

#### Unsupervised pre-training
- 주어진 unsupervised copus tokens U = {u_1,u_2, ... , u_n} 로부터 아래의 likelihood 를 maximize 하기 위해 standard language modeling objective 를 사용

![image](https://user-images.githubusercontent.com/48814946/106703879-76a53780-662e-11eb-809f-bc69ec29defd.png)

- 본 실험에서는 multi-layer Transformer decoder 를 사용
- 이 모델은 multi-head self-attention operation을 적용

#### Supervised fine-tuning
- model을 pre-training한 후에는 supervised target task로 파라미터를 적용함
- input data는 pre-trained model를 지나 final transformer block의 activation 을 얻고, 이는 추가적인 linear layer로 들어가 라벨 y를 예측하게 됨
- fine-tuning 단계에서 auxiliary objective를 추가하는 것은 (a) supervised model의 일반화를 가능하게 하고 (b) 수렴을 빠르게 함

![image](https://user-images.githubusercontent.com/48814946/106703897-83c22680-662e-11eb-8aa9-7fea60da458e.png)

#### Task-specific input transformations
- text classification과 같은 task에서는 우리의 model를 바로 fine-tune할 수 있다
- 하지만, question answering, textual entailment 와 같은 task에서는 ordered sentence pairs, triplets of document, question, answer 과 같은 input이 필요함
- 이러한 task에서는 모델이 약간 수정되어야 함
- 본 논문에서는 traversal-style approach를 사용
- 이를 위해 구조화된 input 를 model이 process할 수 있도록 ordered sequence 로 변환해줌
- 이러한 input transformation은 여러 task에서도 모델의 아키텍쳐에 대한 큰 수정을 하는 것을 피하도록 해줌

### Analysis

![image](https://user-images.githubusercontent.com/48814946/106703944-95a3c980-662e-11eb-8a38-676cd3073139.png)

#### Impact of number of layers transferred
- layer의 수가 많아질수록 성능 향상
- pre-trained model의 각 layer가 target task를 풀기 위한 useful functionality를 포함하고 있음을 알 수 있음

#### Zero-shot Behaviors
- Transformers를 pre-training한 모델이 왜 효과적인지에 대해 분석
- 가정은 다음과 같음
- LSTM과 비교하여 transformers의 attentional memory가 더 잘 transfer 하도록 돕기 때문

#### Ablation studies
![image](https://user-images.githubusercontent.com/48814946/106703978-a3f1e580-662e-11eb-80cf-835e34364bf6.png)

- 1) fine tuning에서 auxiliary LM objectives 제거
- 2) single layer 2047 LSTM과 Transformer의 성능 비교
- 3) pre-training 없이 target task에서 바로 학습된 모델과 성능 비교


