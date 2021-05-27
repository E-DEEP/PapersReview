
2021.05.27  
발표 전 논문리뷰 업데이트에 결석을 하여서  
발표 리뷰로라도 대체합니다! ㅠ


------------

## [ VideoBERT ]

- BERT를 이용한 multi modal
- Text Video generation 가능
ex. 코코아 가루 => 코코아 가루를 활용한 요리

### ((Abstract))
high-level features 가능  
visual과 language 정보를 함께 활용 가능  
다양한 task에도 적용할 수 있다.  
방대한 training data와 cross model classification이 핵심 개념.  

### ((Introduction))
인간의 언어가 high-level object를 구현할 수 있음  
=> self supervision

비디오와 언어의 관계에 대해 모델링을 할 때 핵심 개념  
1. ASR
2. VQ
3. BERT

### ((Related Work))
- Conditional 모델 활용 (ex. 데이터의 파티션을 나눔_partitioning)
- Cross-modal learning 

### ((Method))
1. visual token 
기본적으로 BERT모델을 활용.  
discrete token sequence를 넣는다. => 이것이 visual words  
VQ(?vq를 통해 시퀀스를 구성?)  

2. 구분자

3. training region
  1) text-only
  2) video-only
  3) video-text : 두 도메인 사이의 correspondence. relationship에 대해 학습한다.  
=> 다양한 task에 사용될 수 있음. 예시: zero-shot 

### ((Future Work))
주로 쿠킹 데이터에 대해 다룸.
language representation에 대한 futre work를 기대할 수 있음






## [ Vision Transformern(ViT) ]

paper : An Image is Worth 16x16 Words:
Transformers for Image Recognition at Scale


### ((Background))

LSTM의 시대 대신 Transformer를 응용한 모델이 대세.  
GPT의 응용이 대세.  
Transformer를 Vision에 응용한 예시: VideoBERT, iGPT 등등  

- Attention: Encoder, Decoder와의 상관관계를 바탕으로 추출.


### ((Method))
- Patch+Position Embedding

- Transformer Encoder

Eq1. Embedded Patches : 가까운 patch가 유사한 position embedding을 가진다.  
Eq2. Multiheaded Self-Attention  
Eq3. MLP  
Eq4. LN Layernorm  


### ((Etc.))
- Fine-tuning 과 Higher resolution
- Large-scale 데이터 셋에서는 높은 결론.


### ((+참고 DeiT))













