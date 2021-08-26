---
layout: post
category: Python
title: Matplotlib memo 
tagline: by SunHaozhe
tags: 
  - Python
  - memo
mathjax: true
comments: true
published: true
---

`matplotlib` is a plotting library for the Python programming language and its numerical mathematics extension `numpy`. `matplotlib.pyplot` provides a MATLAB-like plotting framework. `pylab` combines `pyplot` with `numpy` into a single namespace. `pylab` is now discouraged. [1, 2, 3]

```python
import matplotlib.pyplot as plt
```

```python
params = {'legend.fontsize': 'xx-large',
         'axes.labelsize': 'xx-large',
         'axes.titlesize':'xx-large',
         'xtick.labelsize':'xx-large',
         'ytick.labelsize':'xx-large'}
plt.rcParams.update(params)
```

```python
params = {'image.interpolation': 'nearest',
         'image.cmap': 'gray', 
         'figure.dpi': 72 * 1} 
plt.rcParams.update(params)
```

How to use `plt.subplots()`? 

```python
fig, axes = plt.subplots(nrows=nb_rows, ncols=nb_cols, figsize=(10, 6))
for i, c in enumerate(iterable_):
    # if nb_cols = 2, the following can be used:  
    #axes[2*i, 0], axes[2*i, 1], axes[2*i+1, 0], axes[2*i+1, 1]
    pass 
#plt.tight_layout()
plt.show()
```

```python
fig, axes = plt.subplots(nrows=nb_rows, ncols=nb_cols, figsize=(10, 6))
axes = axes.ravel() 
for i, c in enumerate(iterable_):
    #axes[i].set_title("blablabla")
    pass 
#plt.tight_layout()
#plt.suptitle("blablabla")
plt.show() 
```

In `matplotlib` and `PIL`, figure's size is given as (width, height) in inches. Width comes first. 

However, height comes first in `numpy` (thus `OpenCV`), Tensorflow, PyTorch (the image convention is (N, C, H, W) ) and conventional matrix notation in mathematics. 


Figure size (figsize) determines the size of the figure in inches. This gives the amount of space the axes (and other elements) have inside the figure. The default figure size is (6.4, 4.8) inches in matplotlib 2. 

Dots per inches (dpi) determines how many pixels the figure comprises. The default dpi in matplotlib is 100.

For example, a figure of `figsize = (w, h)` will have

```python
px, py = w * dpi, h * dpi  # pixels
# e.g.
# 6.4 inches * 100 dpi = 640 pixels
```

In matplotlib, image size (in pixels) cannot be too large, it must be less than $2^{16}=65536$ pixels in each direction.


# seaborn

For `seaborn` version `0.10.1`, 

```python
# This import will not change the style for the rest of the session
import seaborn as sns

# This command will change the style for the rest of the session  
sns.set()

# This command will restore the style  
sns.reset_orig()
```

One can use the style context manager which sets a style temporarily:

```python
style_name = "seaborn-darkgrid"

with plt.style.context(style_name):
    pass 
```

Valid style names include: `"seaborn-darkgrid"`, `"seaborn-whitegrid"`, `"seaborn-dark"`, `"seaborn-white"`, `"seaborn-ticks"`, `"seaborn-bright"`, `"seaborn-colorblind"`, `"seaborn-dark-palette"`, `"seaborn-paper"`, `"seaborn-poster"`, `"seaborn-talk"`, `"bmh"`, `"classic"`, `"dark_background"`, `"fivethirtyeight"`, `"ggplot"`, `"grayscale"`, etc.


Set font size for seaborn. The value of `font_scale` should be proportional to the figure size.

```python
# sns.set(...) will also influence matplotlib's behavior

sns.set(font_scale=6)
...
sns.reset_orig()
```

Plot pairplot 

```python
# height plays the role of figsize

sns.pairplot(df, diag_kind="hist", kind="kde", height=15)

pairplot = sns.pairplot(df, diag_kind="hist", kind="scatter", height=15)
```


# Save figure

```python
fig = plt.figure(figsize=(xxx, yyy))
...
fig.savefig(image_path, dpi=fig.dpi)
```

For seaborn:

```python
pairplot = sns.pairplot(...)
pairplot.fig.savefig(image_path, dpi=fig.dpi)
```

# References


[1] Matplotlib. (2005, October 14). Wikipedia, the free encyclopedia. Retrieved April 3, 2020, from https://en.wikipedia.org/wiki/Matplotlib

[2] Pyplot — Matplotlib 2.0.2 documentation. (n.d.). Matplotlib: Python plotting — Matplotlib 3.2.1 documentation. https://matplotlib.org/api/pyplot_api.html

[3] Matplotlib, Pyplot, Pylab etc: What's the difference between these and when to use each? (2019, August 31). queirozf.com. https://queirozf.com/entries/matplotlib-pylab-pyplot-etc-what-s-the-different-between-thes`























