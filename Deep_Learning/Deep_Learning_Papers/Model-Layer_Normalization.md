## Layer Normalization

- [Layer Normalization](https://arxiv.org/abs/1607.06450) (2016) by Jimmy Lei Ba, Jamie Ryan Kiros, Geoffrey E. Hinton

----

### Simple summary

- Works well for recurrent networks with mini-batch.
- Independent with batch size.
- 입력 데이터의 scale에 robust
- 가중치 행렬의 scale 및 Shift에 robust
- 학습이 진행되면서 자연적으로 updated scale이 작아짐
- but, Batch Norm still perform better for CNNs.