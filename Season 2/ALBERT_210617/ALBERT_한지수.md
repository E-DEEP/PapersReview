아직 시험기간이라 가볍게 리뷰만하고 종강 후에 내용을 덧붙일 예정입니다.

# ALBERT: A Lite BERT for self-supervised Learning of Language Representation

## Introduction
- common practice to pre-train large models and distill them down to smaller ones for real application
- Model Limitation Problem) Is having better NLP models as easy as having larger models?
### ALBERT incorporates two parameter reduction techniques that lift the major obstacles in scaling pre-trained models.
- **Factorized embedding parameterization**: by decomposing the large vocabulary embedding into two small matrices (size of the hidden layers-the size of vocabulary embedding)
- **Cross-layer parameter sharing**: prevent the parameter from growing with the depth of the network
- Additional- SOP: a self-supervised loss for sentence-order prediction
