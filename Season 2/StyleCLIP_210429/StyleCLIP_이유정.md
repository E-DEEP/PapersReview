# contributions

1. introduce an optimization scheme that utilizes a CLIP-based loss to modify an input latent vector in response to a user-provided text prompt. 
2. describe a latent mapper that infers a text-guided latent manipulation step for a given input image, allowing faster and more stable text-based manipulation. 
4. present a method for mapping a text prompts to input-agnostic directions in Style-GAN's style space, enabling interactive text-driven image manipulation. 


## Latent Optimization
#### term 1
  - w : latent code
  - G : pretrained 된 styleGAN의 generator
  - t : text prompt
  - => D(G(w), t) : CLIP embedding에서 G(w)와 t의 cosine 거리
#### term 2
  - w와 source latent code(given) 사이의 L2 loss 
#### term3 
  - identity loss
