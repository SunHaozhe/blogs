---
layout: post
category: Misc     
title: Docker memo 
tagline: by SunHaozhe
tags: 
  - memo
mathjax: true
comments: true
published: true
---

# General information 

Show the Docker version information 

```zsh
docker version
```

Display system-wide information

```zsh
docker info 
```

Display a live stream of container(s) resource usage statistics

```zsh
docker stats
```

Show the history of an image 

```zsh
docker history [IMAGE_ID]
```

Display low-level information on Docker objects using `docker inspect`. For example, if one wants to compare content of images, one can look at section `RootFS`. If all layers are identical then images contains identical content. 

```zsh
docker inspect <imageName>
```

```
"RootFS": {
        "Type": "layers",
        "Layers": [
            "sha256:eda7136a91b7b4ba57aee64509b42bda59e630afcb2b63482d1b3341bf6e2bbb",
            "sha256:c4c228cb4e20c84a0e268dda4ba36eea3c3b1e34c239126b6ee63de430720635",
            "sha256:e7ec07c2297f9507eeaccc02b0148dae0a3a473adec4ab8ec1cbaacde62928d9",
            "sha256:38e87cc81b6bed0c57f650d88ed8939aa71140b289a183ae158f1fa8e0de3ca8",
            "sha256:d0f537e75fa6bdad0df5f844c7854dc8f6631ff292eb53dc41e897bc453c3f11",
            "sha256:28caa9731d5da4265bad76fc67e6be12dfb2f5598c95a0c0d284a9a2443932bc"
        ]
    }
```

Remove unused data (remove all unused containers, networks, images (both dangling and unreferenced), and optionally, volumes.)

```bash
docker system prune
```

Login to registry (in order to be able to push to DockerHub)

```bash
docker login --username "XXX" --password "YYY"
```


# Docker image

Build an image from the Dockerfile in the current directory and tag the image

```zsh
docker build -t myimage:1.0 
```

Pull an image from a registry

```zsh
docker pull myimage:1.0 
```

Retag a local image with a new image name and tag

```zsh
docker tag myimage:1.0 myrepo/myimage:2.0 
```

Push an image to a registry 

```zsh
docker push myrepo/myimage:2.0 
```

List all images that are locally stored with the Docker Engine

```zsh
# both are equivalent 
docker image ls
docker images
```

Delete an image from the local image store

```zsh
docker image rm [imageName]
docker rmi [imageName] # equivalent 
docker image rm alpine:3.4 # an example 
```

Clean up unused images

```zsh
# remove dangling images, a dangling image is one that is not tagged and is not referenced by any container
docker image prune

# remove all images which are not used by existing containers 
docker image prune -a
```

# Docker container 

List containers (only shows running)

```zsh
docker ps
```

List all containers (including non-running)

```zsh
docker ps -a
```

List the running containers (add `--all` to include stopped containers) 

```zsh
docker container ls 
```

Run a container from the Alpine version 3.9 image, name the running container `web` and expose port 5000 externally, mapped to port 80 inside the container

```zsh
docker container run --name web -p 5000:80 alpine:3.9
```

The commands `docker stop` `docker start` `docker restart`:

```zsh
# to exit a container
docker stop [container]

# to restart a stopped container 
docker start [container]

# to restart a running container 
docker restart [container]
```

### Copy files/folders between a container and the local filesystem 

```zsh
docker cp [OPTIONS] CONTAINER:SRC_PATH DEST_PATH|-
docker cp [OPTIONS] SRC_PATH|- CONTAINER:DEST_PATH
```

An example: (The first line is run in the docker container. The second and third lines are run on the local terminal )

```
root@92d3e93c5606:/# mkdir -p /xxx/yyy/zzz 
% CONTAINER=92d3e93c5606
% docker cp ./upload_turbo.zip $CONTAINER:/xxx/yyy/zzz/hello_world.zip
```

Another example:

