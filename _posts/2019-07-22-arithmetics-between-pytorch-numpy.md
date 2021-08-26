---
layout: post
category: Python
title: Arithmetics between Pytorch tensor and Numpy array
tagline: by SunHaozhe
tags: 
  - Python
  - Numpy
  - PyTorch
mathjax: true
comments: true
published: true
---

Arithmetics between Pytorch tensor and Numpy array (without explicit casting) are not allowed.

For the following experiments:

PyTorch version: 1.2.0.dev20190611

Numpy version: 1.16.4


```
a = np.array([1, 6, 5])
b = torch.tensor([2, 8, 9])
print(a + b)

Out:
TypeError: add(): argument 'other' (position 1) must be Tensor, not numpy.ndarray
```

```
a = np.array([1, 6, 5])
b = torch.tensor([2, 8, 9])
print(b + a)

Out:
TypeError: add(): argument 'other' (position 1) must be Tensor, not numpy.ndarray
```


```
a = np.array([1, 6, 5])
b = torch.tensor([2, 8, 9])
print(a * b)

Out:
TypeError: mul(): argument 'other' (position 1) must be Tensor, not numpy.ndarray
```

```
a = np.array([1, 6, 5])
b = torch.tensor([2, 8, 9])
print(b * a)

Out:
TypeError: mul(): argument 'other' (position 1) must be Tensor, not numpy.ndarray
```

```
a = [1, 6, 5]
b = torch.tensor([2, 8, 9])
print(a * b)

Out:
TypeError: mul(): argument 'other' (position 1) must be Tensor, not list
```

```
a = 3
b = torch.tensor([2, 8, 9])
print(a * b)

Out:
tensor([ 6, 24, 27])
```

```
a = np.array([1, 6, 5])
b = torch.tensor([2, 8, 9])
print(a * b.numpy())

Out:
[ 2 48 45]
```

```
a = np.array([1, 6, 5])
b = torch.tensor([2, 8, 9])
print(torch.from_numpy(a) * b)

Out:
tensor([ 2, 48, 45])
```

According to [Can’t call numpy() on Variable that requires grad](https://discuss.pytorch.org/t/cant-call-numpy-on-variable-that-requires-grad/20763):

Moving to numpy will break the computation graph and so no gradient will be computed. If you don’t actually need gradients, then you can explicitly `.detach()` the Tensor that requires `grad` to get a tensor with the same content that does not require `grad`. This other Tensor can then be converted to a numpy array.

