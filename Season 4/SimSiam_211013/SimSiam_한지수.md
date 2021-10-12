# SimSiam: Exploring Simple Siamese Representation Learning
Few-shot learning을 배우면서 Siamese network을 공부한 적이 있는데, 간단하게 만든 구조를 배워보고 싶었다.

### Goal
- Siamese network maximize the similarity btw two augmentations of one image --> subject to certain conditions for avoiding collapsing solutions

### Method
Simple Siamese Network that can learn meaningful representations even using none of the following: negative sample pairs, large batches, momentum encoders.
- use **Stop-gradient operation** to prevent collapsing: model maximizes the similarity btw both sides (not using negative pairs or a momentum encoder)  
 
![](https://blog.kakaocdn.net/dn/b8aq7J/btq9lnNo4sV/k6cmLBkFxi9UOKcYcdivWK/img.png)
- structure
   - encoder network f = backbone + projection MLP head
   - prediction MLP head h
- output vector 
   - p1 = h(f(x1))
   - z2 = f(x2)
- Goal: Minimize their negative cosine similarity with stop gradient operation
   - Loss function = 1/2(similarity(p1, stopgrad(z2))+similarity(p2, stopgrad(z1)))