```zsh
docker run --name my_container ...
docker cp my_container:/xxx/yyy/ .
```


When you stop a container, it is not automatically removed unless you started it with the `--rm` flag. To see all containers on the Docker host, including stopped containers, use `docker ps -a`. You may be surprised how many containers exist, especially on a development system! A stopped container’s writable layers still take up disk space. To clean this up, you can use the `docker container prune` command.

```zsh
# remove all stopped containers 
docker container prune
```

Remove one or more containers (stop plus "prune")

```zsh
# remove one container 
docker rm CONTATINER_ID

# remove several containers
docker rm CONTATINER_ID_1 CONTATINER_ID_2

# the following two lines remove a container
# even if it is still running 
docker stop CONTAINER_ID
docker rm CONTATINER_ID

# remove the container even if it is still running
docker rm -f CONTAINER_ID

# remove a container and its volumes 
## Option -v removes anonymous volumes 
## associated with the container
docker rm -v CONTATINER_ID
```

# Volume


Volumes can be used by one or more containers, and take up space on the Docker host. Volumes are never removed automatically, because to do so could destroy data.

```zsh
# remove all volumes not used by at least one container 
docker volume prune
```



# Network 

List the networks 

```zsh
docker network ls 
```

```zsh
# network access is disabled
docker run -dit --network none alpine:3.9 /bin/bash
```

# The command: docker run 

`docker run` runs a command in a new container. 

Use `-dit` for `docker run`, in this way, the docker will be run at background and one can use `docker exec -it ...` to enter that container. If one run `exit` here, one can detach from this container and leave it running. 


```zsh
# -v: Bind mount a volume
## Map host's directory ~/xxx/yyy  
## to docker container's directory /zzz 
## Both are absolute path
docker run -dit --name my_env -v ~/xxx/yyy:/zzz [imageName]
```

```zsh
# network access is disabled
docker run -dit --network none [imageName] /bin/bash
```

Run docker which uses cuda GPU

```bash
docker run -dit --gpus all [imageName] bash
```

If you use `DataLoader` of PyTorch with `num_workers` greater than `0` in a docker container, you probably need to raise the shared memory limit by using `--shm-size=2gb` or `--ipc=host` or `-v /dev/shm:/dev/shm` for `docker run`.





# The command: docker commit 

`docker commit` creates a new image from a container's changes. The commit operation will not include any data contained in volumes mounted inside the container. By default, the container being committed and its processes will be paused while the image is committed. This reduces the likelihood of encountering data corruption during the process of creating the commit. If this behavior is undesired, set the `--pause` option to false.

```zsh
# Using the command "docker images",
# sunhaozhe/IMAGE_NAME corresponds to REPOSITORY,
# TAG_NAME corresponds to TAG. 

docker commit -a "John Hannibal Smith <hannibal@a-team.com>" -m "blablabla" [CONTAINER_ID] sunhaozhe/IMAGE_NAME:TAG_NAME

docker commit --author "John Hannibal Smith <hannibal@a-team.com>" --message "blablabla" [CONTAINER_ID] sunhaozhe/IMAGE_NAME:TAG_NAME
```

# Docker Hub

`docker push` pushes an image or a repository to a registry

Maybe create the repository first on Docker Hub's web, before running the following command: 

```zsh
# push local image to Docker Hub
docker push sunhaozhe/IMAGE_NAME:TAG_NAME
```


# Example 


Docker image sunhaozhe/pytorch-cu100-jupyter-gym:

	* linux Ubuntu 18.04
	* python 3.6.8
	* pytorch for GPU (cuda 10.0)
	* jupyter, configure jupyter notebook for remote connection
	* pip install gym 
	* apt-get update
	* apt-get install nano
	* apt install tmux
	* pip install visdom 
	* pip install --upgrade pip 
	* pip install tensorboard 
	* pip install tensorboardx (No !!!!!!!)
	* git clone https://github.com/lanpa/tensorboardX && cd tensorboardX && python setup.py install 

