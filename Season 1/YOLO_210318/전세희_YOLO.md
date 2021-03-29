

## **Abstract**

기존의 object detection 모델들은 detection을 수행하기 위해 classifiers들을 제안했었다.

하지만 이 논문에서 제안하는 yolo는 object detection을 separated **bounding boxes와 class 확률**에 대한 _regression problem_으로 정의한다. 

Yolo의 single neural network는 one evaluation에서의 full images로부터 **bounding boxes**와 **class 확률값**들을 예측하는데, (image내 bounding boxes와 분류를 위한 확률값들)

전체 detection 파이프라인이 하나의 network이기 때문에, end-to-end로 detection에 최적화가 가능하다.

또한, 전체의 통합 구조는 속도가 굉장히 빠르다고 한다. 기본 YOLO 모델은 이미지를 **1초에 45개의 프레임을 처리**한다. 더 빠른, 좀 더 작은 크기의 yolo(Fast YOLO)는 1초당 155개의 프레임을 처리한다고 한다.(wow)

빠르기만 한 것이 아니라 성능 측면에서는 다른 real-time detectors보다 mAP가 두 배이다.

기존의 SOTA detection systems와 비교해서, YOLO는 더 많은 localization errors를 만들지만 배경에서 잘못된 예측(false positive)를 할 가능성은 적다.(object이 아닌데 object이라고 detection하는 경우)

즉, YOLO는 object의 매우 general한 representation을 학습하게 된다. 

YOLO는 이미지를 예술작품과 같은 다른 도메인의 이미지로 일반화하는 것에서 다른 detection 방법들(ex. DPM과 R-CNN)보다 성능이 뛰어나다고 한다. 즉, **image representation** 성능이 매우 뛰어나다고 할 수 있다.

---

## **Introduction**

인간들은 한 이미지를 보고 빠르게 그 이미지안의 물체들에 대해 알게 된다.

가령, 어디에 물체가 있는지 또는 어떻게 서로 놓여져 있는지에 대한 정보를 금세 알아차린다.

인간의 시각 시스템은 빠르고 정확하여 약간의 의식만으로도 운전과 같은 복잡한 일을 할 수 있게 한다.

인간의 시각 시스템과 같은 obejct detection의 빠르고 정확한 알고리즘은 컴퓨터가 특별한 센서를 달지 않고 자동차를 운전하게 하며 보조 장치들이 실시간으로 시각 정보들을 사용자에게 전달하도록 한다. 일반 목적의 반응형 로봇 시스템의 잠재력을 불러일으킨다.

기존 detection 시스템들은 물체를 탐지하기 위해 classfier를 사용하고  테스트 이미지에서 다양한 위치와 스케일에서 성능을 평가했다. Deformable parts models(DPM)과 같은 시스템들은 sliding windows 접근법을 사용했는데 classifier는 이미지내 고른 간격의 위치에서 실행된다.

R-CNN과 같은 더 최근 방법들은 potential bounding boxes를 처음에 만들어내기 위해서 **region proposal** 방식을 사용하고 그 boxes위에서 classfier를 돌리는 식이다.

Classification 후에, 후처리에서는 bounding boxes를 골라내고 중복 detections을 제거하고, 다시 box들에 대한 점수를 매기게 된다.

👉🏻 이러한 복잡한 파이프라인은 느릴 수 밖에 없으며 각각 component가 별개로 학습되어야 하기에 최적화되기 어렵다.

YOLO는 위에서도 언급했듯이 object detection을 classification 관점보다는 _single regression_ 문제 관점에서 바라본다. 이미지 픽셀로부터 bounding box 좌표와 class 확률값을 곧바로 뽑아내는 방식이다. 이러한 시스템을 사용하면 물체가 어디에 무엇인지와 어디에 있는지를 단 한번의 관찰로 예측할 수 있게 된다.

YOLO는 간단하다. 아래의 그림에서 대략적인 방법론을 알 수 있다.

