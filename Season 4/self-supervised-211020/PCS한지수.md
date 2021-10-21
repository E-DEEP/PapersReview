# Prototypical Cross-domain Self-supervised Learning for Few-shot Unsupervised Domain Adaptation
[paper](https://arxiv.org/pdf/2103.16765.pdf)
## Goal
#### Problem
- Expensive to collect labels in the source domain, making most previous works impractical
- **Instance-wise cross-domain self-supervised learning**: only learns and aligns low-level discriminative features
 
## Idea
End-to-end Prototypical Cross-domain Self-Supervised Learning(PCS) Framework for Few-shot Unsupervised Domain Adaptation(FUDA)
- Encode and align semantic structures in the shared embedding space across domains
- capture category-wise semantic structures of the data by in-domain prototypical contrastive learning
- perform feature alignmenthrough cross-domain prototypical self-supervision

[image](https://raw.githubusercontent.com/zhengzangw/PCS-FUDA/master/images/framework.png)
1. In-domain Prototypical Contrastive Learning
2. Cross-domain Instance-Prototype SSL
3. Adaptive Protoypical Classifier Learning
