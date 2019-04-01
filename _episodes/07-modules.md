---
layout: episode
title: "Environment modules"
teaching: 10
exercises: 5
questions:
  - "What are environment modules?"
  - "How can I use different versions of software packages?"
objectives:
  - "Learn to use modules on PDC systems"
keypoints:
  - "Environment modules modify your environment and give you access to executables, 
  libraries, etc."
  - "SNIC environment variables provide shortcuts to your AFS and Lustre directories."
---

On a HPC system, software is typically not loaded by default. If we want to use a software package, 
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

We will be using the `module` command. To see all available subcommands 
of `module`, type:
```bash
[{{ site.cluster }}]$ module help
```

To see available software modules, use `module avail`:
{% if site.cluster == "tegner" %}
```bash
[{{ site.cluster }}]$ module avail
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
{% elsif site.cluster=="beskow" %}
```bash
[{{ site.cluster }}]$ module avail
------------------------------- /pdc/vol/Modules -------------------------------
abaqus/2018                   mercurial/3.2.2
abinit/7.10.2                 midnightcommander/4.8.13
abinit/7.10.2_v2              mono/3.12.0
abinit/7.10.5                 multinest/3.10-gcc
abinit/8.10.2                 namd/2.10
aiida/epfl-0.5.0              namd/2.12
allinea-forge/18.1.1          nano/2.3.6
allinea-forge/7.0             nano/2.9.8
allinea-reports/18.1.1        nco/4.4.8
allinea-reports/7.0           nedit/5.5
allscale_runtime/d821c7d      nest/2.12.0-py27(default)
almost/2.1                    nest/2.2.2-py27
almost/2.1.0                  netcdf/4.1.1-intel-15.0
amber/12(default)             neuron/7.4-py27
amber/14.ambertools15         neuron/7.5-py36
amber/16.ambertools17         nwchem/6.5
anaconda/py27/2.1             nwchem/6.6
anaconda/py27/2.3             nwchem/6.8
...
```
{% endif %}

--- 

## Loading software

To load a software module, use `module load` (or `module add`). 
In this example we will use Jupyter Notebooks.

Initially, a Python environment containing Jupyter is not loaded. We can test this by 
using the `which` command. `which` looks for programs the same way that bash does, 
so we can use it to tell us where a particular piece of software is stored.

```bash
[{{ site.cluster }}]$ which jupyter-notebook

which: no jupyter-notebook in (/usr/local/bin:/usr/bin)
```

We can get access to the `jupyter-notebook` command by loading the 
module for the Anaconda Python distribution:

```bash
[{{ site.cluster }}]$ module load anaconda
[{{ site.cluster }}]$ which jupyter-notebook
{% if site.cluster == "tegner" %} /pdc/vol/anaconda/co7/5.0.1/py36/bin/jupyter-notebook
{%elsif site.cluster == "beskow" %} 
/pdc/vol/anaconda/5.3/py37/bin/jupyter-notebook {% endif %}
```

So what just happened?  
We first need to understand the `$PATH` environment variable, 
which controls where a UNIX system looks for software. 
- `$PATH` is a list of directories (separated by `:`) that 
  the OS searches through for a command before giving up and telling us it 
  can't find it. 
- When we ran `module load anaconda`, it added a few directories to the beginning of
  our `$PATH`. 

We can inspect `$PATH` using `echo`.
```bash
[{{ site.cluster }}]$ echo $PATH
{% if site.cluster == "tegner" %}
/pdc/vol/anaconda/co7/5.0.1/py36/bin:/pdc/vol/latex/20150811/bin:/pdc/vol/openmpi/3.0/gcc/7.2.0/bin:/pdc/vol/gcc/7.2.0/bin:/usr/local/bin:/usr/bin
{% elsif site.cluster == "beskow" %}
/pdc/vol/anaconda/5.3/py37/bin:/pdc/vol/slurm/utils/0.0/bin:/opt/pdc.kth.se/heimdal/1.5.2/bin:/opt/pdc.kth.se/heimdal/1.5.2/sbin:[...]:/opt/cray/bin {% endif %}
```
--- 
{% if site.cluster == "tegner" %}
## Dependencies

`module load` adds the requested software to your `$PATH`, but it also
loads required software dependencies. To see this,
let's use `module list` which shows all loaded software modules.

```bash
[{{ site.cluster }}]$ module list

Currently Loaded Modulefiles:
 1) gcc/7.2.0           3) latex/20150811
 2) openmpi/3.0-gcc-7.2 4) anaconda/py36/5.0.1
```
Clearly, the module system added the required dependencies automatically.  
What if we unload the Anaconda module?
```bash
[{{ site.cluster }}]$ module unload anaconda
[{{ site.cluster }}]$ module list

Currently Loaded Modulefiles:
```

`module unload` (or `module rm`) un-loads a module along with its dependencies.  
To unload all modules at once, we could also run `module purge`.

--- 
{% endif %}
## Inspecting a module
To inspect a particular module, use `module show`:
{% if site.cluster == "tegner" %}
```bash
[{{ site.cluster }}]$ module show anaconda/py36/5.0.1

