## Deep Learning Tricks & Tips

CNN에 해당하는 팁들이 많음

- 항상 Shuffle 할 것. 절대 network에 같은 minibatch가 들어가지 않도록, (TensorFlow의 경우 Input Pipeline을 활용하면 좋을 듯.)
- 딥러닝은 역시 데이터가 많아야 한다. data arguments 등의 방법을 통해서 데이터 늘리기. 저자는 random transformations layer을 만들어서 해결
- 적은 데이터로 우선 network를 overfit 시켜본다. 이걸로 network가 converge 되는지 알 수 있음 from Karpathy
- overfitting을 방지하기 위해서 Dropout을 꼭 사용하자. 불확실성을 다루는 좋은 방법 ([Dropout as a Bayesian Approximation: Representing Model Uncertainty in Deep Learning](https://arxiv.org/abs/1506.02142))
- LRN(Local Response Normalization) pooling 은 피하고, 빠르고 효과 좋은 MAX pooling 을 활용.
- 이제는 거의 사용 안 하지만 비싸고 squeeze 되는 Sigmod, Tanh 는 사용하지말고, ReLU 종류를 쓰자. Backprop 에 효과적
- ConvNet 에서 activation fucntion(ex. ReLU)은 pooling 이후에 사용하자 (계산 줄임)
- 2012년에 나온 ReLU 보다는 0.1 PReLU (Parametric ReLu)를 쓰자. 빠르게 수렴하고 ReLU처럼 처음에 막히는 부분이 없음. ELU는 성능은 좋으나 조금 비쌈
- Batch Normalization 은 언제나 사용. 학습을 빠르게 할 수 있다. 적은 데이터 셋에도 잘 맞음
- Input 데이터를 [-1, 1] 로 Squeeze 해서 사용. 학습이 잘 된다기 보단 성능에 좋음
- Input 데이터를 Preprocessing 할때, zero-center 화 하고, 정규화 시키는 것은 중요!
	- ```>>> X -= np.mean(X, axis = 0) # zero-center```
	- ```>>> X /= np.std(X, axis = 0) # normalize```
- 항상 작은 모델을 선호, 정확도가 조금 떨어지더라도 메모리와 성능을 따져야 하는 경우가 많다.
- 앙상블은 보통 3%의 정확도를 올릴 수 있다 사용!
- xavier initialization을 잘 활용하자. Fully Connected layer에서만 사용하고, Conv layer에서는 사용하지 않음.
- Input 데이터가 spatial parameter(?)를 가지면, CNN의 끝에서 끝으로. SqueezeNet 을 활용.
- CNN 1x1 Layer는 학습도 되고 parameter수를 줄일 수 있는 좋은 방법
- GPU는 무조건 필수.
- 새롭게 모델을 구현한다면 모든 것들에 대해서 파라미터를 받을 수 있도록 만든다.
- 딥러닝은 만능이 아니기 때문에, 풀고자 하는 문제와 맞는 성격의 모델을 선택하자. 그리고 딥러닝은 속 안 을 아직 이해하기 힘들다는 점도 안고 가야하는 문제.

## References

- [The Black Magic of Deep Learning - Tips and Tricks for the practitioner](http://nmarkou.blogspot.kr/2017/02/the-black-magic-of-deep-learning-tips.html)
- [Must Know Tips/Tricks in Deep Neural Networks (by Xiu-Shen Wei)](http://lamda.nju.edu.cn/weixs/project/CNNTricks/CNNTricks.html)