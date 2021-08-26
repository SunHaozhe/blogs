---
layout: post
category: Misc     
title: Anaconda memo 
tagline: by SunHaozhe
tags: 
  - memo
mathjax: true
comments: true
published: true
---

# Managing Conda and Anaconda 

Verify conda is installed, check version

Get the version

```zsh
conda --version
```

```zsh
conda info
```

Update conda package and environment manager

```zsh
conda update conda
```

Update the anaconda meta package

```zsh
conda update anaconda
```

# Managing Environments


Get a list of all my Anaconda environments 

```zsh
conda info --envs

conda env list

# The above two commands are equivalent
```

Create an Anaconda environment 

```zsh
conda create -n env_name
```

Create an Anaconda environment with a specific version of Python

```zsh
conda create -n env_name python=3.6
```

Create an Anaconda environment with a specific package

```zsh
conda create -n env_name scipy

# with a specific version of a package 
conda create -n env_name scipy=0.15.0
```

Create an Anaconda environment from an `environment.yml` file

```zsh
conda env create -f environment.yml

# The first line of the yml file sets 
# the new environment's name.
```

In order to create an Anaconda environment file manually, see [https://conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#create-env-file-manually](https://conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#create-env-file-manually)


Activate an Anaconda environment to use it 

```zsh
conda activate env_name
```

Deactivate the Anaconda environment

```zsh
conda deactivate 
```


Delete an Anaconda environment

```zsh
conda env remove --name env_name
```


# Troubleshooter

* "Collecting package metadata" cannot proceed and never end #9221 [https://github.com/conda/conda/issues/9221](https://github.com/conda/conda/issues/9221)
* failed to create anaconda environment ResolvePackageNotFound [https://stackoverflow.com/questions/48439159/failed-to-create-anaconda-environment-resolvepackagenotfound](https://stackoverflow.com/questions/48439159/failed-to-create-anaconda-environment-resolvepackagenotfound)



# References 

* https://kapeli.com/cheat_sheets/Conda.docset/Contents/Resources/Documents/index 
* https://conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#activating-an-environment 












































































