---
layout: post
category: C++
title: C++ pointer, *, reference, &
tagline: by SunHaozhe
tags: 
  - C++
mathjax: true
comments: true
published: true
---

指针`*`，引用`&`，解引用`*`，取地址`&`

************************************************************************************************************



`&`可以作为`取地址`用。`&a`返回取`a`的地址，`a`是一个对象。`&`映射对象到地址。

In this case, `&` can be called `Address of` operator.

************************************************************************************************************

`*`可以作为`解引用`操作符。`*p`返回`p`所指向的对象，`p`是一个地址。`*`映射地址到对象。

In this case, `*` can be called `Indirection` operator. `Indirection` operator returns the value 
of the variable located at the address specified by its operand.

************************************************************************************************************


`Indirection` operator `*` is the complement of `Address of` operator `&`.


References:
* [C++ 中的&：“&引用” 和“&取地址符”的区别和作用](https://blog.csdn.net/qq_33266987/article/details/52047473)
* [C4learn C++ Pointer Operator](http://www.c4learn.com/cplusplus/cpp-pointer-operator/)
* [初级篇——指针(*)、取地址(&)、解引用(*)与引用(&)的区别](https://blog.csdn.net/synapse7/article/details/10260339)





