* paper: Learning Transferable Cisual Models From Natural Language Supervision
(Alec Radford at al, submitted on 26 Feb 2021)
* code: https://github.com/OpenAI/CLIP
* [CLIP]: Connecting Text and Images 
--------------------
## 0. Abstract

OPEN AI는 'DALL-E'와 'CLIP'이라는 두 가지 새로운 인공지능 훈련 모델을 새로 개발.
언어와 이미지를 결함해 인공지능이 단어와, 그 단어가 무엇을 가리키는 지를 더 잘 이해하도록 한다. 

+ CLIP: 
  + 특징은 인터넷에서 가져온 이미지와 그 설명으로 이미지 인식을 훈련한다는 것. 
(한 단어짜리 라벨x, 이미지에 대한 설명o)
  + CLIP은 GPT-3과 마찬가지로 추가적인 훈련 없이도 여러 성격의 과제를 일반적으로(generally) 수행.
  + 변형된 데이터가 주입되어도 잘못된 결과를 내놓을 확률이 비교적 낮다.

+ DALL-E: 
  + 짧은 자연어 캡션(=설명)을 갖고 이에 상응하는 Image들을 생성. (텍스트-이미지 생성기)
  + 엉뚱한 텍스트에서도 합성 이미지를 생성해내는 능력.
  + ex. CLIP이 고른 32가지 예시
  + 한계: 너무 많은 객체를넣으면 갈피를 못잡는다. 새로운 이미지인가 모방인가.


> SOTA 비전 시스템은 미리 결정된 카테고리에 대해 훈련된다.

   이 제한된 형태는 일반성(generality)와 유용성(usability)를 제한한다. 또, 라벨링된 데이터를 지정해야한다.

> 텍스트(raw text)로부터 바로 학습하는 것은 supervision의 source에서 유망한 대안이다.

  인터넷에서 400 milion pairs 이상 수집된 (image,text)로 간단히 사전훈련.  => 어떠한 캡션이 어떠한 이미지에 붙여지는 지를 예측.

> 자연어는 학습된 visual concpets(묘사)를 참고하는 데에 사용.
> 검증을 위해 30개의 다른 datasets 사용
> downstream task로 zero-shot transfer을 가능하게 함.
 
-----------------------
## 1. Introduction and Motivation Work

![1-2](https://user-images.githubusercontent.com/50253860/114727588-4a6a0d80-9d79-11eb-9d75-e967dfe8e68b.png)
- approach는 다음과 같다.
(1) Contrastive pre-training
(2) Create dataset classifier from label rext
(3) Use for zero-shot prediction

- 기존의 raw rext로부터 바로 사전학습을 하는 방식은 혁신.
- 비교 예시) GPT-3의 경우 특수한 학습데이터 필요없이 많은 task에서 고성능 보임.
- 자연어를 이미지 learning에 사용하는 것은 드물다. 
- zero-shot learning와 weakly supervised approach 방법 간의 차이를 줄였다.
- CLIP은 기존의 task-specific supervised model들과 성능이 비슷하거나 더 좋음.

-----------------------
## 2. Approach
### 2.1. Natural Language Supervision

- 다양한 이미지 분류 데이터셋에서 경쟁력 있는 제로 샷 성능을 달성하려면, 
간단한 사전 훈련 작업을 확장하는 것이 충분하다는 것을 보여줘야 함.
- CLIP 모델이 이미지에서 다양한 시각적 개념을 인식하고 이름과 연결하는 방법을 배워야 함.
- 자연어를 이용해서 학습했을 때
(1) image classification에 사용된 라벨링에 비해 scaling이 쉽다.
(2) 언어에 대한 representation도 학습한다. 

### 2.2. Creating a Sufficiently Large Dataset
- 400 million 개의 (image, text) pairs

### 2.3. Selecting an Effifient Pre-Training Method
### 2.4. Choosing and Scaling a Model

-----------------------
## 3. Experiments
1) Zero-shot Transfer

2) Using CLIP For Zero-Shot Transfer


## 6. Limitations

NLP에서의 웹 스케일 사전 교육의 성공이 다른 domain으로 바뀔 수 있다.
충분한 규모로, 이 제품의 성능 접근방식은 task-specific supervised models와 경쟁할 수 있다.
모델들은 개선될 여지가 많다.











