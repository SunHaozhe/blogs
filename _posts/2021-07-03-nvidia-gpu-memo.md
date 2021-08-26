---
layout: post
category: Misc     
title: nvidia GPU memo  
tagline: by SunHaozhe
tags: 
  - memo
mathjax: true
comments: true
published: true
---

# nvidia-smi

NVIDIA System Management Interface (`nvidia-smi`) 

```zsh
nvidia-smi

# If we just want the GPU name
nvidia-smi --query-gpu=name --format=csv,noheader

# The following will loop and call 
# the view at every second. 
nvidia-smi -l 1
```

# Test whether GPU is being used

### directly test with PyTorch

```python
import torch
torch.cuda.is_available()
```

### with nvidia-smi

With `nvidia-smi` can check whether `Memory-Usage` is (always) `0MiB / XXXXXMiB`, whether `Volatile GPU-Util` is (always) `0%`. In the bottom, there is the `Processes` section, one can check whether there is our process, or whether it is `No running processes found`.



### gpustat

[https://github.com/wookayin/gpustat](https://github.com/wookayin/gpustat)

In order to install the python package `gpustat`

```bash
pip install --upgrade pip
pip install gpustat
```

To use `gpustat`

```bash
# basic command
gpustat

# refresh the information periodically 
# just like the command "top"
gpustat --watch
```

Default display (in each row) contains:
* GPUindex (starts from 0) as PCI_BUS_ID
* GPU name
* Temperature
* Utilization (percentage)
* GPU Memory Usage
* Running processes on GPU (and their memory usage)


# nvcc

The following command may not be found in docker (it is only available in versions with tag `devel` )

```bash
nvcc --version
```

# Docker

```bash
docker run -dit --gpus all [imageName] bash

docker run -dit --gpus all -v /[path1]/[dir_name]:/[path2]/[dir_name] [imageName] bash
```


# Questions

* Running more than one CUDA applications on one GPU

[https://stackoverflow.com/questions/31643570/running-more-than-one-cuda-applications-on-one-gpu](https://stackoverflow.com/questions/31643570/running-more-than-one-cuda-applications-on-one-gpu)

CUDA activity from independent host processes will normally create independent CUDA contexts, one for each process. Thus, the CUDA activity launched from separate host processes will take place in separate CUDA contexts, on the same device. CUDA activity in separate contexts will be serialized. The GPU will execute the activity from one process, and when that activity is idle, it can and will context-switch to another context to complete the CUDA activity launched from the other process. The detailed inter-context scheduling behavior is not specified. 

It seems that at any given instant in time, code from only one context can run. The "exception" to this case (serialization of GPU activity from independent host processes) would be the CUDA Multi-Process Server.


# Troubleshooting

Rebooting can solve some problems: `sudo reboot`, then enter the sudo password

* RuntimeError: CUDA error: CUBLAS_STATUS_NOT_INITIALIZED when calling `cublasCreate(handle)` /opt/conda/conda-bld/pytorch_1616554793803/work/aten/src/THCUNN/ClassNLLCriterion.cu:108: cunn_ClassNLLCriterion_updateOutput_kernel: block: [0,0,0], thread: [0,0,0] Assertion `t >= 0 && t < n_classes` failed.

This possibly means that there is an `IndexError: Target XXX is out of bounds.` error. 


* `torch.cuda.is_available()` returns `False` and `UserWarning: CUDA initialization: Unexpected error from cudaGetDeviceCount(). Did you run some cuda functions before calling NumCudaDevices() that might have already set an error? Error 804: forward compatibility was attempted on non supported HW (Triggered internally at  /opt/conda/conda-bld/pytorch_1616554793803/work/c10/cuda/CUDAFunctions.cpp:109.)
  return torch._C._cuda_getDeviceCount() > 0`


[https://stackoverflow.com/questions/66371130/cuda-initialization-unexpected-error-from-cudagetdevicecount](https://stackoverflow.com/questions/66371130/cuda-initialization-unexpected-error-from-cudagetdevicecount)

This problem is not solved.



