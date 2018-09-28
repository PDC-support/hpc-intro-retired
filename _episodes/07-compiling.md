---
layout: episode
title: "Compilers"
teaching: 20
exercises: 0
questions:
  - "How do I compile serial, parallel and GPU code on PDC systems?"
objectives:
  - "Get familiar with compiler environments and compiler wrappers"
keypoints:
  - "K1"
  - "K2"
---


# Building software at PDC

Compilers can be configured in a variety of ways on clusters.
Tegner is configured in the typical manner, while Beskow (a Cray XC40 system) 
is a little different.

## Building software on Tegner

To compile code on Tegner, you need to load compiler modules and explicitly link 
to any libraries that are needed (MPI, BLAS/LAPACK, FFTW, ...). 

The following table shows which compiler suites are available and the corresponding 
module names and compiler commands.

##### Compilers available on Tegner {#compilers}

|Compiler | Module name                 | Compiler commands               |
| ------- | --------------------------- | ------------------------------- |
|GNU      | $ module load gcc           | `gcc`, `g++`, `gfortran`        |
|OpenMPI  | $ module load PrgEnv-intel  | `mpicc`, `mpicxx`, `mpif90`     |
|Intel    | $ module load PrgEnv-gnu    | `icc`, `icpc`, `ifort`          |
|Intel-MPI| $ module load PrgEnv-gnu    | `mpiicc`, `mpiicpc`, `mpiifort` |
|CUDA     | $ module load cuda          | `nvcc`                          | 

Before proceeding, let's clean our module environment:
```bash
[tegner]$ module purge
```

Compilers also come in different versions, and it can be important to keep track of 
them since software might not compile or run correctly with the wrong version.   
Let's have a look at the GNU `gcc` compiler
(GCC is an extremely widely used C/C++/Fortran compiler):

```bash
[tegner]$ module avail gcc

------------ /pdc/modules/system/base ------------
gcc/4.8.4 gcc/4.9.2 gcc/5.1   gcc/5.3.0 gcc/6.2.0 gcc/7.2.0
```

We will now compile a simple C program using GCC version 7.2:
```bash
[tegner]$ module load gcc/7.2.0
[tegner]$ gcc --version

gcc (GCC) 7.2.0
```

Let's copy-paste this simple C program below to a file called 
`hello_world.c` in a subdirectory `hello` in our Lustre nobackup directory:
```c
#include <stdio.h>

int main(int argc, char** argv) {
  printf("Hello world!\n");
  }
```

We then compile it with `gcc` and execute it:
```
[tegner]$ gcc -o hello hello_world.c
[tegner]$ ./hello

Hello World!
```

### MPI-parallelized code 

