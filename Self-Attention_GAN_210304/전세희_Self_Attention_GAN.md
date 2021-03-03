

## **Abstract**

이 논문에서는 Self-Attention Generative Adversarial Network, 줄여서 **SAGAN**에 대해 다룬다.

이 모델은 **1) attention-driven, 2) long-range dependency modeling**을 이용했다고 한다.

전통적인 convolutional GAN은 고해상도의 디테일을 저해상도 feature map에서의 부분적인 local points의 함수로만 만들어낸다. 하지만, SAGAN에서의 디테일한 부분들은 **모든 feature locations**으로부터의 cue(신호? 큐사인)을 이용해서 만들어내진다. ( 즉, 디테일한 부분 생성이 이미지의 모든 특성들을 이용해서 만들어진다는 뜻 )

거기다가, _discriminator_가 이미지의 멀리있는 부분의 디테일들이 서로 일치하다는 것을 확인할 수 있다. (long range dependency와 같은 맥락)

더욱 더, 최근 연구들이 보여준 바로는, _generator_ conditioning은 GAN의 성능에 영향을 미치는데 이러한 관점을 이용하여 이 논문에서는 **spectral normalization**을 GAN _generator_에 적용하고 이는 학습에 효과가 있음을 밝혀냈다.

이 논문에서의 SAGAN은 기존 모델들보다 성능이 우수한데 이는 ImageNet dataset에서 best published Inception score를 36.8에서 52.52로 올리고, 그리고 Frechet Inception distance를 27.62에서 18.65로 줄였다.

Attetntion 레이어의 시각화는 generator가 고정된 모양의 local region보다 object모양과 일치하는 주변정보들을 이용한다는 것을 보여준다. 

---

## **Introduction**

이미지 합성은 computer vision에서 중요한 문제인데, 많은 open problems이 여전히 남아있지만 GAN이 등장하면서 많은 진보가 되어왔다. 특히, **Deep convolutional network**을 베이스로한 GAN은 특히 성공을 보여줘왔다.

**그러나, ** 이러한 모델들(Deep CNN 기반)로부터 만들어진 샘플들을 주의깊게 조사해본 결과, convolutional GAN은 ImageNet과 같은 multi-class 데이터셋에 훈련된 다른 모델들보다 몇몇의 이미지 classes를 모델링하는데에 어려움을 겪어왔다. 예를 들어, SOTA ImageNet GAN 모델이 몇몇의 구조적인 제한(기하학적 특징보다 텍스쳐로 더 구분되는 바다, 하늘과 같은 풍경 클래스들)을 가지고 이미지 클래스들을 합성하는데 특출난 반면에, 몇개의 클래스에서 일정하게 생기는 기하학적 또는 구조적인 패턴을 잡아내는데에는 좋은 성능을 보여주지 않는다. 예를 들어, 개들은 진짜같은 털 질감으로 그려지지만 명확하게 구분된 발을 가지기는 어렵다. 즉, DeepCNN기반의 GAN은 기하학적 패턴을 잘 못잡아낸다. 전에 예시들 보았을 때도개의 다리가 비정상적으로 나오는 예들이 많았다.

이에 대해 가능한 설명으로는 이전의 모델들이 **다른 이미지 영역간의 dependency**를 모델링할 때에 **convolution에 크게 의존**했다는 것이다. Convolution operator가 local receptive field를 가지기 때문에, long range dependency는 오직 몇개의 컨볼루션 레이어를 지난후에만 처리될 수 있다.

아래의 예시를 보면 이해할 수 있는데, 특정 window에서의 convolution이 일어나기 때문에 다른 영역과 결합(?), dependency를 처리하려면 몇개의 층을 지나야 가능하다. (1,1)에서의 픽셀이 (5,5)의 픽셀과 계산되려면 layer 3에서 가능하다.

[##_Image|kage@cGivvM/btqYZ7lNuCM/gJEAzlNPHSc7xrXorz5b40/img.png|alignCenter|data-origin-width="0" data-origin-height="0" width="390" height="NaN" data-ke-mobilestyle="widthContent"|||_##]

이는 long-term dependency를 학습하는 것을 여러 이유로 방해한다.

(transformer와 같이 attention을 사용하는 이유 중 하나가 long-term dependency를 학습할 수 있다는 장점이기 때문에 long-term dependency라는 용어가 attention을 사용하는 모델에서 계속해서 등장한다.)

---

1) 작은 모델은 long-term dependency를 제대로 얻을 수 없다. 즉, 통과할 수 있는 layer수가 적기 때문에 먼 거리의 픽셀과의 dependency가 떨어진다.

