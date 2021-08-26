---
layout: post
category: Misc     
title: Number of trainable parameters of neural networks in torchvision.models
tagline: by SunHaozhe
tags: 
  - PyTorch
  - Machine learning
mathjax: true
comments: true
published: true
---

```bash
                 model human_readable  nb_trainable_parameters
15       squeezenet1_1          1.24M                  1235496
14       squeezenet1_0          1.25M                  1248424
22  shufflenet_v2_x0_5          1.37M                  1366792
33          mnasnet0_5          2.22M                  2218512
23  shufflenet_v2_x1_0          2.28M                  2278604
28  mobilenet_v3_small          2.54M                  2542856
34         mnasnet0_75          3.17M                  3170208
24  shufflenet_v2_x1_5           3.5M                  3503624
26        mobilenet_v2           3.5M                  3504872
35          mnasnet1_0          4.38M                  4383312
27  mobilenet_v3_large          5.48M                  5483032
36          mnasnet1_3          6.28M                  6282256
25  shufflenet_v2_x2_0          7.39M                  7393996
16         densenet121          7.98M                  7978856
9             resnet18         11.69M                 11689512
21           googlenet            13M                 13004888
17         densenet169         14.15M                 14149480
19         densenet201         20.01M                 20013928
10            resnet34          21.8M                 21797672
29     resnext50_32x4d         25.03M                 25028904
11            resnet50         25.56M                 25557032
20        inception_v3         27.16M                 27161264
18         densenet161         28.68M                 28681000
12           resnet101         44.55M                 44549160
13           resnet152         60.19M                 60192808
0              alexnet          61.1M                 61100840
31     wide_resnet50_2         68.88M                 68883240
30    resnext101_32x8d         88.79M                 88791336
32    wide_resnet101_2        126.89M                126886696
1                vgg11        132.86M                132863336
2             vgg11_bn        132.87M                132868840
3                vgg13        133.05M                133047848
4             vgg13_bn        133.05M                133053736
5                vgg16        138.36M                138357544
6             vgg16_bn        138.37M                138365992
7                vgg19        143.67M                143667240
8             vgg19_bn        143.68M                143678248
```

Source code:

```python
"""
MIT License

Copyright (c) 2021 Haozhe Sun

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.


https://pytorch.org/vision/stable/models.html
"""

import pandas as pd
import torch 
import torch.nn as nn
import torch.nn.functional as F  
import torchvision

# human readable large numbers
from millify import millify

def get_nb_trainable_parameters(model):
    return sum([x.numel() for x in model.parameters() if x.requires_grad])


model_names = ["alexnet",
               "vgg11",
               "vgg11_bn",
               "vgg13",
               "vgg13_bn",
               "vgg16",
               "vgg16_bn",
               "vgg19",
               "vgg19_bn",
               "resnet18",
               "resnet34",
               "resnet50",
               "resnet101",
               "resnet152",
               "squeezenet1_0",
               "squeezenet1_1",
               "densenet121",
               "densenet169",
               "densenet161",
               "densenet201",
               "inception_v3",
               "googlenet",
               "shufflenet_v2_x0_5",
               "shufflenet_v2_x1_0",
               "shufflenet_v2_x1_5",
               "shufflenet_v2_x2_0",
               "mobilenet_v2",
               "mobilenet_v3_large",
               "mobilenet_v3_small",
               "resnext50_32x4d",
               "resnext101_32x8d",
               "wide_resnet50_2",
               "wide_resnet101_2",
               "mnasnet0_5",
               "mnasnet0_75",
               "mnasnet1_0",
               "mnasnet1_3"]

model_strings = []
for model_name in model_names:
    model_strings.append((model_name, "torchvision.models." + model_name + "(pretrained=False)"))

df = []
for model_name, model_string in model_strings:
    model = eval(model_string)
    nb_params = get_nb_trainable_parameters(model)
    df.append((model_name, nb_params))
df = pd.DataFrame(df, columns=["model", "nb_trainable_parameters"])
df["human_readable"] = df["nb_trainable_parameters"].apply(lambda x: millify(x, precision=2))
df.sort_values("nb_trainable_parameters", ascending=True, inplace=True, axis=0)
df = df[["model", "human_readable", "nb_trainable_parameters"]]

pd.set_option('display.max_rows', None)
print(df)
```

