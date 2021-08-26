---
layout: post
category: C++
title: C++ strange behaviors of size_t 
tagline: by SunHaozhe
tags: 
  - C++
mathjax: true
comments: true
published: true
---

size_t是标准C库中定义的，应为unsigned int，在64位系统中为 long unsigned int。

size_t是一种无符号的整型数，它的取值没有负数，在数组中也用不到负数，而它的取值范围是整型数的双倍。

```c++
#include <typeinfo>

vector<int> data;
cout << sizeof(data.size()) << " " << sizeof(int) << endl;
cout << "typeid(data.size()).name() = " << typeid(data.size()).name() << endl;
cout << "data.size() = " << data.size() << endl;
if(data.size() - 1 > 0){
    cout << "data.size() - 1 = " << data.size() - 1 << endl;
}

if(data.size() - 2 > 0){
    cout << "data.size() - 2 = " << data.size() - 2 << endl;
}

cout << "(int) data.size() - 2 = " << (int) data.size() - 2 << endl;

Out:

8 4
typeid(data.size()).name() = m
data.size() = 0
data.size() - 1 = 18446744073709551615
data.size() - 2 = 18446744073709551614
(int) data.size() - 2 = -2
```

Uses of typeid in C++:
```
bool = b
char = c
unsigned char = h
short = s
unsigned short = t
int = i
unsigned int = j
long = l
unsigned long = m
long long = x
unsigned long long = y
float = f
double = d
long double = e
```
