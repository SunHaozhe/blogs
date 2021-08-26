---
layout: post
category: Python
title: Pytorch Tensor type casting
tagline: by SunHaozhe
tags: 
  - Python
  - PyTorch
mathjax: true
comments: true
published: true
---

In Python3 & PyTorch 1.0.0,

`torch.LongTensor` and `torch.cuda.LongTensor` means `int64`.<br>

`HalfTensor` : `float16`<br> 
`FloatTensor` : `float32`<br> 
`DoubleTensor` : `float64`<br>
`ByteTensor` : `uint8`(unsigned)<br> 
`CharTensor` : `int8`(signed)<br> 
`ShortTensor` : `int16`(signed)<br> 
`IntTensor` : `int32`(signed)<br> 
`LongTensor` : `int64`(signed)<br> 

One example of conversion from `LongTensor` to `FloatTensor`:

```python
a = torch.LongTensor(22)
a = a.float()
```

Attention:
```python
a = torch.LongTensor(22)
b = 100. * a

```
`b.type()` equals `LongTensor`. The implicit type casting did not work because `type(a)` is `torch.Tensor` instead of Python raw numbers or Numpy array.
<br>The solution is as follows:
```python
a = torch.LongTensor(22)
b = 100 * a.float()
```
Here, `b.type()` equals `FloatTensor`.
