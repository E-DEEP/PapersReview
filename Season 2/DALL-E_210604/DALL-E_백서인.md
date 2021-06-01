
## Zero-Shot Text-Image Generation

### Abstract
- Transformer 모델을 사용하여 text와 image를 autoregressively하게 모델링하는 방법 제안

### Method
- DALL-E는 autoregressive하게 텍스트와 이미지 토큰을 하나의 스트림으로 받아들여 트랜스포머를 학습
- Stage 1
  * discrete VAE를 학습하여 256x256 RGB 이미지를 32x32 그리드의 이미지 토큰으로 압축
  * 이를 통해 transformer가 처리해야 하는 context 크기를 192배 압축하면서, visual quality는 유지 가능
- Stage 2
  - 256 BPE-encoded text 토큰과 32 * 32 = 1024의 이미지 토큰을 concat
  - autoregressive transformer 학습

### Experiments
- Quantitative Results
<img width="502" alt="image" src="https://user-images.githubusercontent.com/48814946/120315183-06c26780-c317-11eb-90db-bcecb829ab60.png">

- Qualitative Findings
- "a tapir made of accordion"과 같은 문장이 들어왔을 때 모델은 모델은 몸이 아코디언이 있는 tapir를 그리거나, 키보드나 베이스가 tapir의 트렁크나 다리모양인 아코디언을 그리게 그림
- 이는 모델이 높은 수준의 추상화에서 특이한 개념을 구성할 수 있는 초보적인 능력을 갖고 있음을 말함
- image-to-image translation도 어느 정도 가능



### References
- https://littlefoxdiary.tistory.com/74
