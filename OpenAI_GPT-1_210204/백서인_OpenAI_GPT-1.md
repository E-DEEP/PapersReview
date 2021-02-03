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
