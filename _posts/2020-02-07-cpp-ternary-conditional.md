---
layout: post
category: C++
title: C++ ternary conditional
tagline: by SunHaozhe
tags: 
  - C++
  - API
mathjax: true
comments: true
published: true
---



In C++, ternary conditional/operator `?:` can be understood by the following:


```c++
int a = 2, b = 1;
int c = a > b ? a-- : b--;
cout << a << ", " << b << ", " << c << endl; // 1, 1, 2
```

```c++
int a = 2, b = 1;
int c = a <= b ? a-- : b--;
cout << a << ", " << b << ", " << c << endl; // 2, 0, 1
```