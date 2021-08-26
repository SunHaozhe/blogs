---
layout: post
category: Python     
title: Python debug tool - pdb  
tagline: by SunHaozhe
tags: 
  - Python
  - API  
  - Utilities 
mathjax: true
comments: true
published: true
---

https://www.cnblogs.com/klb561/p/12057436.html

```python
python3 -m pdb xxx.py
```

* `c` 相当于continue，从当前位置继续执行代码直到结束
* `q` quit 退出调试 
* `r` 相当于return，快速执行到函数最后一行，需要在函数里用
* `n` next 执行下一条语句。如果本句是函数调用，则执行函数，接着执行当前执行语句的下一条。
* `s` step 执行下一条命令。如果本句是函数调用，则s会执行到函数的第一句。
* `p + the name of a variable` show the content of this variable. For example,
    * `p a` 
    * `p a, b, c`
* `h` help 帮助
* `w` where 打印当前执行堆栈 
* `u` up 执行跳转到当前堆栈的上一层


`pdb` is capable of interpreting any python code, not only those special commands. For example, `locals()` or `globals()` to display all the variables in scope with their values. 































