## **Abstract**

최근 연구들은 image-to-image translation에서 큰 성공을 보여주었다.

하지만, 기존 연구들은 2개이상의 도메인을 다루는데에 _scalability_와 _robustness_를 제한해왔는데, 이는 서로 다른 모델들이 각 이미지 도메인 쌍에 대해 독립적으로 만들어졌기 때문이다. 이 한계점을 극복하기 위해 _**StarGAN**_이 등장했다.

_**🌟StarGAN**_은 image-to-image translations을 수행할 수 있는 새롭고 scalable한 접근으로, **단 하나의 모델**을 사용하여 여러가지 도메인에 대해 image-to-image translation을 할 수 있다.(..!) 🌟

이러한 StarGAN의 특징은 하나의 네트워크안에서 다양한 도메인의 데이터셋에 대해 동시에 학습시키는 것을 가능하게 한다. 이 점이 StarGAN이 기존모델에 비해 유연하게 이미지를 translate하게 만들어 우수한 퀄리티를 만들어낸다. 

논문 뒷부분에서 이러한  StarGAN의 우수성을 얼굴 표정변화와 얼굴 특징 변화 tasks를 통해 보여준다.

---

## **Introduction**

_**Image-to-image translation이란?**_

주어진 이미지의 특정 모습을 다른 모습으로 바꾸는 것. 예를 들면, 얼굴 표정을 웃는 모습에서 찌푸리는 모습으로 바꾸는 것이 있다. 이러한 표정 변화 분야는 GAN이후로 상당히 발전이 되어 왔다. 이외에도 머리 색 바꾸기나 선으로 표현된 그림을 사진처럼 바꾸는 task들이 있다.

두개의 다른 도메인에서 학습 데이터가 주어졌을 때, StarGAN은 **이미지를 한 도메인에서 다른 도메인으로 바꾸는 것을 학습**한다. 

**_<용어 정리>_**

_**1\. attribute** :_ 이미지에 있는 의미있는 특징들을 뜻하는데, 대표적으로는 머리색, 성별이나 나이등이 있다. 

_**2\. attribute value** :_ attribute의 값을 의미하는데 예시로는 머리색의 경우에 흑발/금발/갈색머리, 성별에는 남자/여자가 있다. 

_**3\. domain** : _같은 attribute value를 공유하는 이미지들의 집합을 칭한다. 예를 들면 여성의 이미지들은 하나의 domain을 구성하고 남성의 이미지들은 또 다른 domain을 구성한다.

아래는 **CelebA 데이터셋**을 이용한 예시이다.

<p align="center">
  <img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbjgZ7B%2FbtqURuHc36L%2FoseF74UheULgpb7DElJOik%2Fimg.png" />
</p>

이러한 라벨링 데이셋을 이용한 multi-domain image translation은 기존의 모델에서는 비효율적이고 효과적이지 않다. k개의 도메인사이에서의 모든 매핑들을 학습하기 위해서는 k(k-1)개의 generators가 학습되어야 하기 때문이다.

아래의 (a)는 이를 나타낸 그림이다. 4개의 다른 도메인들 사이에서 이미지를 translation 시키기 위해서는 4\*(4-1) = 12개의 네트워크가 필요하다. 또한,  각 데이터셋이 부분적으로 라벨링되어 있기 때문에, jointly training이 불가능하다.


<p align="center">
  <img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fzxfi4%2FbtqUYGTEut6%2FDS9kTSWYYQCsS86lGGoFZk%2Fimg.png" />
</p>


이러한 문제들을 해결하는 모델이 바로 이 논문에서의 StarGAN이다. 위의 (b)에서 볼 수 있듯이 StarGAN은 모든 가능한 도메인들사이의 매핑을 하나의 generator(G)를 통해 학습한다. 

고정된 translation, 예를 들면 흑발에서 금발로 바꾸는 translation을 학습시키는 대신에, 이 single generator는 **이미지와 도메인 정보를 모두 인풋으로 집어넣고** 유연하게 이미지를 알맞은 도메인으로 바꾸는 것을 학습한다. 이러한 도메인 정보들을 표현하는데에는 **binary나 one-hot vector**와 같은 형식을 사용하였다.

