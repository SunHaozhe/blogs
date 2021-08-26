---
layout: post
category: Mathematics
title: Matrix calculus, gradients 
tagline: by SunHaozhe
tags: 
  - Mathematics
mathjax: true
comments: true
published: true
---

# Rules 

$$\nabla (u^\intercal A) = \nabla (u^\intercal) A$$

$$\nabla (u^\intercal v) = \nabla (u^\intercal) v + \nabla (v^\intercal) u$$



# Derivative of cross entropy loss with softmax function


For a single data point, cross entropy loss:

$$L = - \sum_i y_i \log p_i$$

Softmax function:

$$p_i = \frac{ \exp (o_i) }{\sum_j \exp(o_j) }$$

$$
\begin{equation}\begin{split}
\frac{\partial p_i}{\partial o_k}
= \frac{ \partial \frac{ \exp (o_i) }{\sum_j \exp(o_j)} }{\partial o_k}
= \frac{ \frac{\partial \exp(o_i)}{\partial o_k} \sum_j \exp(o_j) - \frac{\partial \sum_j \exp(o_j)}{\partial o_k} \exp (o_i) }{(\sum_j \exp(o_j))^2} 
\end{split}\end{equation}
$$

If $i = k$,

$$
\begin{equation}\begin{split}
\frac{\partial p_i}{\partial o_k}
&= \frac{ \exp (o_k) \sum_j \exp(o_j) - \exp (o_k) \exp (o_k) }{(\sum_j \exp(o_j))^2} \\ 
&= \frac{\exp (o_k)}{\sum_j \exp(o_j)} \frac{ \sum_j \exp(o_j) - \exp (o_k) }{\sum_j \exp(o_j)} \\
&= p_k (1 - p_k) 
\end{split}\end{equation}
$$

If $i \neq k$,

$$
\begin{equation}\begin{split}
\frac{\partial p_i}{\partial o_k}
&= \frac{ \frac{\partial \exp(o_i)}{\partial o_k} \sum_j \exp(o_j) - \frac{\partial \sum_j \exp(o_j)}{\partial o_k} \exp (o_i) }{(\sum_j \exp(o_j))^2} \\
&= \frac{ - \exp (o_k) \exp (o_i) }{(\sum_j \exp(o_j))(\sum_j \exp(o_j))} \\
&= - p_k p_i 
\end{split}\end{equation}
$$

$$
\begin{equation}\begin{split}
\frac{\partial p_i}{\partial o_k} = 
\begin{cases}
p_k (1 - p_k) & \text{if }\; i=k \\
- p_k p_i & \text{if }\; i\neq k
\end{cases}
\end{split}\end{equation}
$$

In consequence, 

$$
\begin{equation}\begin{split}
\frac{\partial L}{\partial o_k} 
&= - \sum_i y_i \frac{\partial \log p_i}{\partial o_k} \\ 
&= - \sum_i y_i \frac{\partial \log p_i}{\partial p_i} \frac{\partial p_i}{\partial o_k} \\ 
&= - \sum_i \frac{y_i}{p_i} \frac{\partial p_i}{\partial o_k} \\
&= - y_k (1 - p_k) + \sum_{i\neq k} y_i p_k \\ 
&= - y_k + \sum_i y_i p_k \\
&= p_k - y_k 
\end{split}\end{equation}
$$

$\sum_i y_i = 1$ because $y$ is a one-hot vector. $o$ is a vector which represents the raw output of neural network (before softmax activation). 


































