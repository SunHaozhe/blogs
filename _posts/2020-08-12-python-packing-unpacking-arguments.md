---
layout: post
category: Python     
title: Packing and unpacking arguments in Python 
tagline: by SunHaozhe
tags: 
  - Python
  - API  
mathjax: true
comments: true
published: true
---


# Unpacking

We can use `*` to unpack a list. 

```python
def func(a, b, c, d): 
    pass 
    
a = [1, 2, 3, 4] 

# unpacks list into four arguments 
func(*a) 
```

```python
list(range(3, 6)) # [3, 4, 5] 

args = [3, 6] 
list(range(*args)) # [3, 4, 5] 
```

`**` is used for dictionaries. 

```python
def func(a, b, c): 
    pass 

# unpacking of dictionary 
d = {'a':2, 'b':4, 'c':10} 
func(**d) 
```

# Packing

When we don’t know how many arguments need to be passed to a python function, we can pack all arguments in a tuple.

```python
# This function uses packing to take 
# an unknown number of arguments 
def func(*args): 
    sum = 0
    for i in range(0, len(args)): 
        sum = sum + args[i] 
    return sum 

print(func(1, 2, 3, 4, 5)) 
print(func(10, 20)) 
```

```python
def func(**kwargs): 
    # kwargs is a dict 
    print(type(kwargs)) 
    # Printing dictionary items 
    for key in kwargs: 
        print("%s = %s" % (key, kwargs[key])) 

func(name="geeks", ID="101", language="Python") 
```






























