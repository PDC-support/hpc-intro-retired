---
layout: episode
title: "(Optional) HPC accessibility"
teaching: 10
exercises: 20
questions:
  - "Are there alternative ways to interact with HPC systems?"
  - "I love Jupyter Notebooks, can I use them at PDC?"
objectives:
  - "Learn how to use Jupyter notebooks in an HPC environment"
keypoints:
  - "MidnightCommander, GNU screen, tmux and Jupyter offer alternative ways 
    to interact with HPC systems"
---

# HPC accessibility

Basic HPC usage hasn't changed much in decades - we still log in to 
clusters using secure shell, use terminals to execute UNIX commands, 
submit batch jobs to the job scheduler, etc. But there are alternative 
ways to interact with HPC systems.

In this episode we will learn how to use Jupyter Notebooks
to manage SLURM jobs from your browser!

---

## [GNU Screen](https://www.gnu.org/software/screen/) 

`screen` is a full-screen window manager that multiplexes a physical 
terminal between several processes, typically interactive shells
When `screen` is called, it creates a single window with a shell in it 
and then gets out of your way so that you can use the program as you 
normally would.  
With `screen`, you can:
 - Manage persistent terminal sessions, which survive if connection crashes 
   or if you need to abrubtly abandon your work.
 - Save screen processes when logging out and resume where you left off, 
 - Have multiple windows connected to the same terminal session.
 - Copy and paste (including block-copy) between different 
   screens without mouse.

**Screen commands short list**

| command | Explanation |
| ------- | ----------- |
| screen | start a session | 
| screen -S [name] | start a named session |
| screen -ls | list running sessions |
| screen -x | attach to a running session |
| screen -r [name] | attach to session with [name] |

The `screen` commands inside a screen session are prefixed by an 
escape key, by default `Ctrl-a` (`C-a`): 

| `screen` command | Explanation |
| ---------------- | ----------- |
| C-a d | detach from session |
| exit (C-d) | kill the window/session |
| C-a c | create new window |
| C-a C-a | change to last-visited window |
| C-a <number> | change to window by number |
| C-a " | see window list (and select) |

> ## Trying out screen
>
> The `screen` window manager can be really useful to preserve the state
> of a terminal session. Try the following steps:
> - Log in to Tegner, load a couple of random modules and export some 
>   random environment variables (e.g., `export foo=bar`).
> - Imagine that you need to catch the bus, but that you don't want to lose 
>   your environment. Start a screen session with `screen`.
> - Detach from your screen session with `Ctrl-a d`, and log out from Tegner.
> - Imagine that a day has passed, and log back in to Tegner.
> - List all screen sessions. Reattach to "yesterday's" screen session, 
>   and check that the environment is the same as when you left it.
> - If you have time, try playing around with screen some more, 
>   using the commands listed above or by finding inspiration from 
>   [online](https://www.rackaid.com/blog/linux-screen-tutorial-and-how-to/)
>   [tutorials](https://kb.iu.edu/d/acuy).
{: .challenge}

---

### [tmux](https://github.com/tmux/tmux/wiki) 
- [tmux](https://github.com/tmux/tmux/wiki) is similar to screen 
   with some minor pros and cons, try it out to see which one you like better!

## [Jupyter notebooks](https://jupyter.readthedocs.io/en/latest/)

Knowing Linux shell scripting makes life simpler when working in 
an HPC environment, but is it absolutely essential? Are there any other 
more high-level user interfaces out there?

PDC [offers the possibility of running Jupyter on PDC resources](https://www.kth.se/blogs/pdc/2019/01/using-jupyter-notebooks-to-manage-slurm-jobs/), and 
connecting to the running server from your own browser. 

---

### Configuring Jupyter on PDC

The following configuration steps are required before using Jupyter on 
PDC systems. Further details are provided on [the software documentation 
page for Jupyter](https://www.pdc.kth.se/software/software/Jupyter-Notebooks/index.html).

 1. Generate a Jupyter configuration file under ``$HOME/.jupyter``
 2. Set a strong password for Jupyter 
 3. Set up a self-signed certificate with ``openssl``


#### Generating a Jupyter configuration file

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


#### Setting a Jupyter password

For security reasons you should choose a strong Jupyter password.
The main method to set the password is by using the command

```bash
  $ jupyter notebook password
```

- Saved in hashed form in a second config file:
  `$HOME/.jupyter/jupyter_notebook_config.json`.


#### Setting up a self-signed certificate

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

### Running Jupyter on the login node

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


> ## Managing SLURM jobs from Jupyter
>
> With the help of a simple package installed into Python environments on 
> Beskow and Tegner, it is possible to submit and monitor 
> SLURM batch jobs and do light-weight pre- and postprocessing work, 
> as well as interactive on-the-fly analysis during a running job, from 
> within a Jupyter notebook.
>
> - **Log in to Tegner, and start a Jupyter server. Connect to it from your 
>   local browser.
> - **Go through the steps on [this PDC blog post](https://www.kth.se/blogs/pdc/2019/01/using-jupyter-notebooks-to-manage-slurm-jobs/). You will learn how 
>   to submit, monitor and interactively analyze a running job.**
{: .challenge}




