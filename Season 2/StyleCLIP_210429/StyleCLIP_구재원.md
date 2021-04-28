# StyleCLIP: Text-Driven Manipulation of StyleGAN Imagery

~~~
[ 정리 ]
Contribution: CLIP모델에 StyleGAN을 더해줌으로 주어진 text를 바탕으로 이미지를 변화시키는 모델을 제안함
~~~

## 1. Introduction
    
### Problem
    : 기존에는 미리 학습시에 사용되었던 이미지와 비슷한 semantic에 대해서만 image manipulation이 이루어졌다. 
    새로운 semantic으로 manipulate 시키기 위해서는 추가적인 이미지가 필요시되었다. 


### Contribution

  : text prompt에 기반한 방식으로 image manipulate가 되는 network를 제안하였다. 여기서 어떠한 text에 대해서도 
manipulaterk가 된다는 장점이 있다. 이를 위해 StyleGAN과 CLIP을 합쳤는데, 합치기 위해 다음과 같이 3가지 방법을 사용하였다. 

1. Text-guided latent optimization
2. Latent Residual Mapper
3. Mapping a text prompt into an input-agnostic direction in StyleGAN's style space


## 2. Method1: Latent Optimization
    
    - StyleGAN의 latent code가 CLIP space에서 loss가 최소화가 되도록 optimize 시키는 방식이다. 

    위의 식에서 w는 source latent code를 뜻하고, t는 text prompt를 뜻한다. 즉, source latent code가 StyleGAN generator에 나온 결과와 
text prompt의 t가 각각 CLIP의 embedding space에서의 cosine distance가 최소가 되도록 학습된다. L_ID는 identitiy loss를 뜻한다. 

## 3. Method2: Latent Mapper
    
    위의 방법1은 versatile하기에 더 효율적인 학습을 하기 위해 제안한 네트워크이다. 이 네크워크는 어떤 구체적인 text prompt t에 대해 주어진 latent image embedding에 대해
잘 manipulate 되도록 mapping 해주는 network이다. 


## 4. Medthod3: Global Direction

    더 잘 disentagle이 되도록 제안한 방법으로 text prompt가 StyleGAN에서 하나의 독립적인 global direction으로 mapping되도록해주는 방법이다.  

    








