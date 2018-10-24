---
layout: episode
title: "Tips and tricks for efficient HPC usage"
teaching: 20
exercises: 0
questions:
  - "What other nice features does SLURM offer?"
  - "What useful methods can help me use HPC more efficiently?"
objectives:
  - "Learn about job arrays and job dependencies"
  - "Pick up some good HPC practices"
keypoints:
  - "Job arrays and dependencies are SLURM features that sometimes come in handy"
  - "Good practices like organized project directory structure and documented automated workflows help also in HPC work"
---

# Efficient SLURM usage

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


### Job dependencies

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

> ## Submit a small job array
>
> Edit the example job array script such that it
> - asks for a short time (e.g. `#SBATCH -t 00:05:00`)
> - requests a small job array (e.g. `#SBATCH -a 0-4`)
> - runs a small calculation (e.g. `srun -n 1 python -c "print (${SLURM_ARRAY_TASK_ID}**2)"`)
>
> Create a series of folders (`data0`, `data1`, `data2`, `data3`, `data4`) and submit the job.
{: .challenge}

> ## Make use of job dependency
>
> 1. Submit a job that sleeps for 30 seconds and then writes a message to a file.
>    - `sleep 30 && echo "hello" > file.txt`
> 2. Submit a second job with ``--dependency=afterok:<jobID>`` to print the message in the file. 
>    - `cat file.txt`
{: .challenge}

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

> Note that `sacctmgr` only works on Beskow.

### Being friends with SLURM
  - Avoid wide short jobs (massively parallel but very short runtime).
  - Avoid massive output to STDOUT.
  - Try to provide a good estimate of the job duration before submitting.

