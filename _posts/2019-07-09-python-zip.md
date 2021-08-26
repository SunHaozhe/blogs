---
layout: post
category: Python
title: Python zip
tagline: by SunHaozhe
tags: 
  - Python
  - API
mathjax: true
comments: true
published: true
---

Experiments using Python 3.7.3

```
zip([1, 2, 3], [4, 5, 6])

Out: <zip at 0x21ad7134208>
```

```
list(zip([1, 2, 3], [4, 5, 6]))

Out: [(1, 4), (2, 5), (3, 6)]
```

```
zip(*zip([1, 2, 3], [4, 5, 6]))
Out: <zip at 0x21ad7265d88>
```

```
zip(*list(zip([1, 2, 3], [4, 5, 6])))
Out: <zip at 0x21ad7265dc8>
```

```
list(zip(*zip([1, 2, 3], [4, 5, 6])))
Out: [(1, 2, 3), (4, 5, 6)]
```

```
list(zip(*list(zip([1, 2, 3], [4, 5, 6]))))
Out: [(1, 2, 3), (4, 5, 6)]
```

```
a, b = zip(*zip([1, 2, 3], [4, 5, 6]))
a
Out: (1, 2, 3)
b
Out: (4, 5, 6)
```




