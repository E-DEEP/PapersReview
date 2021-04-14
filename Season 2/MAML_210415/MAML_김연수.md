
[Model-Agnostic Meta-Learning for fast adaptation of deep networks](https://arxiv.org/abs/1703.03400)


## Overview
(You should include contents of summary and introduction.)

좋은 Weight로 initialize하는 방법에 대한 것.
**어떻게 하면 좋은 initial weight를 찾을 수 있는가?**
**어떤 inital weight를 가지면 모르는 태스크들에 대해서도 빨리 적응시킬 수 있는가?**


- Key Point

![스크린샷 2020-08-15 오전 12 11 28](https://user-images.githubusercontent.com/48315997/90264311-df83d200-de8b-11ea-8f03-8475e54d2ff7.png)

    - **주어진 태스크들에 대해서 1 step 갔을 때, 모든 태스크에 대해서 로스가 미니멈이 되는 현재의 세타를 찾는 것!**

## Related work (Basic concepts)

- Model-Agnostic
    - 학습에 사용된 model이 무엇인지에 구애받지 않고 독립적으로 모델을 해석할 수 있다는 뜻
    - 즉, 학습에 사용되는 모델과 설명에 사용되는 모델을 분리
    - 이 방법은 어떤 모델이든 상관 없이 적용할 수 있는 방법이다.


- Meta learning
    - learning to learn
    - 좋은 메타 러닝 모델 = 트레이닝 때 접하지 않았던 새로운 태스크나 환경에 대해서 잘 적응되거나 일반화가 잘 됨.
    - Reinforcement learning과 결합한 **meta-learning**(meta reinforcement learning) 얘기가 많이 나오고 있음
    - Few-shot classification은 supervised-learning 상황에서 meta-learning을 활용한 예시임.
        - **하나의 데이터셋 자체가 하나의 data sample로 활용되고 있음.**
        
        ![image](https://user-images.githubusercontent.com/48315997/90264389-fd513700-de8b-11ea-8325-d244ed7ce734.png)
        
        - 즉 Meta-learning에서는 training, test의 개념이 일반과 약간 다르고, 그 때 들어가는 데이터셋도 다르다.
        - 약간의 fine-tuning 과 유사한 접근법

- Few-shot learning
    - 적은 수의 데이터로 학습 시키는 것
    - one-shot learning : 한 장의 데이터만으로 학습 시키는 것
    - K-shot learning이라고 많이 부르는 듯


## Methods
(Explain one of the methods that the thesis used.)

![스크린샷 2020-08-15 오전 12 11 28](https://user-images.githubusercontent.com/48315997/90264311-df83d200-de8b-11ea-8f03-8475e54d2ff7.png)


- 세타 1,2,3 -> 1,2,3번 태스크라고 보자.
- 만약 1번 태스크에 대해서 학습을 시킨다 그러면, 1의 optimum point로 가게끔 학습시켜야 함.
- 근데 샘플이 많지 않으니까 중간에 Local min.에 빠지는 경우 등 원하는 방향으로 흘러가지 않을 가능성이 더 큼.
- 메타러닝을 통해 세타를 저 점(화살표가 가리키는점)에 가지고 오면 1,2,3에 가장 가까운 포인트. 즉, 여기를 어떻게 보낼거냐는 문제!
- 예를 들어, 3번 태스크에 대한 One-shot이 주어졌을 때 **gradient 1번해서 3번쪽으로 딱 가고 싶은 것**.

### 수식으로 보는 아이디어

![image](https://user-images.githubusercontent.com/48315997/90264428-0f32da00-de8c-11ea-8f6f-c95ea78fe789.png)

- i번째 태스크에 대한 세타프라임 정의
- 1 step gradient를 갔을 때의 포인트임.

우리는 이 세타 프라임의 포인트에서 loss가 최소가 되게 하고 싶음! 
--> **세타 프라임에서 로스가 미니멈이 되는 세타**를 찾고 싶다!!

![image](https://user-images.githubusercontent.com/48315997/90264444-13f78e00-de8c-11ea-92b2-353d4b21a491.png)

- 세타 프라임을 위에서 정의했으므로, loss 식에 넣을 수 있다.
- 세타에서 1 스텝 더 간 포인트의 미니멈을 정의하게 되는 것(== 세타프라임)

![image](https://user-images.githubusercontent.com/48315997/90264455-178b1500-de8c-11ea-9ddf-c9e5ae387250.png)

- 세타에 대해서 미분함!
- 태스크가 여러 가지 있으니까 여러 가지에 대해 **전체가 미니멈이 되는 포인트**를 찾아야 함.
- 세타 프라임 안에는 이미 세타에 대한 미분이 들어가있으므로 여기서 미분을 또 하게 되면 **hessian**이 나올 것이다(?)


다시 요약하자면, 

1. 우리가 찾고 싶은 세타는 태스크 각각을 minimize하는 세타가 아니라,

2. 주어진 태스크들에 대해서 1 step 갔을 때 모든 태스크에 대해서 minimum이 되는 

3. 지금 현재의 세타를 찾는 것.


### Algorithm

1. Model-Agnostic Meta-Learning

![image](https://user-images.githubusercontent.com/48315997/90264464-1b1e9c00-de8c-11ea-81c4-7d5263555fc9.png)

- 우선 파라미터들을(세타) 랜덤하게 initialize
- 태스크들을 sampling하고, for문 안에서는 각각의 태스크에 대한 그래디언트를 찾음
    - 1 step 더 가는 그래디언트
- 모든 태스크들에 대해서 다시 그래디언트를 해서 sum함.
- 원래 파라미터 세타를 업데이트
- 위의 과정 반복


2. MAML for Few-shot Supervised learning

![image](https://user-images.githubusercontent.com/48315997/90264528-3093c600-de8c-11ea-94a2-81a9e899ca75.png)

- regression loss 
- classification loss

- Few-shot 이미지 classification일 경우 loss를 크로스 엔트로피로 계산
- 메타 업데이트를 위한 샘플링을 함.
    - 최종 메타 파라미터(세타)를 찾기 위해서 쓰이는 샘플들
    - 이전의 샘플 D는 세타 프라임을 위한 샘플들임.


3. MAML for Reinforcement Learning

![image](https://user-images.githubusercontent.com/48315997/90264539-34bfe380-de8c-11ea-88c6-4c1635a874f1.png)

- Reward는 미분이 가능해야하니까 policy gradient를 사용함.
- f(theta)는 policy를 나타내는 뉴럴 넷
- negative reward를 loss로 사용
- 에피소드 길이만큼 쭉 진행해서 sum한 것이 loss가 됨.
- 마찬가지로 각 태스크에 대해서 샘플을 하고, 전체 에피소드 length 만큼 K번 trajecctories 샘플
- 그래디언트 계산해서 1 step 더 간 포인트를 찾아내고
- 1 step 더 간 파라미터 셋에서의 샘플 trajectories들을 샘플링 한 다음에
- 다 하고 바깥으로 나와서 파라미터 업데이트를 위해 loss를 계산하고 그래디언트를 구함.



<br>

1,2,3번 모두 크게 다르지 않다.

### Experimental Result

- The goal of our experimental evaluation
    - 얼마나 빨리 learning을 할 수 있는가
    - 서로 다른 도메인에서 사용이 될 수 있는가 -> supervised regression, classification, reinforcement learning
    - 여러 번 gradeint update를 할수록 더 좋아지는가

1. Regression

- sine wave fitting 을 실험.
    - 임의의 sine wave 를 만들어서, 그것을 fitting 하는 예제

![image](https://user-images.githubusercontent.com/48315997/90264553-3a1d2e00-de8c-11ea-85ad-e10a89dd15a8.png)

- 삼각형 : 트레이닝 샘플
- 빨간색 = ground truth
- 연두색 = 메타러닝을 통해 학습된 pre-weight
- 초록색 = 그래디언트를 1 step / 10 steps 밟았을 때

a) K = 5(5개의 샘플이 주어졌을 때)

b) K = 10 (10개의 샘플이 주어졌을 때)
- 그래디언트 10번하면 거의 똑같이 됨

c,d) Pre-traineed model 사용
- fitting하는 뉴럴 넷을 sine wave task로 잔뜩 만들어서 평균적인 sine wave에 대해 학습된 것
- pre-update는 meta-learning으로 만들어진 모델과 유사하나 1 step 간다고 해서 막 변하지 않음.
    - fitting이 잘 안된다.


2. Classification

- One-shot, few-shot learning에서 주로 쓰이는 데이터 셋 : Omniglot dataset
    - few-shot learning의 mnist같은 데이터셋

![image](https://user-images.githubusercontent.com/48315997/90264620-51f4b200-de8c-11ea-9a4c-e7bc5aaa7a0b.png)

![image](https://user-images.githubusercontent.com/48315997/90264625-54efa280-de8c-11ea-9b16-7154ffe38326.png)

- First order approx
    - 두 번 미분하기 위해 hessian이 들어간다고 했음. 
    - 근데 ReLU는 중간에 미분 불가능한 포인트가 있고, 이를 제외하면 Linear함.
    - 따라서 이것을 first order까지만 계산하고 업데이트해도 성능이 그렇게 떨어지지 않는다고 함.
    - 정석대로 두 번 미분하면 33%정도 더 오래걸린다고 함.


3. Reinforcement learning

- 2D navigation 실험 : 위치를 정해주고 goal까지 가기

![image](https://user-images.githubusercontent.com/48315997/90264693-6e90ea00-de8c-11ea-8f12-c1399d0668e4.png)

- 3step update하면 잘 간다
- pre-train하고 fine-tuning하는 방법과 비교...


## Code

from [dragen1860 / MAML-Pytorch](https://github.com/dragen1860/MAML-Pytorch/blob/master/meta.py)

```py
    def forward(self, x_spt, y_spt, x_qry, y_qry):
        """
        :param x_spt:   [b, setsz, c_, h, w]
        :param y_spt:   [b, setsz]
        :param x_qry:   [b, querysz, c_, h, w]
        :param y_qry:   [b, querysz]
        :return:
        """
        task_num, setsz, c_, h, w = x_spt.size()
        querysz = x_qry.size(1)

        losses_q = [0 for _ in range(self.update_step + 1)]  # losses_q[i] is the loss on step i
        corrects = [0 for _ in range(self.update_step + 1)]


        for i in range(task_num):

            # 1. run the i-th task and compute loss for k=0
            logits = self.net(x_spt[i], vars=None, bn_training=True)
            loss = F.cross_entropy(logits, y_spt[i])
            grad = torch.autograd.grad(loss, self.net.parameters())
            fast_weights = list(map(lambda p: p[1] - self.update_lr * p[0], zip(grad, self.net.parameters())))

            # this is the loss and accuracy before first update
            with torch.no_grad():
                # [setsz, nway]
                logits_q = self.net(x_qry[i], self.net.parameters(), bn_training=True)
                loss_q = F.cross_entropy(logits_q, y_qry[i])
                losses_q[0] += loss_q

                pred_q = F.softmax(logits_q, dim=1).argmax(dim=1)
                correct = torch.eq(pred_q, y_qry[i]).sum().item()
                corrects[0] = corrects[0] + correct

            # this is the loss and accuracy after the first update
            with torch.no_grad():
                # [setsz, nway]
                logits_q = self.net(x_qry[i], fast_weights, bn_training=True)
                loss_q = F.cross_entropy(logits_q, y_qry[i])
                losses_q[1] += loss_q
                # [setsz]
                pred_q = F.softmax(logits_q, dim=1).argmax(dim=1)
                correct = torch.eq(pred_q, y_qry[i]).sum().item()
                corrects[1] = corrects[1] + correct

            for k in range(1, self.update_step):
                # 1. run the i-th task and compute loss for k=1~K-1
                logits = self.net(x_spt[i], fast_weights, bn_training=True)
                loss = F.cross_entropy(logits, y_spt[i])
                # 2. compute grad on theta_pi
                grad = torch.autograd.grad(loss, fast_weights)
                # 3. theta_pi = theta_pi - train_lr * grad
                fast_weights = list(map(lambda p: p[1] - self.update_lr * p[0], zip(grad, fast_weights)))

                logits_q = self.net(x_qry[i], fast_weights, bn_training=True)
                # loss_q will be overwritten and just keep the loss_q on last update step.
                loss_q = F.cross_entropy(logits_q, y_qry[i])
                losses_q[k + 1] += loss_q

                with torch.no_grad():
                    pred_q = F.softmax(logits_q, dim=1).argmax(dim=1)
                    correct = torch.eq(pred_q, y_qry[i]).sum().item()  # convert to numpy
                    corrects[k + 1] = corrects[k + 1] + correct



        # end of all tasks
        # sum over all losses on query set across all tasks
        loss_q = losses_q[-1] / task_num

        # optimize theta parameters
        self.meta_optim.zero_grad()
        loss_q.backward()
        # print('meta update')
        # for p in self.net.parameters()[:5]:
        # 	print(torch.norm(p).item())
        self.meta_optim.step()


        accs = np.array(corrects) / (querysz * task_num)

        return accs
```


## Additional studies
(If you have some parts that cannot understand, you have to do additional studies for them. It’s optional.)

**Advanced researches**

- Meta-SGD : 성능이 더 괜찮음
- Bayesian Model-Agnostic Meta-Learning
    - 한 포인트로 지정하는 것이 아니라 probability를 이용
    - optimum point들이 서로 가깝고 몰려있으면 좋겠지만, 여러 곳에 있을 수도 있고 확률적으로 분포할 수도 있음. 
    - 이런 것들을 어떻게 잘 정의할 수 있느냐에 대한 approach인 듯

- Gradient-based meta-learning with learned layerwise metric and subspace
    - 그래디언트 포인트들이 마구잡이로 갈 수 있지만, optimum 포인트들이 분포해있는 subspace가 있을 수 있음.
    - subspace 안에서만 그래디언트를 가면 훨씬 더 빨리 갈 수 있음. 그것 관련한 논문

- ICML 2019 : Online Meta-Learning (같은 저자!)

## References
(References for your additional studies)

https://www.youtube.com/watch?v=fxJXXKZb-ik

https://talkingaboutme.tistory.com/entry/DL-Meta-Learning-Learning-to-Learn-Fast

https://elapser.github.io/machine-learning/2019/03/08/Model-Agnostic-Interpretation.html

