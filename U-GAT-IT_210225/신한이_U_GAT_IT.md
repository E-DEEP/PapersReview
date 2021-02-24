## U-Gat-It Review

## 0.Abstract
새로운 attention 모듈과 Normalization function을 이용해
Unsupervised I2I translation 방법을 제안한다.

------------------
## 1.Introduction
- Image2Image translation 분야
- Domain 간의 shape와 texture 두 특징을 모두 잘 변화시키는 모델을 제안.
- Attention 모듈 적용과 새로운 Normalization function(AdaLIN)을 이용한 새로운 unsupervised I2I translation 방법을 제안
- CAM(Class Activation Map)과 Attention 구조의 적용 => 모델의 구조 변경이나 하이퍼파라미터 변경이 필요x 

1)Attention모듈 : auxiliary classifier를 통해 attention map을 얻는다. 
=> Generator, Discriminator에 임베딩 된다.
G: 두 도메인을 구분하는 부분에 포커싱
D: 타겟 도메인이 실제인지 가짜인지 구분하는 차이에 포커싱

2)AdaLIN
: BIN에 영향 받음.
=> shape와 texture 변화 모두에 대응.
(유연한 shape및 texture 변형이 가능)

------------------
### 2.1.Model 
#### 2.1.1.Generator
- Encoder, Decoder, Auxiliary classifier
- CAM
- encoder를 통해 activation map을 얻는다. classifier는 feature map pooling 을 통해 w를 획득 w와 activation map을 곱해 domain specific attention feature map 획득, 이를 바탕으로 G는 이미지 생성. => 베타값, 감마값 얻음

#### 2.1.2.Discriminator
- Encoder, Classifier, Auxiliary classifier
- 실제 이미지와 가짜 이미지를 예측하는 확률=>classifier&auxiliary classifier
- classifier는 auxiliary classifier를 통해 학습된 w와  인코딩된 feature map의 곱을 이용해 확률을 예측.

### 2.2Loss Function

1) Adversarial loss :translated된 image의 분포를 일치
2) Cycle loss :generator에 cycle consistency constraint 적용
3) Identity loss
:입력 이미지와 출력 이미지의 색상분포를 유사하도록 함.
생성자에 identity cycle consistency constraint 적용
4) Cam loss : auxiliary classifiers ηs and ηDt에서 정보를 추출.
------------------
## 3.Experiments
### 3.1.Baseline model
: CycleGAN, UNIT, MUNIT, DRIT, AGGAN, CartoonGAN
### 3.2.Dataset
: 이미지는 256x256 사이즈로 고정
#### 3.3.1.CAM Analysis
![스크린샷 2021-02-24 오후 11 50 31](https://user-images.githubusercontent.com/50253860/109019661-88916c00-76fc-11eb-8e9f-842957b5f40e.png)

#### 3.3.2.AdaLIN Analysis
![스크린샷 2021-02-24 오후 11 50 44](https://user-images.githubusercontent.com/50253860/109019578-77e0f600-76fc-11eb-992d-a3f046ad224f.png)

#### 3.3.3Qualitative Evaluation
![스크린샷 2021-02-24 오후 11 51 07](https://user-images.githubusercontent.com/50253860/109019482-64ce2600-76fc-11eb-9896-9ffa9fb7bd5c.png)

------------------
## 4.Conclusions
- U-GAT-IT 모델을 제안.
- 다양한 데이터셋에서 만족스러운 결과를 생성.
- 고정된 네트워크 아키텍처, 고정된 하이퍼 파라미터
- Unsupervised I2I translation task에서 GAN 기반 모델.
