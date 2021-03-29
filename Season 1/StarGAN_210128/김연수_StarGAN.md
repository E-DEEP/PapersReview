# StarGAN: Unified Generative Adversarial Networks for Multi-Domain Image-to-Image Translation

## Abstract

- 기존의 연구들은 **limited scalability & robustness** in handling more than 2 domains


- StarGAN은 multi-domain I2I를 single model로 가능하게 함.
    - 이 얘기는 즉, 어느 타겟 도메인이든지 flexibly translating가 가능하다는 것이다


## Introduction

![image](https://user-images.githubusercontent.com/48315997/106007616-d749e700-60f9-11eb-9ef0-51bb419ba66e.png)


- 기존까지의 연구들은 two(or more) different domain을 가지고 translate를 시도했었음

- StarGAN은 single model을 가지고 multiple domain을 맵핑할 수 있게 한다.

> We propose StarGAN, a novel generative adversarial network that learns the mappings among multiple do- mains using only a single generator and a discrimina- tor, training effectively from images of all domains.


## Star Generative Adversarial Networks

![image](https://user-images.githubusercontent.com/48315997/106007863-17a96500-60fa-11eb-9867-41b5413e49b1.png)

(a) D는 real/fake를 구분하는 것과 동시에 domain classification 두 가지를 학습한다.

(b) G는 input image, target domain label을 받아 fake img를 생성한다

(c) G는 original domain label을 가지고 fake img를 다시 original로 reconstruct한다

(d) G는 real image와 구분 불가능해진다


### Adversarial Loss

![image](https://user-images.githubusercontent.com/48315997/106008628-d6658500-60fa-11eb-842c-8edf14f59fdb.png)

`src` : real data에서 왔는지 fake data에서 왔는지


### Domain Classification Loss

![image](https://user-images.githubusercontent.com/48315997/106008956-2ba19680-60fb-11eb-9a47-f1ab633621a0.png)


### Reconstruction Loss

![image](https://user-images.githubusercontent.com/48315997/106009028-4247ed80-60fb-11eb-8b1a-a39e51157072.png)

### Full Objective

![image](https://user-images.githubusercontent.com/48315997/106009048-47a53800-60fb-11eb-9afa-281132347048.png)


### Mask Vector

- 다수의 dataset들을 학습시킬때 label 정보가 각 dataset에 부분적으로만 있다는 문제가 있음

- 해결 -> Mask Vector
> we introduce a mask vector m that allows StarGAN to ignore unspecified labels and focus on the explicitly known label provided by a particular dataset. 

