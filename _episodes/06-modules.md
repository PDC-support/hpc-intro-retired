---
layout: episode
title: "Software modules and compilers"
teaching: 20
exercises: 0
questions:
  - "What are software modules?"
  - "How do I compile code on PDC systems?"
objectives:
  - "Learn to use modules on PDC systems"
  - "Learn to compile code with different compilers"
keypoints:
  - "K1"
  - "K2"
---

On a HPC system, no software is loaded by default. If we want to use a software package, 
we will need to "load" it ourselves.

Before we start using individual software packages, we should understand the reasoning 
behind this approach. The two biggest factors are *software incompatibilities* and 
*versioning*.

- *Software incompatibility* is a major headache for programmers. Two famous 
  examples are:
  - Python 3 provides a python command that conflicts with that provided by Python 2. 
  - Software compiled against a newer version of the C libraries and then used when they are not present will result in a nasty `'GLIBCXX_3.4.20' not found` error.
- *Software versioning* is another common issue:
  - A team might depend on a certain package version for their research project - if the software version was updated, it might affect their results. 
  - Having access to multiple software versions allow researchers to prevent software versioning issues from affecting their results.

# Environment modules

Environment modules are the solution to these problems. A module is a self-contained software package - it contains all of the files required to run a software package and loads required dependencies.

To see available software modules, use `module avail`:
```bash
[tegner]$ module avail
------------ /pdc/modules/system/base ------------
abaqus/6.12
abaqus/6.13
abaqus/6.14
abaqus/6.14-6
abaqus/2017
abaqus/2018
admin
afsws
allinea-forge/6.1
allinea-forge/7.0
allinea-forge/18.1.1
allinea-reports/6.1
allinea-reports/7.0
allinea-reports/18.1.1
allpaths-lg/52488
allscale_compiler/fce3de6
amber/14
amber/16
amber/16.at17
anaconda/py27/2.1
...
```

## Loading software

To load a software module, use `module load` (or `module add`). 
In this example we will use Jupyter Notebooks.

Initially, a Python environment containing Jupyter is not loaded. We can test this by 
using the `which` command. `which` looks for programs the same way that bash does, 
so we can use it to tell us where a particular piece of software is stored.

```bash
[tegner]$ which jupyter-notebook

which: no jupyter-notebook in (/usr/local/bin:/usr/bin)
```

We can get access to the `jupyter-notebook` command by loading the 
module for the Anaconda Python distribution:

```bash
[tegner]$ module load anaconda
[tegner]$ which jupyter-notebook

/pdc/vol/anaconda/co7/5.0.1/py36/bin/jupyter-notebook
```

So what just happened?

- We first need to understand the `$PATH` environment variable, 
  which controls where a UNIX system looks for software. 
- `$PATH` is a list of directories (separated by `:`) that 
  the OS searches through for a command before giving up and telling us it 
  can't find it. 
- When we ran `module load anaconda`, it added a few directories to the beginning of
  our `$PATH`. 

We can inspect `$PATH` using `echo`.
```bash
[tegner]$ echo $PATH

/pdc/vol/anaconda/co7/5.0.1/py36/bin:/pdc/vol/latex/20150811/bin:/pdc/vol/openmpi/3.0/gcc/7.2.0/bin:/pdc/vol/gcc/7.2.0/bin:/usr/local/bin:/usr/bin
```

## Dependencies

`module load` adds the requested software to your `$PATH`, but it also
loads required software dependencies. To see this,
let's use `module list` which shows all loaded software modules.

```bash
[tegner]$ module list

Currently Loaded Modulefiles:
 1) gcc/7.2.0           3) latex/20150811
 2) openmpi/3.0-gcc-7.2 4) anaconda/py36/5.0.1
```

What if we unload the Anaconda module?
```bash
[tegner]$ module unload anaconda
[tegner]$ module list

Currently Loaded Modulefiles:
```

`module unload` (or `module rm`) un-loads a module along with its dependencies.
If we wanted to unload everything at once, we could run `module purge`.

## Software versioning

We've learned how to load and unload software packages. But how do we load particular 
versions of the software we want?
We saw above that `module avail` lists multiple versions of some software packages.
It's possible to list all versions of a particular package, for example Anaconda:

```bash
[tegner]$ module avail anaconda

------------ /pdc/modules/system/base ------------
anaconda/py27/2.1          anaconda/py27/5.0.1
anaconda/py27/2.4.1        anaconda/py35/4.2.0
anaconda/py27/4.0.0-serial anaconda/py36/5.0.1
anaconda/py27/4.2.0
```

We can see that multiple versions of the Anaconda distribution are 
installed on Tegner, but when we did `module load anaconda` one particular (default)
version was selected.

To swap one module for another, you can use the `module swap` command. For example,
to swap to the Python 2 Anaconda distribution, type:
```bash
[tegner]$ module swap anaconda/py36/5.0.1 anaconda/py27/5.0.1
```

# Building software at PDC


