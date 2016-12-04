# Word2Vec Summary

## History

- 단어의 Vector화는 다음의 이론([Distributional semantics](https://en.wikipedia.org/wiki/Distributional_semantics#Distributional_Hypothesis))을 기본으로 함 
- Vector화 방법
	1. one-hot representation, Problem: 단어 사이의 관계를 표현할 수 없음
	2. cooccurrence matrix X: 단어 사이의 관계를 표현할 수 있어짐. 그러나 차원이 너무 큼
	3. Singular Value Decompostion : 너무 비쌈

### NNNL

Feed-Forward Neural Net Language Model라 불리느 이 모델은 일반적인 MLP와 유사.  
Input Layer, Projection Layer, Hidden Layer, Output Layer로 구성.

![model](https://shuuki4.files.wordpress.com/2016/01/nnlm.png?w=1000)

출처 : https://shuuki4.wordpress.com/

one-hot -> Projection Layer -> Hidden Layer -> Output Layer -> 모든 단어에 대한 확률 계산

즉, NxP + NxPxH + HxV (이녀석이 가장 비쌈. V = 모든 단어)

### RNNLM

NNLM을 Recurrent Neural Network의 형태로 변형한 것  
Input, Hidden, Output Layer로 구성

![model](https://shuuki4.files.wordpress.com/2016/01/rnnlm.png?w=1000)

출처 : https://shuuki4.wordpress.com/

Input Layer -> Hidden Layer -> Output Layer -> 확률 계산

즉, HxH + HxV (이녀석이 가장 비쌈. V = 모든 단어)

## Word2Vec

모델이 기존의 모델들과 크게 다르지 않으나, 계산량을 확 줄여서 대중적인 모델이 된 녀석.  
두가지 모델이 존재 1. CBOW, 2. Skip-gram.

**Main Idea**

- Instead	of	capturing	cooccurrence	count directly
- Predict	surrounding	words	of	every	word

**Objective function**:	Maximize the log	probability of any context word given the current center word

### Continuous Bag-of-Words

주변에 있는 단어들을 통해서 중앙에 있는 단어를 예측하는 방식을 통해 학습하는 모델.

![model](https://shuuki4.files.wordpress.com/2016/01/cbow.png?w=520&h=600)

출처 : https://shuuki4.wordpress.com/

Input Layer -> Projection Layer -> Output Layer  
즉,  CxN + N x lnV, (C: 윈도우 크기)

### Skip-gram

중앙의 단어를 통해 주변 (앞, 뒤)의 단어를 예측하는 방식을 통해 학습하는 모델.

![model](https://shuuki4.files.wordpress.com/2016/01/skip-gram.png?w=552&h=600)

출처 : https://shuuki4.wordpress.com/

Input Layer -> Projection Layer -> Output Layer  
즉,  C(N + N x lnV) , (C: 윈도우 크기)

### V to ln(V)

모든 단어 수 V를 ln(v)로 줄이는 방법.

1. Hierarchical Softmax  
2. Negative Sampling  

etc. Subsampling Frequent Words: 자주 등장하는 단어를 제외하였을 때 성능이 증가함.


## GloVe

## Reference
- [word2vec 관련 이론 정리](https://shuuki4.wordpress.com/2016/01/27/word2vec-%EA%B4%80%EB%A0%A8-%EC%9D%B4%EB%A1%A0-%EC%A0%95%EB%A6%AC/)
- [Distributed Representations of Words and Phrases and their Compositionality](http://papers.nips.cc/paper/5021-distributed-representations-of-words-and-phrases-and-their-compositionality.pdf) by T Mikolov, 2013
- [Efficient Estimation of Word Representations in Vector Space](https://arxiv.org/abs/1301.3781) by T Mikolov, ‎2013
- [GloVe: Global Vectors for Word Representation](http://nlp.stanford.edu/pubs/glove.pdf)
- [CS224d Lecture 2 - Simple Word Vector representations: word2vec, GloVe](http://cs224d.stanford.edu/lectures/CS224d-Lecture2.pdf)
- [CS224d Lecture 3 - Advanced word vector representations: language models, softmax, single layer networks](http://cs224d.stanford.edu/lectures/CS224d-Lecture3.pdf)