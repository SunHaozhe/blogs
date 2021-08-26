---
layout: post
category: Python  
title: Python assignment, mutable vs. immutable
tagline: by SunHaozhe
tags: 
  - Python 
mathjax: true
comments: true
published: true
---



Everything in Python is an object. An object’s mutability is determined by its type. Objects whose value can change are said to be mutable, objects whose value is unchangeable once they are created are called immutable. 

Among Python built-in types:

* Mutable objects include: `list` `dict` `set`
* Immutable objects include: `tuple` `str` `int` `float` `bool`

User-defined classes are generally mutable. There are some exceptions, such as simple sub-classes of an immutable type. 


Arguments are **passed by assignment** in Python. Since assignment just creates references to objects, the parameter passed in is actually a reference to an object, but the reference is passed by value. When we call a function with a parameter, a new reference is created that refers to the object passed in. This is separate from the reference that was used in the function call. 

If we pass a mutable object into a function, the function gets a reference to that same object and we can mutate it, the change in the function (if any) will be reflected in the outer scope. But if we rebind the reference in the function, the outer reference will still point to the original object. 

If we pass an immutable object into a function, the function gets a reference to that same object but we cannot mutate it as this object does not provide such methods. If we rebind the reference in the function, the outer reference will still point to the original object. 




# id() function 

Assignment in Python means that the variable named on the left should now refer to the value (object) on the right. If the right-hand side is also a name (variable), for example `y = x`, this means `y` should now refer to the object `x` refers to. 

Let's consider this example:

```python
a = 1
a = 2
```

In C, we believe that `a` is a memory location that stores the value `1`, then is updated to store the value `2`. However in Python, `a` starts as a reference to an object with the value `1`, then gets reassigned as a reference to an object with the value `2`. Those two objects may continue to coexist even though `a` doesn't refer to the first one anymore; in fact they may be shared by any number of other references within the program.


Some links to help understand this:

* Understanding Python Variables [https://mathieularose.com/python-variables/](https://mathieularose.com/python-variables/)
* [http://pythontutor.com/visualize.html#mode=display](http://pythontutor.com/visualize.html#mode=display)


Whenever an object is instantiated, it is assigned a unique object id. `id()` function returns the identity of an object. This identity has to be unique and constant for this object during the lifetime. Two objects with non-overlapping lifetimes may have the same `id()` value. If we relate this to C, then they are actually the memory address, here in Python it is the unique id. This function is generally used internally in Python. 

```python
str1 = "abc"
print(id(str1)) # 4330114944
  
str2 = "abc"
print(id(str2)) # 4330114944

str3 = "abcd"
print(id(str3)) # 4333274648

print(id(str1) == id(str2)) # True
print(id(str1) == id(str3)) # False
```

```python
a = [1, 2, 3]
print(id(a)) # 4340769096

a[1] = 4
print(a) # [1, 4, 3]
print(id(a)) # 4340769096

a = [1, 4, 3]
print(id(a)) # 4340468680

print(id([1, 4, 3])) # 4340769096
```

# One trap about tuple's immutability

`tuple`s are immutable, but their values may change. This may happen when a `tuple` holds a reference to any mutable object, such as a `list`. What is immutable is the physical content of a `tuple`, consisting of the object references only. The content/value of a `list` referenced by a name/variable in a `tuple` can be changed, but the id of the referenced `list` object remains the same. A `tuple` has no way of preventing changes to the values of its items, which are independent objects and may be reached through references outside of the `tuple`. 

For example:

```python
a = ([1, 2, 3], 4) 
```



# String 

Strings are immutable in Python:

```python
a = "abcd"
a[0] = "h"
```

```
TypeError: 'str' object does not support item assignment
```

Concatenating string in loops wastes lots of memory. As strings are immutable, concatenating two strings together actually creates a third string which is the combination of the previous two. If we are iterating a lot and building a large string, we will waste a lot of memory creating and throwing away objects. Use list comprehension join technique instead, for example:

```python
"".join(["first", "second", "other"])
```

# Tuple

```python
a = (1, 2, 3)
a[1] = 4
```

```
TypeError: 'tuple' object does not support item assignment
```

# List

```python
a = [1, 2, 3]
a[1] = 4
print(a)
```

```
[1, 4, 3]
```