Docker image sunhaozhe/pytorch-cu100-gym-tmux-tensorboardx:

	* pip install --upgrade pip
	* pip install gym
	* apt-get update
	* apt-get install nano
	* apt install tmux
	* pip install tensorflow (not only tensorboard because reduced feature set)
	* git clone https://github.com/lanpa/tensorboardX && cd tensorboardX && python setup.py install


all-in-one with jupyter, CPU-only / Python 3.

```zsh
docker pull ufoym/deepo:all-py36-jupyter-cpu
```

all-in-one with jupyter, CUDA 10.0 / Python 3.6

```zsh
docker pull ufoym/deepo:all-jupyter-py36-cu100
```

```zsh
docker run -dit --name my_env --mount 
type=bind,source=C:\xxx\yyy\zzz\workspace,target=/workspace 
sunhaozhe/pytorch-cu100-gym-tmux-tensorboardx
```

* `-p 18888:8888` for jupyter notebook 
* `-p 8097:8097` for visdom server
* `-p 6006:6006` for tensorboardx 


```zsh
docker exec -it my_env bash
```

# Missing packages

### Vim

* `apt update`
* `apt search vim`
* `apt install vim`
* `vim --version` 


# Troubleshooter

* WARNING: Your kernel does not support swap limit capabilities or the cgroup is not mounted. Memory limited without swap.
  * Meaning: `docker run` won't impose any limitations on the use of swap space. However, the warning message is also trying to say that option -m, --memory will still take effect, and the maximum amount of user memory (including file cache) will be set as intended.
  * https://stackoverflow.com/a/63726105/7636942 
