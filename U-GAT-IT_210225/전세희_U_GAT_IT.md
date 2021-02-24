## **Abstract**

이 논문에서는 unsupervised image-to-image translation의 새로운 방법을 제시한다.

**Attention 모듈**과 새로운 learnable normalization function(**AdaLIN**)을 통합한 방법이라 설명할 수 있다.

각각의 역할은 다음과 같다.

---

#### **✔️ 1. Attention 모듈**

source와 target 도메인을 **어텐션 맵(obtained by the auxiliary classifier)을 기반으로 구분**하면서 모델이 **더 중요한 영역에 집중**하도록 한다. 이전에 어텐션을 사용한 모델들은 도메인 간의 geometric changes를 제대로 다루지 못했는데 이 모델은 이를 극복하였다.

#### **✔️ 2. AdaLIN (Adaptive Layer-Instance Normalization) function**

모델이 유연하게 모양과 재질에서의 changes를 제어하도록 한다. 

---

---

## **Introduction**

**Image-to-image translation의 목표는 서로 다른 도메인의 이미지들을 매핑하는 함수를 학습하는 것이다. **

사용되는 예로는 _Image inpainting, super resolution, colorization, style transfer_등이 있다. 

짝지어진 데이터들이 주어졌을 때, mapping 모델은 conditional generative model나 간단한 regression model을 이용하여 _supervised_로 학습될 수 있다. (라벨로 사용할 수 있으므로)

_Unsupervised_관점에서 즉 데이터들이 짝지어져서 주어지지 않았을 때, 다양한 모델들이 shared latent space(공유된 잠재적인 공간??이라고 해석을 해야하나)와 cycle consistency assumptions 을 사용해서 성공적으로 이미지들을 변환시켰다. 

그러나 이러한 성공들에도 불구하고, 이전의 방법들은 이미지 도메인간의 모양과 재질 차이의 정도에 따라 다른 성능을 보여주었다. 예를 들어, style transfer에 대해서는 성공적이지만 larger shape change(ex. selfi2anime)와 같은 task에서는 성공적이지 않았다.