Of course, clusters are mostly used for running calculations in parallel, 
and for that purpose it's not enough to only use GCC compilers. 
We also need a 
[**MPI libary**](https://en.wikipedia.org/wiki/Message_Passing_Interface).

> ## Message Passing Interface (MPI)
>
> MPI is a standardized and portable message-passing standard. It defines the 
> syntax and semantics of a core of library routines for
> writing portable message-passing programs in C, C++, and Fortran, 
> supporting both point-to-point and collective communication.
> Bindings are also available for many other languages. Official implementations 
> of MPI include [MPICH](https://en.wikipedia.org/wiki/MPICH) and 
> [Open MPI](https://en.wikipedia.org/wiki/Open_MPI), which are typically available 
> as modules on supercomputers.
{: .callout}

Here is an example of a simple MPI program:
```c
#include <mpi.h>
#include <stdio.h>

int main(int argc, char** argv) {
  int world_size,world_rank;
      
  // Initialize the MPI environment
  MPI_Init(NULL, NULL);
  // Get the number of processes
  MPI_Comm_size(MPI_COMM_WORLD, &world_size);
  // Get the rank of the process
  MPI_Comm_rank(MPI_COMM_WORLD, &world_rank);
  // Print off a hello world message
  printf("Hello world from rank %d out of %d process\n", world_rank, world_size);
  // Finalize the MPI environment.
  MPI_Finalize();
  }
```

To compile this code, we need to load a module for a MPI library which is 
consistent with our loaded compiler (GCC 7.2.0). Let's have a look at the 
available Open MPI modules:
```bash
[tegner]$ module avail openmpi

------------ /pdc/modules/system/base ------------
openmpi/1.8-gcc-4.8   openmpi/1.10-gcc-5.1  openmpi/3.0-gcc-6.2
openmpi/1.8-gcc-4.9   openmpi/1.10-gcc-java openmpi/3.0-gcc-7.2
openmpi/1.8-gcc-5.1   openmpi/2.0-gcc-6.2
```

We see that the `openmpi/3.0-gcc-7.2` module is what we need.  
Compiling our MPI-parallelized hello-world example is left as an exercise.

> ## Compiling code with MPI
> 
> Copy-paste the parallel Hello World code above to a file in your Lustre directory, 
> for example: `/cfs/klemming/nobackup/<initial>/<username>/hello/hello_world_mpi.c`.  
> - **Load the openmpi module which matches the gcc/7.2.0 compiler**.
> - **Find the name of the parallel C GNU compiler from the [table above](#compilers), and compile `hello_world_mpi.c`.**
> - **What do you think will happen if you run the generated executable on the login node with `./hello_mpi`? Try it! (don't worry, you won't break the login node).**
{: .challenge}

In the next section we will go into details about how we actually 
submit parallel jobs to the *SLURM scheduler*.

### OpenMP-parallelized code 

Code can be parallelized in other ways than with message passing. 
[OpenMP](https://en.wikipedia.org/wiki/OpenMP) is another common way to parallelize 
code which is fundamentally different than MPI.

> ## Open Multi-Processing (OpenMP)
> 
> OpenMP is an application programming interface (API) that supports multi-platform 
> shared memory multiprocessing programming in C, C++, and Fortran,
> consisting of a set of compiler directives, library routines, and environment 
> variables that influence run-time behavior.
> OpenMP implements multithreading, a method of parallelizing whereby a master 
> thread forks a specified number of slave threads and the system divides a 
> task among them.
{: .callout}

Here is an example of a simple OpenMP program:
```c
#include <omp.h>
#include <stdio.h>
#include <stdlib.h>

int main (int argc, char *argv[]) {
  int nthreads, tid;

  /* Fork a team of threads giving them their own copies of variables */
  #pragma omp parallel private(nthreads, tid)
    {
    /* Obtain thread number */
    tid = omp_get_thread_num();
    printf("Hello World from thread = %d\n", tid);
    /* Only master thread does this */
    if (tid == 0) {
      nthreads = omp_get_num_threads();
      printf("Number of threads = %d\n", nthreads);
      }
    }  /* All threads join master thread and disband */
  }
```

To compile OpenMP-parallelized code we do not need to load additional 
modules since the OpenMP API is implemented by the compiler. It is enough to 
import the header file in the source code and provide a specific flag during 
compilation.

In order to run the OpenMP-parallelized code with a given number of threads, 
one typically sets the environment variable `OMP_NUM_THREADS` to the number 
of desired threads.

> ## Compiling OpenMP code 
> 
> Copy-paste the OpenMP Hello World code above to a file in your Lustre directory, 
> for example: 
> `/cfs/klemming/nobackup/<initial>/<username>/hello/hello_world_omp.c`.  
> - **Compile the code in the same way as you did in the serial case above, except 
>   add the flag `-fopenmp` to the compilation command.**
> - **What happens now if you run the executable on the login node?**
> - **Try setting OMP_NUM_THREADS to 4, and rerun.**
{: .challenge}


### Building software on Beskow (Cray XC40)

Cray systems work a little differently from "normal" clusters. 

WRITEME...

|Compiler| Module name                 | Compiler commands |
| ------ | --------------------------- | ----------------- |
|Cray    | $ module load PrgEnv-cray   | `cc`, `CC`, `ftn` |
|Intel   | $ module load PrgEnv-intel  | `cc`, `CC`, `ftn` |
|GNU     | $ module load PrgEnv-gnu    | `cc`, `CC`, `ftn` |