* ERROR: Unexpected bus error encountered in worker. This might be caused by insufficient shared memory (shm). [Reference](https://github.com/ultralytics/yolov3/issues/283)
  * Two possibilities:
    * Set `num-workers` to `0` (according to the doc of PyTorch `torch.utils.data DataLoade`, but this will slow down training)
    * Use the flag `--ipc=host` when executing `docker run ...`, be careful of [potential security issues](https://stackoverflow.com/questions/38907708/docker-ipc-host-and-security).
  * If you got a `Killed` message within a docker container, it probably means that you don't have enough memory/RAM (use the command `free -h` to check the amount of available memory). Either increase the amount of memory of the host machine, or increase the amount of memory docker is allowed to use. 


# Shared memory

### Background

Shared memory is memory that may be simultaneously accessed by multiple programs with an intent to provide communication among them or avoid redundant copies. Shared memory is an efficient means of passing data between programs. Depending on context, programs may run on a single processor or on multiple separate processors. Using memory for communication inside a single program, e.g. among its multiple threads, is also referred to as shared memory. 

The shared memory device, `/dev/shm`, provides a temporary file storage filesystem using RAM for storing files. It’s not mandatory to have /dev/shm, although it’s probably desirable since it facilitates inter-process communication (IPC). Why would you use `/dev/shm` instead of just stashing a temporary file under `/tmp`? Well, `/dev/shm` exists in RAM (so it’s fast), whereas `/tmp` resides on disk (so it’s relatively slow). The shared memory behaves just like a normal file system, but it’s all in RAM. 

In order to see how big it is in `/dev/shm`:

```bash
df -h
```

In order to check what’s currently under `/dev/shm`:

```bash
ls -l /dev/shm

# you may get: total 0
# which means there is currently nothing there
```


### Shared memory for Docker containers

Docker containers are allocated 64 MB of shared memory **by default**.

We can change the amount of shared memory allocated to a container by using the `--shm-size` option of `docker run`:

```bash
# 2 GB shared memory
docker run --shm-size=2gb ...
```

Unit can be `b` (bytes), `k` (kilobytes), `m` (megabytes), or `g` (gigabytes). If you omit the unit, the system uses bytes.

Note that in the above example, the container is getting its own `/dev/shm`, separate from that of the host.

What about sharing memory between the host and a container or between containers? This can be done by mounting `/dev/shm`:

```bash
docker run -v /dev/shm:/dev/shm ...
```

The option `--ipc=host`can be used instead of specifying `--shm-size=XXX`. `--shm-size=XXX` would be enough, but you'd need to set shared memory size that's enough for your workload. `--ipc=host` sets shared memory to the same value it has on bare metal. However, `--ipc=host` may have some security concerns, `--ipc=host` removes a layer of security and creates new attack vectors as any application running on the host that misbehaves when presented with malicious data in shared memory segments can become a potential attack vector. 


If you use `DataLoader` of PyTorch with `num_workers` greater than `0` in a docker container, you probably need to raise the shared memory limit because the default is only 64 MB: 

`RuntimeError: DataLoader worker (pid 585) is killed by signal: Bus error. It is possible that dataloader's workers are out of shared memory. Please try to raise your shared memory limit.`


If you still get erros like `RuntimeError: DataLoader worker (pid 4161) is killed by signal: Killed. segmentation faults` even though:

* you already set `--ipc=host` 
* your shared memory (type `df -h` in docker container) is as big as 15G or even 40G+
* you even tried to use 1 for the batch size...
* your code run perfectly with `num_workers=0` but breaks when it becomes larger than 0 (even with `num_workers=1`)

Then you can possibly solve this issue by simplifying the `__getitem__()` method of your custorm PyTorch dataset class. By simplifying, I mean you make the code in `__getitem__()` as simple as possible. For example, you'd better avoid using `pandas` `DataFrame` in `__getitem__()` for a reason that I did not understand. If originally your `__getitem__()` maps `idx` to `img_path` using a `pd.DataFrame`, then you'd better create a python list `items` which stores the same information in PyTorch dataset's `__init__` stage, then `__getitem__()` will only call the list `items`, it will never call `pd.DataFrame`. If this can still not solve the issue, then maybe try to increase the host's total memory or simplify the data augmentation techniques used in the `__getitem__()`.... I tried to reduce the amount of compute related to data augmentation and dataset loading before the training loop begins (outside `__getitem__()` because outside the training loop which involves dataloader multiprocessing), but mysteriously this solved the issue, no clue of why this happened...


Some other clues:

* import torch before sklearn
* upgrade sklearn to the latest version
* moving import pytorch before other imports
* reduce input image size
* the data you read and the python environment should be on the same mount. In his specific case, the python environment was in my home directory (which resides in some remote mount, to be shared across multiple compute nodes) and the data local to the compute node. After moving everything to be local with respect to the compute node, segfault went away.
* Just removed all the pandas logic out of one of my projects and still encounter the issue. Have a feeling it might be more related to cv2 (or modifying images with numpy operations rather than PIL operations). Oh I see. It's definitely cv2. It is known to be not nice with multiprocessing. Sorry I just did a quick glance at the imports and didn't see any cv2. Ok I removed usage of cv2, but it still seems to be happening. Now debating whether it is random or scipy.ndimage related. That's rather weird. What's more puzzling is that Python is segfaulting, not cv2 or pytorch libraries. Could you try a different python version? I upgraded to python 3.6 from 2.7. Seems to have worked. Thanks.


# References 

* http://www.ruanyifeng.com/blog/2018/02/docker-tutorial.html
* https://docs.docker.com/engine/reference/commandline/docker/ 
* https://datawookie.dev/blog/2021/11/shared-memory-docker/#:~:text=Docker%20containers%20are%20allocated%2064%20MB%20of%20shared%20memory%20by%20default.
* https://github.com/pytorch/pytorch/issues/1158
* https://github.com/pytorch/pytorch/issues/8976#issuecomment-401564899
* https://github.com/pytorch/pytorch/issues/4969











































































