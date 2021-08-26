---
layout: post
category: Python
title: Python iterable iterator generator yield 
tagline: by SunHaozhe
tags: 
  - Python
  - API
mathjax: true
comments: true
published: true
---


Iterable: 

* It is an object that contains a countable number of values. 
* It is an object that can be iterated upon, meaning that you can traverse through all the values.
* It is an object that has an `__iter__()` method or defines a `__getitem__()` method. 
* It generates an Iterator when passed to `iter()` function. 
* It stores all the values in memory. 
* It is any object you can get an iterator from. 


Iterator:

* It is an object that lets you iterate on an iterable.
* it must implement `__iter__()`method and `__next__()`method. 
* `__next__()` method returns the next item of the object.
* It is an object with state that remembers where it is during iteration. 
* It is self-iterable (meaning that it has an `__iter__()` method that returns `self`. 


Generator:

* It is an iterator, a kind of iterable you can only iterate over once. 
* It does not store all the values in memory, it generates the values on the fly. 
* `yield` is a keyword that is used like `return`. By using `yield`, the function will return a generator. 
* When you call a function which uses `yield`, the code you have written in that function body does not run. The function only returns the generator object. 





Every iterator is also an iterable, but not vice versa. For example, a list is iterable but it is not an iterator. Compared to generator, iterator is a more general concept: any object whose class has a `__next__()` method and a `__iter__()` method that does `return self` is an iterator. Every generator is an iterator, but not vice versa. A generator is an object that meets the previous definition of an iterator and it can be built by calling a function that has one or more `yield` expressions. A function with `yield` in it is still a function, that, when called, returns an instance of a generator object. 


$$\text{generator} \subsetneq \text{iterator} \subsetneq \text{iterable}$$


When a `for` loop is executed, the `for` statement calls `iter()` on the iterable object. If successful, the `iter()` call will return an iterator object that defines the `__next__()` method, which accesses elements of the object one at a time. The `__next__()` method will raise a `StopIteration` exception, if there are no further elements available. The `for` loop will terminate as soon as it catches a `StopIteration` exception. 

```python
my_iterable = ["A", "B", "C"] 
iterator_obj = iter(my_iterable) 

print(next(iterator_obj)) # A
print(next(iterator_obj)) # B
print(next(iterator_obj)) # C
```


```python
# this is called generator expression
my_generator = (x * x for x in range(4))

for i in my_generator:
    print(i, end =" ") # 0 1 4 9 

print("\n")

for i in my_generator:
    print(i)           #                   
```

It is just the same except you used `()` instead of `[]`. But you cannot perform `for i in my_generator` a second time since generators can only be used once: they calculate the first element, then forget about it and calculate the second element, then forget about it, and so on. 

```python
def createGenerator():
    my_iterable = range(4)
    for i in my_iterable:
        yield i * i

my_generator = createGenerator() 
print(my_generator) # <generator object createGenerator at 0x11cd234c0>

for i in my_generator:
    print(i, end =" ") # 0 1 4 9 

print("\n")

for i in my_generator:
    print(i)           #     
```

`yield` can be used when you know your function will return a huge set of values that you will only need to read once. 

The first time the `for` calls the generator object created from your function, it will run the code in your function from the beginning until it hits `yield`, then it will return the first value of the loop. Then, each subsequent call will run another iteration of the loop you have written in the function and return the next value. This will continue until the generator is considered empty, which happens when the function runs without hitting `yield`. That can be because the loop has come to an end, or because you no longer satisfy an `"if/else"`.


# References


[1] Glossary — Python 3.8.2 documentation. (n.d.). 3.8.2 Documentation. https://docs.python.org/3/glossary.html

[2] Python Iterators. (n.d.). W3Schools Online Web Tutorials. https://www.w3schools.com/python/python_iterators.asp

[3] Python - Difference between iterable and iterator. (2019, August 23). GeeksforGeeks. https://www.geeksforgeeks.org/python-difference-iterable-iterator/

[4] What exactly are iterator, iterable, and iteration? (n.d.). Stack Overflow. https://stackoverflow.com/questions/9884132/what-exactly-are-iterator-iterable-and-iteration

[5] Python Iterators (__iter__ and __next__): How to use it and why? (n.d.). Programiz: Learn to Code for Free. https://www.programiz.com/python-programming/iterator

[6] What does the "yield" keyword do? (n.d.). Stack Overflow. https://stackoverflow.com/questions/231767/what-does-the-yield-keyword-do

[7] Difference between Python's generators and Iterators. (n.d.). Stack Overflow. https://stackoverflow.com/questions/2776829/difference-between-pythons-generators-and-iterators



































