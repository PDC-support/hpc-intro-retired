---
layout: episode
title: "Job scheduling"
teaching: 10
exercises: 10
questions:
  - "What is SLURM?"
  - "What's the difference between interactive and batch jobs?"
  - "What other nice features does SLURM offer?"
objectives:
  - "Understand the difference between interactive and batch jobs"
  - "Learn to write and submit batch scripts"
  - "Learn about job arrays and job dependencies"
keypoints:
  - "SLURM is a powerful job scheduler used in clusters around the world"
  - "Jobs on PDC clusters can be either be *interactive* (`salloc`) or based on *batch* scripts (`sbatch`)"
  - "Job arrays and dependencies are SLURM features that sometimes come in handy"
---

The computational resources on a supercomputer are shared simultaneously between 
hundreds of users. How can resources be allocated
in a fair way between all users, while at the same time ensuring that the system 
is maximally utilized?

# Simple Linux Utility for Resource Management 

<img src="../img/scheduling_jobs.png" alt="scheduling" width="600" align="middle"> 

## What is SLURM?

[SLURM](https://slurm.schedmd.com/) is an open source, 
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

*Jobs* can be run on PDC clusters using SLURM in two different ways:
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

### Interactive jobs

We will now explore different ways to interact with the scheduler and see 
how to run interactive jobs. We will be working on {{ site.Cluster }}.   
Let's first inspect SLURM nodes and partitions:
{% if site.cluster == "tegner" %}
```
[{{ site.cluster }}]$ sinfo

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
{% elsif site.cluster == "beskow" %}
```
[{{ site.cluster }}]$ sinfo

PARTITION AVAIL  TIMELIMIT  NODES  STATE NODELIST
2d4-512   up    4-512    2-00:00:00     64 2:16:2       8 maint*     nid0[0820-0823,1072-1075]
2d4-512   up    4-512    2-00:00:00     64 2:16:2       4 allocated$ nid000[64-67]
2d4-512   up    4-512    2-00:00:00     64 2:16:2       3 idle*      nid0[0045,1080,1328]
2d4-512   up    4-512    2-00:00:00     64 2:16:2       1 down*      nid00243
...
```
{% endif %}

The output shows:
- Several partitions can be seen. What's the difference between them?
- How many nodes are in each partition and what are their names?
- The state of all nodes: *allocated*, *idle*, being *drained*, *reserved* or *down*.

> ## Which allocation should we use?
> 
> You need to belong to a time allocation in order to use PDC resources.
> Generally, you would follow the procedure described 
> [in the previous episode]({{ site.baseurl }}/04-gettingaccess) and apply for an allocation.
> For this course, an allocation has been set up called `{{ site.allocation }}`.
> {% unless site.reservation == "NONE" %}
> You should also use the course allocation called `{{ site.reservation }}`.
> {% endunless %}
{: .callout}

Let us now allocate an interactive node for 10 minutes using the `salloc` command:
{% if site.reservation == "NONE" %}
```bash
[{{ site.cluster }}]$ salloc -A {{ site.allocation }} -t 0:10:0 --nodes=1
```
{% else %}
```bash
[{{ site.cluster }}]$ salloc -A {{ site.allocation }} --reservation {{ site.reservation }} -t 0:10:0 --nodes=1
```
{% endif %}
When your allocation is granted, *a new terminal session starts*.
- Previous command history vanishes.
- Simply typing `exit` will stop the interactive session.
- This can be seen in the output of the `ps` command:
  ```bash
  [{{ site.cluster }}]$ ps
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

> ## What SLURM environment variables have been set? 
>
> - **Try typing `echo $SLUR` and press `Tab` key a couple of times for autocompletion.**
> - **Print out (`echo`) a few of the variables.**
{: .challenge}

Let's now stop our interactive session and move on to batch jobs.

### Batch job scripts

The most common usage of supercomputer resources is through batch scripts.
Here is an basic batch script which contains a minimal number of SLURM options.

```bash
#!/bin/bash -l
# Set the allocation to be charged for this job
#SBATCH -A <allocation-name>
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
{% if site.cluster == "tegner" %}
mpirun -n 48 ./myexe > my_output_file 2>&1
{% elsif site.cluster == "beskow" %}
srun -n 32 ./myexe > my_output_file 2>&1 {% endif %}
```

- All `#SBATCH` options need to be at the top of the script.
- The environment for the job (e.g. modules, environment variables) needs to be specified.
  - **What modules would you need to load on {{ site.Cluster }} in this case?**
- When the script completes (i.e. when `./myexe` finishes) an exit status is returned to SLURM and the job stops (regardless of how much time is left of the requested time)
- Note that on Beskow you should use `aprun -n <nproc>` while on Tegner it should be `mpirun -n <nproc>`.


> ## Submitting a batch script
> Copy-paste the example script above into a file in your Lustre 
> nobackup directory (`$SNIC_NOBACKUP`) where you compiled the MPI Hello World 
> code (if you haven't done this already, revisit 
  [the previous episode](../08-compiling)). Modify it as follows:
> - Use the workshop allocation `{{ site.allocation }}`.
> - Request only one node (24 cores) and 2 minutes.
> - Load the modules `gcc/7.2.0` and `openmpi/3.0-gcc-7.2`
> - Run the command `mpirun -n 24 ./hello_mpi`.
> - Also add the command `srun -n 1 hostname` (what does it do?).
> - Finally add a line `sleep 60` at the bottom of the script.
> 
> When this script gets executed, it will run `hello_mpi` and `hostname` on a 
> compute node allocated for your job, and then "sleep" for 60 seconds.
> - **Submit this script to the SLURM queue using the `sbatch` command.**
> - **When you have submitted it, run `squeue -u $USER` to monitor the queue.**
>   - **What is the job-ID of your running job? Try using it with `scontrol show job <job-ID>`.** 
>   - **What does the `ST` column mean?**
>   - **Which node was allocated to your job?**
> - **After the job finishes, inspect the output files of your job.**
{: .challenge}

> ## `salloc` vs `sbatch`
>
> Keep in mind:
> - `salloc` gives access to one or more *interactive* nodes
> - `sbatch` is used with a script to submit a job to the queue
{: .callout}

### Other useful SLURM options

We have seen a few options to SLURM that can be used as flags on the command line 
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
| `--mail-type`       | What info SLURM should send by email (NONE, BEGIN, END, FAIL, ALL) |
| `--mail-user`  | Email address if other than your address set in `$USER/.profile` |
| `--reservation` | Name of a SLURM reservation (used e.g. for courses) |
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

### Setting a default allocation

If you want to avoid having to set `-A <20XX-YY-ZZ>` in all your batch job scripts and when 
you run `salloc`, you can set a default SLURM account:
```bash
$ sacctmgr modify user <username> set defaultaccount=<20XX-YY-ZZ> where cluster=Beskow
```
To see if a default account is set, run
```bash
$ sacctmgr show User <username>
```

> Note that `sacctmgr` only works on Beskow.

### Being friends with SLURM
  - Avoid wide short jobs (massively parallel but very short runtime).
  - Avoid massive output to STDOUT.
  - Try to provide a good estimate of the job duration before submitting.

---

### (Optional) Job arrays

In scientific computing, one often needs to run a number of calculations of the
same type. SLURM *job arrays* are useful to manage many jobs which run on the
same number of nodes and take roughly the same time to complete.

Suppose you have 100 jobs residing in folders `data0`, `data1`, ..., `data99`.
Below is an example of a job array, in which 100 separate jobs are executed in
one shot in the corresponding directories.

```bash
#!/bin/bash -l
# The -l above is required to get the full environment with modules

# Set the allocation to be charged for this job
# not required if you have set a default allocation
#SBATCH -A {{ site.allocation }}

# The name of the script is myjob
#SBATCH -J myjobarray

# 1 hour wall-clock time will be given to this job
#SBATCH -t 01:00:00

# Number of nodes used for each individual job
#SBATCH --nodes=1

#SBATCH -e error_file.e
#SBATCH -o output_file.o

# Indices of individual jobs in the job array
#SBATCH -a 0-99

# Fetch one directory from the array based on the task ID
# Note: index starts from 0
CURRENT_DIR=data${SLURM_ARRAY_TASK_ID}
echo "Running simulation in $CURRENT_DIR"

# Go to job folder
cd $CURRENT_DIR
echo "Simulation in $CURRENT_DIR" > my_output_file

# Run individual job
{% if site.cluster == "tegner" %}mpirun -n 32 ./myexe >> my_output_file
{% elsif site.cluster == "beskow" %}aprun -n 32 ./myexe >> my_output_file {% endif %}
```

---

### (Optional) Job dependencies

Sometimes you may want one job to start after another job has been completed, for example to 
use the output of the first job as input to the second job.
This can be accomplished with *job dependencies*.   

Let's say we have two jobs, A and B. We want job B to start after job A has successfully completed.

We first start job A by submitting it:
```bash
$ sbatch <jobA.sh>
```
We then make note of the assigned job ID for job A, and submit job B with the added 
condition that it only starts after job A has successfully completed:
```bash
$ sbatch --dependency=afterok:<jobID_A> jobB.sh
```
If we want job B to start after several other jobs have completed, 
we can specify additional jobs, using a ':' as a delimiter:
```bash
$ sbatch --dependency=afterok:<jobID_A>:<jobID_C>:<jobID_D> jobB.sh
```
We can also tell SLURM to run job B, even if job A fails:
```bash
$ sbatch --dependency=afterany:<jobID_A> jobB.sh
```

> ## Submit a small job array
>
> Edit the example job array script such that it
> - asks for a short time (e.g. `#SBATCH -t 00:05:00`)
> - requests a small job array (e.g. `#SBATCH -a 0-4`)
> - creates the folder if it doesn't exist (e.g. `mkdir -p $CURRENT_DIR`)
> - runs a small calculation (e.g. `srun -n 1 python -c "print (${SLURM_ARRAY_TASK_ID}**2)"`)
>
> Submit the job and examine the output in different folders.
{: .challenge}

> ## Make use of job dependency
>
> 1. Submit a job that sleeps for 30 seconds and then writes a message to a file.
>    - `sleep 30 && echo "hello" > file.txt`
> 2. Submit a second job with ``--dependency=afterok:<jobID>`` to print the message in the file. 
>    - `cat file.txt`
{: .challenge}

