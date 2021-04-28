

---

## **Abstract**

이 논문은 "do as I do"라는 **motion transfer**방법에 대해 다룬다.

간단하게 flow를 설명하자면 source subject(ex. 발레리나 비디오)로부터 포즈를 뽑아내고 학습을 통해 이러한 포즈를 target subject로 매핑시키는 방법이다.

---

## **Introduction**

![image](https://blog.kakaocdn.net/dn/33LtF/btq3CeVHy6P/W3R3qPe0W3kZ2e3cI0K9TK/img.png)
위와 같이 발레리나의 비디오가 주어지고 아래의 남자가 standard moves를 하는 영상이 있을 때,

아래의 남자가 발레리나의 움직임을 따라하도록 transfer하는 방법이다. 

두 비디오사이에 모션을 transfer하기위해선 두 개인의 이미지사이의 mapping을 학습해야한다. 

따라서 이 논문의 목적은 source와 target sets사이의 **image-to-image translation**을 학습하는 것이라고도 할 수 있다.

하지만, 이 논문에서는 target이 발레리나의 동작을 하는 비디오가 없다. 즉 supervised learning을 하기 위한 비디오 쌍을 가지고 있지 않다.

만약에 가지고 있다 하더라도 개인에 따라 같은 동작을 해도 몸매와 스타일이 다르기 때문에 정확히 쌍을 짓기가 힘들다.

[![image](https://blog.kakaocdn.net/dn/sIFMF/btq3yx3i8AT/fB4pLHsF48viMOwcJDHI6k/img.png)

이때문에 비디오 쌍이 아닌, source로부터 **keypoint-base pose**를 뽑아내어 이를 매핑에 이용한다.

이 keypoint-base pose는 일종의 중간다리 역할(intermediate representation)을 하게 된다.

이를 위해 human pose detector 모델을 사용했는데 대표적으로는 OpenPose가 있다. 

즉, 모션을 source에서 target으로 transfer시키기위해 위와 같이 pose stick figures를 학습된 모델을 넣어 예측을 하는 방식을 택했다.

이 논문은 또한 이러한 모델링외에도 학습에 필요한 데이터셋을 제공하는 큰 공헌도 했다.

---

## **Relation Work**

이 논문이 나올 당시에  motion transfer에서는 motion을 appearance와 분리해내고 새로운 모션으로 비디오를 합성하는 방법론에 초점이 맞춰져 있었는데, 대표적으로 _MoCoGAN_은 unsupervised adversarial training을 통해 이런 분리에 대해 학습하고 비디오를 만들어냈다고 한다.

또 _Dynamics Transfer GAN_에서는 얼굴 표정을 source에서 target person으로 transfer시켰는데, 이 논문에서도 비슷하게 **모션의 representation을 target에 적용시키는 방법**으로 새로운 모션을 만들어낸다. 하지만, 기존의 방법들과 달리 이 논문은 댄스 영상을 합성하는데에 초점을 맞췄다는 점에서 차이가 있다.

이 논문의 모델은 두 가지의 방향에서 빠른 발전을 이루어냈는데,

#### **1\. Robust pose estimation**

#### **2\. Realistic image-to-image translation**

이 두가지이다. SOTA의 pose detection 모델들인 OpenPose, DensePose등으로 첫번째인 강력하고 안정적인 pose detection이 가능했고, 

SOTA의 image-to-image translation 모델인 pix2pix, CoGAN 그리고 CycleGAN등으로 두번째인 진짜같은 translation이 가능했다.

---

## **Method**

#### **Goal >**  Source person의 비디오와 target person의 비디오가 주어졌을 때 이 모델의 목적은 source person이 하는 모션과 똑같이 움직이는 target person의 비디오를 만들어내는 것.

![image](https://blog.kakaocdn.net/dn/c03Xf0/btq3FdnZXfC/qlNc20bXCzDRcKKV8Wzv21/img.png)

---

이러한 목적을 이루기 위해서 파이프라인은 세가지 단계로 나누어진다.

#### **1\. Pose detection**

#### 👉🏻 pre-trained SOTA pose detector를 사용하여 source video의 frame이 주어지면 pose stick figures를 만들어냄

#### **2\. Global pose normalization**

#### 👉🏻 source와 target의 몸매와 위치등의 차이를 계산하여 normalize시킴.

#### **3\. Mapping from normalized pose stick figures to the target subject**

#### 👉🏻 2번의 결과인 normalized pose stick figures를 adversarial training으로 target person의 이미지로 매핑하는 것을 학습한다.(supervised ❌)

---

이제 위의 세 가지 방법에 대해 더 자세히 하나씩 알아보자.

### **1\. Pose Encoding and Normalization**

#### **1> Encoding body poses**

Subject image의 pose를 encoding하기 위해서 위에서 언급했듯이 pre-trained model을 사용한다. _**(OpenPose) **_

이 detector를 $P$라 해보자. $P$는 2D $x, y$ 좌표들을 정확하게 측정해낸다. 그 다음 이러한 keypoints를 그리고 선으로 연결하여 pose stick figure를 만들어낸다. 

![image](https://blog.kakaocdn.net/dn/btxagd/btq3CBi6SRr/PqVxRgYGz1CC0A6c9ybecK/img.png)

#### **2> Global pose normalization**

영상에서 subject는 다른 카메라와의 위치가 제각각이고 비율들이 모두 다를 것이다. 따라서 mapping을 위해서는 pose keypoints를 변환시켜 target person에 맞춰야 한다.

이러한 변환은 **키**와 **발목위치**를 분석하여 가장 가까운 발목과 먼 발목의 위치간의 linear mapping을 사용하였다.

### **2\. Pose to Video Translation**

이 논문에서의 영상 합성 방법은 adversarial single frame generation 과정을 기본으로 한다. 

Generator network $G$는 multi-scale Discriminator $D = (D\_1, D\_2, D\_3)$에 대항하여 minmax game를 한다.

$G$는 이미지들을 "잘" 합성하여 $D$를 속이게 된다. (자세한  사항은 생략)

즉,  $G$는 pose stick figure가 주어졌을 때, 한 사람의 이미지를 합성해내게 된다.

이러한 **single-frame image-to-image translation** 방법은 비디오 합성에 잘 맞지 않는데, 일시적인 artifacts를 만들어내거나 모션인식에 정교한 디테일을 잘 못만들어내기 때문이다. 따라서 이 논문에서는 학습된 모델을 추가하여 고해상도의 generation을 완성시켰다.

#### **1> Temporal smoothing**

비디오 시퀀스를 만들어내기 위해서, 저자들은 single image generation setup을 수정하여 인접한 프레임사이의 밀접함을 증가시켰다

더 자세히 설명해보자면, 각각의 프레임을 만들어내는 것 대신에, 두 개의 연속적인 프레임을 예측하는 방식을 사용했다. 

처음 output $G(x\_{t-1})$은 pose stick figure $x\_{t-1}$와 zero image $z$에 대해 조건화(?)된 값이고, 두번째 output $G(x\_t)$는 pose stick figure $x\_t$와 첫번째 output $G(x\_{t-1})$에 대해 조건화된 값이다.

(독립적이지 않고 이전의 output을 이용하여 다음의 output을 계산해내게 된다.)

따라서 $D$는 real sequence $(x\_{t-1}, x\_t, y\_{t-1}, y\_t)$과 fake sequence $(x\_{t-1}, x\_t,G(x\_{t-1}), G(x\_t))$ 의 차이를 계산하여 판단하게 된다. 

이에 대한 **objective**는 다음과 같다.

![image](https://blog.kakaocdn.net/dn/1uGcu/btq3CfApkwa/j4uGUrhLKm7K7WXkbLq7v0/img.png)


#### **2> Face GAN**

이 논문에서는 또한 특수한 GAN setup을 추가해서 디테일을 살리고 얼굴을 좀 더 현실적으로 만들었다.

![image](https://blog.kakaocdn.net/dn/babJ3J/btq3IwIIDxj/5geMpSNTS4yUPoBxWTDUek/img.png)

Generator $G$로 이미지를 만들어낸 후 이미지의 작은 부분 $G(x)\_F$을 넣고 같은 방법으로 pose stick figure $x\_F$를 또 다른 generator $G\_f$에 넣는다.(위의 그림을 보면 좀 더 이해가 쉬움)

$G\_f$를 거치게 되면 residual $r = G\_f(x\_F, G(x)\_F)$가 나오게 되는데 이를 $G(x)\_F$와 더하여 최종적으로 합성된 얼굴이 나오게 된다. 이런 합성 이미지를 마찬가지로 discriminator $D\_f$가 진짜와 구별을 하며 학습을 하게 된다. 

아래의 loss function 식을 보면 좀 더 이해가 쉽다.(기존의 GAN과 동일)

![image](https://blog.kakaocdn.net/dn/LVArT/btq3ySe65bb/slLDZ2rAAxYteDKGK6MPEK/img.png)

$x\_F$는 origianl pose stick figure $x$의 얼굴 영역이고 $y\_F$는 ground truth target person image $y$의 얼굴 영역을 뜻한다.

또한, 전체 이미지에서와 비슷하게, perceptual reconstruction loss를 추가한다.

이제 _**1>Temporal smoothing**_과 _**2>FaceGAN**_의 loss function을 합친 Full Objective는 다음과 같다.

첫번째로 main generator $G$와 discriminator $D$를 다음의 objective로 훈련을 시킨다.

![image](https://blog.kakaocdn.net/dn/b3AFq4/btq3GFeYiX2/6h4uWNLxohErYlMa9Pg4iK/img.png)

여기서 $L\_{GAN} (G,D)$는 pix2pix논문의 adversarial loss이다.
![image](https://blog.kakaocdn.net/dn/bpsNAy/btq3GGkA5xS/NhCyeUn9sqVAr5FafRXAPK/img.png)

또, Feature Matching loss $L\_{FM}(G,D)$와 perceptual reconstruction loss $L\_P(G(x), y)$까지 사용한다.

이러한 main generator, discriminator 훈련 후에는 full image GAN의 가중치들을 고정시켜놓고 다음의 두번째 objective를 학습한다.

![image](https://blog.kakaocdn.net/dn/b46uX0/btq3GDH5YO2/cxqgjEKEGbqbtKRsTQIWVK/img.png)
---

## **Experiments**

논문에서는 기존의 방법들과 비교를 한 실험의 결과들을 실어놓았는데, setup부터 살펴보자.

### **1\. Setup**

실제로 실험을 하기 위해 저자들은 자신을 직접 찍은 비디오를 사용하여 모델 학습을 하였다. (공개도 함)

**<Baseline methods>**는 다음과 같다.

#### **1) Nearest Neighbors**

#### **2) Balakrishman(PoseWarp)**

#### **3) Temporal smoothing(FBF + TS)**

또한 사용한 **<Evaluation metrics>**은 다음과 같다.

#### **1) SSIM**

#### **2) LPIPS**

### **2\. Quantitative Evaluation**

#### **1) Comparison to Baselines**

저자들은 정량적으로 baseline methods와 논문의 모델을 비교하였는데, 그 결과는 다음과 같다.

![image](https://blog.kakaocdn.net/dn/dywjkA/btq3GFlJUGV/hoqKuYEZvskAjoi05y0Gnk/img.png)


#### **2) Ablation Study**

또한 사용한 방법들을 하나씩 제거해나가면서 성능이 어떻게 되는지에 대해 연구도 하였다.

![image](https://blog.kakaocdn.net/dn/pXvdc/btq3ITRdqkI/udOVQvmMAieHTWaHhS237K/img.png)

### **3\. Qualitative Results**

이제 결과를 눈으로 직접 확인해보자.
![image](https://blog.kakaocdn.net/dn/pXvdc/btq3ITRdqkI/udOVQvmMAieHTWaHhS237K/img.png)

Ground Truth와는 좀 다르지면 보기에 그럴듯한 결과인 것을 직접 확인할 수 있다. 심지어 밑에는 Ground truth보다 더 선명하고 진짜 같기도 하다.!!

---

## **Detecting Fake Videos**

합성기술말고도 합성된 것을 구분하는 것도 가능하다고 한다.(Discriminator를 사용했으니 될거라고 예상이 가능하다!)


![image](https://blog.kakaocdn.net/dn/CysuV/btq3EuY9RsW/qOqmDhy1tKscr1VlK3GGu0/img.png)

정량적인 수치로 굉장히 높은 값으로 fake detection이 가능함을 확인할 수 있다.

---