2) 최적화 알고리즘이 파라미터 값들을 찾아내는데에 어려움을 겪도록 하는데 여기서 파라미터 값들이란 이러한 dependency를 잡아내는 여러 레이어들을 조정하는 파라미터 값들을 뜻한다. 즉 loss function을 최적화하는 알고리즘이 제대로 파라미터를 조정하지 못하게 한다. 

3) 이러한 매개 변수화가 통계적으로 취약할 수 있으며 새로운 input에 적용될 때 제대로 작동하지 않게 된다.

---

Convolution 커널의 사이즈를 증가시키는 것은 네트워크의 representational capacity(이미지를 잘 represent하는 능력)을 증가시킬 수 있지만, 또한 computational과 local 컨볼루션의 구조를 사용함으로써 얻는 통계적인 효율성을 떨어뜨린다.

(커널 사이즈가 작으면 세세하게 이미지의 부분을 이용하므로 제너럴하게 이미지를 represent하는 능력이 떨어지므로 커널 사이즈가 커지면 representational capacity를 얻는다. 하지만, 당연히 계산 효율성이 떨어지게 된다. 요약하자면 deep CNN기반의 GAN은 이러한 trade-off가 있다는 것.)

반대로 Self-attention은 long-range dependency를 모델링하는 능력과 compuational and statistical 효율성간의 더 나은 발란스를 보여준다.

Self-attention 모듈은 한 위치에서의 반응을 **모든 위치에서의 features의 weighted sum**으로 계산한다.

(참고로, 여기서 weights나 attention vectors을 계산하는데에는 매우 작은 cost만 필요하다.)

이 논문에서는 self-attention을 convolutional GAN에 적용한 Self-Attention GAN를 제안한다. Self-attention 모듈은 convolution에 상호보완적이고 이미지 영역을 가로질러 long range, multi-level dependency를 모델링하는데에 도움을 준다.

Self-Attention를 갖춘 generator은 모든 위치에서의 작은 디테일이 **멀리 있는 디테일과 조화되는 이미지**를 그릴 수 있다.

또한, discriminator는 전체 이미지 구조에서의 더 정확하게 복잡한 기하학적인 제한들 강화시킨다.

(앞에서 언급했듯이, deep CNN기반의 GAN은 기하학적 패턴을 잘 못잡아내는데 SAGAN의 discrimator는 이런 제한들을 통해 더 가짜이미지를 잘 구분해내서 결국에는 generator가 더 기하학적 패턴을 잘 잡아내는 이미지들을 생성하도록 한다.)

Self-attention에 더해, 이 논문에서는 최근의 _network conditioning_과 관련된 인사이트들을 GAN에 통합시켰다.

이전의 한 모델은 well-conditioned generator이 더 성능이 좋다는 것을 보여주었다. 

SAGAN 저자들은 기존에 discriminator에만 적용해왔던 **Spectral normalization**을 generator에도 적용시켰다.

위의 제안된 self-attention 메커니즘과 안정화 테크닉의 효과를 입증하기위해 ImageNet 데이터셋에 대해 수행하였는데, 그 결과, SAGAN은 **이전 이미지 합성에 관한 연구들을 크게 뛰어넘는 성능을 보여주었다**.‼️‼️‼️

## **Related Work**

Self-Attention + GAN에 관한 논문이니 이 두가지에 대해 간단하게 짚고 넘어가 봅시다.

### **GAN **

GAN은 많은 image-to-image translation, image super-resolution 그리고 text-to-image 합성과 같은 이미지 생성 tasks에서 큰 성공을 보여준것이 사실이다. 하지만 이런 성공들에도 불구하고 GAN을 학습하는 것은 불안정할뿐만 아니라 hyperparameter에 민감하였다.(사실 실제 GAN을 훈련해본 결과 그리 좋은 결과를 보지 못했다. 주변에서 들은 경험들에서도 그렇고...)

몇몇의 연구들은 이를 위해 새로운 네트워크를 디자인하고 학습 objectives와 dynamics등을 수정하고 정규화 과정을 추가하는 등의 GAN을 안정화시키는 작업을 시도했다. 