![image](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FZ0DC4%2Fbtq0ehgCKjo%2F3YKzssmjKCSsQwMj7af0H0%2Fimg.png)


하나의 convolutional network가 동시에 여러개의 **bounding boxe**s와 그에 대한 **class 확률값**을 계산한다.

YOLO는 전체 이미지에서 학습하고 직접적으로 detection의 성능을 최적화할 수 있는데, 통합된 구조이기 때문이다. 기존에 별개의 network이 합쳐져있던 형태가 아니기 때문에 여러 이점이 있다.

---

### **Benefits of unifed architecture in YOLO**

**🌟 1\. 빠르다.**

Detection을 하나의 regression문제로 보기 때문에 복잡한 파이프라인이 없으니 빠르다.

detection을 테스트하기 위해서는 neural network을 하나의 새로운 이미지에서 간단하게 돌리기만 하면 된다.

YOLO base 모델은 Titan X GPU에서 batch processing없이 1초당 45개의 프레임을 처리가능하다고 한다. 그리고 base보다 빠른 버전인 YOLO fast는 150fps이상 빠르다고 한다. 

이는 streaming video 또한 실시간으로 25ms이하의 latency로 처리가능함을 의미한다. 

또한, YOLO는 다른 실시간 시스템보다 정확도 측면에서 2배를 기록한다.(mAP)

****🌟 **2. 문맥 정보를 읽어낼 수 있다.**

YOLO는 unified architecture덕분에 예측할 때에 이미지에 대해 global하게 추론하게 된다.

sliding window와 region proposal-based 방법론들과는 달리, YOLO는 전체 이미지를 학습과 테스트하는 동안 보고 classes에 대한 외형뿐만 아니라 문맥정보 또한 implicitly하게 encoding한다.

Fast R-CNN, 기존의 top detection 방법의 경우에는, 이미지에서 배경 패치들을 잘못 추론하게 되는데 더 큰 그림을 못보기 때문이다.(문맥 정보를 못읽어내기 때문)

YOLO는 Fast R-CNN과 비교하여 절반 이하의 background error를 일으킨다.

****🌟**3. object의 generalizable representations에 대해 학습**한다.

일반 이미지에서 학습되고 예술작품 등 다른 도메인의 이미지에서 테스트될 때, YOLO는 DPM과 R-CNN보다 월등히 성능이 좋다.

YOLO가 매우 generalizable하기 때문에 새로운 도메인이나 기대못한 인풋 이미지들에 대해서도 실패할 확률이 적다.

---

YOLO는 여전히 정확도면에서는 기존의 SOTA detection보다는 떨어진다. 

이러한 속도와 정확도간의 tradeoff를 실험에서 보여줄 예정이다.

---

## **Unifed Detection**

위에서도 언급했듯이, YOLO는 분리된 components들을 하나의 neural network으로 통합한 형태이다.

Network응 전체 이미지로부터 각각의 bounding box를 예측하기 위해 features를 사용하고, 동시에 모든 classes에 걸쳐 모든 bounding boxes를 예측함으로써 어떤 물체인지에 대한 추론을 한다.

이는 network이 full image와 이미지의 모든 물체에 대해 globally 추론을 한다는 것을 의미한다.

YOLO 디자인은 end-to-end 학습이 가능하게 하고 실시간으로 처리할 수 있게 할뿐만 아니라 높은 평균 정확성도 유지한다.

YOLO system은 **input image를 S X S grid로 나눈다.** 만약 물체의 중심이 한 grid cell안에 있다면, 그 grid cell은 그 물체를 탐지하는 역할을 하게 된다.

각 grid cell은 B개의 bounding boxes와 각 box에 대한 **confidence score**를 예측한다. 이러한 confidence score는 얼마나 yolo 모델이 해당 박스가 물체를 가지고 있는지에 대해 얼마나 확실한지와 그 bounding box가 얼마나 정확한지에 대해 말해주는 지표이다. 

