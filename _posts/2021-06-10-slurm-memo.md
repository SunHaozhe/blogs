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

```bash
# Main work starts

args=()

for SEED in "21" "31" "41"
do
	for MODEL in "xxx" "yyy"
	do
		for WD in "0" "0.01" "0.1"
		do
			for LR in "0.001" "0.01"
			do
				args+=("blabla.py --model_name ${MODEL} --random_seed ${SEED} --num_workers 8 --lr ${LR} --weight_decay ${WD}")
			done
		done 
	done
done

echo "Starting python ${args[${SLURM_ARRAY_TASK_ID}]}"

srun python ${args[${SLURM_ARRAY_TASK_ID}]}

echo "End python ${args[${SLURM_ARRAY_TASK_ID}]}"

# Main work ends
```


How to choose nodes (suppose that we want to use nodes 51, 52, 53, 54, 55, 101, 102, we do not want to use nodes 1, 2, 3, 4, 5):

```bash
#SBATCH --exclude=n[1-5]
```

Or equivalently, 

```bash
#SBATCH --nodelist=n[51-55,101-102]	
```


# Advanced

Changing the maximum number of simultaneously running tasks for a running array job

```bash
scontrol update JobId=<jobID> ArrayTaskThrottle=<count>
```