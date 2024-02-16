---
layout: post
category: Python
title: Python modulo for negative numbers
tagline: by SunHaozhe
tags: 
  - Python
  - Computer science
  - Mathematics
  - API
mathjax: true
comments: true
published: true
---

# Description

* When negative numbers are involved, the modulo operator `%` does not have the same behavior in Python and in C/C++. 
* Both variants are correct, but Python's modulo is most commonly used in mathematics (number theory in particular). However C's modulo might be more intuitive for programming... 
* Python has a "true" modulo operation, while C has a remainder operation. It has a direct relation with how the negative integer division is handled, i.e. rounded towards 0 or minus infinite. Python rounds towards minus infinite and C(99) towards 0. 
* In this post, we only consider the case where the numerator (分子, numérateurs) can be negative, the denominator (分母, dénominateurs) is always positive. 


# In Python (version 3.8.9)


Background


```python
int_max = 2 ** 31 - 1
int_min = - 2 ** 31

print(int_max, int_min)
print(int_max / 10, int_min / 10)
```

```
2147483647 -2147483648
214748364.7 -214748364.8
```

Python's native `//` and `%` operators (rounds towards minus infinite)


```python
print(int_max // 10, int_min // 10)
print(int_max % 10, int_min % 10)
```

```
214748364 -214748365
7 2
```

Using `math` to get similar behavior as in C (rounds towards 0)


```python
import math

print(int_max // 10, math.ceil(int_min / 10))
print(int_max % 10, int(math.fmod(int_min, 10)))
```

```
214748364 -214748364
7 -8
```

Note that:

* `math.ceil` and `math.floor` return integers, `math.fmod` returns floats. 
* `math.ceil` and `math.floor` take the sign into account, see below:

```python
print(math.floor(4.5))   # 4
print(math.ceil(4.5))    # 5
print(math.floor(- 4.5)) # -5
print(math.ceil(- 4.5))  # -4
```


# References 

* [https://stackoverflow.com/questions/1907565/c-and-python-different-behaviour-of-the-modulo-operation](https://stackoverflow.com/questions/1907565/c-and-python-different-behaviour-of-the-modulo-operation)
* [https://stackoverflow.com/questions/3883004/how-does-the-modulo-operator-work-on-negative-numbers-in-python](https://stackoverflow.com/questions/3883004/how-does-the-modulo-operator-work-on-negative-numbers-in-python)