-------------------------------------------------------------------
/pdc/modules/system/base/anaconda/py36/5.0.1:

        Anaconda V.5.0.1
        Python3.5 for scientific computing.

module load gcc/7.2.0 openmpi/3.0-gcc-7.2
module load latex/20150811
setenv ANACONDA_HOME	/pdc/vol/anaconda/co7/5.0.1/py36
prepend-path		PATH	/pdc/vol/anaconda/co7/5.0.1/py36/bin
-------------------------------------------------------------------
```
{% elsif site.cluster == "beskow" %}
```bash
[{{ site.cluster }}]$ module show gromacs/2018.3

-------------------------------------------------------------------
/pdc/vol/Modules/gromacs/2018.3:

module-whatis   GROMACS molecular dynamics code 2018.3
For advanced shell completion features source /pdc/vol/gromacs/2018.3/GMXRC

module     unload PrgEnv-cray
module     unload PrgEnv-intel
module     load PrgEnv-gnu
module     load cdt/17.10
setenv     GMXBIN /pdc/vol/gromacs/2018.3/bin
setenv     GMXLDLIB /pdc/vol/gromacs/2018.3/lib64
setenv     GMXMAN /pdc/vol/gromacs/2018.3/share/man
setenv     GMXDATA /pdc/vol/gromacs/2018.3/share
prepend-path   LD_LIBRARY_PATH /pdc/vol/gromacs/2018.3/lib64
prepend-path   PATH /pdc/vol/gromacs/2018.3/bin
prepend-path   MANPATH /pdc/vol/gromacs/2018.3/share/man
-------------------------------------------------------------------
```
{% endif %}
The `module show` command shows us:

- Where this module is installed.
- What other modules are loaded (**dependencies**).
- What environment variables are set.
- What paths are added to `$PATH`.

--- 

## Software versioning

We've learned how to load and unload software packages. But how do we load particular 
versions of the software we want?
We saw above that `module avail` lists multiple versions of some software packages.
It's possible to list all versions of a particular package, for example Anaconda:

{% if site.cluster == "tegner" %}
```bash
[{{ site.cluster }}]$ module avail anaconda
------------ /pdc/modules/system/base ------------
anaconda/py27/2.1          anaconda/py27/5.0.1
anaconda/py27/2.4.1        anaconda/py35/4.2.0
anaconda/py27/4.0.0-serial anaconda/py36/5.0.1
anaconda/py27/4.2.0
```
{% elsif site.cluster == "beskow" %}
```bash
------------------------------- /pdc/vol/Modules -------------------------------
anaconda/py27/2.1 anaconda/py27/4.3 anaconda/py27/5.3
anaconda/py27/2.3 anaconda/py27/4.4 anaconda/py36/4.3
anaconda/py27/2.5 anaconda/py27/5.1 anaconda/py37/5.3
```
{% endif %}
We can see that multiple versions of the Anaconda distribution are 
installed on {{ site.Cluster }}, but when we did `module load anaconda` one particular (default)
version was selected.

> ## Switching between software versions
> 
> One can change versions of software packages using the `module swap` command.  
> First load the default Anaconda module again and check its Python version:
> ```bash
> [{{ site.cluster }}]$ module add anaconda
> [{{ site.cluster }}]$ python --version
> Python 3.6.3 :: Anaconda custom (64-bit)
> ```
> - **Now swap the loaded Anaconda module for the Python 2.7 version, and check 
> that this successfully changes the version of Python.**
{: .challenge}

> ## How does your shell know which modules are loaded?
> 
> Clearly, something is stored in our environment since `module list` "knows"
> which modules we have loaded. Let's try to figure it out by using the `printenv` 
> command which prints all the current environment (i.e. all environment variables):
> ```bash
> [{{ site.cluster }}]$ printenv
> ```
> - **Can you see which environment variable stores the information about the 
> loaded modules?**
> - **Try "[piping](https://en.wikipedia.org/wiki/Pipeline_(Unix))" (chain together)
> the `printenv` command to `grep` and search for "MODULE"**
{: .challenge}

--- 

## SNIC environment variables

To facilitate portability of batch scripts and user workflows between different 
supercomputers in Sweden, PDC along with the other SNIC centers have defined 
a few common environment variables. On Beskow these variables are loaded 
by default after login, while on {{ site.Cluster }} you need to load a module:
```bash
[{{ site.cluster }}]$ module load snic-env
```

The following table shows which environment variables are defined and what they 
mean at PDC.

| Env. variable name | Meaning |
| ------------------ | ------- |
| `SNIC_SITE`        | pdc     |
| `SNIC_RESOURCE`  | tegner or beskow |
| `SNIC_BACKUP`  | `/afs/pdc.kth.se/home/<initial>/<username>` |
| `SNIC_NOBACKUP` | `/cfs/klemming/nobackup/<initial>/<username>` | 
| `SNIC_TMP`  | `/cfs/klemming/scratch/<initial>/<username>` |