### Being friends with the file system
  - Avoid creating very many small files (it slows down Lustre).
  - Try instead to write to few large files.
  - Always use the Lustre file system, not AFS (AFS isn't even available on Beskow compute nodes).

---

# HPC best practices

### [Ahmdahl's law](https://en.wikipedia.org/wiki/Amdahl%27s_law)

  Speedup `S_P` on `P` processors is restricted by (f is the sequential fraction)  
  <img src="../img/eq_ahmdahl.png" alt="scheduling" width="200" align="middle"> 


  > If a program needs 20 hours using a single processor core, and a particular part of the 
  > program which takes one hour to execute cannot be parallelized, while the remaining 
  > 19 hours (p = 0.95) of execution time can be parallelized, then regardless of how many 
  > processors are devoted to a parallelized execution of this program, the minimum execution time 
  > cannot be less than that critical one hour. Hence, the theoretical speedup is limited to at most 
  > 20 times (1/(1 − p) = 20). For this reason, parallel computing with many processors is useful 
  > only for highly parallelizable programs.

#### Strong scaling
- Defined as how the solution time varies with the number of processors for a *fixed total problem size*.
- Linear **strong** scaling if the speedup (work units completed per unit time) is equal to the number of processing elements used.
- Harder to achieve good strong-scaling at larger process counts since communication overhead typically increases with the number of processes used.

#### Weak scaling
- Defined as how the solution time varies with the number of processors for a *fixed problem size per processor*.
- Linear **weak** scaling if the run time stays constant while the workload is increased in direct proportion to the number of processors. 

### Parallel scaling performance measurements

 - It is almost always worthwhile to test how your job scales with number of processes.
 - Use a problem state or configuration that best matches your intended production runs.
 - Scaling should be measured based on the overall performance of the application.
 - Running a test:
    1. Run a series of jobs with varying number of nodes and threads
       - Ranging from 1 to the number of processing elements per node for threaded jobs.
       - Ranging from 1 to the total number of processes requested for MPI.
    2. Time how long it takes to complete (using wallclock time or equivalent).
    3. Measure multiple independent runs per job size.
    4. Measure using multiple computer systems if possible/relevant.
    5. Find the *sweet spot* where the scaling is still good and job finishes in reasonable time.

<img src="../img/StrongScalingPlot1.jpg" alt="scheduling" width="400" align="middle"> 

> adapted from [SHARCNET documentation](https://www.sharcnet.ca/help/index.php/Measuring_Parallel_Scaling_Performance)

 - **Play around with job parameters/algorithms/settings** and see if you can improve the parallel efficiency.

> ## Measure the scaling of a small program
>
> 1. Copy the C code for computing &pi; from 
> [here](https://pdc-web-01.csc.kth.se/files/support/files/scale.c)
> and paste to a file named `scale.c`.
>
> 2. Submit a job with a script containing the following lines
>
>    ```
>    gcc scale.c -fopenmp -o scale
>    
>    echo Strong scaling test
>    ./scale  1  10000000
>    ./scale  2  10000000
>    ./scale  4  10000000
>    ./scale  8  10000000
>    ./scale 16  10000000
>    ./scale 24  10000000
>    ./scale 48  10000000
>    
>    echo Weak scaling test
>    ./scale  1  10000000
>    ./scale  2  20000000
>    ./scale  4  40000000
>    ./scale  8  80000000
>    ./scale 16 160000000
>    ./scale 24 240000000
>    ./scale 48 480000000
>    ```
>
>    Here, `./scale` is followed by two numbers. The first one is the number of threads,
>    and the second one is the number of integration points.
>
> 3. Plot your results.
{: .challenge}

### Benchmark before you optimize

- Before attempting to optimize your own code, you should profile it!
- Typically, most of the runtime is spent in a few functions/subroutines, focus your optimization efforts on those.
- Excellent profiling tools exist, use them! 

### Calibrate your jobs
- Uncalibrated experimental procedures are considered bad science.
- Scientific code and calculations/simulations should also be calibrated!
- Use the code on a well-known test case where you know what the correct answer should be.

### Directory structure for projects

- It is good to keep all files associated with a project in a single folder.
- Different projects should have separate folders.
- Use consistent and informative directory structure.
- Add a README file to describe the project and instructions on reproducing the results.
- But your mileage may vary, it's not a one-size-fits-all.

A project directory can look something like this:
```bash
project_name/
|-- data/                        contains input data files of the project
|   |-- README.md                describes where input data came from
|   |-- sub-folder/              may contain subdirectories as well
|   |-- ...
|-- processed_data/              will contain intermediate files from the analysis
|-- manuscript/                  will contain the manuscript describing the results
|-- results/                     will contain the results of the analysis (including tables and figures)
|-- source/                      will contain all code
```

### Documenting and automating your workflow

Reproducible workflows enable you to figure out precisely what data and what code were used to generate a result:

 - Provide a historical record (provenance) of data, its origins and causal relationships.
 - Can be used to ensure quality of data based on ancestral data, or find sources of errors.
 - Allow automated recreation of data.
 - Implemented in many [workflow management tools](https://github.com/common-workflow-language/common-workflow-language/wiki/Existing-Workflow-systems).

#### Using [Snakemake](https://snakemake.readthedocs.io/en/stable/index.html) to automate workflow

- Workflows defined in Python scripts extended by declarative code to define rules 
  - anything that can be done in Python can be done with Snakemake
- Rules work much like in GNU Make
- Possible to define isolated software environments per rule
- Also possible to run workflows in Docker or Singularity containers
- Workflows can be pushed out to run on a cluster or in the cloud without modifications to scale up


# Getting help

- `man` pages
  - `$ man <some-command>`
- List of good resources:
  - [An HPC wiki for another HPC center containing tutorials](https://dccn-hpc-wiki.readthedocs.io/en/latest/index.html).
  - [HPC Carpentry](https://hpc-carpentry.github.io/).
  - [SNIC knowledge base with a list of training events](http://docs.snic.se/wiki/Training).
  - [List of software installed on SNIC centres along with links to documentation (poorly maintained)](http://docs.snic.se/wiki/Software).
- Send in a support request.
  - SNIC (and other national HPC providers) employs support staff and application experts who can help with basic and advanced problems.

