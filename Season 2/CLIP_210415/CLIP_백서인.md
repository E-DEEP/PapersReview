## Learning Transferable Visual Models From Natural Language Supervision

### Introduction
- natural language supervision으로부터 visual concept을 효과적으로 학습하는 neural network 제안 - "CLIP"
- CLIP은 단순히 visual category의 이름만 제공하여 어떠한 visual classification 문제에도 적용할 수 있음

### Major problems of Current approaches
- vision dataset을 생성하기 위해서는 매우 많은 노동이 필요함
- 일반적인 vision models은 하나의 task에는 잘 동작하지만 새로운 task에는 잘 동작하지 않음
- benchmarks에서는 좋은 성능을 내는 모델이 stress tests에서는 poor performance를 냄
-> 위의 문제를 해결하기 위한 neural net 제안
- 제안하는 모델은 풍부한 데이터로 학습, 최적화하지 않아도 classification에서 좋은 성능

![image](https://user-images.githubusercontent.com/48814946/114701308-99a24500-9d5d-11eb-9a1d-ab4444e1bc19.png)

### Approach
- 본 논문에서는 단순한 pre-training task를 scailing 하는 것이 competitive zero-shot performance를 달성하는 데 있어 충분함을 보임
- 본 논문에서 제안하는 방법론은 풍부한 양의 supervision을 사용:  the text paired with images found across the internet
- 이러한 데이터는 다음과 같은 proxy training task를 위해 사용됨: given an image, predict which out of a set of 32,768 randomly sampled text snippets, was actually paired with it in our dataset
- 이를 위해, CLIP Model은 image에서 다양한 visual concept를 인식하고 그겋을 그들의 name과 연결시키는 법을 학습해야 함
- 결과적으로 CLIP은 거의 모든 visual classification tasks에 적용 가능

![image](https://user-images.githubusercontent.com/48814946/114702783-78425880-9d5f-11eb-956f-154b28bd5322.png)

- CLIP은 computer vision의 major problems를 완화시키도록 고안됨
(1) Costly datasets: deep learning datasets은 직접 라벨링 등 많은 노동이 필요하지만, CLIP은 인터넷에 공개되어 있는 데이터로부터 text-image pair를 학습
(2) Narrow: 기존 model은 학습한 dataset외의 다른 카테고리에 대해 예측이 필요하다면 데이터셋을 새로 구축하여 재학습을 해야하지만, CLIP의 경우 단지 Model에 the names of the task’s visual concepts에 대해 알려주기만 하면 됨
(3) Poor real-world performance: benchmark task에 대해 좋은 성능을 내는 모델을 실제 task에 적용할 때 poor performance를 내는 경우가 많은데, CLIP의 경우 benchmark 데이터로 학습하지 않아도 benchmark task로 평가할 수 있어서 real task에서도 좋은 성능을 냄

### Key takeaways
(1) CLIP is highly efficient
![image](https://user-images.githubusercontent.com/48814946/114704414-88f3ce00-9d61-11eb-9f07-81ab8a23aafd.png)

(2) CLIP is flexible and general
![image](https://user-images.githubusercontent.com/48814946/114704460-97da8080-9d61-11eb-81e6-296ca8c76370.png)

### Limitations
- 추상적이거나 systematic한 문제에 대해 어려움을 겪음 
  > ex 1) 물건의 개수 counting, predicting how close the nearest car is in a photo
  > ex 2) 차 모델간 차이, 꽃의 종류 
- training data에서 커버되지 않은 데이터에 대해 poor generalization



### References
1) https://openai.com/blog/clip/
2) https://inforience.net/2021/02/09/clip_visual-model_pre_training/