저자들은 confidence를 $\\Pr(Object) \* IOU^truth\_pred$ 로 정의했는데, 만약 아무 물체도 그 grid cell안에 없다면, confidence score는 0이 될 것이다. 반대로 물체가 해당 박스안에 있다면, confidence score가 예측된 박스와 ground truth 사이의 intersection over union(IOU)가 된다.

각 bounding box는 5개의 예측값을 가지는데, x, y, w, h 와 confidence이다.

confidence는 위에서 언급한 score를 의미하고 x, y, w, h는 각 box center의 x, y좌표 그리고 box의 width와 height를 의미한다.

(참고로, width와 height는 전체 이미지의 상대적인 값이다. 이미지를 먼저 정사각형으로 resize한 후 처리하므로 실제 box의 width, height도 그에따라 실제 이미지에서는 다른 값이 될 것이다.)

각 grid cell은 또한 C개의 conditional class 확률값인 $\\Pr(Class\_i | Object)$ 들을 예측한다. 이러한 확률값들은 물체를 포함하고 있는 grid cell위에서 조건화(conditioned)된다.

($\\Pr(Class\_i | Object)$ 는 물체가 존재할 때 i번째 class일 확률이라고 생각하면 된다.)

YOLO는 box수인 B와 상관없이 grid cell당 오직 class 확률값의 한 set만을 예측한다.

테스트시엔 condtional class 확률값과 각각의 box confidence 예측을 곱하게 된다.

즉 해당 박스가 진짜로 물체를 가진 박스이며 i번째 class의 물체를 가졌을 확률(confidence score)이 된다.

![image](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FdCfGWs%2Fbtq0g1rZl4m%2FTc3dmDp1WWXPJKiPbVNIk0%2Fimg.png)


이러한 score는 박스에서 나타나는 물체의 class에 대한 확률과 얼마나 box가 object와 딱 맞는지에 대한 확률을 의미한다.

![image](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2F3Hl2h%2Fbtq0bwzWG7r%2Fq0N3iBYeLU9rFRDioatsk1%2Fimg.png)



YOLO를 PASCAL VOC에 대해서 평가하기위해, 실험에서는 S=7, B=2로 사용하였다. 

따라서, predictions은 7X7X30 tensor가 된다.(PASCAL VOC 데이터셋은 20개의 class를 가진다.)

### **1\. Network Design**

저자들은 YOLO를 convolutional neural network으로 실행하였고, 앞서 언급했듯이 PASCAL VOC 데이터셋에 대해 평가하였다. 

네트워크의 처음 convolutional layer들은 image로부터 feature를 추출하고 말단의 fully connected layer들은 output 확률값과 좌표값(x, y, w, h)을 예측한다.

YOLO의 네트워크 구조는 이전 classification 모델인 GooGleNet으로부터 많은 영향을 받았는데, YOLO는 24개의 convolutional layers와 2개의 fully connected layers를 가진다.

GooGleNet에서 사용한 inception 모듈 대신 YOLO에서는 간단하게 1X1 reduction 레이러을 사용한 후 3X3 convolutional 레이어를 사용하였다.

![image](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbcuaAW%2Fbtq0jNzMYlW%2FmL7wT9KHY8AyLfOCQkTK50%2Fimg.png)

논문에서는 또한 YOLO의 빠른 버전또한 학습시켰는데 이는 좀 더 적은 수의 convolutional 레이어를 가진 neural network를 사용한다.(기존의 24개의 레이어에서 9개로 줄인 버전) 

레이어 수 뿐만 아니라 레이어에서의 filter또한 적게 사용한다.  레이어 수와 같은 네트워크의 사이즈 이외에의 모든 training과 testing 파라미터들은 YOLO와 같다.

### **2\. Training**

