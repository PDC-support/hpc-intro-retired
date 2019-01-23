---
layout: episode
title: "Scaling tests for efficient HPC usage"
teaching: 20
exercises: 10
questions:
  - "How can I maximize the efficiency of my HPC jobs?"
objectives:
  - "Learn about strong and weak scaling"
  - "Learn how to perform scaling tests"
keypoints:
  - "You should always perform parallel scaling measurements of your jobs 
  before submitting jobs requiring extensive resources"
---


# Parallel scaling 

### [Ahmdahl's law](https://en.wikipedia.org/wiki/Amdahl%27s_law)

  Theoretical speedup is restricted by

  <img src="../img/eq_ahmdahl.png" alt="scheduling" width="200" align="middle"> 

  where _p_ is the proportion of execution time that is subject to parallelization, and _s_
  is the speedup of that part.

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
>    
>    echo Weak scaling test
>    ./scale  1  10000000
>    ./scale  2  20000000
>    ./scale  4  40000000
>    ./scale  8  80000000
>    ./scale 16 160000000
>    ./scale 24 240000000
>    ```
>
>    Here, `./scale` is followed by two numbers. The first one is the number of threads,
>    and the second one is the number of integration points.
>
> 3. Plot your results.
{: .challenge}

### Benchmark before you optimize

  > "We should forget about small efficiencies, say about 97% of the time:
  > premature optimization is the root of all evil." -- Donald Knuth

- Before attempting to optimize your own code, you should profile it!
- Typically, most of the runtime is spent in a few functions/subroutines, focus your optimization efforts on those.
- Excellent profiling tools (e.g. **ARM Forge**) exist, use them! 


