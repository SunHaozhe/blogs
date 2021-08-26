---
layout: post
category: Python    
title: Python virtual environment  
tagline: by SunHaozhe
tags: 
  - Python 
  - API 
mathjax: true
comments: true
published: true
---


To create a virtual environment: 

```bash
# navigate to the dedicated directory 
# this command creates venv_X in the current directory 
virtualenv venv_X

# create virtual env with a specific version of Python
## here /usr/bin/python3 is Python 3.8.2
virtualenv --python=/usr/bin/python3 venv_msr
```

To install the kernel for Jupyter notebook:

```bash
cd venv_X/
source bin/activate 
pip3 install --upgrade pip 
pip3 install ipykernel

python3 -m ipykernel install --user --name=venv_X

# install packages
pip3 install --upgrade matplotlib 
pip3 install --upgrade ipywidgets 
pip3 install --upgrade pandas 
pip3 install --upgrade sklearn 
pip3 install --upgrade opencv-python 

# 按顺序做完以上步骤之后激活虚拟环境或者不激活虚拟环境，都可以直接通过在 jupyter notebook 命令进入notebook并选与虚拟环境对应的kernel
```



To activate the virtual environment:

```bash
source venv_X/bin/activate
```

To deactivate the virtual environment:

```bash
deactivate
```

To delete the virtual environment:

```bash
rm -r venv_X
```

To uninstall the kernel for Jupyter notebook:

```bash
jupyter kernelspec uninstall venv_X
```

To see which kernels are available for Jupyter notebook:

```bash
jupyter kernelspec list
```






