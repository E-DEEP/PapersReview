# VideoBERT: A Joint Model for Video and Language Representation Learning

## 1. Introduction
    
### Problem
 : 기존의 self-supervised learning들은 texture와 같은 low level feature, 혹은 1-2초 정도의 짧은 temporal scale만
 학습하였다.


### Contribution

  : 본 논문에서는 더 오랜 시간, 몇 분 정도의 시간 지속되는 motion 혹은 이벤트들을 배울 수 있는 모델을 만들어보고자 한다. 이런 모델을 만들기 위해
인간이 vision 데이터 뿐만 아니라 언어를 같이 사용해서 학습한다는 점을 바탕으로 언어와 이미지 도메인의 관계를 학습할 수 있는 모델을 만들었다. 다음과
같은 3가지 모델을 합쳐서 VideoBERT를 만들었다. 

1. Automatic Speech Recognition (ASR) system: 음성 데어터를 text 데이터로 만들어줌
2. Vector Quantization (VQ) : pretrained된 비디오 분류 모델에서부터 나온 low-level spatio-temporal visual features에 적용됨
3. BERT : token들을 바탕으로 joint distribution을 학습함

 궁극적으로 VideoBERT는 visual words x와 spoken words y를 넣은 model, p(x,y)를 BERT를 사용해 만드는 것이다.
 이런 모델은 text-to-video prediction 테스크 같은 task도 수행이 가능하다. 뿐만 아니라 video captioning도 가능하고 좋은 성능을 보인다. 
VideoBERT를 unimodal처럼 사용하면, p(x)처럼 사용할 수 있는데, 이는 미래를 예측하는 모델로 사용이 가능하다. 




    