학습하는 동안 **랜덤하게 타겟 도메인 라벨을 만들어내고** 모델이 유연하게 이미지를 타겟 도메인으로 변환하도록 학습시켰다. (GAN에서 generator가 이미지를 만들어내는 맥락) 이를 통해, 도메인 라벨을 제어할 수 있고, 이미지를 원하는 도메인으로 변환시킬 수 있다.

이에 더해, **mask vector**를 도메인 라벨에 추가함으로써, joint 학습이 가능하도록 하였다. 모델이 모르는 라벨을 _무시_할 수 있고 특정 데이터셋의 라벨들에 초점을 맞출 수 있다. 이러한 맥락에서, 모델은 얼굴 표정합성과 같은 task를 잘 수행할 수 있다.

---

정리해보자면 이 논문에서의 내용은 다음과 같다.

**1\. StarGAN : 하나의 generator, discriminator만을 이용하여 multiple domains간의 매핑을 학습.**

**2\. 다양한 데이터셋 간의 multi-domain image translation을 mask vector method를 통해 학습한 방법**

**3\. 얼굴 특징 변환과 같은 task에 대한 결과들.**

---

## **Star Generative Adversarial Networks**

이 섹션에서는 StarGAN을 설명하고 이 모델이 어떻게 다양한 데이터셋을 통합하여 유연하게 image translation을 했는지에 대해 다룬다. 

### **    1. Mutli-Domain Image-to-Image Translation**

     original GAN과 같은 맥락으로 목표는 여러 도메인간의 매핑을 학습하는 single generator G를 학습시키는 것이다.

     이를 위해서, 인풋 이미지 x를 타겟 도메인 라벨 c의 조건에서 output image y로 변환시키도록 G를 학습한다.

<p align="center">
  <img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbJUyof%2FbtqU0x9Kmak%2Fr1rZUgj5EHJh1KHUEALYF0%2Fimg.png" />
</p>


    랜덤하게 target 도메인 label c를 만들어내어 G가 유연하게 이미지를 변환시키도록 한다. 또한 auxiliary classifier를 통해 하나의         discriminator가 sources와 domain labels에 대해 확률 분포를 만들어내도록 한다.

<p align="center">
  <img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FeeF3qj%2FbtqUTmWcVVZ%2FbuQKnZfkSWsHnzxZeEdVyK%2Fimg.png" />
</p>

<p align="center">
  <img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FcxCL1H%2FbtqUWt1zqEp%2FEfTqePMEla4lq72GPEpO20%2Fimg.png" />
</p>


## _다음으로 Loss function들을 살펴보자._

#### **<Adversarial Loss>**

 만들어진 이미지가 진짜 이미지와 구분되지 않도록 만들기 위해, original GAN과 마찬가지로 adversarial loss를 이용한다.

<p align="center">
  <img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbkVCMZ%2FbtqUYGlOGt5%2FhdbRdQoFgcRItHAskkBy5k%2Fimg.png" />
</p>

generator G가 image G(x,c)를 만들어내고 D는 진짜와 가짜 이미지들을 구분하는 역할을 한다. 여기서 D\_src는 D에 의해 주어진 sources에 관한 확률 분포이다. G는 위의 loss 함수를 최소화하고자 하고 D는 최대화하고자 한다.(original GAN과 같음)

#### **<Domain Classification Loss>**

주어진 인풋 이미지 x와 타겟 도메인 라벨 c에 대해 목표는 x를 타겟 도메인 c로 분류된 output image y로 변환하는 것이다. 예를 들자면 여성 이미지 x를 타겟 도메인 라벨 c(남성)으로 변환하는 것이다. 이러한 조건을 만족시키기 위해 auxiliary classifier를 discriminator D위에 추가하고 도메인 분류를 한다.

즉, loss를 두 가지로 나누는데,

1) 첫번째는 **D**를 최적화하기 위해 사용되는 _진짜 이미지_들에 대한 도메인 분류 loss이다.

<p align="center">
  <img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbiDBnL%2FbtqUTmBTadx%2FFk2MImODh6xc0a711arUu0%2Fimg.png" />
</p>

