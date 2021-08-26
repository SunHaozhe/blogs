---
layout: post
category: Python
title: How to deal with data type of lists/numpy array in Python
tagline: by SunHaozhe
tags: 
  - Python
  - Numpy 
mathjax: true
comments: true
published: true
---

Python中，一个`list`可以包含不同类型的`object`。例如:  
```
a = [0, 10, -1.]
type(a[0]) : <class 'int'>
type(a[1]) : <class 'int'>
type(a[2]) : <class 'float'>
```

但是需要注意的是，当我们试图将`list`通过`np.array()`, `np.zeros_like()`, etc.转换为`numpy array`的时候，如果`list`中只包含`int`，则转换得到的`numpy array`的`dtype`将也是`int64`。如果`list`中包含至少一个`float`，则得到的`numpy array`的`dtype`将是`float64`。

For example:

```
a = [0, 10, -1]
type(a[0]) : <class 'int'>
type(a[1]) : <class 'int'>
type(a[2]) : <class 'int'>

np.array(a).dtype : int64
```

```
a = [0, 10, -1.]
type(a[0]) : <class 'int'>
type(a[1]) : <class 'int'>
type(a[2]) : <class 'float'>

np.array(a).dtype : float64
```

## 实例

我是通过一下这段代码发现的这个问题:

```
def sigmoid(x):
    positive = x >= 0
    negative = x < 0
    xx = 1 / (1 + np.exp(- x[positive]))
    x[positive] = 1 / (1 + np.exp(- x[positive]))
    z = np.exp(x[negative])
    x[negative] = z / (z + 1)
    return x

def s1(x):
    return 1 / (1 + np.exp(- x))
def s2(x):
    return np.exp(x) / (np.exp(x) + 1)
```

```
a = [0, 10, -1]
b = sigmoid(np.array(a))
c = np.zeros_like(a)
d = np.zeros_like(a)

print(b.dtype)
print(c.dtype)
print(d.dtype)

for i, el in enumerate(a):
    c[i] = s1(el)
    d[i] = s2(el)
    
print("b =", b)
print("c =", c)
print("d =", d)
print("b - c", b - c)
print("b - d", b - d)


int64
int64
int64
b = [0 0 0]
c = [0 0 0]
d = [0 0 0]
b - c [0 0 0]
b - d [0 0 0]
```

```
a = [0, 10, -1.]
b = sigmoid(np.array(a))
c = np.zeros_like(a)
d = np.zeros_like(a)

print(b.dtype)
print(c.dtype)
print(d.dtype)

for i, el in enumerate(a):
    c[i] = s1(el)
    d[i] = s2(el)
    
print("b =", b)
print("c =", c)
print("d =", d)
print("b - c", b - c)
print("b - d", b - d)


float64
float64
float64
b = [0.5        0.9999546  0.26894142]
c = [0.5        0.9999546  0.26894142]
d = [0.5        0.9999546  0.26894142]
b - c [0. 0. 0.]
b - d [0. 0. 0.]
```
