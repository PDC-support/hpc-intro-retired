---
layout: episode
title: "Scheduling interactive and batch jobs"
teaching: 10
exercises: 0
questions:
  - "What is SLURM?"
  - "What's the difference between interactive and batch jobs?"
objectives:
  - "Understand the difference between interactive and batch jobs"
  - "Learn to write and submit batch scripts"
keypoints:
  - "K1"
  - "K2"
---

The computational resources on a supercomputer are shared simultaneously between 
hundreds of users. How can resources be allocated
in a fair way between all users, while at the same time ensuring that the system 
is maximally utilized?

# Simple Linux Utility for Resource Management 

<img src="../img/scheduling_jobs.png" alt="scheduling" width="600" align="middle"> 

## What is SLURM?

[SLURM](https://computing.llnl.gov/linux/slurm/) is an open source, 
fault-tolerant, and highly scalable cluster management and job scheduling system:
- Allocates access to resources for some duration of time.
- Provides a framework for starting, executing, and monitoring work on the set of allocated nodes.
- Arbitrates contention for resources by managing a queue.

How does SLURM ensure that resources are shared in a fair and balanced way 
between competing users?   
A key quantity is the *Job Priority*, which SLURM computes based on
- **Age:** The length of time a job has been waiting.
- **Fair-share:** The difference between the portion of the computing resource 
  that has been promised and the amount of resources that has been consumed.
- **Job size:** The number of nodes and duration of time a job is allocated.
- **Partition:** A factor associated with each node partition.

## How is the SLURM scheduler used?

*Jobs* can be run on PDC clusters in two different ways:
- **Batch jobs** 
  - The user prepares a *batch script* with directives to 
    SLURM about the number of nodes and time duration requested for the job, along
    with commands which perform the desired calculations.
  - The script is *submitted* to the batch queue, SLURM evaluates its priority 
    and starts executing it when it reaches the front of the queue.
  - When the job is finished, the user retrieves the output files.
- **Interactive jobs** 
  - The user runs a shell command which allocates interactive resources on a 
    specified number of nodes and duration of time.
  - The interactive job waits in the queue in the same way as batch jobs.
  - When the job reaches the front of the queue, the user gets access to the 
    resources and can run commands or open GUIs to perform the desired calculations.

### SLURM commands

To work with the SLURM scheduler, you need to learn a few SLURM commands. The 
first five commands in the table below are typically all you need to use SLURM,
while the remaining four can be useful but are not needed.

| Command	| Action	|
| ------------- | ------------- |
| `sbatch <script>`     | Submit batch script to queue |
| `salloc <resource specs>`     | Request interactive resources |
| `squeue [-u <username>]`     | Inspect the SLURM queue       |
| `srun <script>`       | Initiate job |
| `scancel <job-id>`    | Cancel running job            |
| ------------- | ------------- |
| `scontrol` | View and modify SLURM config and state |
| `scontrol show job <job-id>` | Show details about job |
| `sinfo`              | View SLURM nodes and partitions |
| `sacct`              | Get info on running or completed jobs |
| ------------- | ------------- |

### SLURM environment variables

A number of environment variables get defined when a job starts executing.
These are not necessary to know about or use, but they can be convenient for, e.g., 
writing general batch scripts that can work on different clusters.

| Variable name	| Meaning	|
| ------------- | ------------- |
| `SLURM_NNODES`       | Number of nodes allocated for the job  |
| `SLURM_TASKS_PER_NODE` | Number of tasks initiated on each node |
| `SLURM_NODELIST`       | Names of all nodes allocated for job   |
| `SLURM_JOB_ID`         | Job-ID for the job    |
| `SLURM_MEM_PER_NODE`   | Amount of memory available per node |
| `SLURM_CLUSTER_NAME`   | Name of cluster  |
| `SLURM_JOB_ACCOUNT`    | Name of the project to charge for used resources |
| `SLURM_SUBMIT_DIR`     | Directory where job was submitted |

### Batch job scripts

The most common usage of supercomputer resources is through batch scripts.
Here is an basic batch script which contains a minimal amount of SLURM options.

```bash
#!/bin/bash -l
# Set the allocation to be charged for this job
#SBATCH -A <201X-X-XX>
# The name of the job
#SBATCH -J myjobname
# 1 hour wall-clock time is requested 
#SBATCH -t 1:00:00
# Two nodes are requested
#SBATCH --nodes=2
# Names of output and error files
#SBATCH -e error_file.e
#SBATCH -o output_file.o

# Loading modules needed by your job
module add X
module add Y

# Run the executable named myexe with 48 MPI ranks and direct output to my_output_file
mpirun -n 48 ./myexe > my_output_file 2>&1
```

- All `#SBATCH` options need to be at the top of the script.
- The environment for the job (e.g. modules, environment variables) needs to be specified.
  - **What modules would you need to load on Tegner?**
- When the script completes (i.e. when `./myexe` finishes) an exit status is returned to Slurm and the job stops (regardless of how much time is left of allocation)
- Note that when running on Beskow, use `aprun -n <nproc>` instead of `mpirun -np <nproc>`.


> ## Which allocation should we use?
> 
> You need to belong to a time allocation in order to use PDC resources.
> Generally, you would follow the procedure described 
> [in the previous episode](../03-gettingaccess) and apply for an allocation.
> For this workshop, an allocation has been set up called `edu18.intropdc2`.
{: .callout}

> ## Submitting a batch script
> Copy-paste the example script above into a file in your Lustre 
> nobackup directory (`$SNIC_NOBACKUP`) where you compiled the MPI Hello World 
> code. Modify it as follows:
> - Use the workshop allocation `edu18.intropdc2`.
> - Request only one node (24 cores) and 10 minutes.
> - Load the modules `gcc/7.2.0` and `openmpi/3.0-gcc-7.2`
> - Run the command `mpirun -n 24 ./hello_mpi` (instead of `./myexe`).
> - Also add the command `srun -n 1 hostname` (what does it do?).
> - Finally add a line `sleep 60` at the bottom of the script.
> 
> When this script gets executed, it will run `hello_mpi` and `hostname` on a 
> compute node allocated for your job, and then "sleep" for 60 seconds.
> - **Submit this script to the Slurm queue using the `sbatch` command.**
> - **When you have submitted it, run `squeue -u $USER` to monitor the queue.**
>   - **What is the job-ID of your running job? Try using it with `scontrol show job <job-ID>`.** 
>   - **What does the `ST` column mean?**
>   - **Which node was allocated to your job?**
> - **After the job finishes, inspect the output files of your job.**
{: .challenge}
