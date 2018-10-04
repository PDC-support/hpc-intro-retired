---
layout: episode
title: "Tips and tricks for efficient HPC usage"
teaching: 20
exercises: 0
questions:
  - "What other nice features does SLURM offer?"
  - "What useful commands can help me use HPC more efficiently?"
objectives:
  - "Learn about job arrays and job dependencies"
keypoints:
  - "K1"
  - "K2"
---

# SLURM tricks

### Job arrays

Suppose you have 100 jobs residing in folders `data0`, `data1`, ..., `data99`. 
Below is an example of a job array, in which 100 separate jobs are executed in one shot 
in the corresponding directories. Job arrays are useful to manage large numbers of jobs 
which run on the same number of nodes and take roughly the same time to complete.

```bash
#!/bin/bash -l
# The -l above is required to get the full environment with modules

# Set the allocation to be charged for this job
# not required if you have set a default allocation
#SBATCH -A <201X-X-XX>

# The name of the script is myjob
#SBATCH -J myjobarray

# 10 hours wall-clock time will be given to this job
#SBATCH -t 10:00:00

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
echo "Simulation in $CURRENT_DIR" > result

# Run individual job
aprun -n 32 ./myexe > my_output_file 2>&1
```


- Job dependencies

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
We can also tell Slurm to run job B, even if job A fails:
```bash
$ sbatch --dependency=afterany:<jobID_A> jobB.sh
```


### Setting a default allocation

If you want to avoid having to set `-A <20XX-YY-ZZ>` in all your batch job scripts and when 
you run `salloc`, you can set a default Slurm account:
```bash
$ sacctmgr modify user <username> set defaultaccount=<20XX-YY-ZZ> where cluster=Beskow
```
To see if a default account is set, run
```bash
$ sacctmgr show User <username>
```
Note that `sacctmgr` only works on Beskow.


# HPC best practices

- Being friends with Slurm
  - Avoid massive short jobs
  - Avoid massive output to STDOUT
  - Try to provide a good estimate of the job duration

- Being friends with the file system
  - Avoid creating very many small files (it slows down Lustre)
  - Try instead to write to few large files

- [Ahmdahl's law](https://en.wikipedia.org/wiki/Amdahl%27s_law)

  Speedup $S_P$ on $P$ processors is restricted by (f is the sequential fraction)  
  $$ S_P = \frac{1}{1 + (P-1)f} < \frac{1}{f} $$

  > If a program needs 20 hours using a single processor core, and a particular part of the 
  > program which takes one hour to execute cannot be parallelized, while the remaining 
  > 19 hours (p = 0.95) of execution time can be parallelized, then regardless of how many 
  > processors are devoted to a parallelized execution of this program, the minimum execution time 
  > cannot be less than that critical one hour. Hence, the theoretical speedup is limited to at most 
  > 20 times (1/(1 − p) = 20). For this reason, parallel computing with many processors is useful 
  > only for highly parallelizable programs.

- Testing your scaling

- Calibrate your jobs
  - uncalibrated experimental instruments are bad science, code and simulations should also be 
    calibrated!

# Getting help

- man pages

- list of good resources
  - https://dccn-hpc-wiki.readthedocs.io/en/latest/index.html
