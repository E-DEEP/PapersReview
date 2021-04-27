# StyleGAN  
작성자: 한지수  
#SynthesisNetwork #StyleMixingMetrics

## Introduction
- Re-design the generator architecture in a way that exposes novel ways to control the image synthesis process.
- Generator
    - a learned constant input -> adjust the "style" of the image at each convolution layer -> directly controlling the strength of image featues at different scales
    - embed the input latent code -> an intermediate latent space (by perceptual path length and linear separability)
        - input latent code: follow the probability density of the training data: some degree of unavoidable entanglement.(얽힘)
        - intermediate latent code: free from the restriction and allowed to be disentnagled

## Style-based generator  

<img src = "https://bloglunit.files.wordpress.com/2019/02/e18489e185b3e1848fe185b3e18485e185b5e186abe18489e185a3e186ba-2019-02-24-e1848be185a9e18492e185ae-3.43.31.png" width="400px" >   

### Difference from Style-based generator and Traditional generator  
Feed foward Network -> omit the input layer altogether and starting from a learned constant instead  
**- z -(Mapping Network)-> W -(Affine Transformation)-> A -(Synthesis network) -> (Result)**  
  
### Model Architecture
1. Mapping Network f: 8-layer MLP
2. Affine Transformation  
    : Learned affine transformations then specialize w to styles y that control AdaIN operations  

    <img src="https://chart.apis.google.com/chart?cht=tx&chl=AdaIn(x_i%2C%20y)%20%3D%20y_%7Bs%2Ci%7D%20%20%5Cfrac%7Bx_i%20-%20%20%5Cmu%20(x_i)%7D%7B%20%5Csigma%20(x_i)%7D%2By_%7Bb%2Ci%7D%20"> 
: a way to draw samples from each style from a learned distribution

3. Synthesis Network: Adaptive instance Normalization(AdaIN)
    - Gaussian Noise is added after each convolution
    - Add "A": learned affine transform
    - Add "B" : learned per-channel scaling factors to the noise input  
        : uncorrelated Gaussian noise, a dedicated noise image to each layer of the synthesis network.   


: a way to generate a novel image based on a collection of styles --> each style are localized in the network.

## Properties of the style-based generator
### 1.Style Mixing  
: mixing regularization(given percentage of images are generated using two random latent codes instead of one)  
### 2. Stochastic variation  
: Regraded as stochastic features -> follow distribution => by adding per-pixel noise after each convolution
### 3. Separation of global effects from stochasticity  
: Spatially invariant statistics reliably encode a specific instance  

## Disentanglement studies
<img src="https://i.imgur.com/tMan6dt.png">
**Goal**- a latent space that consists of linear subspace, each of which controls one factor of variation  
**Problem**- sampling density is induced by the learned piecewise continuous mapping  
-> generate realistic images based on a disentagled representation than on an entangled representation  
-> expect the training to yield a less entangled representation in an unsupervised setting   

### 1. Perceptual path length
Features that are absent in either endpoint may appear in the middel -> not properly seperated  
=> **measure how drastic changes the image undergoes** as we perform interpolation in the latent space  
--> use perceptually-based pair wise image distance --> the average perceptual path length   

### 2. Linear seperability
: find direction vectors that consistently correspond to individual factors of variation  
=> **metric that quantifies direction vectors that how well the space is seperated**
: fit a linear SVM to predict the label based on the latent-space point
