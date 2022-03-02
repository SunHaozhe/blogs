---
layout: post
category: Python     
title: Python type hints  
tagline: by SunHaozhe
tags: 
  - Python
mathjax: true
comments: true
published: true
---


# Declare argument type and return type of a function


* Add a colon `:` and a data type after each function parameter
* Add an arrow (`->`) and a data type after the function (and before the colon `:`) to specify the return data type
* If you’re working with a function that shouldn’t return anything, you can specify None as the return type: `-> None:`


```python
def greeting(name: str) -> str:
    return "Hello " + name
```

This states that the expected type of the `name` argument is `str`, the expected return type is `str`. Expressions whose type is a subtype of a specific argument type are also accepted for that argument. 

Type hints may be built-in classes (including those defined in standard library or third-party extension modules), abstract base classes, types available in the types module, and user-defined classes (including those defined in the standard library or third-party modules).

You can still set a default value for the parameter, as shown below:

```python
def greeting(name: str = "Mike") -> str:
    return "Hello " + name
```


Adding type hints has no runtime effect by default. A static type checker like `mypy` can solve this “issue”.



# Variable annotations

Just like with functions, you can add type hints to variables.


# The typing Module

Python’s `typing` module can make data type annotations even more verbose. For example, you can specifically declare a list of strings, a tuple containing three integers, and a dictionary with strings as keys and integers as values.

```python
from typing import List, Tuple, Dict

e: List[str] = ['a', 'b', 'c']
f: Tuple[int, int, int] = (1, 2, 3)
g: Dict[str, int] = {'a': 1, 'b': 2}

def square_(arr: List[float]) -> List[float]:
    return [x ** 2 for x in arr]
```

The `Union` operator allows you to specify multiple possible data types for variables and return values:

```python
from typing import Union

# The function can now both accept and 
# return a list of integers or floats, 
# warning-free.

def square_(arr: List[Union[int, float]]) -> List[Union[int, float]]:
    return [x ** 2 for x in arr]
```


# The mypy library

[https://github.com/python/mypy](https://github.com/python/mypy)

12.6 k stars and 2.1 k forks up to March 2, 2022. 


`mypy` is a **static type checker** for Python.

Type checkers help ensure that you're using variables and functions in your code correctly. With `mypy`, add type hints (PEP 484) to your Python programs, and `mypy` will warn you when you use those types incorrectly.

Python is a dynamic language, so usually you'll only see errors in your code when you attempt to run it. `mypy` is a static checker, so it finds bugs in your programs without even running them!

`mypy` is designed with gradual typing in mind. This means you can add type hints to your code base slowly and that you can always fall back to dynamic typing when static typing is not convenient.

By default, mypy will not type check dynamically typed functions. This means that with a few exceptions, mypy will not report any errors with regular unannotated Python.


After installation, you can type-check the statically typed parts of a program like this:

```bash
mypy PROGRAM
```

You can always use the Python interpreter to run your statically typed programs, even if `mypy` reports type errors:

```bash
python PROGRAM
```

`mypy` can be integrated into popular IDEs, including Vim, Emacs, Sublime Text, Atom, PyCharm, VS Code. 


# History and motivation


Python has always been a dynamically typed language, which means you don't have to specify data types for variables and function return values. PEP 484 introduced type hints — a way to make Python feel statically typed.


Since the initial introduction of type hints in PEP 484 (Type Hints) and PEP 483 (The Theory of Type Hints), a number of PEPs have modified and enhanced Python’s framework for type annotations, this includes PEP 526, PEP 544, PEP 585, etc. 

As the code base gets larger, type hints can help to debug and prevent some dumb mistakes. If you’re using an IDE like PyCharm, you’ll get a warning message whenever you’ve used the wrong data type, provided you’re using type hints.


Type hints in Python can be both a blessing and a curse. On the one hand, they help in code organization and debugging, but on the other can make the code stupidly verbose.
To compromise, we can use type hints only for function declaration — both parameter types and return values — but avoid them for anything else, such as variables. In the end, Python should be simple to write.























































