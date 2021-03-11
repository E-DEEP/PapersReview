
이미지 깨지는건 내일 수정하겠습니당

## **Abstract**

많은 분야의 텍스트에 대해 pre-trained된 언어 모델들은 오늘날 NLP의 토대를 구성하고 있다.

이 논문은 pre-trained model을 **특정 task의 도메인**에 적용하는 것이 여전히 효과가 있는지에 대해 다루었다. 4가지의 도메인에 대해서 연구를 진행하였는데, Biomedical, computer science publications, new 그리고 review에 대해서 진행하였다. 그리고 8가지의 분류 task에 대해 진행하였는데 도메인에서 pre-training하는 두번째 단계가 성능의 개선을 만들었다고 한다.

또한, 라벨링되지 않은 데이터에 적용하는 것(task-adaptive pretraining)은 domain-adaptive pretraining이후에도 성능을 개선시켰다. 

마침내, 한 augmented task corpus에 적용하는 것이 효과적인 대안임을 밝혀냈다. 특히, domain-adaptive pretraining의 자원(데이터)를 이용할 수 없을 때 효과적이였다. 

이 논문에서는 지속적으로 **여러단계의 adaptive pretraining**이 성능 개선에 큰 기여를 했음을 밝힌다.

---

## **Introduction**

오늘날 pretrained 언어모델은 방대한 양의, 그리고 다양한 코퍼스에서 학습된다. 

예를 들어, 페이스북의 RoBERTA(BERT를 최적화한 모델)는 160GB의 텍스트에 대해 학습되었는데 텍스트는 영어 백과사전과 뉴스 기사들부터 문학 작품들 그리고 웹에 있는 자료들까지들로 이루어져있다.

이러한 모델들에 의한 representation은 다양한 task에서 높은 성능을 보여준다. 이러한 점은 우리에게 물음을 던진다.

---

_**task의 \*문맥적인 도메인이 여전히 적절한가?**_

_**\*여기서 문맥적인 도메인이란 주어진 토픽이나 장르를 특정짓는 언어에 대한 분포를 의미한다.**_

_**(**__**예를 들어, 미스터리 소설에서는 사용하는 언어 분포가 뉴스에서의 언어분포와 다를 것이다.)**_

---

-   최신 large pretrained models이 광범위하게 작동하는가?
    
-   여전히 특정 도메인에 대한 별개의 pretrained model을 만드는게 도움이 되는가?
    

몇몇의 연구들이 특정 도메인의 라벨링되지 않은 데이터에 대해 **continued pretraining**하는 것에 대한 이점을 증명해왔지만, 이러한 연구들은 한 번에 하나의 도메인에 대해서만 고려하고 가장 최근 언어모델들보다는 더 작고 덜 다양한 코퍼스에 사전학습된 언어모델(BERT와 같은 모델이 아닌)을 사용한다.

또한, continued pretraining의 장점이 사용가능한 **라벨링된 task 데이터의 양**과 같은 요소나 **오리지날 도메인과 타겟 도메인과의 거리**와 같은 요소에 따라 어떻게 다른지에 대해서도 알려지지 않았다. 

