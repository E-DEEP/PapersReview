오늘은 최신 멀티모달 모델인 Open ai의 CLIP에 대해 리뷰를 해보고자 합니다. 

공식 홈페이지 : [openai.com/blog/clip/](https://openai.com/blog/clip/)

[

CLIP: Connecting Text and Images

We’re introducing a neural network called CLIP which efficiently learns visual concepts from natural language supervision.

openai.com



](https://openai.com/blog/clip/)

리뷰 작성을 시작하며 멀티모달 카테고리를 하나 만들까 했지만 우선 포스팅의 수가 더 적은 CV에 작성해보기로 했습니다.

이활석님이 텐플코에 올려주신 OpenaiI에서 CLIP관련 분석을 한 게시물도 있으니 참고부탁드립니다. ㅎㅎ

[https://distill.pub/2021/multimodal-neurons/](https://distill.pub/2021/multimodal-neurons/)

[

Multimodal Neurons in Artificial Neural Networks

We report the existence of multimodal neurons in artificial neural networks, similar to those found in the human brain.

distill.pub



](https://distill.pub/2021/multimodal-neurons/)

그럼 시작하겠습니다.

---

## **Abtract**

이제까지의 SOTA의 Computer vision 시스템들은 미리 정의된 카테고리의 데이터셋에 대해서 예측하도록 훈련되어 왔다.

이런 supervision방식은 **generality**와 **usability**를 한정짓게 된다. 다른 visual concept을 특정화지으려면 추가적인 라벨링된 데이터가 필요하기 때문이다. 

이에 대한 대안으로는 Raw text로부터 바로 이미지에 대해 러닝하는 것이 있다. 

이 논문의 방식이 그러한데, 어떠한 캡션이 어떠한 이미지에 붙여지는 지를 예측하는 간단한 사전 학습 task(웹상의 (image,text) 4억개의쌍을 이용)가 SOTA image representation을 학습하는데에 효과적이며 확장가능한 방법임을 보여준다. 

사전 학습 후에, 자연어는 학습된 visual concept을 참고하는데 사용되어 downstream task로 zero-shot transfer 하는 것을 가능하게 한다. 이 논문에서는 이러한 접근법을 검증하기 위해 30개의 다른 datasets을 사용하였고 이를 이용하여 OCR, action recognition 등의 task로 확장이 가능하였다. 

모델은 대부분의 task에 transfer가 가능했고 기존의 모델보다 때때로 나은 성능을 보여주기도 했다고 한다. 

---

## **Introduction and Motivating Work**

기존의 Raw text로부터 바로 사전학습하는 방식은 NLP분야에서 지난 몇년간 혁신을 보여줘왔다.

(BERT가 그 대표적인 예)

Autoregressive와 masked language modeling과 같은 task-agnostic(task와 관계없는) objectives는 컴퓨팅, 모델 용량 및 데이터의 다양한 순서로 확장되어 성능을 꾸준히 향상시켰는데, Text-to-text의 발달은 **커스터마이징 등이 필요없이** task-agnostic 구조가 downstream dataset으로의 zero-shot transfer이 가능하도록 했다. 대표적인 예로, GPT-3와 같은 시스템은 특수한 학습데이터가 필요없이 많은 task에서 좋은 성능을 보여주고 있다.

이러한 결과들이 의미하는 바는 자연어분야에서는 (웹에서 모을 수 있는 텍스트를 이용한) 사전학습 모델이 기존의 라벨링된 고퀄의 NLP 데이터셋을 이용한 모델을 능가한다는 것이다.

하지만, 자연어 외의 분야들, 예를 들어 비전분야에서는 아직까지 기존의 라벨링된 데이터셋에서(ex. ImageNet) 사전학습하는 것이 표준방식이였다. 그렇다면, 웹 텍스트로 **사전학습하는 방식이 컴퓨터 비전에서도 좋은 결과를 낼까?** 이 논문을 읽고나면 이에 대한 답을 어느정도 찾을 수 있을 것이다.

---

<멀티모달 히스토리> - 멀티모달 분야 히스토리에 대해 잘몰라서 꼼꼼하게 읽어보았습니다. 아시는 분은 패스 ㅎㅎ(👉🏻는 인과관계가 아닌 순서만을 의미함)

지난 20년간, 텍스트와 이미지쌍을 이용하여 모델을 학습시킴으로써 콘텐츠 기반의 이미지 retrieval을 계산하는 것에 대해 많은 연구가 있어왔다.

👉🏻 한 논문에서는 이미지와 관련된 캡션에서의 단어들을 예측하도록 트레이닝된 classifiers의 weight space에서의 manifold learning을 통해, 더 데이터 효율적인 이미지 representation을 러닝하는 것이 가능하다는 것을 보여줬다.

👉🏻 또한, low-level image와 text tag features를 이용하여 멀티모달 Deep Boltzmann Machines을 학습함으로써 deep representation learning을 연구하기도 했다.

👉🏻 2016년 한 논문에서 이러한 작업들을 현대화하며 이미지 캡션에서 단어들을 예측하도록 훈련된 CNN이 유용한 image representation을 학습한 것 또한 보여주었는데 해당 논문에서는 YFCC100M데이터셋에서 이미지의 제목, 설명 그리고 해쉬태그 메타데이터를 a bag-of-words 멀티라벨 분류 task로 변환하였다. 이를 통해 이러한 라벨을 예측하기위해 AlexNet을 사전학습하는것인 transfer task에서 ImageNet 데이터셋을 이용하여 사전학습한것과 유사한 성능을 냄을 보였다.

👉🏻 또, 2017년 한 논문에서는 이러한 접근법을 n-grams을 예측하는 단계로 확장시키고 다른 이미지 분류 데이터셋으로의 zero-shot transfer (학습된 visual n-grams의 사전에 기반하여 target class를 점수매기는 방법 -> 가장 높은 점수로 클래스를 예측함)에 대한 가능성 또한 보여주었다.

👉🏻 2020년에는 몇개의 논문들이 transformer 기반의 언어모델링, masked language modeling 그리고 텍스트로부터 이미지 representation을 학습하는 방식에 대한 잠재력을 보여주었다.

즉, 멀티모달 분야는 이미지/자연어 모델이 발달함에 따라 같이 발전해왔다. 당시의 가장 핫한 모델들을 거의 다 적용해보았다고 볼 수 있다.

---

하지만, 위의 많은 히스토리에도 불구하고 아직 자연어를 이미지 representation learning에 사용하는 것은 여전히 드물다. 이유로는, 측정된 성능이 다른 대안들보다 훨씬 낮기 때문이다.

예를 들어, 한 논문에서는 zero-shot setting에서(즉 라벨링 되지 않은 데이터 상태) ImageNet 데이터셋에 대해 11.5%의 성능을 보였는데 이는 현재 88.4%의 성능을 보이는 모델에 비해 정말 부족한 수치이기 때문이다. 심지어 기존의 classic vision 접근법의 성능인 50%보다도 낮다. 

대신에, weak supervision(라벨링의 비율이 낮은 세팅이라고 생각)은 성능을 향상시켜오긴 했다. 2018년의 논문에서는 인스타그램 이미지에서 ImageNet과 관련된 해쉬태그를 예측하는 것이 효과적인 사전학습임을 보였다. ImageNet에 대해 fine-tuning할 때 이러한 사전학습 모델은 5%넘게 정확성을 높이고 당시의 SOTA모델의 성능을 뛰어넘었기 때문이다. 

이러한 연구들은 제한된  gold labels의 데이터셋으로 학습하는 것과 라벨링되지 않은 무한한 텍스트로부터 학습하는 것 사이의 현재 위치를 보여준다. 두 방법 모두 static softmax classifiers를 예측에 사용하였기에 동적인 output이 부족하다. 이로인해 유연함이 떨어지고, zero-shot 능력을 제한시키게 된다. 

[##_Image|kage@rs9Go/btq2Eg0naJd/WdF69Hy0QRQorvDYHI0eD1/img.png|alignCenter|data-origin-width="0" data-origin-height="0" data-ke-mobilestyle="widthContent"|||_##]

이 논문에서는 **CLIP**이라는 모델을 통해 두 방법(weakly supervised approach & zero shot learning using raw text)간의 차이를 줄였다. 인터넷에 공개되어 있는 많은 데이터셋을 통해 4억개 쌍(이미지, 텍스트)의 새로운 데이터셋을 만들었으며 CLIP은 또한 _**ConVIRT**_의 간단한 버전이라 칭할 수 있다. 

CLIP은 앞서 언급했듯이 기존의 task-specific supervised model들과 성능이 비슷할뿐만 아니라 몇몇의 경우에서는 뛰어넘는 성능을 보여주었다. 또한 zero-shot CLIP은 같은 정확도의 다른 모델들보다 강력한 것을 보여주었는데 이는 task-agnostic model의 zero-shot이 모델의 capability를 더 잘 표현한다고도 할 수 있다. 

---

## **Approach**

### **1\. Natural Language Supervision, 왜 자연어 supervision?**

CLIP의 핵심은 자연어에 포함된 supervision으로부터 학습하는 것이라 할 수 있다. Introduction에서도 언급된 내용이지만, 이러한 학습방식은 기존에도 존재했다. 이전의 많은 논문들이 이미지와 짝지어진 텍스트로부터 visual representation을 학습하였지만 이를 unsupervised, self-supervised, weakly supervised 등등 각각 다르게 묘사했다. 

(사실 난 weakly supervised가 그나마 맞는 표현이지 않나 싶다.)

이 논문의 모든 접근방식은 _natural language supervision _로부터 나왔는데, 기존의 연구들이 자연어의 complexity로 어려움이 있었지만, 점점 개선이 됨에따라 효과적으로 이러한 자원을 이용할 수 있게 되었다. 

자연어를 이용하여 학습하는 것은 다른 학습 방법들보다 몇가지 잠재적인 능력을 가지고 있다.

**첫째로는,** 기존의 image classification에 사용된 라벨링에 비해 **scaling이 쉽다.** Annotation이 classic "ML compatible format"에 있지 않아도 되기 때문이다. 즉, 기존의 라벨링 방식은 사람이 직접 표기를 해서 "gold" label"이라는 것을 만들어야 했었는데 자연어를 이용하게 되면 그러한 과정이 필요없다. 자연어를 이용한 학습은 인터넷의 방대한양의 텍스에 포함된 supervision으로부터 수동적으로 학습이 가능하다.(interaction이 필요없다는 의미로 받아들이면 될 듯) 

**둘째로는, **자연어를 이용한 학습은 기존의 대부분의 unsupervised or self-supervised 학습 방식보다 중요한 장점을 가지는데, 바로 **모델이 단지 representation만을 학습하는 것이 아니라 언어에 대한 reprentation 또한 학습한다는 것이다.** 이는 좀 더 유연한 zero-shot transfer를 가능하게 한다. 

### **2\. Creating a Sufficiently Large Dataset, 어떤 Data로 학습?**

기존의 연구는 주로 세가지 데이터셋을 사용해왔다. (MS-COCO, Visual Genome 그리고 YFCC100M)

MS-COCO dataset과 Visual Genome dataset이 고퀄의 라벨링된 데이터셋임에도 현대의 기준에서는 사실 적은 양이다.

다른 computer vision시스템은 35억개의 인스타그램 사진으로 학습되기 때문이다. 1억개의 사진인 YFCC100M이 대안이 될 수 있지만, 각 이미지에 대한 메타데이터가 sparse하고 일관된 품질이 아니기에 훌륭한 대안은 아니다. 

자연어 supervision에 대한 주요한 Motivation은 인터넷에 공개된 데이터셋이 방대하기 때문이다. 기존의 데이터셋이 이러한 방대한 데이터셋을 충분히 반영하지 않았기 때문에, 결과를 적절하게 비교하기는 쉽지 않다. 이러한 문제를 해결하기 위해,논문의 저자들은 새로운 4억개의 데이터셋 (이미지, 텍스트)쌍을 만들어 사용했다.

### **3\. Selecting an Efficient Pre-Training Method, 어떻게 사전학습?**

SOTA CV 시스템은 많은 데이터셋을 이용한다. 이 논문의 저자는 이러한 방대한 데이터셋을 이용한 효과적인 학습이 성공적으로 자연어 supervision을 스케일링하는것에 대한 핵심임을 강조했다.

처음의 접근법은, VirTex와 유사하게, 이미지 캡션을 예측하기 위해 Image CNN과 Text Transformer에서 학습하는 식이였다. 하지만,이러한 방법은 스케일링을 효과적으로 하는데에 어려움이 있었는데 아래의 그림을 보자.

[##_Image|kage@3o8l5/btq2zDv0A53/mgBjSvrJaeBZeCXb1s14I0/img.png|alignCenter|data-origin-width="0" data-origin-height="0" width="801" height="NaN" data-ke-mobilestyle="widthContent"|||_##]

ResNet-50 image encoder 계산량 두배의 6300만개의 파라미터의 transformer 언어모델은 간단한 모델들보다 3배나 느린 것을 볼 수 있다. 이러한 방식들은 유사성을 가지는데 이미지에서 "정확한"단어를 예측하고자 한다는 것이다. 하지만 이러한 방식은 묘사하는 방식과 코멘트 등의 다양함때문에 쉽지 않다. 이러한 문제점때문에 이 논문에서는 한 단어가 아닌 텍스트 전체로 짝지어지도록 학습시켰다. Bag-of-words 인코딩 방식을 사용하여 위의 그래프에서도 확인할 수 있듯이 ImageNet에서의 zero-shot transfer에서 4배의 효율성을 확보했다. 

N개의 (이미지, 텍스트) 쌍의 배치가 주어졌을 때, CLIP은 NXN개의 가능한 (이미지, 텍스트) 쌍을 예측하도록 학습된다. 이를 위해, CLIP은 N개의 실제 쌍의 이미지 임베딩과 텍스트 임베딩의 코사인 유사도를 최대화하고 $N^2-N$개의 잘못된 쌍의 코사인 유사도는 최소화하도록 이미지 인코더와 텍스트 인코더를 같이 학습함으로써 multi-modal 임베딩 공간을 학습한다.  (아래의 그림 참조)

[##_Image|kage@VSL6p/btq2EfN1cYh/BV4JdFwq3d62XWd6AUA7D1/img.png|alignCenter|data-origin-width="0" data-origin-height="0" width="659" height="NaN" data-ke-mobilestyle="widthContent"|||_##]

아래 peudocode의 logits부분이 이러한 score를 기반으로 cross-entropy를 계산하는 부분이다.

[##_Image|kage@bkSxEP/btq2DsNGpDV/1HF4FfcMux2vbkz9JBgre1/img.png|alignCenter|data-origin-width="0" data-origin-height="0" width="679" height="NaN" data-ke-mobilestyle="widthContent"|||_##]

CLIP은 pre-trained weights이 아닌 아예 **랜덤값으로 초기화되어 학습**된다. 또, Representation과 contrastive embedding 공간사이의 non-linear projection을 사용하지도 않았다. 대신에, 각 인코더의 representation에서 multi-modal 임베딩 공간으로의 **linear projection**만을 사용했다. 추가로 text transformation과정도 제거하였으며(데이터셋이 single 문장이 대부분이기 때문) 이미지 transformation 함수도 단순화하였다. 

Data augmentation은 이미지 사이즈 변환만을 사용했다. 마지막으로, softmax의 logit을 제어하는 temperature 파라미터 $\\tau$는 하이퍼파라미터로 튜닝되는 것을 피하기 위해 학습과정에서 바로 최적화된다.

### **4\. Choosing and Scaling a Model**

앞서서 모델 학습의 효율성이 핵심이라고 했는데 어떻게 모델을 선택하고 스케일링했을지 알아보자.

### **<Image Encoder>**

CLIP의 저자들은 이미지 인코더로 두 개의 구조를 고려했는데,

1\. ResNet-50

2.Vision Transformer(ViT) 

이다.

#### **1) ResNet-50**

오리지날 ResNet에 조금 수정된 버전을 사용했는데, ResNet-D버전을 사용했으며 rect-2 blur pooling을 사용했다. 또한, global average pooling 레이어를 attention pooling 메커니즘으로 대체했다. Attention pooling은 하나의 "Transformer-style"의 multi-head QKV attention의 하나의 층으로 실행되는데 여기서 Q(Query)는 global average-pooled 이미지 representation에서 조건화된다. 

#### **2) ViT**

최근 소개된 Vision Transformer로도 실험을 했는데 하나의 추가적인 layer normalization을 추가하는 것 외에 거의 기존의 모델을 수정하지 않고 사용했다. 

### **<Text Encoder>**

텍스트 인코더로는 Transformer를 사용했는데 텍스트의 **BPE representation**을 이용했다. 

계산 효율성 측면때문에 max sequence length는 76로 제한했고 텍스트 시퀀스는 \[SOS\]와 \[EOS\] 토큰과 함께 묶였으며 \[EOS\] 토큰 위치의 highest laeyrs의 activations은 text의 representation으로 다뤄진다. Masked self-attention이 기존에는 사용되었지만 여기서는 사용하지 않았다. 

---

이전의 컴퓨터비전 연구들은 종종 ResNet의 width나 depth를 조정함으로써 모델을 스케일링했지만, 이 논문에서는 width, depth 그리고 해상도에 추가적인 계산을 할당하는 것이 모델의 한 차원에만 할당할 때만 잘된다는 사실을 이용하여 모델의 폭, 깊이 및 해상도를 높이기 위해 추가 컴퓨팅을 균등하게 할당하는 간단한 베이스라인을 사용했다.(이 말이 무슨말일까...)

텍스트 인코더에서는 ResNet 너비의 증가 폭에 비례하여 모델의 너비만을 스케일링했고 깊이는 아예 건들지 않았다.

(CLIP의 성능이 텍스트 인코더의 capacity에는 그리 민감하지 않기 때문)

### **5.Training**

이 논문에서는 5개의 ResNet과 3개의 Vision Transformer 시리즈를 학습했다. ResNet으로는 ResNet-50, ResNet-101 그리고 EfficientNet 스타일의 모델 3개를 더 학습했고 대략적으로 4배, 16배, 64배의 계산을 사용하게 되었다. (RN50x4, RN50x 16 그리고 RN50x64로 각각 칭하자.)

ViT로는 ViT-B/32, ViT-B/16 그리고 ViT-L/14를 학습했고 모두 32 epoch만큼 학습했다. 

Optimizer로는 Adam을 사용했으며 cosine schedule을 이용하여 learning rate를 점차 감소시켰다. 

초기 하이퍼파라미터는 1 epoch 학습시에 grid search, random search 그리고 ResNet-50에서의 manual tuning의 조합을 사용했다. 하이퍼파라미터는 그다음 계산효율성을 위해 휴리스틱하게 조정이 되었다. 

---

## **Experiments**

### **1\. Zero-shot Transfer**

비전분야에서 zero-shot이란 이전에 한번도 보지 못한 물체에 대한 분류를 예측하는 방식을 말하는데 이 논문에서는 좀 더 넓은 범위로 한번도 보지 못한 데이터셋에 대한 정의를 내렸다. 

Unsupervised learning 분야에서 많은 연구가 representation learning 능력에 초점을 맞추는 반면에 저자들은 zero-shot transfer를 **task-learning capabilities**를 측정하는 방법으로 영감을 받았다. 

이러한 관점에서 데이터셋은 특정 분포에서의 task에서 성능을 측정한다. 하지만 많은 인기있는 비전 데이터셋은 연구 커뮤니티에서 분류에 초점을 맞춰서 주로 만들어졌기에 특정 task에서의 성능을 측정하는데에 초점이 맞춰져있지 않다.

이 논문에서 zero-shot transfer를 trask learning의 평가로서 초점을 맞추는 것은 NLP 분야의 task learning에서 영감을 받았다. 한 2018년 논문에서는 처음으로 (위키피디아 글을 생성해내도록 학습된 언어모델이 언어번역이 가능하게 학습되었을 때) **task learning을 "unexpected side-effect"으로 정체화했다.**

### **1.2 Using CLIP For Zero-Shot Transfer**

CLIP은 이미지와 텍스트가 짝지어지도록 학습이 된다. Zero-shot 분류를 수행하기 위해 저자들은 이러한 capability를 재사용했는데, 각 데이터셋에 대해 모든 클래스의 이름들을 잠재적인 text pairing으로서 사용하고 가장 그럴듯한 이미지, 텍스트 쌍을 예측했다. 더 자세히 말하자면, 이미지의 feature embedding과 텍스트의 feature embedding을 계산한 뒤 이러한 임베딩의 코사인 유사도를 계산한 후 softmax를 통해 확률값으로 뱉어낸다. 

이러한 prediction layer는 L2-normalized inputs, weights, no bias 그리고 temperature scaling의 multinomial logistic regression classifier이다. 이러한식으로 해석되었을 때, 이미지 인코더는 feature representation을 계산하는 컴퓨터 비전의 backbone이라 할 수 있고 텍스트 인코더는 (클래스가 나타내는 시각 개념들을 특정화하는) linear classifier의 weights를 만들어내는 hypernetwork이라 할 수 있다. 

## **Results**

이러한 텍스트 인코더, 이미지 인코더의 구조의 CLIP은 아래와 같이 fully supervised(라벨이 다 주어진) learning과 비교해서 경쟁력있는 성능을 보여준다. 

[##_Image|kage@bqaD2o/btq2CFmu1m7/VktS9dzgKiLA9zpFsFqJEk/img.png|alignCenter|data-origin-width="0" data-origin-height="0" width="716" height="NaN" data-ke-mobilestyle="widthContent"|||_##]

또한 오히려 few shot 방법보다는 더 나은 성능을 보여준다.

[##_Image|kage@c1o1lp/btq2CX8hiyr/xYlm7l0uJekUTRZoxI5MQ1/img.png|alignCenter|data-origin-width="0" data-origin-height="0" width="718" height="NaN" data-ke-mobilestyle="widthContent"|||_##]

또 다른 결과로는 기존의 SOTA 비전 시스템과 CLIP을 비교한 그래프이다.

[##_Image|kage@cIVYzF/btq2CJvA9Dh/mefviDzqkZ57EC7bfAUSu1/img.png|alignCenter|data-origin-width="0" data-origin-height="0" width="729" height="NaN" data-ke-mobilestyle="widthContent"|||_##]

Score를 보면 CLIP-ViT가 기존의 시스템보다 더 나은 결과를 보여주는 것을 확인할 수 있다. 

또한 CLIP은 텍스트간의 연관성또한 학습하여 좀 더 강력하다는 특징이 있는데 아래의 그래프에서 이를 확인할 수 있다. 

(Transfer Score가 높다. 즉 의미론적으로 잘 학습하여 어떤 task에서도 잘 작동함)

[##_Image|kage@bRBAMR/btq2CYe3lgF/sXeaf5j12T7KfKD7QRsI8k/img.png|alignCenter|data-origin-width="0" data-origin-height="0" data-ke-mobilestyle="widthContent"|||_##]
