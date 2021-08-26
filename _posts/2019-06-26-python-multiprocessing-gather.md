---
layout: post
category: Python
title: Python multiprocessing gather
tagline: by SunHaozhe
tags: 
  - Python
  - Multiprocessing
mathjax: true
comments: true
published: true
---

This experiment was run on Linux-4.9.125-linuxkit-x86_64-with-Ubuntu-18.04-bionic (indeed, in a docker Virtual Machine) with Python 3.6.8, the system had 4 physical cores with 4 hyperthreads, thus 8 logical cores.

An incorrect way to do:
```python
import multiprocessing, os
import numpy as np
import gym

def worker(seed, **kwargs):
    print("begin worker", seed)
    res = 0
    for i in range(100000000):
        res += seed
    print("end worker", seed)
    return res, res / 2

if __name__ == '__main__':

    nb_processes = 16

    print("There are %d CPUs, we run %d experiments in parallel." % (os.cpu_count(), nb_processes))

    pool = multiprocessing.Pool(processes=nb_processes)

    random_seeds = [10 * i for i in range(1, nb_processes + 1)]

    kwargs = {"attribute1": 0, "attribute2": 3}

    array1 = np.zeros(nb_processes)
    array2 = np.zeros(nb_processes)

    for i, seed in enumerate(random_seeds):
        array1[i], array2[i] = pool.apply_async(worker, (seed,), kwargs).get()

    print('Waiting for all subprocesses done...')
    pool.close()
    pool.join()
    print('All subprocesses done.')

    print(array1)
    print(array2)
```

The output was
```python
There are 8 CPUs, we run 16 experiments in parallel.
begin worker 10
end worker 10
begin worker 20
end worker 20
begin worker 30
end worker 30
begin worker 40
end worker 40
begin worker 50
end worker 50
begin worker 60
end worker 60
begin worker 70
end worker 70
begin worker 80
end worker 80
begin worker 90
end worker 90
begin worker 100
end worker 100
begin worker 110
end worker 110
begin worker 120
end worker 120
begin worker 130
end worker 130
begin worker 140
end worker 140
begin worker 150
end worker 150
begin worker 160
end worker 160
Waiting for all subprocesses done...
All subprocesses done.
[1.0e+09 2.0e+09 3.0e+09 4.0e+09 5.0e+09 6.0e+09 7.0e+09 8.0e+09 9.0e+09
 1.0e+10 1.1e+10 1.2e+10 1.3e+10 1.4e+10 1.5e+10 1.6e+10]
[5.0e+08 1.0e+09 1.5e+09 2.0e+09 2.5e+09 3.0e+09 3.5e+09 4.0e+09 4.5e+09
 5.0e+09 5.5e+09 6.0e+09 6.5e+09 7.0e+09 7.5e+09 8.0e+09]
```
By doing so, only 1 core among 8 cores was used at 100%, whereas other 7 cores were almost at 0% (checked by linux command `top`). At a given time, only 100% (instead of 800%) of CPU charge was used, even though this 100% CPU charge may move from one core to another every time a new process started.

******************************************************************************************************************************
The correct way to do:
```python
import multiprocessing, os
import numpy as np
import gym

def worker(seed, **kwargs):
	print("begin worker", seed)
	res = 0
	for i in range(100000000):
		res += seed
	print("end worker", seed)
	return res, res / 2

if __name__ == '__main__':

	nb_processes = 16

	print("There are %d CPUs, we run %d experiments in parallel." % (os.cpu_count(), nb_processes))

	pool = multiprocessing.Pool(processes=nb_processes)

	random_seeds = [10 * i for i in range(1, nb_processes + 1)]

	kwargs = {"attribute1": 0, "attribute2": 3}

	array1 = np.zeros(nb_processes)
	array2 = np.zeros(nb_processes)

	async_results = []
	for i, seed in enumerate(random_seeds):
		async_results.append(pool.apply_async(worker, (seed,), kwargs))

	for i in range(len(random_seeds)):
		array1[i], array2[i] = async_results[i].get()

	print('Waiting for all subprocesses done...')
	pool.close()
	pool.join()
	print('All subprocesses done.')

	print(array1)
	print(array2)
```

The output of the correct way was:
```python
There are 8 CPUs, we run 16 experiments in parallel.
begin worker 10
begin worker 20
begin worker 30
begin worker 40
begin worker 50
begin worker 60
begin worker 70
begin worker 80
begin worker 110
begin worker 90
begin worker 140
begin worker 150
begin worker 160
begin worker 130
begin worker 120
begin worker 100
end worker 10
end worker 30
end worker 20
end worker 40
end worker 160
end worker 50
end worker 140
end worker 70
end worker 60
end worker 100
end worker 110
end worker 80
end worker 120
end worker 90
end worker 130
end worker 150
Waiting for all subprocesses done...
All subprocesses done.
[1.0e+09 2.0e+09 3.0e+09 4.0e+09 5.0e+09 6.0e+09 7.0e+09 8.0e+09 9.0e+09
 1.0e+10 1.1e+10 1.2e+10 1.3e+10 1.4e+10 1.5e+10 1.6e+10]
[5.0e+08 1.0e+09 1.5e+09 2.0e+09 2.5e+09 3.0e+09 3.5e+09 4.0e+09 4.5e+09
 5.0e+09 5.5e+09 6.0e+09 6.5e+09 7.0e+09 7.5e+09 8.0e+09]
```

By using the correct way, all 8 cores were used at 100% (checked by linux command `top`).

The difference is the following:
```python
for i, seed in enumerate(random_seeds):
	array1[i], array2[i] = pool.apply_async(worker, (seed,), kwargs).get()
```
becomes
```python
async_results = []
for i, seed in enumerate(random_seeds):
	async_results.append(pool.apply_async(worker, (seed,), kwargs))

for i in range(len(random_seeds)):
	array1[i], array2[i] = async_results[i].get()

```


