## Multi-Source Domain Adaptation with Collaborative Learning for Semantic Segmentation

- Multi-source unsupervised domain adaptation (MSDA)는 여러 source domain에서 학습된모델을 라벨링되지 않은 target domain에 적용시키는 것을 목표로 함
- 본 논문에서는 semantic segmentation을 위한 collaborative learning에 기반한 multi-source domain adaptation framework를 제안
- 첫째, source domain과 target domain간의 차이를 줄이기 위한 simple image translation method를 제안
- 둘째, target domain의 데이터를 전혀 보지 않는 domain adaptation을 위한 collaborative learning method 제안, 이는 source domain 전체에 걸쳐 필수적인 의미 정보를 충분히 활용가능하도록 함
- 또한, unsupervised domain adaptation과 비슷하게 레이블이 지정되지 않은 target domain 데이터를 활용하여 domain adaptation 성능을 더욱 향상
- 제안 방법은 라벨링된 Synscapes 및 GTA5 validation dataset과 라벨링되지 않은 Cityscapes training dataset에서 59.0% mIoU를 달성 
- SOTA 달성
