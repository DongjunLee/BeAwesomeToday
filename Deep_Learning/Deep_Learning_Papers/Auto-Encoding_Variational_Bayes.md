# Auto-Encoding Variational Bayes

[28] Kingma, Diederik P., and Max Welling. "Auto-encoding variational bayes." arXiv preprint arXiv:1312.6114 (2013). [pdf] (VAE) ⭐️⭐️⭐️⭐️


## Summary

> introduce stochastic variational inference and learning algorithm that efficient inference and learning in directed probabilistic models, in the presence of continuous latent variables with intractable poesterior distributions, and large dataset.

1. reparameterization of the variational lower bound yields a lower bound estimator that can be straightforwardly optimized using a standard stochastic gradient methods.
2. datasets with continuous latent varialbes per datapoint, posterior inference can be made especially efficient by fitting an approximate inference model to the intractable posterior using the propsed lower bound estimator.

### Manifold Hypothesis

- data is concentrated around a lower dimensional manifold

![image](../../images/manifold.png)

### Linear Regerssion

![image](../../images/linear_regression.png)

- auto-encoder find the red line
- VAE is find the red line and the distribution of data

### Algorithms

![image](../../images/vae_1.png)


![image](../../images/vae_3.png)

- Reparameterised Trick

![image](../../images/vae_4.png)


### Experiments

- Results

![image](../../images/vae_6.png)


## Reading

- [초짜 대학원생의 입장에서 이해하는 Auto-Encoding Variational Bayes (VAE) (1)](http://jaejunyoo.blogspot.com/2017/04/auto-encoding-variational-bayes-vae-1.html)
- [초짜 대학원생의 입장에서 이해하는 Auto-Encoding Variational Bayes (VAE) (2)](http://jaejunyoo.blogspot.com/2017/04/auto-encoding-variational-bayes-vae-2.html)
- [초짜 대학원생의 입장에서 이해하는 Auto-Encoding Variational Bayes (VAE) (3)](http://jaejunyoo.blogspot.com/2017/05/auto-encoding-variational-bayes-vae-3.html)

## Video
- [PR-010: Auto-Encoding Variational Bayes, ICLR 2014](https://www.youtube.com/watch?v=KYA-GEhObIs&index=11&list=PLlMkM4tgfjnJhhd4wn5aj8fVTYJwIpWkS)
