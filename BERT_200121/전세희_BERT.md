## **Abstact**

BERT는 대부분의 Language Representation model들과 달리, unlabeled text를 이용하여 모든 레이어에서 양방향 문맥을 이용하여 deep bidirectional representations를 **미리 학습시킨다. **그 결과, 사전 학습된 BERT는 **단 하나의 레이어를 추가함**으로써 다른 구조를 수정하지 않고도 파인 튜닝이 되어 많은 task에서(question answering, language inference 등) SOTA의 모델을 만들어낸다. 

BERT는 개념적으로 간단하지만 강력한데, 11개의 NLP tasks에서 새로운 SOTA 결과를 만들어냈다고 한다.(🤭...)

GLUE score에서 기존보다 7.7% 높인 80.5%를 달성하였고, MultiNLI accuracy는 4.6% 높인 86.7%를 달성하였다. 이외에도 많은 수치에서 압도적인 성능을 보여준다.

---

## **Introduction**

언어 모델 _**사전학습**_은 많은 task에서 효과가 있는 것이 증명되어 왔다. 

사전학습 언어 representation을 downstream task에 적용하는 데에는 두가지 방식이 있는데, _**feature-based**_ 와 _**fine-tuning**_ 이다.

### _**1) Feature-based**_

이 방식을 사용하는 모델로는 대표적으로 _**ELMo**_가 있다. 이 방식은 task-specific 구조를 사용하는데(task마다 구조가 다르다.) 구조 내에서 사전학습된 representation을 additional feature로 사용한다. 

### _**2) Fine-Tuning**_

대표적으로 **_OpenAI GPT_**모델에서 사용하는 방식인 fine-tuning은 task-specific 파라미터의 수가 적기 때문에 모든 사전학습된 파라미터들을 간단하게 fine tuning함으로써 downstream task에서 학습된다. 

이 두가지 방식은 사전 학습동안에 같은 목적 함수 또는 손실함수를 공유한다고 한다.

현재 기술들은(BERT이전의 기술들은) 사전 학습 방식 representation의 능력을 잘 이용하지 못하고 있다.

특히, **fine tuning** 접근에 대해서 더욱 그 진가를 알지 못한다. 주요 한계점은 일반적인 언어 모델은 **단방향**이라는 것이고 이 때문에 사전 학습의 구조에 대한 선택지가 많지 않다.

예를 들어, _OpenAI GPT_에서, 독자들은 왼쪽에서 오른쪽으로 가는 구조를 사용하는데, Transformer의 self-attetention layers의 모든 토큰들이 **이전 토큰에만 관여하는 구조이다.**

이러한 단방향(왼쪽에서 오른쪽으로만 가는 방향)은 _문장 단위 Task_에 대해서 최선책이 아닐뿐만 아니라

_토큰 단위 task_에서도 fine tuning 기반의 접근을 적용할 때 문맥을 제대로 읽어내지 못하기 때문에 올바른 방법이 아니다. 

이 논문에서는 _**fine-tuning**_ 기반의 접근의 성능을 BERT라는 모델을 통해 개선시킨다.

1) BERT는 **Masked Language Model(MLM)**을 이용하여 이전의 단방향이라는 제약조건을 완화시키는데. MLM은 랜덤하게 input의 말그대로 몇몇 토큰들을 마스크화하는(가리는) 방법이다. 문맥에 기반해 masked 토큰을 예측하는 것을 학습함으로써 **문맥 파악 능력** 또한 모델에게 학습시킨다.

2) MLM에 더해 **"Next sentence prediction"** task 또한 사용하는데 이는 text-pair representations을 사전학습 시키는 것을 말한다.

---

## **BERT**

BERT에는 두 가지 단계가 있는데, _**Pre-training**_과 _**Fine-Tuning**_이다.

_**Pre-training**_ 단계에서는 모델은 _unlabeled data_에서 학습되고, _**Fine-tuning**_ 단계에서는 BERT는 사전학습단계에서 학습된 파라미터들로 초기화되고 모든 파라미터들은 downstream tasks로부터 _labeled data_를 이용하여 fine-tuned된다.