[##_Image|kage@bR23Oi/btqYgGv2D8u/7YsnKwZnH9Snmxwv83loiK/img.jpg|alignCenter|data-origin-width="0" data-origin-height="0" data-ke-mobilestyle="widthContent"|https://images.app.goo.gl/jF5ayHPkW6WS2suu6||_##]

그러므로, 이미지를 자르거나 정렬을 맞추는 전처리 단계들이 이러한 문제들을 해결하기 위해 필요했다. 

추가적으로, DRIT와 같은 기존 방법들은 **고정된 네트워크 구조와 hyper parameters**를 가지고 모양을 보존하거나 바꾸는 translation 모두에 대해 바람직한 결과들을 얻었다. 

이 논문에서는 앞서 말했듯이 attention module을 이용하여 translation이 더 중요한 영역에 초점을 맞추고 덜 중요한 영역에 대해서는 무시를 하도록 한다. attention map들은 의미론상 중요한 영역에 초점을 맞추기 위해 generator와 discriminator로 임베딩된다. 그러므로 모양 변환을 용이하게 한다.(어쨋든 이미지의 semantic정보를 가지고 있어야 거기서 style이나 해상도등을 바꾸는 것이므로 다른 모델들과 같이 이미지의 semantic 정보가 중요하다.) 

**<Attention>**

Generator와 Discriminator 두 방면에서 각각 설명해보자.

[##_Image|kage@vXlZl/btqYbIBtsmR/OtEJweMsF5kfVltONPDBJ0/img.png|alignCenter|data-origin-width="0" data-origin-height="0" width="532" height="NaN" data-ke-mobilestyle="widthContent"|||_##]

**Generator **

생성자(generator)에서의 attention map은 특히 두 도메인을 구분하는 영역에 초점을 맞춘다.

**Discrimiator**

구분자(discriminator)에서의 attention map은 타겟 도메인의 진짜 이미지와 생성된 이미지(=가짜 이미지)간의 차이에 초점을 맞춤으로써 fine-tuning하는 것을 돕는다.

---

**<Adaptive Layer- Instance Normalization (AdaLIN)>**

Abstract에서도 언급되었듯이, 이 논문은 어텐션 메커니즘에 더해 **새로운 normalization function**을 사용하였다. 이는 변환된 결과에 상당한 영향을 미친다고 나와있다.

_Batch-Instance Normalization(BIN)_에서 영감을 받아 이 논문에서는 AdaLIN이라는 정규화를 제시하였는데 여기서의 파라미터는 _Instance normalization(IN)_와 _Layer Normalization(LN)_간의 적절한 비율을 adaptively 선택함으로써 dataset으로부터 학습된다. 

이 AdaLIN함수는 어텐션 모델이 적절하게 변화의 양을 제어할 수 있게 도와주는데 그 결과 모델의 구조나 하이퍼파라미터를 따로 조정하지 않고서도 모델은 image translation을 수행할 수 있다. 전체적인 변화뿐만 아니라 큰 형태에서의 변화도 일어난다.(이 말이 아직 이해가 안가므로 뒤에서 더 알아보자...)

이 논문에서의 주요 내용은 다음 세가지이다.

1\. 새로운 attetention module과 normalization function(AdaLIN)을 이용하여 Unsupervised image-to-image translation 제시.

2\. 어텐션 모듈은 source와 target 도메인간의 차이를 구분함으로써 어디를 강력하게 변환할 것인지를 모델이 알도록 돕는다. 이는 auxiliary classifier로부터 얻은 attention map을 근간으로한다.

3\. AdaLIN 함수는 어텐션 모델이 유연하게 변화의 양을 제어하도록 돕는다. 모델 구조나 하이퍼파라미터의 수정은 불필요하다.

---

## **UNSUPERVISED GENERATIVE ATTENTIONAL NETWORKS WITH ADAPTIVE LAYER\-INSTANCE NORMALIZATION**

이 논문에서는 각 도메인의 **짝지어지지 않은 데이터**들을 이용하여(unsupervised) source domain $X\_s$의 이미지들을 target domain $X\_t$로 매핑시키는 _**function $G\_{s \\rightarrow t}$을 학습**_시키는 것을 목표로 한다. (s: source, t: target)

논문의 framework는 두 개의 generators $G\_{s \\rightarrow t}$, $G\_{t \\rightarrow s}$ 와 두 개의 discriminators $D\_s$, $D\_t$로 구성되어 있다.

위에서도 언급했듯이 어텐션 모듈을 generator와 discriminator에 통합시키는데 **discriminator에서의 어텐션 모듈은 generator가 진짜같은 이미지들을 만들어내는데 중요한 영역에 초점을 맞추도록 가이드**한다. 한편 generator에서의 어텐션 모듈은 다른 도메인으로과 구별되는 영역에 어텐션을 준다.

### **🌟 1. Model**

#### **1.1  Generator **

$x \\in\\left\\{X\_{s}, X\_{t}\\right\\}$ 가 source와 target 도메인에서의 sample이라 해보자.

이 논문에서의 translation model $G\_{s \\rightarrow t}$ 는 encoder $E\_s$와 decoder $G\_t$ 그리고 auxiliary classifier $\\eta\_{s}$ 로 이루어져있다. 여기의 이 $\\eta\_{s}(x)$는 x가 source domain $X\_s$에서 왔을 확률을 뜻한다.(단어그대로 보조적인 역할을 담당한다.)

$E\_{s}^{k}(x)$ 가 encoder의 k번째 activation map이라 하고 $E\_{s}^{k\_{i j}}(x)$가 $(i,j)$에서의 값이라 해보자. 

Auxiliary classifier, 즉 보조적인 역할을 하는 구분자는 source domain의 k번째 feature map의 weight($w\_{s}^{k}$)를 학습하기위해 훈련된다. 이는 global average pooling과 global max pooling을 사용하여 훈련된다. 

위의 내용을 수식으로 써보자면 다음과 같다.

[##_Image|kage@WUJPG/btqX82Ait6Z/uyw7msE0eef7kWH64ygxhk/img.png|alignCenter|data-origin-width="0" data-origin-height="0" width="406" height="NaN" data-ke-mobilestyle="widthContent"|||_##]

weight($w\_{s}^{k}$)를 얻음으로써, 우리는 도메인에 특화된 어텐션 **feature map** $a\_{s}(x)=w\_{s} \* E\_{s}(x)=\\left\\{w\_{s}^{k} \* E\_{s}^{k}(x) \\mid 1 \\leq k \\leq n\\right\\}$ 을 계산할 수 있다. (여기서 $n$은 encoded feature maps의 수를 뜻한다.)

그러면 우리의 translation model $G\_{s \\rightarrow t}$은, 즉 source domain의 이미지를 target domain 이미지로 바꾸는 함수는 $G\_{t}\\left(a\_{s}(x)\\right)$와 같아진다. 즉, 도메인 특화된 어텐션 feature map을 decoder에 넣어 얻은 이미지가 된다.(encoder-decoder구조 전체가 $G\_{s \\rightarrow t}$ 이므로 encoder를 거쳐 얻은 feature map을 decoder에 넣으면 전체 모델의 output이 된다.)

---

다음으로 _**AdaLIN**_에 대해 살펴보자.

Normalization layers에서 affine transformation 파라미터들을 사용하고 normalization 함수들을 통합한 최근 연구들에 영감을 받아, 이 논문에서는 **residual block**들을 AdaLIN과 통합하였는데 여기서 $\\gamma$와 $\\beta$는 attention map으로부터의 fully connected layer에 의해 계산된다.

affine transformation은 선형대수에서 배우는 개념인데 간단하게 말하자면 점, 직선, 평면을 보존하는 선형 매핑 방법중 하나라고 생각하면 된다.

[##_Image|kage@cuxHwo/btqYiDloKBp/s5kbzNB6kmsLOudW0lxba1/img.png|alignCenter|data-origin-width="0" data-origin-height="0" width="821" height="NaN" data-ke-mobilestyle="widthContent"|AdaLIN||_##]

여기서 ${\\mu\_I}, {\\mu\_L}$ 그리고 ${\\sigma\_I}, {\\sigma\_L}$ 은 각각 channel-wise, layer-wise 평균과 표준편차이다. 또한 $\\gamma$와 $\\beta$ 는 위에서 언급했듯이 fully connected layer에 의해 generate된 파라미터들이고 ${\\tau}$는 learning rate, $\\Delta \\rho$ 는 optimizer에 의해 결정되는 gradient를 가리킨다.

$\\rho$의 값들은 0과 1사이의 범위로 조절되는데 파라미터를 업데이트할 때(위의 세번째식) bound를 부과함으로써 조절된다.

Generator는 instance normalization(IN)이 중요한 task에서는 $\\rho$의 값을 조절하여 1에 가까워지도록 하고, (위 세번째 식에서 clip부분) layer normalization(LN)이 중요한 task에서는 $\\rho$의 값이 0에 가까워지도록 한다.

$\\rho$의 값은 **decoder의 residual blocks에서 1으로 초기화**되고 **decoder의 up-sampling에서는 0으로 초기화**된 task이다.

Content feature를 Style features로 바꾸는 최적의 방법은 **_Whitening and Coloring Transform(WCT)_**를 적용하는 것이지만, covariance 행렬과 역행렬을 계산하는 과정에서의 computational cost가 너무 크다.

_**AdaIN**_은 **feature channels간의 uncorrelation을 가정**하기 때문에 WCT보다 훨씬 빠름에도 불구하고 WCT에 대한 차선책이다.

👉🏻  따라서, 변환된 features는 **content보다 더 많은 패턴을 포함**하고 있다. (feature channels간에 상관이 없음을 가정하고 변환시켰기 때문에)

반대로, Layer Normalization(LN)은 channels간의 uncorrelation을 가정하지는 않지만, 때때로 **LN은 original 도메인의 content 구조를 잘 유지하지 못한다.** (Feature maps에 대해서만 global statistics를 고려하기 때문이다.) 아마 feature map은 기존의 content 정보를 많이 잃어버렸기 때문에 그런듯 하다..

이러한 문제를 해결하기 위해 이 논문에서 제안한 normalization technique인 AdaLIN은 선택적으로 content information을 유지하거나 바꿈으로써, AdaIN과 LN의 장점을 결합시켰다. 즉, AdaLIN = AdaIN + LN이라 할 수 있다. (선택적으로 합친것이니 + 표시는 옳지 않지만 우선 이해를 위해 이렇게 말해두도록 한다.)

이와 같은 선택적인 결합은 큰 범위의 image-to-image translation 문제들을 해결하는데에 도움이 된다.

**1.2  Discriminator **

$x \\in\\left\\{X\_{t}, G\_{s \\rightarrow t}\\left(X\_{s}\\right)\\right\\}$ 가 target domain($X\_t$)과 translated source domain에서의 샘플이라고 하자. 

다른 translation 모델들과 비슷하게 discriminator $D\_t$은 encoder $E\_{D\_t}$, classifier $C\_{D\_t}$, 그리고 보조 classifier $\\eta \_{D\_{t}}$ 로 구성된 multi-scale model이다. 

반대로 다른 translation 모델과 다르게 $\\eta \_{D\_{t}}$와 $D\_t(x)$ 모두 x가 $X\_t$로부터 온건지 $G\_{s \\rightarrow t}\\left(X\_{s}\\right)$로부터 온건지, 즉 만들어내진 데이터인지 구분하기 위해 학습된다.

Sample x가 주어졌을 때, $D\_t(x)$는 $w\_{D\_t}$를 $\\eta\_{D\_t}(x)$에 의해 학습된 encoded feature maps $E\_{D\_t}(x)$이용하여 attention feature maps $a\_{D\_{t}}(x)=w\_{D\_{t}} \* E\_{D\_{t}}(x)$ 을 얻어낸다. 그러므로 discriminator $D\_t(x)$는 $C\_{D\_{t}}\\left(a\_{D\_{t}}(x)\\right)$와 같아진다.

### **🌟 2. Loss function**

이 논문의 모델의 full objective는 4가지의 loss function으로 구성되어 있다.

여기서는, vanilla GAN objective를 쓰는 대신, 안정적인 학습을 위해 **Least Squares GAN objective**를 사용하였다.

#### **1) Adversarial loss**

이 loss는 translated images의 분포를 target image 분포에 맞추기 위해 사용된다.

[##_Image|kage@bytr0O/btqYhyxXqBs/pAgbK3iMUTb0GaGK6GC9A0/img.png|alignCenter|data-origin-width="0" data-origin-height="0" data-ke-mobilestyle="widthContent"|Least Squares GAN||_##]

#### **2) Cycle loss**

또한, 이 논문에서는 **Mode collapse** 문제(같은 이미지를 계속해서 만들어냄)를 완화시키기 위해, cycle consistency constraint를 generator에 적용하였다.

_**Cycle consistency constraint :**_ 한 이미지 x가 주어졌을 때, $X\_s$로부터 $X\_t$, 그리고 $X\_t$에서 $X\_s$로의 순차적인 translation(cycle형태) 후에, 이미지는 성공적으로 원래의 도메인으로 변환되어야 한다.

[##_Image|kage@dBK4PA/btqX6f7L04M/qO8Z9zDH3GD67mgIrkQli0/img.png|alignCenter|data-origin-width="0" data-origin-height="0" data-ke-mobilestyle="widthContent"|||_##]

#### **3) Identity loss**

Input 이미지와 Output 이미지의 컬러 분포가 비슷도록 만들기 위해, **identity consistency constraint**를 generator에 적용하였다.

_**Identity consistency constraint :**_ target domain에서 한 이미지 x가 주어졌을 때, $G\_{s \\rightarrow t}$를 사용하여 x를 변환하였을 때, 이미지는 변하지 않아야 한다.

즉, 이미 target 도메인에 있는 이미지를 target domain으로 보낸다고 해도 달라지지 않아야한다.

[##_Image|kage@cTuAgl/btqYgHvbtPK/Kk94KeIR00zUHLNe5P39k0/img.png|alignCenter|data-origin-width="0" data-origin-height="0" data-ke-mobilestyle="widthContent"|||_##]

#### **4) CAM loss**

보조적인 classifier $\\eta\_s$와 $\\eta\_{D\_t}$로부터 정보를 얻음으로써 한 이미지$x \\in\\left\\{X\_{s}, X\_{t}\\right\\}$가 주어졌을 때 $G\_{s \\rightarrow t}$와 $D\_t$는 개선해야하는 위치와 현재 상태에서 두 개의 도메인간의 차이를 만드는 것이 무언인지 알게 된다.

[##_Image|kage@cJG6SJ/btqYkaXEtmf/b6sDzeFvKx09zOQpcXtXJ0/img.png|alignCenter|data-origin-width="0" data-origin-height="0" data-ke-mobilestyle="widthContent"|||_##]

#### **✔️ Full objective**

마침내 이 objective를 최적화함으로써 이 논문에서의 모델은 encoders, decoders, discriminators 그리고 auxiliary classifiers 모두를 학습시키게 된다.

[##_Image|kage@cLhJ5g/btqYecCnMOg/fWpoPlEdgcJSKIw1CMXNA1/img.png|alignCenter|data-origin-width="0" data-origin-height="0" width="831" height="NaN" data-ke-mobilestyle="widthContent"|||_##]

---

## **Experiments**

**1> Baseline Models**

이 논문에서는 CycleGAN, UNIT, MUNIT, DRIT 등의 다양한 기존 모델들과의 비교를 통해 실험을 하였다.

모든 baseline 방법들은 각 저자의 코드를 이용하여 사용되었다.

**2> Dataset**

데이터셋으로는 5개의 unpaired image datasets(4개는 representative image translation datasets, 1개는 real 사진들과 애니메이션화한 작품들로 구성된 새로운 dataset, i.e. selfie2anime)이 사용되었다. 

모든 이미지들은 학습을 위해 256X256사이즈로 조정되어있다.

**3> Results**

결과 분석으로는 이 논문의 핵심 내용인 attention module과 AdaLIN의 효과를 분석한 뒤 다른 unsupervised image translation model과 퍼포먼스를 비교하는 방법을 사용하였다. 

결과 이미지들의 visual quality를 평가하기 위해 user study를 수행하였는데, 이는 유저들이 5가지 방법으로 변환된 이미지들중 best image를 고르도록 하는 방법이다. 

user study의 결과를 아래에서 확인할 수 있다.

[##_Image|kage@X21h2/btqYxo2usdL/rN8SK7FOhNLM3hYXhmBJt0/img.png|alignCenter|data-origin-width="0" data-origin-height="0" data-ke-mobilestyle="widthContent"|||_##]
