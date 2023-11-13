---
layout: post
category: Python     
title: Create a list of N empty lists in Python  
tagline: by SunHaozhe
tags: 
  - Python
  - API  
  - Utilities 
mathjax: true
comments: true
published: true
---

In order to create a list of N empty lists in Python: 

```python
[[],[],[]...]
```

The following would not work, because each created empty list is indeed the same object, modifying one of them will impact all others. This can be verified by checking that they all share the same python object ids (`id()`) 


```python
[[]] * N
```

One correct way of doing is the following:

```python
[[] for _ in range(N)]
```




