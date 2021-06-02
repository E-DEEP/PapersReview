# DALL-E:Zero-shot 
작성자: 한지수

## Abstract
- Text-to-image generation task
- Model based on Transformer, autoregressively models the text and image tokens as a single stream of data.  

## Method
- Problem: using pixel directly as image tokens -> inordinate amount of memory for high-resolution image/ hard to prioritize modeling short-range dependencies
- Solution: a two-stage training procedure
  - Stage 1. Learning the Visual Codebook
        - dVAE(discrete variational autoencoder) to compress 256x256RGB -> 32x32 image tokens(8191 values) 
        - reduce the context size of the transformer without a large degradation in visual quality
  - Stage 2. Learning the Prior
        - concatenate up to 256 BPE-encoded text tokens with 1024 image tokens 
        - to model the joint distribution over the text and image tokens
        - to maximize the evident lower bound(ELB)

