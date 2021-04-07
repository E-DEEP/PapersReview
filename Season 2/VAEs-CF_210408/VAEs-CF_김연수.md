# Variational Autoencoders for Collaborative Filtering

## Abstract

- VAE to CF for **implicit feedback**
- HOW? -> non-linear probabilistic model enables to go beyond the limited modeling capacity of linear factor models
- Result
  - information-theoretic connections to maximum entropy discrimination and the information bottleneck principle
  - outperforms several SOTA baselines
  - Video extended experiments

## Introduction

Collaborative filtering predicts what items a user will prefer by discovering and exploiting the similarity patterns across users and items

- Latent factor가 우세했음
  - **However, these models are inherently linear, which limits their modeling capacity**
  - -> applying neural networks to the collabora- tive filtering

> Vaes generalize linear latent-factor models and enable us to explore non-linear proba- bilistic latent-variable models, powered by neural networks, on large-scale recommendation datasets. We

- Key point
  - multinomial likelihood
  - 베이지안 inference



## Method

### Model

![image](https://user-images.githubusercontent.com/48315997/113880082-2006d600-97f6-11eb-8eb4-ad817d6f3914.png)



- similar to deep latent Gaussian model

The multinomial likelihood is less well studied in the context of latent-factor models such as matrix factorization and autoencoders.

- multinomial distribution is well suited to modeling <u>click data</u>. 
  - likelihood of the click matrix (Eq. 2)
- comparison
  - Gauss- ian and logistic likelihoods
  - e adopt the convention "confidence"

### Variational Inference

- To learn the generative model in Eq. 1, we are interested in esti- mating θ

- Selecting β: We propose a simple heuristic for setting β

  ![image](https://user-images.githubusercontent.com/48315997/113881084-10d45800-97f7-11eb-89dc-2326917fbe60.png)

### A taxonomy autoencoders

### Prediction



## Metrics

- Recall@R
- DCG@R
