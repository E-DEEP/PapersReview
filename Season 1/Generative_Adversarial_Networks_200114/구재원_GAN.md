## Generative Adversarial Network

Original Paper: [[pdf](https://arxiv.org/pdf/1406.2661.pdf)]



#### Key Point

- GAN을 처음으로 제안한 논문: 

  : 이전에도 generative model 은 존재하였지만 방식이 까다로워 사람들이 사용을 잘 안했다고 함

- Generator 와 Discriminator로 구성되어 있음 

  + Generator: 위조지폐를 만드는 도둑

  + Discriminator: 진짜 지폐인지 아닌지를 판별하는 경찰 

    => 서로가 같이 학습되어 가는 과정 

    *** Generator, Discriminator 모두 multilayer perceptron임 



#### Adversarial Nets

1. Two-player  Minimax Game

   ![image-20210112162238241](C:\Users\Home2\AppData\Roaming\Typora\typora-user-images\image-20210112162238241.png)

   

   - Discriminator:  수식의 첫 부분 

     + output: single scalar

     + D(x): 데이터 x가 generator에서 나온 것이 아닌 data일 확률

     + 학습 방법:  D(x)가 input에 대해 올바르게 어디서 온 것인지 판별 할 수 있도록 학습 

       ​                  => **maximization** 

   - Generator: 수식의 뒷 부분

     + output: 데이터 x와 비슷한 output ex. 데이터 x들과 비슷한 이미지

     + noise variable을 데이터 x의 distribution가 맞춰주는 것
     + 학습 방법: log(1-D(G(z)))을 **minimization**

     

2.  Theoretical Analysis of Adversarial Nets

   - generator와 discriminator 학습 방식

     ![image-20210112202544439](C:\Users\Home2\AppData\Roaming\Typora\typora-user-images\image-20210112202544439.png)

     

     - 위 그림에서 파란선: discriminator의 분포, 점선: data의 분포, 초록선: generator의 분포를 나타냄

     - generator는 data와 비슷한 분포를 띄게끔 학습을 할 것임 

     - 학습이 진행되면 왼쪽에서 오른쪽으로 순차적으로 분포가 바뀔것임

     - 만약 마지막처럼 된다면, 즉 generator의 분포가 data의 분포와 같아진다면 discriminator는 1/2로 수렴할것

          => 방지 위해 두 가지 방법 사용 

       1.   Discriminator는 k번 optimize 할때, generator는 1번 optimize
       2. 학습 초반에는 log(1-D(G(z)))를 minimize 하는 것이 아니라 log D(G(z))를 maximize하도록 학습