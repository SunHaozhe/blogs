---
layout: post
category: Mathematics
title: Generative models and discriminative models
tagline: by SunHaozhe
tags: 
  - Mathematics
  - Machine learning
mathjax: true
comments: true
published: true
---

Let's say we have input data $X$ and we want to classify the data into labels $Y$. 

* A generative model (生成模型) learns the joint probability distribution $\mathbb{P}(X, Y)$ 
* A discriminative model (判别模型) learns the conditional probability distribution $\mathbb{P}(Y \| X)$

Discriminative models learn the (hard or soft) boundary between classes, generative models model the distribution of individual classes. 


Examples of generative models:

* HMM (hidden markov model)
* Naive Bayes 
* Gaussian mixture model and other types of mixture model 
* GAN (generative adversarial network) 
* VAE (variational autoencoder)
* PCFG (probabilistic context-free grammar)
* Latent Dirichlet allocation
* Boltzmann machine


Examples of discriminative models:

* CRF (conditional random field)
* Logistic regression 
* SVM
* Decision tree 
* Random forest 
* KNN (k-nearest neighbors) 
* Neural networks 
* Classical (sparse, denoising, etc.) autoencoders are generally classified as discriminative models, although denoising autoencoders can be used as generative models [1]. 


For sequence labeling tasks (such as Part-of-Speech tagging and Name Entity Recognition), discriminative models are said to be preferred to generative models [2]. 

There is a widely-held belief that discriminative models are almost always to be
preferred in classification tasks [3]. 

One of the advantages of generative models is that they can be used to generate new data similar to existing data. 



# References


[1] Bengio, Y., Yao, L., & Alain, P. (2013). Generalized Denoising Auto-Encoders as Generative ModelsarXiv e-prints, arXiv:1305.6663.

[2] Why discriminative models are preferred to generative models for sequence labeling tasks? (n.d.). Cross Validated. https://stats.stackexchange.com/questions/73015/why-discriminative-models-are-preferred-to-generative-models-for-sequence-labeli

[3] Andrew Y. Ng and Michael I. Jordan, On discriminative vs. generative classifiers: A comparison of logistic regression and naive bayes, Advances in Neural Information Processing Systems 14 (T. G. Dietterich, S. Becker, and Z. Ghahramani, eds.), MIT Press, 2002, pp. 841–848.