최근, 한 논문에서는 discriminator 함수의 Lipschitz constant를 제한하기 위해 discriminator에서의 weight 행렬의 _**spectral norm(= L2 norm, 즉 the largest singular value)**_을 제한시키는 방법을 제안하였다.

**Projection 기반의 discriminator**와 결합하여 spectrally normalized model은 ImageNet 데이터셋에서 크게 class-conditional image 생성을 개선시켰다.

---

projection discriminator는 아래의 그림의 (d)를 보면 이해가 갈텐데

[##_Image|kage@bGV3u3/btqZeg3YiMp/lhhkUkXYbQOLL9BhW4TJMk/img.png|alignCenter|data-origin-width="0" data-origin-height="0" data-ke-mobilestyle="widthContent"|CGANS with Projection Discriminator||_##]

Discriminator가 image가 진짜인지 가짜인지 분류할 때 inner product(projection 과정)를 통과하여 분류되는 방법이다.

---

### **Attention**

최근 어텐션 메커니즘은 global dependency를 확보하는데에 거의 필수적인 파트라고해도 무방하다. 특히, self-attention(=intra-attention)은 같은 sequence안에서의 모든 위치에 참여함으로써 한 위치에서의 response를 계산한다.

아래는 트랜스포머의 모델 구조인데 self-attention이 트랜스포머의 핵심이다.

[##_Image|kage@mfZmD/btqY7n4mWxr/OwAY87GlVmBKPowKkM6o9k/img.png|alignCenter|data-origin-width="0" data-origin-height="0" data-ke-mobilestyle="widthContent"|Attention is All You Need||_##]

Transformer논문(Attention is All You Need)에서 저자는 기계번역 모델이 오직 self-attention 모델만 사용함으로써 SOTA 수준의 결과를 이룬 것을 보여주었다. 다른 논무에서는 Image Transformer 모델을 제안했는데, self-attention을 이미지생성을 위해 autoregressive 모델에 추가하기 위함이였다. 또 다른 논문은 self-attention을 비디오 시퀀스에서 spatial-temporal dependency를 모델링 하기위해 non-local operation으로 형태화하였다.

어쨋든 global함을 갖기 위해 attention을 적용하는 것이 트렌드임은 확실하다.

이러한 진보에도 불구하고, self-attention은 아직 GAN에서는 적용되지 않았다. 이전 **AttnGAN** 모델이 attention을 한 input sequence에서 word embedding에 사용하였지만, _internal model states_에서의 self-attention이 아니였다.

이 논문에서는 SAGAN은 글로벌하고 long-range dependency를 이미지의 internal representations안에서 효과적으로 찾는 것을 배운다.

---

## **Self-Attention Generative Adversarial Networks**

대부분의 이미지 생성에 사용되는 GAN 기반의 모델들은 convolutional layers를 사용하는데, convolution은 정보를 local neighborhood안에서 처리한다.(window 단위로 convolution하기 때문에)

[##_Image|kage@RtLES/btqY5L5wBGq/tFebMCqGboPBTmwekv6IQ0/img.png|alignCenter|data-origin-width="0" data-origin-height="0" width="538" height="NaN" data-ke-mobilestyle="widthContent"|convolution 시각화(https://images.app.goo.gl/zWcfpuTQgmHcym8E6)||_##]

그렇기 때문에 convolution 레이어들만 사용하는 것은 long-range dependency를 모델링 할 때 계산 측면에서 비효율적이다. 

이 단원에서는 SAGAN가 self-attention을 GAN에 소개하기 위해 **non-local** 모델을 적용한 것에 대해 다룬다.

(non-local means가 연상되는군...)

이는 generator와 discriminator 모두가 효과적으로 **멀리 떨어진 영역과의 관계를 모델링하게 해준다**. (long-range dependency)

[##_Image|kage@xA2Sh/btqZeholXB7/9C2qFUqJ8oOS7ATztZQU50/img.png|alignCenter|data-origin-width="0" data-origin-height="0" data-ke-mobilestyle="widthContent"|논문에서 나온 self-attention&nbsp;||_##]

이전의 hidden layer부터 나온 image feature vector $x \\in \\mathbb{R}^{{C} \\times N}$는 어텐션을 계산하기 위해 처음에 두 개의 feature spaces $f, g$로 변환되는데, $f(x) = W\_f x$, $g(x) = W\_g x$이다

$$\\beta\_{j, i}=\\frac{\\exp \\left(s\_{i j}\\right)}{\\sum\_{i=1}^{N} \\exp \\left(s\_{i j}\\right)}, \\text { where } s\_{i j}=f\\left(\\boldsymbol{x}\_{i}\\right)^{T} \\boldsymbol{g}\\left(\\boldsymbol{x}\_{\\boldsymbol{j}}\\right)$$

그리고 위의 $ \\beta\_{j, i}$ 는 j번째 영역을 합성할 때 어느 정도로 모델이 i번째 위치에 상관하는지(?)를 가르킨다.

즉, j번째 영역처리할 때 i번째에 대한 weight라고 생각하면 될 듯 하다.

여기서 C는 채널의 수, N은 이전 hidden layer로부터의 features의 feature 위치의 갯수를 뜻한다.

어텐션 layer의 output $ \\boldsymbol{o}=\\left(\\boldsymbol{o}\_{1}, \\boldsymbol{o}\_{2}, \\ldots, \\boldsymbol{o}\_{j}, \\ldots, \\boldsymbol{o}\_{N}\\right) $ 에서

j번째 원소는 다음과 같다.

$$  
\\boldsymbol{o}\_{\\boldsymbol{j}}=\\boldsymbol{v}\\left(\\sum\_{i=1}^{N} \\beta\_{j, i} \\boldsymbol{h}\\left(\\boldsymbol{x}\_{\\boldsymbol{i}}\\right)\\right), \\boldsymbol{h}\\left(\\boldsymbol{x}\_{\\boldsymbol{i}}\\right)=\\boldsymbol{W}\_{\\boldsymbol{h}} \\boldsymbol{x}\_{\\boldsymbol{i}}, \\boldsymbol{v}\\left(\\boldsymbol{x}\_{\\boldsymbol{i}}\\right)=\\boldsymbol{W}\_{\\boldsymbol{v}} \\boldsymbol{x}\_{\\boldsymbol{i}}  
$$

위의 형태에서, $ \\boldsymbol{W}\_{\\boldsymbol{g}} \\in \\mathbb{R}^{\\bar{C} \\times C}, \\boldsymbol{W}\_{\\boldsymbol{f}} \\in \\mathbb{R}^{\\bar{C} \\times C}, \\boldsymbol{W}\_{\\boldsymbol{h}} \\in \\mathbb{R}^{\\bar{C} \\times C}, \\text { and } \\boldsymbol{W}\_{\\boldsymbol{v}} \\in \\mathbb{R}^{C \\times \\bar{C}} $ 은 학습된 weight 행렬들이다. 이 행렬들은 1X1 convolution을 거쳤다. 

ImageNet에서 몇번의 epoch로 학습 후에 채널의 수 $\\bar{C}$를 $C/k$로 줄일 때 (when $k = 1, 2, 4, 8$) 큰 성능 저하가 없었기 때문에, 메모리 효율화를 위해 저자들은 이 논문에서의 모든 실험에서 $k$를 8로 정하였다. 즉, ($\\bar{C} = C/8)$

추가로, 저자들은 attention layer의 output을 한 스케일 파라미터로 곱한 후 input feature map을 더하였다. 

이 결과로, 최종 output은 

$$  
\\boldsymbol{y}\_{i}=\\gamma \\boldsymbol{o}\_{\\boldsymbol{i}}+\\boldsymbol{x}\_{\\boldsymbol{i}}  
$$

로 주어진다. 여기서 $\\gamma$는 학습가능한 상수값이고 처음에 0으로 초기화된다. $\\gamma$를 도입하는 것은 네트워크가 처음에는 local neighborhood에서 단서를 얻도록 한다. 그리고 점차적으로 더 Non-local 단서들에 중점을 맞추도록 한다.(처음에는 감마가 0이므로 input feature map과 같지만 감마가 점점 커지면서 attention의 결과들에 초점을 맞춘다. attention이 global 정보를 맞추기 위해 도입한 방법이므로, 모델은 점점 global에 초점을 맞추게 된다.)

저자들도 이러한 방법을 사용한 이유가 직관적이라고 설명하는데, 처음에는 쉽게 학습을하고 점차적으로 task의 복잡성을 높이고 싶어서 그랬다고 한다. 

SAGAN에서는, 제안된 attention 모듈이 generator와 discriminator 모두에 적용된다. 이 generator와 discriminator는 adversarial loss의 hinge version을 최소화함으로써 번갈아가며 학습하는 방식으로 훈련된다.

Generator와 Discriminator의 Loss funtion은 각각 다음과 같다.

$$  
\\begin{aligned} L\_{D}=&-\\mathbb{E}\_{(x, y) \\sim p\_{d a t a}}\[\\min (0,-1+D(x, y))\] \\\\ &-\\mathbb{E}\_{z \\sim p\_{z}, y \\sim p\_{d a t a}}\[\\min (0,-1-D(G(z), y))\] \\\\ L\_{G}=&-\\mathbb{E}\_{z \\sim p\_{z}, y \\sim p\_{d a t a}} D(G(z), y) \\end{aligned}  
$$

---

## **Techniques to Stabilize the Training of GANs**

이 논문에서는 또한 앞서 언급한 GAN의 단점인 학습에서의 불안정성을 해결하기 위해 두 가지 방법을 조사했다.

1\. Generator와 Discriminator에서의 **Spectral Normalization** 사용

2\. 정규화된 Discriminator에서의 느린 학습을 해결하기 위해 **Two-timescale update rule(TTUR)** 사용

첫번째부터 차근차근 알아보자.

---

### **1) Self-Attention Generative Adversarial Networks Spectral normalization for both generator and discriminator**

cGAN에서 원래 GAN의 학습을 spectral normalization을 통해 안정화시키는 것을 제안하였다.

이런 정규화는 _**각 층에서의 spectral norm을 제한시킴으로써 Discriminator의**_ _**Lipschitz constant를 억제시킨다.**_(수학적으로 정확한 정의는 아니지만, Lipschitz constant를 억제시킨다는 것은 gradient, 즉 파라미터간의 차이를 억제시킨다고 간단하게 이해할 수 있다.)

다른 정규화 방법들과 비교해서, **spectral normalization은 추가적인 hyper parameter 튜닝을 필요로 하지 않는다.**(모든 weight layer의 spectral norm을 1로 지정하는 것은 실제로 잘 작동한다고 한다.)

또한, 계산하는데 드는 비용도 상대적으로 적다.

저자들은 generator 또한 spectral normalization을 이용하는 것이 이득이라고 하였는데,  generator의 conditioning이 GAN의 성능에 중요한 요소이기 때문이다. Generator에서의 Spectral normalization은 파라미터 크기가 증가하는 것을 막을 수 있고 비정상적인 gradients 또한 피할 수 있다. 

Spectral normalization을 generator와 discriminator 모두에 적용하는 것은 generator를 업데이트하는 횟수에 비해 discriminator를 덜 업데이트할 수 있게 하고, 그럼으로써 학습에 필요한 계산 비용을 줄인다. 또한 학습의 안정성에도 도움이 된다고 한다.

### **2)** **Imbalanced learning rate for generator and discriminator updates**

두번째 방법으로는 generator와 discriminator에 imbalanced learning rate를 사용하는 것이다.

이전 연구에서, **discriminator의 정규화**는 종종 GAN의 학습과정을 느리게 했다고 한다. 실제로, 정규화된 discriminator를 사용한 방법들은 generator update step당 보통 multiple discriminator update step들이 필요했다.(1:1이아니라 discriminator를 더 많이 update시켜야 했음. 업데이트가 느리기 때문에)

독립적으로, 한 연구( _Self-Attention Generative Adversarial Networks __GANs trained by a two time-scale update rule converge to a local nash equilibrium_) 는 seperate learning rate(TTUR)를 generator와 discriminator에 사용하는 것에 호의적이였다. 이 연구에서는 **TTUR를 사용하는 것을 제안하는데 특히 정규화된 discriminator에서 느린 학습을 위해서 적용**하는 식이다. 이는 generator step당 더 적은 수의 discriminator update를 가능하게 한다. 이로써 SAGAN은 **같은 시간에도 더 나은 결과**를 보여준다.(덜 학습 step이 필요하므로)

<TTUR>: 간단하게만 말하자면 learning rate를 generator와 discriminator에 각각 다르게 적용하는 것이다. 각자의 learning rate는 n에 대한 함수로 표현된다.

[##_Image|kage@bkmJ8l/btqY9ZvhXgs/1f5uR8vim1jkGiGk5ZLs00/img.png|alignCenter|data-origin-width="0" data-origin-height="0" data-ke-mobilestyle="widthContent"|https://simonezz.tistory.com/manage/newpost/77?type=post&amp;returnURL=https%3A%2F%2Fsimonezz.tistory.com%2F77||_##]

TTUR에 대해서는 따로 공부를 해서 블로깅을 해야겠다. 

---

## **Experiments**

SAGAN의 성능을 측정하기 위해서, 이 논문에서는 LSVRC2012(ImageNet) 데이터셋을 이용하였다.

---

첫번째로는 GAN의 학습을 안정화하기위한 두 개의 제안된 방법(Spectral Normalization, TTUR)의 효과를 평가한다.

두번째로는 제안된 self-attention 매커니즘에 대해 평가된다.

마지막으로는, SAGAN모델을 다른 SOTA 모델들과 비교한다.

---

모델들은 4개의 GPU를 사용했을 때 각각 2주씩 시간이 소요되었다.

### **Evaluation metrics**

평가지표로는 Inception score(IS)와 Frechet Inception distance(FID)가 정량적인 평가로 사용되었다.

대안들이 존재하지만, 널리 쓰여지지 않는 방법이기에 위의 두 지표를 사용하였다.

### **Network structures and implementation details.**

모든 SAGAN 모델들은 128X128 사이즈의 이미지를 만들어내도록 디자인되었다. 

디폴트로는 spectral normalization이 generator와 discriminator 모두의 층에서 사용되었다. 

SAGAN은 또한 conditional batch normalization을 generator와 discriminator의 projection에 사용하였다.

모든 모델에 대해서 Adam optimizer를 사용하였고 Adam의 parameter로는 $\\beta\_{1}=0 \\text { and } \\beta\_{2}=0.9$ 를 학습에 사용하였다. 또한 learning rate의 기본값은 discriminator에는 0.0004이고 generator로는 0.0001이다.

각각의 방법들을 적용한 metric의 결과는 다음과 같다.

[##_Image|kage@dLl1o3/btqY7ooNMqE/JbldT3YaQztUeAnNr4VpE1/img.png|alignCenter|data-origin-width="0" data-origin-height="0" data-ke-mobilestyle="widthContent"|||_##]

이미지의 결과는 다음과 같다.

[##_Image|kage@sR1Mh/btqY5MXG4R0/JkdNQeXUiawyGAukKjHNUK/img.png|alignCenter|data-origin-width="0" data-origin-height="0" data-ke-mobilestyle="widthContent"|||_##]

GAN에 self-attention과 Residual block을 각각 적용한 결과의 지표는 다음과 같다. 두 지표에서 모두 SAGAN이 우수함을 확인할 수 있다.

[##_Image|kage@dy0MpH/btqY90Oqcjl/d5DBT3KSEsAFlkV6GKOwz1/img.png|alignCenter|data-origin-width="0" data-origin-height="0" data-ke-mobilestyle="widthContent"|||_##]

Attention map을 시각화한 이미지로는 다음과 같다.

[##_Image|kage@cNnfRZ/btqZcsKJnd9/otZ1LTKFSexAjE10QMrtZ0/img.png|alignCenter|data-origin-width="0" data-origin-height="0" data-ke-mobilestyle="widthContent"|||_##]

다른 SOTA 모델들과의 결과는 다음의 이미지로 비교할 수 있다.

[##_Image|kage@bp36lX/btqY8HBC48p/5P0dDAXRgkslawam8IhhYk/img.png|alignCenter|data-origin-width="0" data-origin-height="0" data-ke-mobilestyle="widthContent"|||_##]

---

## **Conclusion**

이 논문에서는 SAGAN에 대해 소개하였다. 이는 Self-attention을 GAN에 적용한 방법으로 self-attention모듈이 long-range dependency를 모델링하는데에 우수함을 보여주었다. 추가적으로 spectral normalization이 GAN 학습을 안정화시키고 TTUR이 정규화된 discriminator의 학습의 속도를 올려줌을 보여주었다. 

---

원래 언어모델들에 적용되던 Attention이 요즘 비전쪽에서도 적용되는 것을 보며 점점 두 분야의 영역이 허물어져 가는 것을 잘 느끼고 있다. 몇달 전 나온 DALL E가 이미지와 자연어간의 결합을 잘 보여주는 예라 생각하는데 두 가지 모두에 관심을 가진 나로서는 환영이다. 

이 논문 덕분에 attention을 이미지에 적용한 원리를 더 잘 이해하게 된 것 같다.

