## An Image is Wort 16x16 words: transformers for image recognition at scale

### Vision Transformer
Transformer를 image recognition에 적용한 것. SOTA 성능 달성 + 계산 비용 줄임



### Method

![image](https://user-images.githubusercontent.com/45954821/119675932-697bb500-be78-11eb-9cbd-7ef342c214b0.png)

이미지를 patch들로 나눔 (단어와 같음) --> linear하게 embedding --> 위치 embedding --> 결과 vector들을 trasformer encoder에 넣음 --> 학습 가ㅡㅇ한 classification token을 추가함

#### Vision Transformer (ViT)
- Standard transformer가 1D 시퀀스를 인풋으로 받기 때문에, 이미지를 flatten해서 sequence로 만들어준다. 
- Position embedding은 image patch의 위치(position) 정보를 보존한다. 

#### Fine tuning nd higher resolution 
- Pre train : Large Datasets, JFT-300M
- Fine tune : smaller downstream tasks

#### Architecture
- Pre norm 
- GELU 
