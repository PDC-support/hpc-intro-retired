---
layout: episode
title: "Job scheduling"
teaching: 10
exercises: 0
questions:
  - "What is Slurm?"
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

## What is Slurm?

[Slurm](https://slurm.schedmd.com/) is an open source, 
fault-tolerant, and highly scalable cluster management and job scheduling system:
- Allocates access to resources for some duration of time.
- Provides a framework for starting, executing, and monitoring work on the set of allocated nodes.
- Arbitrates contention for resources by managing a queue.

How does Slurm ensure that resources are shared in a fair and balanced way 
between competing users?   
A key quantity is the *Job Priority*, which Slurm computes based on
- **Age:** The length of time a job has been waiting.
- **Fair-share:** The difference between the portion of the computing resource 
  that has been promised and the amount of resources that has been consumed.
- **Job size:** The number of nodes and duration of time a job is allocated.
- **Partition:** A factor associated with each node partition.

## How is the Slurm scheduler used?

*Jobs* can be run on PDC clusters using Slurm in two different ways:
- **Batch jobs** 
  - The user prepares a *batch script* with directives to 
    Slurm about the number of nodes and time duration requested for the job, along
    with commands which perform the desired calculations.
  - The script is *submitted* to the batch queue, Slurm evaluates its priority 
    and starts executing it when it reaches the front of the queue.
  - When the job is finished, the user retrieves the output files.
- **Interactive jobs** 
  - The user runs a shell command which allocates interactive resources on a 
    specified number of nodes and duration of time.
  - The interactive job waits in the queue in the same way as batch jobs.
  - When the job reaches the front of the queue, the user gets access to the 
    resources and can run commands or open GUIs to perform the desired calculations.

### Slurm commands

To work with the Slurm scheduler, you need to learn a few Slurm commands. The 
first five commands in the table below are typically all you need to use Slurm,
while the remaining four can be useful but are not needed.

| Command	| Action	|
| ------------- | ------------- |
| `sbatch <script>`     | Submit batch script to queue |
| `salloc <resource specs>`     | Request interactive resources |
| `squeue [-u <username>]`     | Inspect the Slurm queue       |
| `srun <script>`       | Initiate job |
| `scancel <job-id>`    | Cancel running job            |
| ------------- | ------------- |
| `scontrol` | View and modify Slurm config and state |
| `scontrol show job <job-id>` | Show details about job |
| `sinfo`              | View Slurm nodes and partitions |
| `sacct`              | Get info on running or completed jobs |
| ------------- | ------------- |

### Slurm environment variables

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

### Interactive jobs

We will now explore different ways to interact with the scheduler and see 
how to run interactive jobs. We will be working on Tegner.   
Let's first inspect Slurm nodes and partitions:
```
[tegner]$ sinfo

PARTITION AVAIL  TIMELIMIT  NODES  STATE NODELIST
main*        up   infinite      2  down* t02n[07,33]
main*        up   infinite      1  drain t03n08
main*        up   infinite      1   resv t02n40
main*        up   infinite     29  alloc t02n[01-05,08-09,13-20,30-32,34-36,45-46],t03n[02,13,15-16,18,28]
main*        up   infinite     33   idle t01n[01,05,09,13,17,26,30,34,41,45],t02n[06,21,28-29,41-44,47-48],t03n[01,03-07,09,14,17,19-21,29]
K80          up   infinite      1  drain t03n08
K80          up   infinite      1  alloc t03n02
K80          up   infinite      7   idle t03n[01,03-07,09]
1TB          up   infinite      5   idle t01n[26,30,34,41,45]
2TB          up   infinite      5   idle t01n[01,05,09,13,17]
Transfer     up   infinite      2   idle t04n[27-28]
nbis         up   infinite      1   idle t04n03
```

The output shows:
- Several partitions: `main`, `K80`, `1TB`, `2TB`, `Transfer` and `nbis`
  - **What's the difference between them?**
- How many nodes are in each partition and their names.
- The state of all nodes: *allocated*, *idle*, being *drained*, *reserved* or *down*.

> ## Which allocation should we use?
> 
> You need to belong to a time allocation in order to use PDC resources.
> Generally, you would follow the procedure described 
> [in the previous episode](../03-gettingaccess) and apply for an allocation.
> For this workshop, an allocation has been set up called `edu18.intropdc2`.
{: .callout}

Let us now allocate an interactive node for 20 minutes using the `salloc` command:
```bash
[tegner]$ salloc -A edu18.intropdc2 -t 0:20:0 --nodes=1
```
When your allocation is granted, *a new terminal session starts*.
- Previous command history vanishes.
- Simply typing `exit` will stop the interactive session.
- This can be seen in the output of the `ps` command:
  ```bash
  [tegner]$ ps
  PID TTY          TIME CMD
  12770 pts/17   00:00:00 bash
  17222 pts/17   00:00:00 salloc
  17226 pts/17   00:00:00 bash      
  17418 pts/17   00:00:00 ps
  ```

> ## Which node did we get?
>
> When we are granted access to the compute node, there are at least three ways to 
> see which node we got. Try the following:
> - **Type `squeue -u $USER` and inspect the last field.**
> - **Inspect the (just created) environment variable SLURM_NODELIST.**
> - **Run `srun -n 1 hostname`. What does this command do? Compare this to just 
>   running `hostname` (without `srun`), is it what you expected?**
{: .challenge}

> ## What Slurm environment variables have been set? 
>
> - **Try typing `echo $SLUR` and press `Tab` key a couple of times for autocompletion.**
> - **Print out (`echo`) a few of the variables.**
{: .challenge}

Let's now stop our interactive session and move on to batch jobs.

### Batch job scripts

The most common usage of supercomputer resources is through batch scripts.
Here is an basic batch script which contains a minimal number of Slurm options.

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
  - **What modules would you need to load on Tegner in this case?**
- When the script completes (i.e. when `./myexe` finishes) an exit status is returned to Slurm and the job stops (regardless of how much time is left of the requested time)
- Note that when running on Beskow, use `aprun -n <nproc>` instead of `mpirun -np <nproc>`.


> ## Submitting a batch script
> Copy-paste the example script above into a file in your Lustre 
> nobackup directory (`$SNIC_NOBACKUP`) where you compiled the MPI Hello World 
> code (if you haven't done this already, revisit 
  [the previous episode](../07-compiling)). Modify it as follows:
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

### Other useful Slurm options

We have seen a few options to Slurm that can be used as flags on the command line 
with the `salloc` command, or as options to `#SBATCH` in batch scripts. Here's 
a longer list which is still far from exhaustive. 
Some work for both `salloc` and `sbatch`, others only for `sbatch`.

| `salloc`/`sbatch` flag    |  Meaning               |
| ------------------------- | ---------------------- |
| `-A` or `--account`       | Allocation to be charged |
| `-t` or `--time`          | Requested time for job  |
| `-N` or `--nodes`         | Requested number of nodes |
| `-J` or `--job-name`      | Job name |
| `-o` or `--output` (`sbatch` only) | Filename for standard output |
| `-e` or `--error` (`sbatch` only) | Filename for standard error |
| `--mail-type`       | What info Slurm should send by email (NONE, BEGIN, END, FAIL, ALL) |
| `--mail-user`  | Email address if other than your address set in `$USER/.profile` |
| `--reservation` | Name of a Slurm reservation (used e.g. for courses) |
| `-a` or `--array` (`sbatch` only) | Submit a job array (see [next episode](../09-tipstricks))|
| `--ntasks-per-node`  | Request that ntasks be invoked on each node |
| `-c` or `--cpus-per-task` | How many CPUs each task should have |
| `-C` or `--contraint` | Request nodes with particular feature |
| ------------------------- | ---------------------- |
| `--mem=1000000` | Request a 1 TB node on Tegner |
| `--mem=2000000` | Request a 2 TB node on Tegner |
| `--gres=gpu:K420:1` | Request a K420 GPU node on Tegner |
| `--gres=gpu:K80:2` | Request a K80 GPU node on Tegner |
| ------------------------- | ---------------------- |
| `--ntasks-per-node=32` | Request a 32-core Haswell node on Beskow |
| `--ntasks-per-node=36` | Request a 36-core Broadwell node on Beskow |