[##_Image|kage@NFiAt/btqZlQeSjjB/YGhPodQkpCM899LW8DygR0/img.png|alignCenter|data-origin-width="0" data-origin-height="0" width="538" height="NaN" data-ke-mobilestyle="widthContent"|||_##]

이 논문에서는 하나의 high performing 모델에 대해서 이러한 의문을 처리하였는데, 바로 **RoBERTA**이다.

앞에서도 언급하였듯이 4개의 도메인(biomedical, computer science publications, news and reviews)를 고려하였다. 또 각 도메인당 2개의 task를 수행하여 총 8개의 분류 task를 진행하였다. 

이미 RoBERTA 학습 도메인 외의 데이터들에 대해서, 논문의 실험은 continued pretraining**(= DAPT : domain-adaptive pretraining) **이 일관되게 high- and low resource settings 모두에서 target domain의 task에서의 성능을 개선시킴을 보였다. 

이는 더 task에 직접적으로 연관된 말뭉치에서 사전학습하는것이 더 성능을 개선하는 지에 대한 물음을 던진다.

(오리지날 도메인과 타겟 도메인간의 거리가 중요한 가에 대한 물음)

논문에서는 어떻게 _**domain adaptive pretraining(DAPT)**_이 더 작지만 더 직접적으로 연관된 말뭉치에 대해서 _**task adaptive pretraining(TAPT)**_과 비교되는지에 대해 연구하였다.

TAPT는 효과적임을 증명해왔지만 가장 최근의 모델들에 대해서는 사용되지 않았다. 이 논문의 저자들은 TAPT가 DAPT의 유무에 상관없이 RoBERTA에 큰 성능 개선을 보임을 찾았다.

마침내, 이들은 task 디자이너나 annotators에 의해 일일이 정제된 unlabelded data를 추가했을 때 TAPT의 장점이 증가함을 보였다. 이러한 성공에 영감을 받아, 저자들은 자동으로 추가적인 task-relevant unlabeled text를 선택하는 방법에 대해 제시하고 어떻게 이게 특정 케이스에서 성능을 개선시켰는지에 대해 보여주었다. 

모든 tasks에서, adaptive pretraining을 사용한 논문의 결과는 SOTA와 견줄만 하다.

요약하자면, 이 논문의 기여는 다음 세 가지와 같다.

-   4개의 도메인과 8개의 tasks에 **DAPT와 TAPT에 대한 철저한 분석**
    
-   \]Adapted 언어모델의 **transferability**에 대한 조사
    
-   **인간이 직접 큐레이팅한 데이터셋**에 대해 사전학습하는 것에 대한 강조 그리고 퍼포먼스를 위한 **간단한 데이터 선택 전략**
    

---

## **Background : Pretraining**

2018년 이후로 대부분의 NLP 연구시스템은 다음 두 단계의 training으로 구성되어 있다.

1) 몇백만개의 파라미터를 가진 Neural Language Model(LM)이 큰 **unlabeled data에서 학습**된다. 2) 사전 학습된 모델을 통한 Word representations은 **fine-tuning**등의 과정과 함께 downstream task을 위한 **supervised training**에서 재사용된다.

이러한 사전학습된 언어모델의 한 예가 RoBERTA이다. RoBERTA는 BERT와 같은 트랜스포머기반의 구조를 가지고 있는데 RoBERTA는 masked 언어 모델링 objective에서 학습된다. RoBERTA가 BERT보다 더 좋은 성능을 보여줬기 때문에 이 논문은 RoBERTA를 baseline 모델로 선택하였다.

RoBERTA의 사전학습 말뭉치가 많은 곳에서부터 왔음에도 불구하고 이것이 많은 영역을 커버할 수 있을만큼 충분히 다양한지에 대해서는 확실하지 않다. 달리 말하자면, 이 논문의 저자들은 RoBERTA의 도메인 외의 것들을  이해하고자 한다.

이를 위해서, RoBERTA를 두 가지 카테고리의 unlabeled data를 이용한 continued pretraining을 해서 adaptation하였다.

첫번째 카테고리는 domain-specific text의 큰 말뭉치이고 두번째는 주어진 task와 관련된 unlabeled data이다.

---

## **Domain-Adaptive Pretraining**

이 논문에서의 DAPT에 대한 방법은 직관적이다. RoBERTA를 **unlabeled domain-specific text**의 큰 말뭉치에서 pre-training하는 것이다.

위에서 언급한 4가지 도메인에 대해 실험을 하였다. 아래의 표에서 각 도메인에 대해 사용한 사전학습 말뭉치를 확인할 수 있다.