각 downstream task는 비록 처음에는 사전학습된 같은 파라미터를 가지지만, 각각의 fine-tuned 모델들을 가지게 된다. 아래의 Question-Answering에 대한 예시를 보자.

[##_Image|kage@deeSda/btqUhzAoxdS/RATb3q9NGuK1p5EhysQejK/img.png|alignCenter|data-origin-width="0" data-origin-height="0" width="659" height="NaN" data-ke-mobilestyle="widthContent"|Figure 1: Overall pre-training and fine-tuning procedures for BERT||_##]

사전학습과 파인튜닝 모두 같은 구조를 가지고 있다. 동일한 사전학습된 모델 파라미터로 초기화되고 파인 튜닝 과정에서, 모든 파라미터들이 튜닝된다. CLS는 모든 input 예시 앞에 나오는 심볼이고 SEP는 질문과 대답을 구분짓는 심볼이다.

### **<Model Architecture>**

BERT의 구조는 multi layer 양방향 Transformer encoder라고 말할 수 있는데, Transformer를 사용하는 것이 매우 흔해졌고 오리지날 Transformer([_"Attention is All You Need"_](https://arxiv.org/pdf/1706.03762.pdf))와 크게 다르지 않기 때문에 구조에 대한 상세한 설명은 생략한다.

### **<Input/Output Representations>**

이 모델에서 _**"sentence"**_는 실제 언어학적인 문장이라기보다는, 인접한 텍스트의 임의의 연결일 수도 있다.(즉 말이 안되는 문장일 수도 있다는 뜻.)

또한, _**"sequence"**_는 BERT의 input token sequence를 뜻하는데, 1~2개의 문장이다.

이 논문에서는 3만개의 토큰을 가진 WordPiece embeddings을 사용하는데, 모든 sequence의 첫번째 토큰은 항상 \[CLS\]이다. 이 CLS토큰에 대응하는 마지막 hidden state는 분류에서 sequence representation들을 종합하는데에 사용된다. Sentence 쌍들은 하나의 sequence로 묶이는데 이 sentence들은 두가지로 나뉜다.

첫번째는 special token \[SEP\]로 구분짓는 것이고 두번째는 학습된 임베딩(어떤 문장에 속하는 지에 대한 정보를 가진)을 모든 토큰에 추가하는 것이다. 위의 figure 1에서도 나와있듯이, input embeddding은 E로 표기하고 \[CLS\] 토큰의 마지막 hidden vector는 C로 표기한다. 또한 i번째 토큰의 마지막 hidden vector는 T\_i로 표기한다.

한 토큰이 주어졌을 때, 이 토큰의 input representation은 이 토큰과 segment, 그리고 position embedding을 모두 합해서 만들어진다. (즉, input representation of a token = sum( token, segment, position embedding ) 아래 그림 참고.

[##_Image|kage@cKaUUt/btqUei7e66d/iJCLSDkk0Pc4E32APqMSGk/img.png|alignCenter|data-origin-width="0" data-origin-height="0" data-ke-mobilestyle="widthContent"|||_##]

### **1 Pre-training BERT**

앞에서도 언급했듯이 BERT는 _사전학습_과 _파인튜닝_, 두 가지 단계로 나뉜다. 먼저 사전학습 단계부터 알아보자.

기존의 방식과는 달리 BERT에서는사전 학습과정에 전통적인 left-to-right나 right-to-left 언어 모델을 사용하지 않았다. 대신에, 두 가지 _unsupervised tasks_를 사용하여 BERT를 학습시켰다. 앞에서도 나온 **Masked LM(MLM)**과 **Next Sentence Prediction(NSP)**이다.

#### **TASK #1 : Masked LM**

직관적으로 deep bidirectional model 즉 양방향 모델은 left-to-right나 얕은 concatentation 모델들보다 성능이 좋을 거라는 것은 합당하다. 아무래도 정보를 더 많이 사용하기 때문이다.

Deep bidirectional representation을 학습하기 위해서는, input tokens의 일정 부분을 랜덤하게 마스크 처리한다음 예측하게 한다. 이러한 과정을 _**masked LM (MLM)**_ 이라 한다. Masked token에 해당하는 마지막 hidden vectors은 softmax에 넣어지는데, 이 모델 실험에서는 _15%_정도를 마스크처리 했다고 한다.

(Denoising auto-encoders와는 달리 전체 input을 다시 만들어내기 보다는, 마스크 처리된 단어들을 예측하는 것에만 초점을 맞췄다.)

비록 이러한 마스크처리가 양방향의 사전학습된 모델들을 만들어내지만, MASK 토큰이 파인 튜닝과정에서 나타나지 않기 때문에 사전학습과 파인튜닝간의 mismatch가 생겨날 수 있다. 이 mismatch 상황을 줄이기 위해 항상 mask처리한 토큰을 \[MASK\] 토큰으로 대체하지 않는다.

만약 i번째 토큰이 마스크처리 후보 토큰이라면, 전체에서 80%의 경우에서만 \[MASK\] 토큰으로 대체하고 10%는 랜덤 토큰, 나머지 10%에서는 그대로 둔다. 이로인해, T\_i는 original 토큰을 예측하는데 사용된다. 

#### **TASK #2 : Next Sentence Prediction(NSP)**

Question-Answering(QA)과 같은 많은 downstream tasks와 Natural Language Inference(NLI)는 언어 모델링에서 곧바로 포착하기 힘든 _**두 문장간의 관계에 대한 이해**_를 기반으로 한다.

MLM에 이은 사전 학습 task는 뜻 그대로, 다음 sentence를 예측하는 것이다. 각 샘플은 2개의 문장으로 구성되어있는데, 50%의 확률로 2개의 문장에서 뒤의 문장을 다른 문장으로 대체한다. 그리고나서 다음 문장이 맞는지 안맞는지로 binary classification(0/1로 구분) 한다. NSP를 통해, 사전 학습 모델을 토큰 레벨의 task뿐만 아니라 문장 레벨에도 활용 가능하다. 이를 통해 BERT 모델은 문장 간의 _연관성_에 대해서 학습된다.

### **2 Fine-tuning BERT**

BERT 모델은 앞서 언급했듯이 토큰 레벨에서뿐만 아니라 문장 레벨에서도 활용이 가능하다.

_문장레벨_에서의 task는 문장 분류가 있고 _토큰 레벨_ task로는 Question Answering(QA), Named entity recognition이 있다.

문장 분류에서는 첫번째 자리의 transformer의 output을 활용하고, QA의 경우는 첫 번째 문장에는 question, 두 번째 문장에 Paragraph를 넣어서 input 데이터를 만든다.

모델이 맞춰야 하는 것은 answer가 paragraph에서 어디서 시작해서 어디에서 끝나는 지이다. 결국, start vector와 end vector만 학습하면 되는데, start vector는 transformer의 dimension의 크기를 가진다. 문단의 각 위치 마다의 output T와 start vector를 내적하여 softmax를 취하게 된다. End vector도 마찬가지로 확률을 구한 뒤 label과 cross-entropy를 취해서 학습한다.

## **Experiments**

BERT의 실험 결과들은 다음과 같다.

[##_Image|kage@bsx0S5/btqT7VytsVh/bt6ieBPiMkGy1KHdZ5JTX0/img.png|alignCenter|data-origin-width="0" data-origin-height="0" data-ke-mobilestyle="widthContent"|||_##]

[##_Image|kage@cRNSGb/btqT6HmZLsG/wqTiFKVuqefSCbQa1xEwf0/img.png|alignCenter|data-origin-width="287" data-origin-height="253" data-filename="blob" data-ke-mobilestyle="widthContent"|||_##]
