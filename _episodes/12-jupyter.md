---
layout: episode
title: "(Optional) Jupyter Notebooks"
teaching: 10
exercises: 10
questions:
  - "I love Jupyter notebooks, can I use them at PDC?"
objectives:
  - "Learn how to use Jupyter notebooks in an HPC environment"
keypoints:
  - "Jupyter offers an alternative way to interact with HPC systems"
---

# [Jupyter notebooks in HPC](https://jupyter.readthedocs.io/en/latest/)

Basic HPC usage hasn't changed much in decades - we still log in to 
clusters using secure shell, use terminals to execute UNIX commands, 
submit batch jobs to the job scheduler, etc. But there are alternative 
ways to interact with HPC systems.

PDC [offers the possibility of running Jupyter on PDC resources](https://www.kth.se/blogs/pdc/2019/01/using-jupyter-notebooks-to-manage-slurm-jobs/), and 
connecting to the running server from your own browser. 
In this episode we will learn how to use Jupyter Notebooks
to manage SLURM jobs from your browser!

---

## Configuring Jupyter on PDC

The following configuration steps are required before using Jupyter on 
PDC systems. Further details are provided on [the software documentation 
page for Jupyter](https://www.pdc.kth.se/software/software/Jupyter-Notebooks/index.html).

 1. Generate a Jupyter configuration file under ``$HOME/.jupyter``
 2. Set a strong password for Jupyter 
 3. Set up a self-signed certificate with ``openssl``


### Generating a Jupyter configuration file

Log in to Tegner and load an Anaconda module:

```bash
$ ssh <username>@tegner.pdc.kth.se
$ module load anaconda/py36/5.0.1  # or anaconda/py27/5.0.1 for Python2.7
```

Create a configuration file by

```bash
$ jupyter notebook --generate-config
```

- Creates the Jupyter folder `$HOME/.jupyter` and the 
  notebook config file ``jupyter_notebook_config.py``.
- Contains many lines with possible configuration options.


### Setting a Jupyter password

For security reasons you should choose a strong Jupyter password.
The main method to set the password is by using the command

```bash
  $ jupyter notebook password
```

- Saved in hashed form in a second config file:
  `$HOME/.jupyter/jupyter_notebook_config.json`.


### Setting up a self-signed certificate

We now use SSL with a web certificate to enable connecting to the 
notebook via https, so that your hashed password is not sent unencrypted:

```bash
$ cd $HOME/Private
$ openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout mykey.key -out mycert.pem
```

- Generates a keyfile (``mykey.key``) and a certificate file (``mycert.pem``).
- We now set the following two flags in our config file `jupyter_notebook_config.py`
  ```bash
  c.NotebookApp.certfile = u'/afs/pdc.kth.se/home/<initial>/<username>/Private/mycert.pem'
  c.NotebookApp.keyfile = u'/afs/pdc.kth.se/home/<initial>/<username>/Private/mykey.key'
  ```
---

## Running Jupyter on the login node

Running Jupyter on a login node allows us to:
- Manage Slurm jobs from within a notebook.
- Do light-weight pre- and post-processing work.

> ## Good manners on the login node
> 
> Remember that the login node is a shared resource, and all
> heavy calculations should be done on a compute node.
{: .callout}

We log in to Tegner and launch Jupyter:
```bash
$ ssh username@tegner.pdc.kth.se
$ module load anaconda/py36/5.0.1  # or anaconda/py27/5.0.1 for Python2.7
$ jupyter-notebook --no-browser
```

The output will show a line similar to:

```bash
[I 14:16:11.737 NotebookApp] The Jupyter Notebook is running at:
[I 14:16:11.737 NotebookApp] https://localhost:8888/
```

We next set up an ssh tunnel so that connections to a port on your local 
machine are forwarded to a port on Tegner.
Open a **new terminal** on your machine, and type:
```bash
$ ssh -N -L 8888:localhost:8888 username@tegner.pdc.kth.se 
```

- `-N` tells ssh that no remote commands will be executed.
- `-L` lists the port forwarding configuration. 
- To let ssh run in the background one can add the -f option.

Now copy the https link over to your browser. The browser will likely 
complain that your certificate is not secure or not recognized (since it's 
self-signed), but tell it to proceed anyways. After entering your Jupyter 
password you can start working with notebooks on PDC!

### Managing SLURM jobs from Jupyter

With the help of a [simple package](https://github.com/NERSC/slurm-magic) 
installed into Python environments on 
Beskow and Tegner, it is possible to submit and monitor 
SLURM batch jobs using SLURM magic commands.
This enables you to do light-weight pre- and postprocessing work of 
SLURM jobs as well as interactive on-the-fly analysis during a running job, 
from within a Jupyter notebook.

To load the SLURM-magics package, enter the following in a code cell:
```python
%load_ext slurm_magic
```

You can now see the new SLURM magic commands with the `%lsmagic` command:

| Magic command | Effect |
| ------------- | ------ |
| %salloc | Obtain a Slurm job allocation (a set of nodes), execute a command, and then release the allocation when the command is finished. |
| %sbatch | Submit a batch script to Slurm.|
| %scancel | Used to signal jobs or job steps that are under the control of Slurm. |
| %sinfo | View information about Slurm nodes and partitions. |
| %squeue | View information about jobs located in the Slurm scheduling queue. |
| %srun | Run parallel jobs. |
| %%sbatch | Submit a batch script to Slurm (whole cell). |



> ## Managing SLURM jobs from Jupyter
>
>
> - **Log in to Tegner, and start a Jupyter server. Connect to it from your 
>   local browser.**
> - **Create a new notebook with the default kernel.**
> - **In a code cell, import the slurm_magic package.** 
> - **Type** `%lsmagic` **to see the new SLURM-related magics that have appeared**
> - **In new code cells, test the commands** `%sinfo` **and** `%squeue`
> - **Now copy-paste your job script from the 
>   [previous section]({{ site.baseurl }}/10-scheduling) into a code cell. You can also use the** `%load <filename>` **command to load contents of a file into a cell.**
> - **Submit the job using the relevant cell magic command, and monitor the job with** `%squeue -u <username>`. 
{: .challenge}

### When to use Jupyter for HPC work?

How does the "Jupyter way" compare to the usual approach to pre-processing, running and post-processing jobs? 

- There is some amount of overhead involved (security setup and config, extra ssh command for ssh tunnel, copy-paste a URL and enter Jupyter password).
- Workflows can be automated by creating notebooks 
  containing any number of pre-processing steps, batch scripts, monitoring 
  commands and post-processing steps to be performed during and after job 
  execution. 
- Can make HPC workflows more reproducible and shareable. 
- Can help, e.g., new PhD students get started.
- Suitability depends on specifics of the HPC simulation codes and workflows 
  involved. 
- Maybe you like the user interface of Jupyter, but maybe you don't!