YOLO의 convolutional layers를 1000개의 class를 가진 ImageNet 데이터셋에서 사전학습시켰다.

사전학습에서 초반부의 20개의 convolutional layers후에 average-pooling layer와 a fully connected layer를 사용한다. 

Pretraining 후에 detection을 수행하게 되는데, 이전의 한 연구에서 나온 **convolutional와 connected layers 모두를 사전학습된 network에 추가하는 것이 성능 향상에 도움을 준다**는 아이디어를 사용하였다.

랜덤으로 초기화된 weight를 가진 4개의 convolutional layers와 2개의 fully connected layers를 추가하였다. Detection은 **fine-grained visual 정보**를 때때로 요구하기 때문에, **input 해상도를 224X224에서 448X448로 증가시켰다.**

이렇게 사전학습된 네트워크에 layers를 추가한 network의 **final layer는 _class 확률값_과 _bounding box 좌표값_들을 예측하게 된다**. Bounding box의 width와 height를 전체 이미지의 width와 height에 대해서 normalize시켜 0과 1사이의 값이 되도록 한다.(상대적인 값으로 변경)

또한, final layer에는 linear activate function를 사용하고 다른 layers에는 다음과 같은 leaky rectified linear action를 사용한다.

![image](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2F7o9Qg%2Fbtq0fjzQrOv%2Fqohim59f0KzTODOSUuOY6k%2Fimg.png)

모델에서 나온 output에서 sum-squared error를 최적화하는 방식으로 학습이 되는데, sum-squared error를 사용하는 이유는 최적화하기가 쉽기 때문이다. 

하지만, 이는 average precision을 최대화하기에 완벽한 error 형식은 아니다.

Sum-squared error는 localization error와 classification error에 동일하게 가중치를 두기 때문에다.

또한, 모든 이미지에서 많은 grid cells은 물체를 담고 있지 않을 것이다. 이는 해당 cells의 confidence score를 0으로 만드는데 이러한 cell이 대다수이기 때문에 막상 물체를 가진 cell로부터의 gradient를 압도해버린다. 이는 모델이 불안정해지게 만들고 학습이 converge하지 못하게 만든다.

이를 해결하기 위해, objects를 가지지 않은 box에 대해서 bounding box 좌표 예측에 대한 loss를 증가시키고 confidence 예측에 대해서는 loss를 감소시킨다. (물체를 가지지 않았으니 box의 좌표를 고정해야한다. 라고 모델에게 알려주는 방식이다.)

이를 위해 두 개의 파라미터를 사용하는데 $\\lambda\_{coord}$와 $\\lambda\_{noobj}$이다. 저자들은 두 파라미터를 각각 5와 0.5로 설정했다고 한다.

Sum-squred error는 또한 큰 box와 작은 box들에서 동일하게 가중치를 두는 문제가 있다. 

Error metric이 큰 박스에서의 small deviation를 작은 박스에서 보다 덜 중요하게 반영해야하기 때문에 width, height를 바로 사용하지 않고 $\\sqrt{width}$, $\\sqrt{height}$를 사용하게 된다.

(아래의 loss function을 보면 이해가 갈텐데 loss function을 각 coordinate과 class에 대한 항으로 구성하는데 w와 h를 각각 sqrt취함으로써 상대적으로 덜 강조하게 된다.)

학습하는 동안, 하나의 bounding box predictor가 하나의 object만 다루기를 원하기 때문에 하나의 predictor가 하나의 물체를 예측하는데에 역할을 하도록한다. 

(어디서 ground truth와 가장 높은 IOU를 가졌는지를 기반으로 예측을 하게 된다.)

이로서, 각각의 predictor는 특정 사이즈, 비율, 물체의 class를 예측하는데에 점점 성능이 좋아진다.

(하나의 preditor가 하나의 object에 집중하기 때문에)

결과적으로, 이는 전체 recall을 높게 만든다.

Loss function은 다음과 같다.

