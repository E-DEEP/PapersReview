## StyleCLIP: Text-Driven Manipulation of StyleGAN Imagery

### Abstract
- In this work, we explore leveraging the power of recently introduced Contrastive Language-Image Pre-training (CLIP) models 
- in order to develop a text-based interface for StyleGAN image manipulation that does not require such manual effort
- (1) First, introduce an optimization scheme that utilizes a CLIP-based loss to modify an input latent vector in response to a user-provided text prompt
- (2) Next, describe a latent mapper that infers a text-guided latent manipulation step for a given input image, allowing faster and more stable text-based manipulation 
- (3) Finally, present a method for mapping a text prompts to input-agnostic directions in Style-GAN’s style space, enabling interactive text-driven image manipulation

### Latent Optimization
<img width="422" alt="image" src="https://user-images.githubusercontent.com/48814946/116401296-0b05eb80-a866-11eb-9083-26dfed5009fb.png">
- w_s: latent code
- t: text prompt
- G: Pretrained StyleGAN generator
- D_CLIP: cosine distance between the CLIP embeddings of its tow arguments
<img width="422" alt="image" src="https://user-images.githubusercontent.com/48814946/116401350-1a853480-a866-11eb-911d-a27259af03a0.png">
- R: pretrained ArcFace network for face recognition
- <.,.> : computes the cosine similarity between it's arguments
<img width="422" alt="image" src="https://user-images.githubusercontent.com/48814946/116401946-c9297500-a866-11eb-9203-fbe6eafdce0e.png">

### Latent Mapper
<img width="848" alt="image" src="https://user-images.githubusercontent.com/48814946/116402114-faa24080-a866-11eb-8164-43050f941f95.png">
- three groups(coarse, medium, and fine)
- three fully-connected networks
<img width="367" alt="image" src="https://user-images.githubusercontent.com/48814946/116402411-5f5d9b00-a867-11eb-85d6-5c81c14f8d69.png">

### Global Directions
<img width="408" alt="image" src="https://user-images.githubusercontent.com/48814946/116403379-7486f980-a868-11eb-8947-77220bfbde49.png">
<img width="446" alt="image" src="https://user-images.githubusercontent.com/48814946/116403441-85d00600-a868-11eb-8d4a-e8a41145c17d.png">

