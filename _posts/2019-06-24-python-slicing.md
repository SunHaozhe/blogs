---
layout: post
category: Python
title: Python slicing
tagline: by SunHaozhe
tags: 
  - Python
  - API
mathjax: true
comments: true
published: true
---


* `a[start:stop]` items start through stop-1
* `a[start:]` items start through the rest of the array
* `a[:stop]` items from the beginning through stop-1
* `a[:]` a copy of the whole array
* `a[-1]` last item in the array
* `a[-2:]` last two items in the array
* `a[:-2]` everything except the last two items
* `a[::-1]` all items in the array, reversed
* `a[1::-1]` the first two items, reversed
* `a[-3::-1]` everything except the last two items, reversed 
* `a[:-3:-1]` the last two items, reversed 
* `a[::2]` extracts elements of list at even positions
* `a[1::2]` extracts elements of list at odd positions
* `a[::3]` similar to above
* `a[1::3]` similar to above

If we adopt the notation `[start:stop:step]`, `start` is always inclusive, `stop` is always exclusive. 

```python
a = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10] 

print(a[5:2])      # []
print(a[5:2:1])    # []
print(a[5:2:-1])   # [6, 5, 4] 
print(a[-5:-8:-1]) # [6, 5, 4] 
print(a[-5:-8:1])  # []
print(a[-5:-8])    # [] 
print(a[-2:-9:-2]) # [9, 7, 5, 3]    
print(a[-2:-9:-3]) # [9, 6, 3]    
```

One can substitute None for any of the empty spaces. For example [None:None] makes a whole copy. This is useful 
when you need to specify the end of the range using a variable and need to include the last item.

Slicing builtin types returns a copy but that's not universal. Notably, [slicing NumPy arrays]
(https://docs.scipy.org/doc/numpy/reference/arrays.indexing.html) returns a view that shares memory with the original.