![image](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FYdch1%2Fbtq0myJ50et%2FE8aHux74dvXDPCWk50vXb0%2Fimg.png)

---

위의 loss function에서 명심해야할 점이 두가지있다.

1\. 물체가 해당 grid cell에 있을 때만 classfication error를 penalize한다는 것이다.

($\\mathds{1}\_i$ 와 $\\mathds{1}\_{i,j}$는 물체가 cell i에 없으면 0이기 때문.)

2\. predictor가 ground truth box에 대해 책임이 있을때만(해당 grid cell에서 가장 높은 IOU를 가졌을 때이다.) bounding box 좌표값 error를 penalize한다는 것이다. 

---

모델 학습에 사용되는 Learning rate는 다음과 같다.

처음 epoch에서는 천천히 learning rate를 $10^{-3}$에서 $10^{-2}$로 증가시켰는데, 처음부터 큰 learning rate로 학습을 시키면 불안정한 grdient때문에 모델이 diverge할 수 있기 때문이다.(업데이트가 너무 많이 되므로)

그 후 75 epochs만큼에서는 learning rate를 $10^{-2}$로 하였고 30 epochs에서는 $10^{-3}$,

마지막 30 epochs에서는 $10^{-4}$로 하였다.

Overfitting을 예방하기 위해서는 **1) dropout**과 **2)extensive data augmentation**을 사용하였다.

**1) Dropout**

처음 connected layer후의 droupout 확률이 0.5인 한 dropout layer는 layer간의 co-adatation을 예방한다.

---

_★ Co-adapatation이란?_

