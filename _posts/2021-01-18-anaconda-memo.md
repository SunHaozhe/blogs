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
conda env list

conda info --envs

# The above two commands are equivalent
```

Create an Anaconda environment 

```zsh
conda create -n env_name
```

Create an Anaconda environment with a specific version of Python

```zsh
conda create -n env_name python=3.6

conda create --name env_name python=3.9
```

Create an Anaconda environment with a specific package

```zsh
conda create -n env_name scipy

# with a specific version of a package 
conda create -n env_name scipy=0.15.0
```

Create an Anaconda environment from an existing environment (fork / branch)

```zsh
conda create --clone <existing enviroment> -n <new environment>
```


Create an Anaconda environment from an `environment.yml` file

```zsh
conda env create -f environment.yml

# The first line of the yml file sets 
# the new environment's name.
```

Copy the Anaconda environment into an `environment.yml` file

```zsh
conda env export > environment.yml
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


# Installing python packages


```bash
conda install [PackageName]
```

If you encounter the error `PackagesNotFoundError: The following packages are not available from current channels`, do the following: 

```bash
conda config --append channels conda-forge
```

`conda-forge` is a GitHub organization containing repositories of conda recipes. Thanks to some awesome continuous integration providers, each repository automatically builds its own recipe in a clean and repeatable way on Windows, Linux and OSX.

If a package cannot be easily installed with `pip` because of issues with system libraries (e.g. version of `gcc`), a possible solution is to install the package with `conda`, which is less dependent on system libraries. 


# Troubleshooter

* "Collecting package metadata" cannot proceed and never end #9221 [https://github.com/conda/conda/issues/9221](https://github.com/conda/conda/issues/9221)
* failed to create anaconda environment ResolvePackageNotFound [https://stackoverflow.com/questions/48439159/failed-to-create-anaconda-environment-resolvepackagenotfound](https://stackoverflow.com/questions/48439159/failed-to-create-anaconda-environment-resolvepackagenotfound)



# References 

* https://kapeli.com/cheat_sheets/Conda.docset/Contents/Resources/Documents/index 
* https://conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#activating-an-environment 











































































