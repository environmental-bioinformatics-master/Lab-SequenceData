# Job Submission with Slurm

## HPCS
Generally: an HPC looks like this:

![Image from Software Carpentry](images/hpc_system_diagram.png)

We have already used SSH to log in to the log in nodes (`l1` and `l2`). However, using a batch system (aka SLURM) we can actually access the power of the HPC which is in the compute nodes. To do this we use SLURM.

SLURM is a Workload Manager that helps users share computer resources. Posiedon is more than just the two log in nodes that we have been working on thus far in class. It is actually a grouping of many computers whose shared use is controlled by SLURM (Simple Linux Utility for Resource Management). SLURM is broadly used across HPC systems and allows access to resources over specified periods of time to do work, provides a framework to monitor jobs, and arbitrates multiple users requesting the same resources.

## Poseidon

Let's take a look at our particular HPC. Poseidon consists of the following:

*As a point of reference: Harriet's Mac has 8Gb of RAM and 2 cores.*

- 2 login nodes for user access to the cluster via 10 Gb Ethernet
- 77 compute nodes: 192GB RAM and 36 cores per node
- 1 shared memory node with 3TB RAM and 80 cores
- 4 Nvidia Volta V100 GPUs connected via NVLINK installed in 1 node with 384GB RAM

These are the *common* resources for every user of the HPC. In addition there are purchased sets of nodes that are only used by one group.

Type the command `sinfo`. This prints the status of our HPC and the current free / occupied nodes.

### Jargon

|Term   	| Definition|
|--------	|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------	|
| Node   	| One system, server or computer.  	|
| CPU    	| Central Processing Unit, and is the part of a computer which executes software programs.|
| Core   	| A core is an individual processor: the part of a computer which actually executes programs. CPUs used to have a single core, and the terms were interchangeable  |
| Thread 	| Threads refers to the ability for a cpu/core/processor to split a process to two or more running tasks (like having two queues to one cash register, it is pseudo-simultaneous). We do not have threading on our HPC but jobs can be parallelized across Cores or Nodes 	|
| RAM 	| Random Access Memory a form of memory in computing that can be actively read and used by a program	|

## Requesting resources on Poseidon

So, let's say you have a job you want to run-- and you think it is going to take a while or be too big for your computer. You are able to run that job on the HPC by using SLURM to request the resources (time and space) that you predict the job is going to require.

#### Information needed by SLURM
|Term   	| Definition|
|--------	|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------	|
| `partition`   	| Which queue do you want your job to run on (e.g. `compute`)  	|
| `job-name`    	| What is the name of your job?|
| `ntasks`   	| How many nodes do you want to run your job on? For *most* things in bioinformatics this is going to be 1. |
| `ntasks-per-node` 	| How many cores do you need on your node?	If your tool allows threading-- this is how you can do it. (Max on `compute`: 36)|
| `mem`| How much RAM does your job need per node?	(Max on `compute`: 192Gb)|
| `time` 	| How long does your job need to run? (Max on `compute`: 24 hours)	|

Yes, you do have to guess what kind of resources your job is going to require. It is kind of like going to a gas station that is cash only that requires that you pay some amount of money before you can start pumping your gas. If you guess too little, you won't fill your tank, and if you guess too much you will have to go get change. Generally speaking, on an HPC it is better to guess too much...  because instead of being able to drive away with a half tank of gas if you underestimate the gas cost, here you will end up with partially completed program that returns often times nothing if you don't guess correctly.

So, why not totally max out your request every time? Well, the scheduler will have a hard time placing your job and it will count against your fair usage (happy to talk about this later).

There are two main options for requesting time on the HPC:

1. Interactive runs with `srun` in real time (kind of like logging onto a node)
2. Submitting jobs or runs with `sbatch` which will be added to a queue and executed when resources are available.

Generally speaking, `srun` is useful when de-bugging your scripts or running active analysis (e.g. running a big `jupyter notebook`). On the other hand, `sbatch` is used to submit many or big jobs to the queue-- sort of a set it and forget it method of computation. You would use `sbatch` to run a big assembly or long blast search.

Today, we are going to be sticking with `srun` but we will use `sbatch` later in the class.

```bash
srun -p scavenger --time=00:05:00 --ntasks-per-node=1 --mem=1gb --pty bash
```

> What happened to your prompt?

Now, we can use the command `squeue` to check and see the status of our job. You should see that your name is listed next to a job number and the resources being used (this time the scavenger queue).

Type `exit` to log off of our interactive run and get back to the log in node.

Now, if you type `squeue` you should see that your job is still active. You can use the command `scancel <JOB NUMBER>` to cancel the slurm job.

## Resources
1. https://hpc.whoi.edu
2. https://whoi-it.whoi.edu/hpc-cluster-cost-comparison/
3. https://slurm.schedmd.com/rosetta.pdf
4. https://slurm.schedmd.com/
5. https://modules.readthedocs.io/en/latest/module.html