이 용어에 대해 언급한 논문([arxiv.org/pdf/1207.0580.pdf](https://arxiv.org/pdf/1207.0580.pdf))의 Abstract에서 내용을 확인할 수 있었다.

_When a large feedforward neural network is trained on a small training set, it typically performs poorly on held-out test data. This “overfitting” is greatly reduced by randomly omitting half of the feature detectors on each training case. This prevents complex **co-adaptations in which a feature detector is only helpful in the context of several other specific feature detectors.** Instead, each neuron learns to detect a feature that is generally helpful for producing the correct answer given the combinatorially large variety of internal contexts in which it must operate. Random “dropout” gives big improvements on many benchmark tasks and sets new records for speech and object recognition._

요약하자면, Co-adaptation은 각각의 layer들이 feature를 뽑아내는 과정에서 서로 독립적으로 학습이 되지 않아서 자기들끼리의 일종의 dependency를 만든 것이라 볼 수 있다. 이는 각 레이어들이 그 숫자만큼 full power로 작동하지 않아서 resource 낭비가 되므로 dropout으로 이를 완화시킬 수 있다. 

맞는 예일지는 모르겠으나 만약 매우 큰 matrix가 rank낮다면, 즉 column끼리 dependent한 경우가 많다면 이는 몇개의 vector만으로 matrix가 이루어진 것이므로 필요없는 column이 많다는 얘기가 된다. 그러므로 큰 공간을 차지하는 것이 자원낭비가 되는 것이다.

---

**2) Extensive Data Augmentation**

데이터를 늘려 overfitting을 하는 방식으로 random scaling과 translation을 original image들의 20퍼센트에 적용하여 사용하는 것이다.

랜덤으로 exposure과 saturation을 이미지에 최대 HSV color space에서 1.5로 적용하여 사용한다.

---

### **3\. Inference**

학습에서와 같이 test image에서 detection을 예측하는 것은 하나의 network evaluation만을 필요로한다.

Grid design은 bounding box 예측에서 공간 다양성을 추구한다.

종종 물체가 어떤 grid cell에 속하는지 명확하고 네트워크는 각 객체에 대해 하나의 상자만 예측하지만, 몇몇의 큰 물체나 cell의 경계에 있는 물체들은 multiple cells에 의해 잘 localized된다. 이런 detection을 교정하기 위해서는 Non-maximal suppression이 사용될 수 있다. 이는 mAP에서 3%정도의 점수를 올릴 수 있다고 한다.

---

### **4\. Limitations of YOLO**

**1\. Spatial constraints**

공간적 제약때문에 새떼와 같이 작은 물체들이 모여있는 것을 탐지하는 것이 어렵다.

**2\. Generalization**

일반적이지 않은 가로세로비율의 bounding box와 같은 경우에 예측이 어렵다. 또한 여러 downsampling layer를 거친 상대적으로 rough한 feature를 사용한다.

**3\. bounding box size**

Bounding box의 사이즈에 관계없이 error를 다루는데 같은 error라도 box가 작을수록 IOU에 큰 영향을 미치게 때문에 문제가 된다.

---

## **Experiments**

#### **1) 논문에서는 YOLO를 다른 실시간 detection systems을 _PASCAL VOC 2007_에서 비교하였다. **

앞서 언급했듯이 YOLO는 R-CNN의 background false positives의 수를 줄여 큰 성능 향상을 하게 했다.

![image](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbBsiJ3%2Fbtq0kDZeJMd%2F87tr5tNMyARvI2uytI3xtk%2Fimg.png)

위의 결과표를 통해 YOLO의 우수함을 알 수 있다.

또한, Fast R-CNN과의 error도 비교하였는데,

![image](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fdv6Hn8%2Fbtq0pxQ0JP8%2FVEgcVRRBTry1IQ7OwkCC8K%2Fimg.png)

빨간부분의 background부분에서 YOLO가 더 error가 적음을 보여준다.

하지만 다른 부분에서는 Fast R-CNN이 더 적은 error를 보여주었는데, 이를 보고나면 자연스럽게 합치면 되

지 않을까? 라고 생각할 것이다.

그래서 저자들은 합친 결과도 공개하였다.

![image](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FKy3JU%2Fbtq0mychhrJ%2F6kkkGCrAwjENGW8bXOJf21%2Fimg.png)

모든 R-CNN이 예측한 bounding box에서 YOLO또한 비슷하게 예측을 하고 있는지 체크하고, 만약 그렇다면 YOLO가 예측한 확률과 두 박스간의 겹치는 면적을 기반으로 boost를 주는 방식이다.

위에서 볼 수 있듯이 합친 버전은 원래의 Fast R-CNN보다 mAP를 3.2% 증가시켰음을 알 수 있다.

#### **2) 또한 _VOC 2012_에 대한 결과도 보여주었으며 당시 SOTA 시스템과 mAP를 통해 비교하였다.**

기본 YOLO모델은 다른 SOTA 시스템보다 특정 카테고리에서 낮게 나왔음을 확인할 수 있다. 이는 이전에 언급한 YOLO의 한계점 때문인데, 이와달리 YOLO를 Fast R-CNN와 합친 버전은 우수한 성능을 보여주었다.

![image](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fou3IA%2Fbtq0nN771cs%2Fp1vViJgokiRInKxWKkUnrK%2Fimg.png)

#### **3) 마지막으로, YOLO가 _새로운 도메인_에 대해서 다른 모델들보다 우수함을 확인한다.**

아래에서 확인할 수 있듯이 다른 domain에서의 detection이 다른 system보다 월등히 좋다.

![image](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FmyZ9s%2Fbtq0nOlEtnQ%2FIleE74kGzan7ZxG8WjjbO1%2Fimg.png)

---

## **Conclusion**

YOLO는 unified model로 다른 classifier based 방법들보다 속도가 빠르다는 장점이 있다.

Fast YOLO는 가장 빠른 버전으로 실시간 탐지가 가능하게 한다. 

아래에서 결과를 직접 눈으로 확인할 수 있다.

![image](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FdPI9fn%2Fbtq0mypN3DL%2FfkUDVsOIL4nFtjxVNeU4B1%2Fimg.png)

---
