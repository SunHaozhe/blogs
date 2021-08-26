---
layout: post
category: Python
title: Do not forget to reset index of pandas DataFrame
tagline: by SunHaozhe
tags: 
  - Python
  - Pandas
mathjax: true
comments: true
published: true
---

Don't forget to reset indices of pandas DataFrame after slicing operations. Otherwise, there might be key errors later.

Let's say 

`Xtr`: pandas DataFrame 6000 rows × 2 columns

```
Xtrain = Xtr.loc[:5000 - 1, :]
Xval = Xtr.loc[5000:, :]
Ytrain = Ytr.loc[:5000 - 1, :]
Yval = Ytr.loc[5000:, :]
```

```
Xtrain.loc[0, Xtrain.columns[1]]

Out: 'GGAGAATCATTTGAACCCGGGAGGTGGAGGTTGCCGTGAGCTGAGATTGCGCCATTGCACTCCAGCCTGGGCAACAAGAGCAAAACTCTGTCTCACAAAAC'
```

```
Xval.loc[0, Xval.columns[1]]

Out: KeyError: 0
```

The correct way is the following:
```
Xtrain = Xtr.loc[:5000 - 1, :]
Xval = Xtr.loc[5000:, :]
Ytrain = Ytr.loc[:5000 - 1, :]
Yval = Ytr.loc[5000:, :]

Xtrain.reset_index(drop=True, inplace=True)
Xval.reset_index(drop=True, inplace=True)
Ytrain.reset_index(drop=True, inplace=True)
Yval.reset_index(drop=True, inplace=True)

Xval.loc[0, Xval.columns[1]]

Out: 'ACCACCGTCATCTATGTGGCCGTCGTGGGCCAACACATTGGTGTGAAGCACATGGCCGACCACGTGTTGTATCTGAATCTAGGTCGACCCACTGTGCGATT'
```

The reason is that `.loc` is label-based indexer, `0` is not interpreted as the location `0` but the label `0`. 
However, after slicing, the index of each row remains the same. 



When using `.loc` with slices, both the start point and end point are included.

When using `.iloc` with slices, the start point is included, while the end point is excluded (like standard Python slicing convention). 
