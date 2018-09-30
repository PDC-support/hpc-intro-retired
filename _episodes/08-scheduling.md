---
layout: episode
title: "Scheduling interactive and batch jobs"
teaching: 10
exercises: 0
questions:
  - "What is SLURM?"
  - "What's the difference between interactive and batch jobs?"
objectives:
  - "O1"
  - "O2"
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
- **Job size:** The number of nodes or CPUs a job is allocated 
- **Partition:** A factor associated with each node partition

## How do we use the SLURM scheduler?

*Jobs* can be run on PDC clusters in two different ways:
- **Batch jobs** 
  - The user prepares a *batch script* with directives to 
    SLURM about the amount and time duration of the requested resources, and 
    commands which perform the desired calculations.
  - The script is *submitted* to the batch queue, SLURM evaluates its priority 
    and starts executing it when it reaches the front of the queue.
  - When the job is finished, the user retrieves the output files.
- **Interactive jobs** 
  - The user runs a shell command which allocates interactive resources on a 
    specified amount of resources and duration of time.
  - The interactive job waits in the queue in the same way as batch jobs.
  - When the job reaches the front of the queue, the user gets access to the 
    resources and can run shell commands to perform the desired calculations.

### SLURM commands

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

| Variable name	| Meaning	|
| ------------- | ------------- |
| `SLURM_NNODES`     | WRITEME  |


### Batch job scripts

```bash
#!/bin/bash -l
# Set the allocation to be charged for this job
#SBATCH -A <201X-X-XX>
# The name of the script is myjob
#SBATCH -J myjob
# 1 hour wall-clock time will be given to this job
#SBATCH -t 1:00:00
# Number of nodes
#SBATCH --nodes=2
# Number of MPI processes per node
#SBATCH --ntasks-per-node=24
# Names of output and error files
#SBATCH -e error_file.e
#SBATCH -o output_file.o

# Loading modules needed by your job
module add X
module add Y

# Run the executable named myexe with 48 MPI ranks and direct output to my_output_file
mpirun -n 48 ./myexe > my_output_file 2>&1
```