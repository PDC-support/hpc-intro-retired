---
layout: episode
title: "Compilers"
teaching: 20
exercises: 0
questions:
  - "How do I compile serial, parallel and GPU code on PDC systems?"
objectives:
  - "Learn to compile code with different compilers"
keypoints:
  - "K1"
  - "K2"
---


# Building software at PDC

Compiler environments can be configured in a variety of ways on clusters.
Tegner is configured in the typical manner, while Beskow (a Cray XC40) 
is a little different.

### Building software on Tegner

##### Compiler available on Tegner {#compilers}

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
[tegner]$ gcc -o hello.x hello_world.c
[tegner]$ ./hello.x

Hello World!
```
It works!  

Of course, clusters are mostly used for running calculations in parallel, 
and for that purpose it's not enough to only use GCC compilers. 
We also need a 
[**MPI libary**](https://en.wikipedia.org/wiki/Message_Passing_Interface).

> ## Message Passing Interface (MPI)
>
> MPI is a standardized and portable message-passing standard. It defines the 
> syntax and semantics of a core of library routines for
> writing portable message-passing programs in C, C++, and Fortran.
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

> ## Compiling parallel codes
> 
> Copy-paste the parallel Hello World code above to a file in your Lustre directory, 
> for example: `/cfs/klemming/nobackup/<initial>/<username>/hello/hello_world_mpi.c`.  
> - **Load the openmpi module which matches the gcc/7.2.0 compiler**.
> - **Find the name of the parallel C GNU compiler from the [table above](#compilers), and compile `hello_world_mpi.c`.**
> - **What do you think will happen if you run the generated executable on the login node with `./hello_mpi.x`? Try it! (don't worry, you won't break the login node).**
{: .challenge}


In the next section we will go into details about how to submit parallel jobs 
on Tegner and Beskow.

### Building software on Beskow (Cray XC40)

Cray systems work a little differently from "normal" clusters. 

WRITEME...

|Compiler| Module name                 | Compiler commands |
| ------ | --------------------------- | ----------------- |
|Cray    | $ module load PrgEnv-cray   | `cc`, `CC`, `ftn` |
|Intel   | $ module load PrgEnv-intel  | `cc`, `CC`, `ftn` |
|GNU     | $ module load PrgEnv-gnu    | `cc`, `CC`, `ftn` |

