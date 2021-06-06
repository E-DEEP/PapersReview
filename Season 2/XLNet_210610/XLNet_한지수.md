# XLNet: Generalized Autoregressive Pretraining for Language Understanding
### Unsupervised representation learning on NLP
: Autoregressive (AR) language modeling and Autoencoding(AE)
    1. Pretrain neural networks on large-scale unlabeled text corpora
    2. Fine-tune the models or representations on downstream tasks      
  
- AR Language Modeling: estimate the probability/density distribution of a text corpus with an autoregressive model
- AE Language Modeling: aims to reconstruct the original data from corrupted input ex. BERT
    - Problem 1) Artificial symbols like [MASK] are absent form real data at finetuning time -> pretrain-finetune discrepancy
    - Problem 2) not able to model the joint probability using the product rule

### XLNet
: a generalized autoregressive method that leverage both AR & AE
1. maximize the expected log likelihood of a sequence all possible permutations of the factorization order
2. Autoregressive objective > does not suffer from pretrain-finetune discrepancy 
3. integrates the segment recurrence mechanism and relative encoding scheme of Transformer-XL
4. reparameterize the Transformer(-XL) network 
