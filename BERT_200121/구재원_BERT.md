## Keypoint

- 기존의 pre-training language model은 unidirectional 했는데, 처음으로 Masked Model Method를 사용하여 bidirectional 방식을 제안함.

  

## Introduction

- Problem: 기존의 pre-trained language representation
    1. feature based  : task-specific구조를 갖고 있음 ex. ELMO
    
2.  fine-tuning approach : 최소한의 task-specific parameter를 갖고 있음 ex. GPT
    
        → 모두 unidirectional 방식으로 학습이 되기에 제약이 있음
        
        

## BERT Model Architecture

: Pretraining Stage와 Finetuning Stage로 나뉘어짐. Pretraining Stage에서는 unlabel된 data를 사용하여 학습시키고, finetuning stage에서는 label된 data로 학습함. 

→ 다른 task에도 동일한 pretrained model를 사용한다는 것이 가장 큰 특징



### [Pretraining Stage]

- 2가지의 unsupervised task를 사용하여 학습함
    1. Masked LM:  input의 일정 비율의 token을 mask를 씌워, 그 token을 예측하는 것. 
    
    2. Next Sentence Prediction: 다음에 올 문장인지 아닌지를 판단하는 task.
    
       

### [Finetuning Stage]

- self-attention을 사용하기에 여러 문장을 한번에 넣을 수 있음 ( 기존에는 2 단계로 나눠서 학습시킴)
- 각 task에 맞는 input과 output를 넣어서 학습을 시키면 됨



## Result

 11개의 task에서 state of the art를 기록하였음