이 함수를 최소화하기 위해서, D는 진짜 이미지 x를 original 도메인 c'에 분류하는 것을 학습한다.

(즉, D\_cls(c'|x)가 1에 가까워지도록 학습)

2) 두번째는 **G**를 최적화하기 위한 _가짜 이미지_들에 대한 도메인 분류 loss이다.

<p align="center">
  <img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FefaTkC%2FbtqU0xIF9Vm%2FFgxbHTTHrqsAhuwSbkKZY0%2Fimg.png" />
</p>

위를 최소화하기 위해 D\_cls(c|G(x,c))를 1에 가까워지도록 G를 학습한다.

#### **<Reconstruction Loss>**

_Adversarial loss_와 _classification loss_를 최소화하기 위해, **G**는 진짜같은, 올바른 타겟 도메인에 분류되는 이미지들을 만들어내도록 학습된다. 하지만, 위의 Loss를 최소화하는 것은 변환된 이미지가 인풋 이미지들의 내용을 보존한다는 것을 보장하지 않는다. 이러한 문제를 완화하기 위해서, 이 논문에서는 generator에 **"Cycle consistency loss"**를 적용하였다. 

<p align="center">
  <img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FdBg6sy%2FbtqUZuel1Js%2F1XNHGx89zUN1sTbvvIg711%2Fimg.png" />
</p>

Generator G는 변환된 이미지 G(x,c)와 오리지날 도메인 라벨 c'를 인풋으로 하고, 오리지날 이미지 x를 다시 생성해내도록 시도한다. **(Reconstruction)**

여기서 L1 norm을 사용하였다. 하나의 generator를 두번 사용하는데 첫번째는 오리지날 이미지 인풋 x를 타겟 도메인으로 변환시킬 때이고, 두번째는 변환된 이미지를 오리지날 이미지로 reconstruct할 때이다.(Encoder-Decoder와 같은 역할.)

#### **<Full objective>**

위에서 나온 loss들을 모두 정리해보면 다음과 같은 식을 얻는다.


<p align="center">
  <img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FD4AkJ%2FbtqUYgAS09Z%2FitVKenXytXsXMcV2q4SLC1%2Fimg.png" />
</p>


<p align="center">
  <img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FO3ayT%2FbtqUOCrYyfj%2FjYcAazXkQRRKKRHGOLvc0K%2Fimg.png" />
</p>


<p align="center">
  <img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FxfTnY%2FbtqUWs9rhgu%2F91AktyMf8c4drqk0gnSxZ0%2Fimg.png" />
</p>


하이퍼 파라미터인 lambda cls와 lambda rec를 통해 (도메인 분류 loss + reconstruction loss)/ adversarial loss 간의 중요도를 조절한다. 

### **2. Training with Multiple Datasets**

StarGAN의 중요한 장점중의 하나는 다른 라벨들을 가지고 있는 여러개의 데이터셋을 동시에 처리할 수 있다는 점이다.

그러나, 여러개의 데이터셋으로부터 학습시킬 때, **각 데이터셋에서의 라벨 정보가 부분적으로만 알려져있다는 것이 문제점이다. **

예를 들어, CelebA나 RaFD 데이터셋에서 CelebA는 머리색과 성별과 같은 라벨을 포함한 반면, 행복, 분노와 같은 표정에 관한 라벨은 가지고 있지 않다.

즉, **모든 데이터셋이 동등하게 라벨을 가지고 있는 것이 아니라 어떤 데이터셋은 특정 라벨만 가지고 있고 다른 데이터셋은 또 그 데이터셋만의 라벨을 가지고 있다는 것이다.**

(변환된 이미지 G(x,c)에서 Input image x를 Reconstructing 하는 과정에서 라벨 벡터 c'에 대한 완전한 정보가 필요하기 때문에 문제가 된다.)


<p align="center">
  <img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FdBg6sy%2FbtqUZuel1Js%2F1XNHGx89zUN1sTbvvIg711%2Fimg.png" />
</p>


### **<Mask vector>**

위의 문제점( 라벨을 부분적으로만 가지고 있는 문제 )를 완화시키기 위해서, 이 논문에서는 _**mask vector m**_를 제안한다. 이 mask vector는 StarGAN이 **특정화되지 않은 라벨들을 무시**하고 특정 데이터 셋에서 존재하는 확실히 알려진 라벨에 초점을 맞추도록 한다.

(예를 들어, CelebA의 데이터셋에서 행복과 같은 라벨은 무시하고 머리색과 같은 라벨에 초점을 맞추도록 함.)

StarGAN에서는 _**mask vector m**_을 표현하기 위해 **n차원의 one-hot vector**를 사용하는데 n은 데이터셋의 수를 뜻한다.

또한, label의 통합된 버전을 다음과 같이 정의한다.


<p align="center">
  <img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbE3hnQ%2FbtqUWtN3JHW%2FbMyBmSly6vqkjEazjyLrk1%2Fimg.png" />
</p>



c\_i는 i번째 데이터셋의 라벨에 대한 벡터를 뜻한다. 알려져 있는 라벨의 벡터 c\_i는 binary attributes에 대해서는 binary vector로 표현될 수 있고, 카테고리 attributes에 대해서는 one-hot bector로 표현될 수 있다. 

남은 n-1개의 알려지지 않은 라벨에 대해서는 zero값으로 지정한다. 

이 논문 실험에서는 CelebA와 RaFD dataset을 사용하였으므로 n=2가 된다.

### **<Training Strategy>**

Training과정에서는, domain label


<p align="center">
  <img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbE3hnQ%2FbtqUWtN3JHW%2FbMyBmSly6vqkjEazjyLrk1%2Fimg.png" />
</p>



를 generator의 인풋으로 사용하였다. **이를 통해 generator는 알려지지 않은 라벨에 대해서는 무시를 하게 된다.**(zero vectors이기 때문에) 또한, 확실하게 주어진 라벨에 초점을 맞추어 학습하게 된다. generator의 구조는 input label의 차원을 제외하고는 하나의 데이터셋에 대해 학습할 때와 같은 구조이다. 

반면, 이 논문에서는 discriminator의 auxiliary classifier를 확률 분포를 만들어내기 위해 확장시켰다. 그 후 다양한 task에 대한 학습을 하였고 여기에서 discriminator는 **classification error만을 최소화**하게 된다.

예를 들어, CelebA 데이터셋 이미지에 대해 학습할 때에는 disciminator가 CelebA attributes(ex. 머리색, 성별)에 대해서만 classification error를 최소화하게 되고 RaFD의 표정과 같은 특징들은 무시한다.

이러한 세팅에서, 다양한 데이터셋을 번갈아가며 학습하며 **discriminator**는 모든 데이터셋에 관한 discriminative 특징들을 학습하고 **generator**는 모든 라벨들을 제어하는 것에 대해 학습하게 된다.

---

## **Implementation**

### **Improved GAN Training**

학습 과정을 안정화시키고 더 좋은 퀄리티의 이미지들을 만들어내기 위해 이 논문에서는 Eq(1)을 **gradient penalty**와 함께 **Wasserstein GAN**으로 대체하였다. 


<p align="center">
  <img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbLvujA%2FbtqU0ygxmCi%2FTwvIKjAFhKFAekFQapleQk%2Fimg.png" />
</p>

[##_Image|kage@bLvujA/btqU0ygxmCi/TwvIKjAFhKFAekFQapleQk/img.png|alignCenter|data-origin-width="0" data-origin-height="0" width="649" height="NaN" data-ke-mobilestyle="widthContent"|||_##]

### **Network Architecture**

StarGAN은 두 개의 convolutional layers로 구성된 (stride size of 2 for downsampling, & 6 residual blocks and 2 transposed convolutional layers with the stride size of 2 for upsampling.) **generator network**을 가진다. 

또한 generator에 instance normalization을 사용하고 discriminator에는 사용하지 않는다.

---

## **Experiments **

**On RaFD,**

<p align="center">
  <img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FdGJiEw%2FbtqURvfaThi%2FBgCyeZ8Kq6AMl6rlmxGbY0%2Fimg.png" />
</p>

**On CelebA+RaFD,**


<p align="center">
  <img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2F8JEsC%2FbtqUWtmXBa8%2FIR4eNPtSJG0oz0M9rvEcF1%2Fimg.png" />
</p>



