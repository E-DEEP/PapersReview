

## **Abstract** 

이 논문에서는 **Styletransformer** 모델에 대해 다룬다. 이는 GAN의 transformer 버전이라 보면 되는데, 즉 convolution 연산이 빠진 모델로 이미지를 생성해내는 모델이다.

어떻게 트랜스포머가 고화질의 이미지를 생성해낼 수 있었는지에 대해 다루며 또한, StyleGAN2과 기존의 트랜스포머 구조를 수정하여 style-based generator를 만들어낸다. 

또한, _Linformer_를 적용시켜 모델을 가볍게 만들었고, 동시에 고해상도의 이미지를 만들어내서 속도와 메모리측면에서 이점을 보여준다. 

논문에서는 CIFAR-10과 같은 저해상도 이미지 데이터셋과 고해상도 이미지셋 (ex. LSUN-church)을 사용하였으며, 정량적인 수치로는 FID 2.82, IS 9.94를 CIFAR-10에서 기록했다. 이는 SOTA수준의 모델에 견줄만 하다. 또 STL-10과 CelebA 데이터셋에서는 새로운 수치를 기록하였는데, 이는 뒤에서 확인할 수 있다.

## **Introduction**

이미지 생성 모델에서 GAN은 광범위하게 사용되고 있다. GAN을 사용하여 생성한 이미지들은 기존보다 상당히 개선되었으며 이미지 합성 분야에서 활발하게 사용되고 있다_. StyleGAN2_나 _BigGAN_와 같은 SOTA 모델은 현실적이고 high-fidelity 이미지들을 만들어낼 수 있으나, 이러한 **모든 모델들이 convolution backbone에 기반**한 만큼, locality problem을 갖는다.(convolution 자체가 filter가 돌며 윈도우 내의 연산을 하기 때문에 global features를 잡아내기에는 한계가 있음을 의미하는 듯.)

Transformer는 다들 잘 알겠지만, NLP분야에 처음 소개됐는데 큰 성과를 보여주었으며 최근에 이러한 트랜스포머 기반의 모델들이 컴퓨터 비전분야에서도 활발하게 사용되고 있다.

GANsformer는 convolution과 transformer를 결합하여 이미지 생성에서 우수한 성능을 보여주었으며 이외에도 transformer를 이미지 생성에 사용한 예시들을 있었다.

하지만, 위에서도 언급했듯이 convolution연산을 사용하게 되면 locality problem은 필연적으로 나타나게 된다. 이러한 단점을 convolution 기반의 generators는 layer를 쌓음으로써 해결해보고자 하였으나 속도나 gradient vanishing과 같은 측면에서 또다른 단점이 나타나게 된다. 

이 논문에서는 Styleformer를 제안하는데, 이는 style-based generator로 transformer로만 이루어져 있다. 

## **Generation with Transformer**

[##_Image|kage@2sX1j/btrgU1JxUxb/aGL3rxn9s6gPUE5FQeDGE1/img.png|alignCenter|data-origin-width="590" data-origin-height="457" data-ke-mobilestyle="widthOrigin"|||_##]

Transformer로만 이루어진 모델이기에 트랜스포머에 대해 잠깐 짚고 넘어가자.

트랜스포머는 위의 (a)와 같이 Query, Key, Value에서 Query와 Key를 dot product한 것을 weight로 하여 Value를 weighted sum한 것이라 생각하면 된다. 

트랜스포머를 Prepare와 Main module로 나누어 생각하면,

아래와 같이 생각할 수 있다.

[##_Image|kage@cx5tfW/btrgU04WSkd/KWay8kjDFovGwlUIDGTawK/img.png|alignCenter|data-origin-width="556" data-origin-height="205" data-ke-mobilestyle="widthOrigin"|||_##]

Prepare는 Query, Key 그리고 Value를 만들어내는 부분이며 Main은 이를 weighted sum하고 concat 등등을 하는 곳이다.

위에서 나온 행렬 A가 Attetion map이라 불리는 행렬인데, 이 attention map(kernel)이 각 채널에 적용되기에 강력한 generator가 될 수 없다고 생각할 수도 있다.(depthwise separable convolution에 비해)

하지만, Multi-head attention을 사용함으로써 이러한 문제는 해결된다.(마지막에 Concat하는 부분)

Head수를 늘림으로써 다양한 Attetion map을 만들어낼 수 있기 때문이다.

## **Demodulation with Transformer**

StyleGAN은 layer-wise style vector를 이용하여 이미지를 만들어내고 이러한 벡터들을 이용해 생성 이미지들을 제어할 수 있다는 점에서 이점을 가진다. 

생성 과정에서, style vector는 input feature map을 각 레이어에서 스케일링 하는데(=style modulation) 이는 특정 feature map을 강조하는 것으로 이해할 수 있다. 

## **Styleformer Architecture**

전체 구조는 다음과 같다.

[##_Image|kage@ydEeX/btrgOsOWXgA/buHjv3e9lxZ7ce95LpHngK/img.png|alignCenter|data-origin-width="612" data-origin-height="466" data-ke-mobilestyle="widthOrigin"|||_##]

### \- Style injection

Vanila GAN과는 다르게,  StyleGAN은 다른 style vectors를 각 convolution operations에 받음으로써 이미지를 만들어냈는데, 이와 비슷하게 스타일포머도 각 모듈마다 다른 style vector를 사용하였다. 

### \- Residual connection

앞서 말한대로, Style vector가 각 operation마다 달라야하기에 residual connection을 사용하여 style modulation을 진행하였다. (디테일 유지를 위해)

### \- Layer normalization

저자들은 layer normalization위치또한 바꾸었는데, 트랜스포머에서 레이어 정규화의 역할이 attention map을 만들어내는 것을 준비하는 것이라 보았기 때문이다. 

### \- Feed-Forward network

저자들은 feed-forward 구조를 지웠는데, 지움으로써 generator가 더 효과적이면서 성능이 나아졌기 때문이다. 이는 아래 ablation study로 확인할 수 있다.

[##_Image|kage@6g9uB/btrgMA0iB26/jYe7QZSox3bnaVa4UbLkFk/img.png|alignCenter|data-origin-width="568" data-origin-height="186" data-ke-mobilestyle="widthOrigin"|||_##]

## **Experiments**

**(정량적 결과)**

[##_Image|kage@uWA43/btrgTEA5fnv/JWjfPlx2BMowRmRuiYKhuk/img.png|alignCenter|data-origin-width="590" data-origin-height="260" data-ke-mobilestyle="widthOrigin"|||_##]

**(정성적 결과)**

[##_Image|kage@GLTsi/btrgVRNsvZJ/raPUIUIqrcxCMxAZxdlKg1/img.png|alignCenter|data-origin-width="637" data-origin-height="655" data-ke-mobilestyle="widthOrigin"|||_##]

**(Styleformer의 큰 장점인 빠른 속도와 적은 메모리 용량)**

[##_Image|kage@bShZJN/btrgVNEeY1F/XTsYMC48Q28RV7pKJYBT40/img.png|alignCenter|data-origin-width="631" data-origin-height="178" data-ke-mobilestyle="widthOrigin"|||_##]

## **Conclusion**

이 논문에서는 Styleformer에 대해 다루었다. 이는 convolution을 사용하지 않는 transformer 기반의 네트워크로 이미지 생성에 사용된다.

또한, 저자들은 Styleformer-Linformer를 제안함으로써 복잡한 연산을 줄였다.

이를 통해 고해상도 이미지를 빠르고 메모리를 덜 차지하며 만들어낼 수 있었다.
