---
layout: post
category: Misc     
title: slurm memo  
tagline: by SunHaozhe
tags: 
  - memo
mathjax: true
comments: true
published: true
---


# Basics

```bash
squeue

squeue|grep <user_name>

sacct

sacct|grep FAILED

scontrol show job <jobID>

sshare

sshare|grep <user_name>

sshare -a

sbatch <slurm_script>

srun <command>
```

# Format

header 

```bash
#!/bin/bash
#SBATCH --gres=gpu:1
#SBATCH --job-name=<job_name>
#SBATCH --partition=<partition_name>
#SBATCH --qos=<qos_name>
#SBATCH --mem=32g
#SBATCH --nodes=1
#SBATCH --cpus-per-task=4
#SBATCH --output=logs/%j.stdout
#SBATCH --error=logs/%j.stderr
#SBATCH --open-mode=append
#SBATCH --signal=B:USR1@120
#SBATCH --requeue
#SBATCH --nodelist=<node_name>

SECONDS=0

restart(){
	echo "Calling restart" 
	scontrol requeue $SLURM_JOB_ID
	echo "Scheduled job for restart" 
}

ignore(){
	echo "Ignored SIGTERM" 
}
trap restart USR1
trap ignore TERM

date 

# Main work starts


# Main work ends

DURATION=$SECONDS

echo "End of the program! $(($DURATION / 60)) minutes and $(($DURATION % 60)) seconds elapsed." 
```

```bash
which python 

echo "Slurm job ID: $SLURM_JOB_ID" 

echo "CUDA_VISIBLE_DEVICES is $CUDA_VISIBLE_DEVICES" 

echo "Visualize environment variables HTTP_PROXY and HTTPS_PROXY:" 

echo $HTTP_PROXY 
echo $HTTPS_PROXY 
```

Job array:

```bash
#SBATCH --array=0-19%5
#SBATCH --output=logs/%A_%a.stdout
#SBATCH --error=logs/%A_%a.stderr
```


# Advanced

Changing the maximum number of simultaneously running tasks for a running array job

```bash
scontrol update JobId=<jobID> ArrayTaskThrottle=<count>
```