[##_Image|kage@bBKvgR/btqZlO2uiuM/WB2qN4b2RPoZiCIVQx2maK/img.png|alignCenter|data-origin-width="0" data-origin-height="0" data-ke-mobilestyle="widthContent"|||_##]

### **1> Analyzing Domain Similarity**

DAPT를 하기전에, 이 저자들은 우선 RoBERTA의 pretraining 도메인과 target 도메인간의 유사도를 정량화하고자 하였다. 이를 위해 각 도메인에서 가장 빈번한 unigrams 10000개(stopwords 제외)를 포함한 도메인 사전을 만들었는데, REVIEWS 데이터셋은 데이터들의 길이가 짧기 때문에 150K개의 문서를 사용하였고 다른 세 개의 도메인에서는 50K개의 문서를 사용하였다. 또한 오리지날 사전학습 말뭉치는 공개되지 않았기 때문에 RoBERTA의 사전학습 말뭉치와 유사한 sources에서 50K개의 문서를 샘플링하였다.

이러한 도메인 사전간의 유사도(overlapping)을 아래에서 확인할 수 있다.

[##_Image|kage@Wf2Dy/btqZqo2y3Z2/KknS8B0BUYzfLOCj2fQ8U0/img.png|alignCenter|data-origin-width="0" data-origin-height="0" width="517" height="NaN" data-ke-mobilestyle="widthContent"|||_##]

RoBERTA의 사전학습 domain이 NEWS와 REVIEWS와 큰 overlap을 보임을 확인할 수 있다.

이를 통해 RoBERTA를 다른 도메인에 adaptation하는 이점의 정도를 알 수 있다.

_**더 도메인이 다를수록, 더 DAPT에 대한 잠재력이 높다.**_

### **2> Experiments**

이 논문에서의 LM adaption은 RoBERTA를 학습하는데에 사용된 셋팅을 따라했는데, 각 도메인에 대해 12.5K 스텝 학습하였다. 

저자들은 각 도메인에서의 RoBERTA의 masked LM loss을 DAPT 전과 후에서 각각 보여주었다. 여기서 masked LM loss가 DAPT후에 NEWS데이터셋을 제외한 모든 도메인에서 감소함을 알 수 있다. (NEWS dataset에서는 증가했다고 한다.)

각 도메인에서 2가지의 텍스트 분류 tasks를 고려하였는데, 아래의 표에서 확인할 수 있다.

[##_Image|kage@b8vCiK/btqZywzG6zY/Z95KQ855udcVD5FETLaHh0/img.png|alignCenter|data-origin-width="0" data-origin-height="0" data-ke-mobilestyle="widthContent"|||_##]

논문에서의 tasks는 high resource와 low resource 둘 다를 대표한다.(즉, 두 tasks중 하나는 low resource, 다른 하나는 high resource)

#### **Baseline**

모델의 baseline으로는 기존의 RoBERTA-base model를 사용하였고 supervised fine-tuning을 각 분류 tasks에 맞게 수행하였다. 평균적으로, RoBERTA는 SOTA에 크게 뒤떨어지지 않으며 좋은 baseline이 된다.

#### **Classification Architecture**

전형적인 실험방식을 따르며 저자들은 **final 레이어 \[CLS\] token representation**을 task-specific feed-forward 레이어에 예측을 위해 넣는다.

#### **Results**

테스트 결과는 다음과 같다.

[##_Image|kage@XTQs1/btqZtzjpIBp/Qa5SRGGfmNQaFH5Q3EKzl1/img.png|alignCenter|data-origin-width="0" data-origin-height="0" width="472" height="NaN" data-ke-mobilestyle="widthContent"|||_##]

모든 도메인에서 ROBERTA보다 우수함을 확인할 수 있다.

BIOMED, CS 그리고 REVIEWS 데이터셋에 대해서는 일관된 성능개선을 확인하면서 **target 도메인이 ROBERTA의 source 도메인보다 먼 경우에서의 DAPT의 이점을 확인하였다.** 패턴은 resource에 관계없이 일관된 결과를 보여주었다.

DAPT가 NEWS 도메인의 AGNEWS task에서 더 나은 성능을 보여주지 않았음에도 불구하고, HYPERPARTISAN에서 확인한 이점들은 **DAPT가 심지어 ROBERTA의 source 도메인과 더 밀접한 tasks에도 유용한 것을 확인시켜주었다.**

### **3> Domain Relevance for DAPT**

저자들은 또한 모델의 결과가 단지 데이터를 더 많이 이용해서 얻은 것이 아님을 보여주기 위해 LM을 _**outside the domain of interest**_에도 적용을 하였다. (즉, 관련이 적은 도메인에 adaption.)

위의 데이터셋을 가지고 설명하자면, NEWS dataset에 CS LM, REVIEWS dataset - BIOMED LM, CS - NEWS LM 그리고 BIOMED - REVIEWS LM을 사용하였다. (위의 표에서 "ㄱDAPT" 컬럼이 결과를 나타낸다.)

대부분의 경우에서 DAPT가 ㄱDAPT보다 성능이 우수함을 보였고 ㄱDAPT는 RoBERTA보다 낮은 성능을 보여주었다. 

(관련성이 적은 도메인의 데이터를 사용하는 것이 end-task 성능에 악영향을 끼치는 것을 나타낸다.)

두 가지 경우에서는 RoBERTA보다 나은 점수를 보여주였는데 이는 몇몇의 경우에는 데이터를 추가하여 continued pretraining하는 것이 유용한 것을 나타낸다.(이 부분이 조금 애매모호한 것 같다.)

### **4> Domain Overlap**

DAPT에 대한 분석은 **어떻게 task data가 특정 도메인에 지정되어있는지**에 대한 이전의 직관들을 바탕으로 하였는데, 예를 들어 HELPFULNESS(유용한 리뷰인지에 대해 분류하는 task)에 DAPT를 수행하기 위해 저자들은 AMAZON reviews 데이터만을 적용하고 REALNEWS 기사들에 대해서는 적용하지 않았다.

(직관적으로 당연하다. 리뷰 분류를 위해 리뷰 데이터셋만 사용하는 것이므로...)

그러나, 위의 figure 2의 그라데이션은 도메인간의 경계선들이 어떤 점에서 "fuzzy"한 것을 알 수 있다. 

예를 들어, unigram의 40%은 REVIEWS와 NEWS가 공유한다. 또한 질적으로(qualitatively) 도메인간의 overlapped documents를 확인해보았는데, 아래 Table 4에서 확인할 수 있다.

(왼쪽이 REVIEWS, 오른쪽이 NEWS) -> 사실 이것도 keyword 분석이므로 정량적인 분석이 아닌가 싶다.

[##_Image|kage@k4ubx/btqZrgLde3j/24y16S4sBul7mybfX9dw01/img.png|alignCenter|data-origin-width="0" data-origin-height="0" width="850" height="NaN" data-ke-mobilestyle="widthContent"|||_##]

RoBERTA를 NEWS 데이터에 적용하는 것은 REVIEWS tasks에 적용하는 것보다 악영향을 끼치지 않는다.

비록 이러한 분석이 포괄적이지는 않지만, 이는 도메인간의 차이를 야기하는 요소들이 mutually exclusive하지 않은 가능성이 있음을 나타낸다.(뭔말)

전통적인 도메인 경계선을 넘는 pretraining이 더 효과적인 DAPT를 낳을 수 있다. 일반적으로, 데이터의 출처는 사전학습 과정을 디자인할 때와 일반화 가능성을 테스트하기 위한 새로운 벤치마크들을 생성할 때 중요하다.

---

## **Task-Adaptive Pretraining**

특정 tasks에 맞춰 큐레이트된 데이터셋은 넓은 도메인에서 오직 한 부분만 커버하는 경향이 있는데,

예를 들어, 화학물질과 단백질간의 관계를 추출하기 위해 CHEMPROT 데이터셋은 선택된 PubMed 카테고리의 최근 published된, 영향력있는 기사들의 abstracts만 초점을 맞춘다. 

저자들은 task 데이터가 큰 도메인에서 작은 영역으로 정의된 부분일 경우, task dataset 자체 또는 task와 관련된 데이터에서 사전학습하는 것이 도움이 될 것이라 가정했다.

(즉, 전체에서 작은 부분만을 차지하는 경우 전체 데이터셋에서나 관련 데이터에서 사전학습하는 것이 효과가 있다.)

---

_**Task-adaptive pretraining**_**(TAPT)**는 주어진 task에 대해 unlabeled 학습 set에서 사전학습하는 것

_(Task-adaptive pretraining (TAPT) refers to pre- training on the unlabeled training set for a given task;)_

---

이전의 연구들은 TAPT의 효과를 증명해왔는데, **Domain-adaptive pretraining(DAPT)**와 비교해서 TAPT 방식은 다른 trade-off를 가진다.

#### **TAPT의 trade-off : **훨씬 작은 사전학습 말뭉치를 사용하지만 task와 더 관련이 있다.

(학습 set이 task를 잘 대표하기 때문이다.)

👉🏻이는 TAPT가 DAPT보다 더 계산량이 적게 하며, 실험에서 보였듯이, TAPT의 성능이 DAPT와 견줄만 하게 한다.

---

## **Experiments**

**DAPT와의 유사점** : DAPT와 유사하게 TAPT는 RoBERTA 사전학습의 두번째 단계로 이루어져있지만 사용가능한 task-specific 학습 데이터에 대해서만이다. 

**DAPT와의 차이점** : 12.5K step만큼 학습한 DAPT와는 반대로, TAPT는 100 epochs만큼 학습하였다.

저자들은 인위적으로 각 데이터셋을 무작위로 다른 단어들을 masking 처리**(masking probability of 0.15)**함으로써 늘렸다. DAPT 실험에서와 마찬가지로, final layer \[CLS\] token representation을 분류를 위해 task-specific feedforward layer로 넘긴다.

아래 표의 TAPT column에서 결과를 확인할 수 있다.

[##_Image|kage@bPGzty/btqZLsbWEVG/1RipptlfzzV81YO2C2Oz8K/img.png|alignCenter|data-origin-width="0" data-origin-height="0" width="736" height="NaN" data-ke-mobilestyle="widthContent"|||_##]

TAPT는 일관되게 RoBERTA의 성능을 뛰어넘었는데, 심지어 NEWS 도메인(RoBERTA의 사전학습에서 쓰인)에서도 TAPT의 성능이 더 나았다. 이는 task adaptation의 이점을 보여주는 경우이다. 

**특히 주목할 점은,** TAPT와 DAPT간의 차이이다.

DAPT에 더 resource가 많이 들어감에도 불구하고 TAPT는 몇몇의 task에서 이에못지 않은 성능을 보여주고 DAPT를 뛰어넘는 성능을 보여주기도 하였다.

### **<Combined DAPT and TAPT>**

두 가지 adaptation 방법을 공부하면 자연스럽게 두 방법을 결합하면 서로의 단점을 커버하고 장점을 살릴 수 있지 않을까 생각하게 된다. 이 논문에서도 앞서 설명한 DAPT와 TAPT 두 가지를 합치는 방법에 대해 실험해보았다.

RoBERTA를 가지고 DAPT를 한 후 TAPT하는 방식이다. 사전 훈련의 세 단계가 더해져 앞서 나온 설정 중 가장 cost가 많이 든다. 예상한대로 DAPT+TAP는 모든 tasks에서 가장 좋은 성능을 보여준다.

논문에서의 결과는 DAPT+TAPT가 domain과 task 모두에서 가장 좋은 성능을 보여주었다.

또한, 논문에서는 순서를 반대로 하는 것, TAPT후에 DAPT를 적용하는 것은 task 관련 말뭉치의 치명적인 망각에 취약할 것으로 추측하고 있다. 

이후에 연구는 더 구체적인 domain과 task 분포의 커리큘럼의 사전학습에 대해 다룰 것이다.

(어떻게 분포가 되어있을 때 adaptation의 성능이 좋을 것인가?)

## **<Cross-Task Transfer>**

이 논문에서는 또한 **한 task에 적용시키면 같은 domain의 다른 task로 transfer하는지**를 연구하며 DAPT와 TAPT간의 비교를 진행하였다. 예를 들어, RCT unlabeled data를 사용하여 LM를 사전학습하고 이를 CHEMPROT labeled data로 fine-tuning하는 식이다. (BIOMED 도메인의 두 task)

✔️ 이러한 setting을 **Transfer-TAPT**라고 한다.

4개의 도메인에서의 tasks에 대한 결과는 아래의 표에서 확인할 수 있다.

[##_Image|kage@z66ke/btqZRso8pyf/oBVQiG3ETIScCdrJP7jECk/img.png|alignCenter|data-origin-width="0" data-origin-height="0" data-ke-mobilestyle="widthContent"|||_##]

위의 결과를 통해, TAPT는 하나의 task performance에 대해 최적으로 성능을 내는 것을 알 수 있다.

(cross-task transfer에서는 성능이 떨어짐)

즉, 이 결과는 한 도메인에서도 tasks의 데이터 분포는 다름을 알려준다. 

또한, 왜 한 broad domain으로의 adapting이 충분하지 않은지, 왜 DAPT+TAPT이 효과적인지를 알려준다.

(한 도메인에서도 tasks가 다르면 transfer이 잘 되지 않으니 같은 domain, task 데이터를 가지고 학습해야하는데 데이터가 충분하지 않기 때문에)

---

## **Augmenting Traning Data for Task-Adaptive Pretraining**

직전 section에서 논문은 task adaptation을 위해 supervised task의 학습데이터만을 사용하여 사전학습을 계속하였다.

TAPT의 우수한 성능에 영감을 받아, 다음으로는 또다른 setting을 고려해보았는데, 이는 **task 분포로부터 unlabeled data의 더 큰 pool이 존재하는 경우**이다.(curated by humans)

두개의 시나리오에 대해 조사하였는데,

1) 세 개의 tasks(RCT, HYPERPARTISAN and IMDB)에 대해서 사용가능한 human-curated 말뭉치에서 unlabeled data의 pool을 사용하는 것.

2) TAPT에 관련된 unlabeled data를 큰 unlabeled in-domain corpus로부터 _retrieving 하는 것이다. _

(_extra human-curated data_가 사용불가능할 때)

### **1\. Human Curated-TAPT**

데이터셋 생성은 종종 알려진 sources로부터 큰 unlabeled 말뭉치 collection을 포함한다.

그런 다음 이 말뭉치는 annotation budget에 기초하여 annotation을 수집하기 위해 다운샘플링된다.

그러므로, 더 큰 unlabeled 말뭉치는 task의 학습데이터와 비슷한 분포를 가질 것이라 생각된다. 

task-adaptive 사전학습에서 그러한 말뭉치의 역할에 대해 알아보자.

#### **<Data>**

이 논문에서는 RCT 데이터셋을 500개의 예제로 다운샘플링하는 경우를 고려하였다.(전체는 18만개)

그리고 학습 데이터의 나머지를 unlabeled 데이터로 가정하였다.

또한, HYPERPARTISAN shared task는 low- and high-resource의 두 가지 트랙을 가진다.

저자들은 5천개의 문서들을 high-resource 셋팅으로부터 Curated-TAPT unlabeled data로 사용하고 original low-resource 학습 문서들을 task fine-tuning에 대해 사용하였다.

IMDB에 대해서는 task annotators에 의해 일일이 curated된 추가적인 unlabeled data를 사용하였다.

(labeled data와 같은 분포로부터 뽑아짐)

#### **<Results>**

논문에서는 Curated-TAPT를 TAPT와 DAPT+TAPT에 비교하였다. 아래의 표에서 결과를 확인할 수 있다.

[##_Image|kage@bCyGej/btqZJ5JtLkZ/vV8nIpLwylsWIRaAgXKRHk/img.png|alignCenter|data-origin-width="0" data-origin-height="0" width="678" height="NaN" data-ke-mobilestyle="widthContent"|||_##]

Curated-TAPT는 이전의 결과들보다 모든 3개의 데이터셋에서 더 나은 성능을 보여주었다. 

Curated-TAPT를 DAPT후에 적용하는 것은 모든 tasks에서 가장 큰 boost를 보여주었다.

이 결과를 통해서, 많은 데이터를 task 분포로부터 curating하는 것은 end-task 성능 향상에 큰 영향을 미치는 것을 알 수 있다.

저자들은 또한 task designers에게 **많은 unlabeled task data**를 사전학습에서의 모델 adaptation에 사용하라고 추천한다고 한다.(일단 데이터가 많으면 어찌저찌 방법을 써서 성능이 향상된다는 말이다.)

**_그럼 데이터가 적을 때는 어떻게 해야할까? 아래의 방법을 참고할 수 있다._**

### **Automated Data Selection for TAPT**

위의 방법들과 같이 많은 unlabeled data뿐만 아니라 계산을 위한 resource 또한 충분치않다고 해보자.

논문에서는 큰 도메인내의 말뭉치에서 task 분포와 유사한 unlabeled text를 가져오는 간단한 unsupervised 방법을 제시한다.

이 접근은 text embedding을 통해 task-relevant 데이터를 도메인에서 찾는 방식이다. 그런다음 task data를 사용한 쿼리를 기반으로 도메인으로부터 후보들을 선택하는 방식이다. 

(정리하자면, text embedding을 통해 유사한 분포의 데이터를 뽑아오는 형식이다.)

중요한 점은, 임베딩방식이 reasonable한 시간내에 모든 문장들을 임베딩할 수 있을 정도로 가벼워야한다는 것이다. 

이러한 계산소요 방면에서, 논문에서는 _**VAMPIRE**_라는 간단한 bag-of-words language model를 사용하였다.

task와 domain 모두에서 텍스트 임베딩을 처리하기 위해 VAMPIRE를 큰 중복제거된 도메인의 샘플(1M개의 문장)에서 사전학습을 한다. 그다음 각 task 문장들에 대해 도메인 샘플로부터 k개의 후보를 선정한다.

(한 task sample에 대해 top k개의 domain sample을 선정한다고 생각하면 된다.)

후보들은 두 가지 방식 중 하나를 사용하여 선정된다.

(i) KNN-TAPT(knn)

(ii) RAND-TAPT(random)

이렇게 선정된 후보들을 추가하여 augmented된 말뭉치를 가지고 RoBERTA를 더 학습한다.

#### **<Results>**

결과는 다음과 같다.

[##_Image|kage@5FkrW/btqZPG9xwrI/kSi0exVnEFuoU3QStsymLK/img.png|alignCenter|data-origin-width="0" data-origin-height="0" width="658" height="NaN" data-ke-mobilestyle="widthContent"|||_##]

결과를 통해 knn 방법을 통해 데이터를 추가한 kNN-TAPT가 TAPT을 모든 경우에서 이긴 것을 볼 수 있다.

RAND-TAPT는 오히려 TAPT보다 성능이 낮았다. 

(💫 관련있는 데이터 추가의 중요성!)

하지만 한 표준편차내의 random 선택은 몇몇의 데이터셋에서 성능의 향상을 보여주었다.

k를 증가시킬수록 kNN-TAPT의 성능은 점점 증가했고 DAPT의 성능까지 가까이 갔다.

후의 연구는 kNN-TAPT에 대한 밀도있는 연구가 될 것이다. 또한 다양성과 task 관련성에 대한 tradeoff에 대한 연구 또한 가능하다.

---

## **Conclusion**

논문 실험을 한눈에 정리하자면 다음과 같다.

[##_Image|kage@xg5Q7/btqZLsjSov3/USwOHVuwfyZ32j4VKVWm6k/img.png|alignCenter|data-origin-width="0" data-origin-height="0" width="673" height="NaN" data-ke-mobilestyle="widthContent"|||_##]

---
 

