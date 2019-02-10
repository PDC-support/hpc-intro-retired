---
layout: episode
title: "Linux shell tutorial"
teaching: 30
exercises: 30
questions:
  - "How does the Linux shell work?"
  - "Why use it?"
objectives:
  - "Learn the basics of Linux shell"
  - "Prepare for work on Tegnér"
keypoints:
  - "kp1"
---

# Linux shell tutorial

## Course basics


> This material is based on several excellent resources available online, 
> in particular [this course material developed at Aalto, Finland](https://scicomp.aalto.fi/training/linux-shell-tutorial.html), and [these documentation pages at the University of Oslo](https://www.mn.uio.no/geo/english/services/it/help/using-linux).

This material consists of the following parts: 
 - [What is a shell?](#what-is-shell)
 - [Bash basics](#bash-basics)
 - [Processes and files](#processes-files)
 - [More advanced bash usage](#interactive-usage)

Exercises marked "optional" are for advanced users who would like further stimulation.

---


## What is a shell? {#what-is-shell}

### About the Linux Shell

- **A *shell* is what you get when your terminal window is open.**

  It is a command-line interface (CLI).
  
- **It is a "layer" around the operating system.**

  It connects and binds all programs together.
  
- **Provides efficiency**

  Often required to use HPC systems (i.e. at PDC).
  
- **There are multiple shells.**

  This session is about [bash](https://en.wikipedia.org/wiki/Bash_(Unix_shell))
  
- **Concepts are also relevant for Mac and Windows** 
  
---
---
---

## BASH BASICS {#bash-basics}

---
#### Getting a BASH shell

Set yourself up with a BASH shell. 

- **Linux and Mac users:**

  just open a terminal window.

- **Windows users:**  [TODO] Log these in on Tegner right away??

   [install PuTTY](https://www.pdc.kth.se/support/login/windows_login.html), follow the configuration instructions and then log in to Tegner.

---

#### Your very first bash commands 

We are now going to get familiar with the bash shell in a very low pace mode.

We start with two commands.

| command | Explanation |
| ------- | ----------- |
| pwd     | present work directory | 
| ls      | list contents | 


> ## Type-along-exercise: What is this bash thing?
>
> - What do you get if you type ``pwd`` in the terminal?
> - If you are not on Tegnér, open a File Explorer.
>
>   Find out the location you are viewing. Compare with output from ``pwd``.
>
> - Type ``ls`` in the terminal. Compare with the items you see in the File Explorer.
{: .challenge}

**Take home messages:**

  *bash shell is a text based tool to interact with the computer.*

  *you are always in the directory (folder) given by ``pwd``.*

**Main advantage:**

  *can be made highly efficient*.

---

#### Files and folders

When you work you typically want to
- manipulate files (ascii text, images, raw data etc)
- manage folders and their contents in some way

Some typical bash commands for these purposes are:

| command | Explanation |
| ------- | ----------- |
| cd example_hpc | change directory to *example_hpc* |
| mkdir new_dir  | create a new directory called *new_dir* |
| cp file1 file2 | make a copy of *file1* called *file2* |
| mv file1 new_dir/file2 | move *file1* into *new_folder* and call that *file2* |


> ## Type-along-exercise: Copying files and moving between folders.
>
> - Create a directory called *example_hpc*. Verify that it was created.
> - Move into *example_hpc*. Obtain the name of the current location. Check contents.
> - Execute ``cd ..`` and then check location. What happened? Then move back into *example_hpc*.
> - Execute ``cd example_hpc`` in the terminal. Do you understand the output?
> - Start a text editor in your usual way. Create a new text file named *file1.txt* and save it inside the folder *example_hpc*.
> - Use the terminal to copy this file to a new file named *file2.txt*. Check result.
{: .challenge}

---

#### Relative vs. Absolute paths

The final task in the feather weight class of this tutorial is to understand the concept of relative and absolute paths.

| Relative | Absolute |
| ------- | ----------- |
| cd example_hpc | cd /home/tkl/example_hpc |
| cd ..      | cd /home/tkl/ |
| cd ../..   | cd /home/ |

**Take home message:**
  
  *Absolute paths always work but relative paths are more convenient.*

---

#### Learning some more commands


| command | Explanation |
| ------- | ----------- |
| man ls    | prints manual on the command ls (just an example) | 
| rm file1  | remove file1 (careful! no return in general)      |
| ls -l     | list files in long format                         |
| history   | prints the command line history in order.         |
| cat file  | prints all contents of file to stdout.            |
| head file | prints first lines of file to stdout.             |
| tail file | prints last lines of file to stdout.              |


``ls -l`` is an example of using a command with a *flag*. These are usually options to customize the command. Use *man \<command\>* to learn about options for commands you use.

We will now use a slightly more advanced command to create a dummy text file for next exercise.




> ## Type-along-exercise: Reading the manual
> - Type ``printf ' %i Hej \n' {1..20} > tmp_file.txt ``
> - Type ``ls -l`` and then check time stamp of ``tmp_file.txt``
> - Use ``cat`` to print all contents of this file. 
> - Print the last 4 lines, (read manual of ``tail``)
> - Try to guess the meaning of the different parts in the text string you used to produce the text file.
> - Type ``history``. 
{: .challenge}


**Take home messages:**

  *Customize the command usage with flags.*

  *The manual is your friend - consult it before googling. NB: Some special commands need ``help``* 

  *In the learning phase, use ``history`` regurarily.*



---
#### Some practice and sneak peek ahead


Often you wish to combine commands in different ways to tailor the effect.


> ## Type-along-exercise: Practice
>
> - Type ``history > hpc_feb13.txt`` . Useful documentation!
> - Type ``cd`` . Where are you now? Quick way to "go home".
> - Type ``ls -lrt | tail -n 4`` . What happened?
{: .challenge}


In many cases you quickly want to find specific information in files. Then ``grep``
is your command to use.


| command | Explanation |
| ------- | ----------- |
| grep pattern file  | grabs all matches of *pattern* in *file* | 


[TODO] Choose file that is guaranteed to exitst on all systems?
> ## Type-along-exercise: grep
>
> - Type ``cat /proc/cpuinfo``.
> - Type ``cat /proc/cpuinfo | grep vendor_id``.
> - Type ``cat -n /proc/cpuinfo | grep vendor_id``.
{: .challenge}



**Take home message:**

   *``commandA > fileA`` redirects output from commandA to text fileA*

   *``commandA | commandB`` means that you* ***pipe*** *the output from commandA to commandB.*

   *Most commands work well alone and you can cherry pick which you need to combine into a unique result. **Modularity**.*


---

#### Processes {#processes}

Uptil now we have only focused on how to handle files and folders.

But we typically also want to **run programs.**

- All running programs are *processes*
- Processes have:

  - Process ID (integer)
  - Name (command being run)
  - Command line arguments
  - input and output: ``stdin`` (input, like from keyboard),
    ``stdout`` (output, like to screen), ``stderr`` (like stdout)
  - Return code (integer) when complete
  - Working directory
  - environment variables: key-values which get inherited across processes.

- These concepts bind together *all* UNIX programs, even graphical ones.

As an example, do the following:

> #### Exercise: Watch processes
>
> - type ``top`` in your terminal
> - open a new terminal and type ``firefox``.
> - find the *pid* of firefox

---

#### Foreground and background processes

The shell has a concept of *foreground* and *background* processes:

**foreground**

When you used ``top`` you started a *foreground* process. Our keyboard is connected as input to it and the screen is its output. There can only be one of these active at a time.

To kill a foreground process: Ctrl-c

**background**

On the contrary, a *background* process does not have any input connected. You can have as many of these as your resources permit. Normally, you need an & after the comman to put the process in the background.

To kill a background process: ``kill`` or ``pkill``, possible also from within ``top``.


---
---

### File/directory permissions

When you share resources with other people, it is important that your files have the correct access settings. 

- Permissions are a type of *file metadata*.

- They tell you who can: **r**ead, **w**rite, e**x**ecute a file/list directory.

Let's see an example, produced by ls -l

```bash
-rw-r--r-- 1 tkl tkl  120 feb  3 19:51 file1.txt
-rwx------ 1 tkl tkl 9865 feb  2 08:51 my_secret_code.ex
drwx------ 2 tkl tkl 4096 feb  1 13:53 private_folder
drwxrw-r-- 2 tkl tkl 4096 feb  6 19:54 public_folder
```

- First character is specific for type of object. *d* is for directory.

- Next we have three triplets:  *user*, *group*, and *others*.


| command | Explanation |
| ------- | ----------- |
| chmod u+rwx fileA  | add **r**ead, **w**rite and e**x**ecution rights of fileA to **u**ser |
| chmod o+r  fileA   | add **r**ead permission of fileA to **o**thers |
| chmod o-wx fileA   | remove **w**rite and e**x**ecution rights of fileA for **o**thers | 


**Exercises on this will be available at the end of the session.**


---


### How to make things faster: hotkeys

- Avoid typing every character in the shell - **use hotkeys!**
- These are **short cuts** to find command you are looking for in the shell.
- Most important key: **TAB**: autocomplete.

**You should never be  typing full filenames or command names. TAB can complete almost anything**

#### Common hotkeys:

| Hotkey | Effect |
| ------ | ------ |
| TAB | autocompletion |
| Home ``or`` Ctrl-a | start of the command line |
| End ``or`` Ctrl-e | end |
| Ctrl-left/right arrows ``or`` Alt-b/Alt-f  | moving by one word there and back |
| up/down arrows | command history |
| Ctrl-l | clear the screen |
| Ctrl-Shift-c | copy |
| Ctrl-Shift-v | paste |
| Ctrl-Shift- -  | undo the last changes on cli |
| Alt-r | undo all changes made to this line |
| Ctrl-r | command history search: backward (hit Ctrl-r, then start typing the search word, hit Ctrl-r again to go through commands that have the search word in it) |
| Ctrl-s  | search command history furtherword (for this to work one needs to disable default suspend keys ``stty -ixon``) |
| Ctrl-u  | remove beginning of the line, from cursor |
| Ctrl-k | remove end of the line, from cursor |
| Ctrl-w | remove previous word |

**inputrc:**  
Check */etc/inpurc* for some default key bindings, more can be defined *~/.inputrc* (left as a home exercise)

**CDPATH:**  
Helps changing directories faster. When you type ``cd dirname``, the shell tries to go
to one of the local subdirectories and if it is not found shell will try the same command from every
directory listed in the *$CDPATH*.

```bash
export CDPATH=$HOME:/cfs/klemming/nobackup/u/username
``` 



#### Environment variables {#environment-variables}


---


---



---
---

## Summary of Bash Basics
- You type things on the screen (standard input or stdin).  The shell
  uses this to make a **command**.
- The shell takes the command, splits it into words, does a lot more
  preprocessing, and then runs it.
- When the command runs, the keyboard (still standard input) goes to
  the **process**, output (standard output) goes to the screen. 


---
---
---

original contents

---
---
---
---



### Working with processes

All processes are related, a command executed in shell is a child process of
the shell. When a child process is terminated it is reported back to parent process.
When you log out, all shell child processes are terminated along with the
shell.  You can see the whole family tree with ``ps af``.
One can kill a process or make it "nicer".

```bash
pgrep -af <name>
kill <PID>
pkill <name>
renice #priority <PID>
```

Making process "nicer", ``renice 19 <PID>``, means it will run only when nothing
else in the system wants to.
User can increase nice value from 0 (the base priority) up to 19. It is 
useful when you backup your data in background or alike.



**Hint:** For running X Window apps while you logged in from other
Linux / MacOS make sure you use ``ssh -X ...`` to log in. For Windows users,
you need to install [Xming](http://www.straightrunning.com/XmingNotes/).

**Hint:** For immediate job-state change notifications, use ``set notify``. To automatically
stop background processes if they try writing to the screen ``stty tostop``



---

If you add ``&`` right after the command it will send the process to
background. 
- Example: ``firefox --no-remote &`` (same can be done with
  any terminal command/function, like ``man pstree &``).  
- The ``&`` serves the same role as ``;`` to separate commands,
  but backgrounds the first and goes straight to the next.

If you have a process already running, you can send it to background 
with Ctrl-z and then
``bg``. 
- Drawback: no easy way to redirect the running task
  output, so if it generates output it covers your screen.

List the jobs running in the background with ``jobs``, get a job back
online with  ``fg`` or ``fg <job_number>``. There can be multiple
background jobs.


> ## Exercise: Processes
> 
>  - Find out with *man* how to use *top* / *pstree* / *ps* to list all the running processes that belong to you.  
>    Tip: *top* has both command line options and hotkeys.
> 
>    - (optional) see ``man ps`` and find out how to list a processes tree with ps, both
>      all processes and only your own (but all your processes, associated with all terminals)
> 
>  - With pgrep list all bash and then zsh sessions on Tegner.
>  - Log in to Tegner and run ``man ps``, send it to background, and ``logout``, then
>    log in again. Is it still there? Play with the ``screen``, run a session, 
>    then detach it and log out, then log in back and get 
>    your original screen session back.
>  - Run ``man htop``, send it to backround, and then kill it with ``kill``. Tip: one can
>    do it by background job number or by PID.
>  - Imagine a use case: your current ssh session got stuck and does not response. Open another
>    ssh session to the same remote host and kill the first one. Tip: ``echo $$`` gives you current
>    bash PID.
> 
>    - (optional) get any X Window application (firefox, xterm, etc) to run on Tegner
{: .challenge}




#### Files and directories

Files contain data.  They have a name, permissions, owner
(user+group), contents, and some other metadata.

Filenames may contain any character except '/', which is reseved as a separator between
directory and filenames. The special characters require quotation while dealing
with such filenames, so it makes sense to avoid them.

Path can be absolute (starting with '/') or relative (related to the current directory).

``ls`` is the standard way of getting information about files. By default it lists 
your current directory (i.e. *pwd*), but there are many options:

```bash
# list directory content
ls /scratch/work

# list directory files including dot files (i.e. hidden ones)
ls -A ~/directory1
 
# list all files and directories using long format (permissions, timestamps, etc)
ls -lA ../../directory2
```

Special notations and expansions in BASH, can be used with any command:

```bash
./, ../, ~, *, ?, [], [!], {abc,xyz}, {1..10}
``` 

For the quotation:

```bash
'', "", \
```

Quotation matters; try typing both ``echo "$USER"`` and ``echo '$USER'``.

BASH first expands the expansions and substitute the wildcards, and then
executes the command. Could be as complex as:

```bash
ls -l ~/[!abc]???/dir{123,456}/filename*.{1..9}.txt
```

There are a variety of commands to manipulate files/directories:

```bash
cd, mkdir, cp, cp -r, rm, rm -r, mv, ln, touch
```
 
For file/directory meta information or content type:

```bash
ls, stat, file
``` 

Note that ``cd`` is a shell builtin which changes the shell's own
working directory.  This is the base from which all other commands
work:
 - ``ls`` by default tells you the current directory.  
 - ``.`` is the current directory. 
 - ``..`` is the parent directory. 
 - ``~`` is your HOME.  

This is inherited to other commands you run. 

``cd`` with no options drops you to your $HOME.

```bash
# create several directories at once
mkdir dir1 dir2 dir3
# -or-
mkdir dir{1,2,3}

# copy a directory recursively
cp -r dir1 dir2/

# copy a directory preserving all the metadata to two levels up
cp -a dir1/ ../../

# move all files with the names like filename1.txt, filename_abc.txt etc to dir2/
mv filename*.txt dir2/

# remove a directories/files in the current dir without asking for the confirmation
rm -rf dir2/ dir1/ filename*

# create an empty file if doesn't exist or update its access/modification time
touch filename

# make a link to a target file (hard link by default, -s for symlinks)
ln target_file ../link_name
```

**Discover other ls features** ``ls -lX``, ``ls -ltr``, ``ls -Q``

You may also find the ``rename`` utility useful.

---

### File/directory permissions

- Permissions are one of the types of file metadata.
- They tell you if you can *read* a file, *write* a file, and
  *execute a file/list directory*.
- Each of these apply to *user*, *group*, and *others*.
- Here is a typical permission bits for a file: ``-rw-r--r--``.
- In general, it is ``rwxrwxrwx`` -- read, write, execute/search for
  user, group, others respectively.
- ``ls -l`` gives you details on files.

---

### Modifying permissions: the easy part

chmod/chown is what will work on all filesystems:

```bash
chmod u+rwx,g-rwx,o-rwx <files>   # u=user, g=group, o=others, a=all
# -or-
chmod 700 <files>   # r=4, w=2, x=1
 
# recursive, changing all the subdirectories and files at once
chmod -R <perm> <directory>

# changing group ownership (you must be a group member)
chgrp group_name <file or directory>
```

Extra permission bits:

- s-bit:  setuid/setgid bit, preserves user and/or group IDs.
- t-bit: sticky bit, for directories it prevents from removing file by
  another user (example */tmp*)

Setting default access permissions: add to *.bashrc* ``umask 027``
[(see here)](https://www.computerhope.com/unix/uumask.htm).  
The ``umask`` number respresents what permissions are *removed* from any newly
created file by default.  So ``umask 027`` means "by default,
g-w,o-rwx any newly created files".  It doesn't change any
permissions, just sets the default that the operating system will create 
files with.

**Hint:**  
Even though a file has read access, the top directory must be
searchable before external user or group will be able to access
it. Sometimes people do ``chmod -R o-rwx $WRKDIR; chmod o+x
$WRKDIR``.  Execute (``x``) without read (``r``) means that you can
access files inside if you know the exact name, but not list the
directory.  The permissions of the files themselves still matter.

---

### Exiting the shell, and the [GNU screen](https://www.gnu.org/software/screen/) utility

To exit the shell, type `logout` or press Ctrl-d.

However, quitting your shell can be annoying if you have customized 
your environment and would need to start over when reopening the shell.
Luckily there are programs so that you don't have to start over.
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

Some people have their ``screen`` open forever, which just keeps
running and never gets closed.  Wherever they are, they ssh in,
connect, and resume right where they left off.

#### [tmux](https://github.com/tmux/tmux/wiki) 
- [tmux](https://github.com/tmux/tmux/wiki) is similar to screen 
   with some minor pros and cons, try it out to see which one you like better!
  
---

### Modifying permissions: advanced 

Access Control Lists (ACLs) are advanced access permissions.  They
don't work everywhere, for example mostly do no work on NFS
mounted directories.  They are otherwise supported on ext4, Lustre,
etc. (thus work on /cfs/klemming).

* In "normal" unix, files have only "owner" and "group", and permissions
  for owner/group/others.  This can be rather limiting.
* Access control lists (ACLS) are an extension that allows an
  arbitrary number of users and groups to have access rights to
  files.   
* ACLs don't show up in normal ``ls -l`` output, but there is an extra
  plus sign: ``-rw-rwxr--+``.  ACLs generally work well, but there are
  some programs that won't preserve them when you copy/move files, etc.
* POSIX (unix) ACLs are controlled with ``getfacl`` and ``setfacl``
  - Allow read access for a user ``setfacl -m u:<user>:r <file_or_dir>``
  - Allow read/write access for a group ``setfacl -m g:<group>:rw <file_or_dir>``
  - Revoke granted access ``setfacl -x u:<user> <file_or_dir>``
  - See current stage ``getfacl <file_or_dir>``

**File managers**  
On Beskow and Tegner we have installed Midnight Commander: ``mc``.

**Advanced file status**   
To get file meta info: ``stat <file_or_dir>``



> ## Exercise: Files and directories
>
> - What does ``touch`` do? Read the first paragraph in the manpage.
> - Create a directory in your ``$HOME``, cd there and ``touch`` a file.
>   Rename it. Make a copy and then remove the original. 
> - List all files in /usr/bin that start with letter x.
> - (Optional) List all files in /usr/bin and /usr/sbin that start with 
>   non-letter characters, using only one ``ls`` command.
> - (Optional) list with ``ls`` dot files/directories only (by default it
>   lists all files/directories but not those that begin with ``.``).
>   "dotfiles" are a convention where filenames that begin with ``.``
>   such as ``.bashrc`` are considered "hidden".
> - Explore ``stat file`` output. What metadata do you find?  Try
>   to stat files of different types (regular file, directory, link,
>   special device in /dev, named pipe)
{: .challenge}

> ## Exercise: Permissions
>
> - Create a directory, use ``chmod`` to allow user and any group members
>   full access and no access for others
> - (Optional) Change that directory group ownership with ``chown`` or 
>   ``chgrp`` (any group that you belong to is fine), set s-bit for the 
>   group and apply t-bit to a directory, check that the upper directory 
>   has *o+x* bit set: now you should have a private working space for 
>   your group. Tip: see groups that you are a member of using ``id -Gn``
> - (Optional) Create a directory and a subdirectory in it and set their 
>   permissions to 700 with one command.
> - Create a directory (in /cfs/klemming/nobackup if on Tegner and in /tmp if on any other server),
>   use ``setfacl`` to set its permissions so that only you and some
>   user/group of your choice would have access to it.
{: .challenge}

---

## Interactive usage {#interactive-usage}

### find

* ``find`` is a very unixy program: it finds files, but in the most
  flexible way possible.
* It is a amazingly complicated program.
* It is number one in searching files in shell.

With no options, just recursively lists all files starting in current directory:

```bash
find
``` 

The first option gives a starting directory:

```bash
find /etc/
``` 

Other search options: by modification/accessing time, by ownership, by access
type, joint conditions, case-insensitive, that do not match, etc. 
(see [here](https://alvinalexander.com/unix/edu/examples/find.shtml) 
and [here](http://www.softpanorama.org/Tools/Find/index.shtml)):

```bash
# -or-  'find ~ /cfs/klemming/nobackup/u/username -name file.txt' one can search more than one dir at once
find ~ -name file.txt
 
# look for jpeg files in the current dir only
find . -maxdepth 1 -name '*.jpg' -type f

# find all files of size more than 10M and less than 100M
find . -type -f -size +10M -size -100M

# find everything that does not belong to you
find ~ ! -user $USER | xargs ls -ld

# open all directories to group members
# tip: chmod applies x-bit to directories automatically
find . -type d -exec chmod g+rw {} \;

# find all s-bitted executable binaries
find /usr/{bin,sbin} -type f -perm -u+x,u+s

# find and remove all files older than 7 days
find path/dir -type f -mtime +7 -exec rm -f {} \;
``` 

Find syntax is actually an entire boolean logic language given on the
command line: it is a single expression evaluated left to right with
certain precedence.  There are match expressions and action
expressions.  Thus, you can get amazingly complex if you want to.
Take a look at the 'EXAMPLES' section in *man find* for the comprehensive list
of examples and explanations.

**find on Lustre:**   
In your klemming directories it's usually more efficient to use ``lfs find``.  
This uses a raw lustre connection to make it more efficient than 
accessing every file. It has somewhat limited abilities as comparing
to GNU find. For details ``man lfs`` on Tegner.

**Fast find -- locate:**   
Another utility that you may find useful ``locate <pattern>``, but on
workstations only.  This uses a cached database of all files, and
just searches that database so it is much faster.

**Too many arguments**  error solved with the ``find ... | xargs``

---

### File archiving


``tar`` is the de-facto standard tool for saving many files or
directories into a single archive file.  Archive files may have
extenssions *.tar*, *.tar.gz* etc depending on compression.

```bash
# create tar archive gzipped on the way
tar -caf arhive_name.tar.gz directory_to_be_archived/

# extract files
tar -xaf archive_name.tar.gz -C path/to/directory
``` 

- *f* is for the filename
- *a* selects the compression method based on the archive file suffix (in
  this example gzip, due to the .gz suffix. 
- Without compression files/directories are simply packed as is. 

Other command line options: 
- *r* - append files to the end of an archive.
- *t* - list archive content.


```bash
# xz has better compression ratio than gzip, but is very slow
tar -caf archive_file.tar.xz dir1/ dir2/
``` 

Individual files can be compressed directly, e.g. with ``gzip``:

```bash
# file.gz is created, file is removed in the process.
gzip file
# Uncompress
gunzip file.gz
``` 
 
---

### Transferring files (+archiving on the fly)

For PDC users the ability to transfer files to/from PDC systems is essential.
For large transfers (big data, many files), you should use the transfer nodes:

```bash
# transferring a file from your HOME on PDC to your home worstation
scp -r username@t04n27.pdc.kth.se:file_to_copy .  # or t04n28
``` 

(Optional) Another use case, copying to PDC, or making a directory backup with ``rsync``:

```bash
rsync -urlptDxv --chmod=Dg+s somefile username@t04n27.pdc.kth.se:/cfs/klemming/nobackup/u/username/  # copy a file to klemming
rsync -urlptDxv --chmod=Dg+s username@t04n28.pdc.kth.se:/cfs/klemming/nobackup/u/username/dir1/  dir1/  # sync two directories
``` 

(Optional) Transferring and archiving your PDC data on the fly to some other place:

```bash
# login to Tegner
cd $WRKDIR
tar czf - path/to/dir | ssh kosh.aalto.fi 'cat > path/to/archive/dir/archive_file.tar.gz'
``` 

> ## Exercise: find, tar and scp/rsync
>
> - Find with ``find`` all the files in your $HOME that are readable or writable by everyone
>   - (Optional) apply ``chmod o-rwx`` to all recently found files with ``find``
> - Make a tar.gz archive of any of your directory at your HOME (or WRKDIR if on Tegner), when done
>   list the archive content, then append another file/directory to the existing archive.
>   
>   - (Optional) Extract only one particular file to some subdirectory from the archive
>   
> - Transfer just created archive using either ``scp`` or ``rsync``.
> 
>   - (Optional) Try ssh+tar combo to make transfer and archive on the fly.
{: .challenge}

---

### How to make things faster: hotkeys

- Is it annoying to have to type everything in the shell?  No, because
  we have hotkeys!  In fact, it can become much more efficient and
  powerful to use the shell.
- Most important key: **TAB**: autocomplete.  You should never be
  typing full filenames or command names.  TAB can complete almost anything

Common hotkeys:

| Hotkey | Effect |
| ------ | ------ |
| TAB | autocompletion |
| Home ``or`` Ctrl-a | start of the command line |
| End ``or`` Ctrl-e | end |
| Ctrl-left/right arrows ``or`` Alt-b/Alt-f  | moving by one word there and back |
| up/down arrows | command history |
| Ctrl-l | clear the screen |
| Ctrl-Shift-c | copy |
| Ctrl-Shift-v | paste |
| Ctrl-Shift- -  | undo the last changes on cli |
| Alt-r | undo all changes made to this line |
| Ctrl-r | command history search: backward (hit Ctrl-r, then start typing the search word, hit Ctrl-r again to go through commands that have the search word in it) |
| Ctrl-s  | search command history furtherword (for this to work one needs to disable default suspend keys ``stty -ixon``) |
| Ctrl-u  | remove beginning of the line, from cursor |
| Ctrl-k | remove end of the line, from cursor |
| Ctrl-w | remove previous word |

**inputrc:**  
Check */etc/inpurc* for some default key bindings, more can be defined *~/.inputrc* (left as a home exercise)

**CDPATH:**  
Helps changing directories faster. When you type ``cd dirname``, the shell tries to go
to one of the local subdirectories and if it is not found shell will try the same command from every
directory listed in the *$CDPATH*.

```bash
export CDPATH=$HOME:/cfs/klemming/nobackup/u/username
``` 

---

### Initialization files and configuration

- When the shell first starts (when you login), it reads some files.
  These are normal shell files, and it evaluates normal shell commands
  to set configuration.
- You can always test things in your own shell and see if it works
  before putting it in the config files.  Highly recommended!
- Customizing your environment means setting or expanding aliases,
  variables, functions, etc.
- The config files are:
  - ``.bashrc`` (when SSH) and
  - ``.bash_profile`` (interactive login to a workstation)
  - they are often a symlink from one to another
  
- To get inspiration for things to put in your .bashrc file, 
  take a look at this [(very elaborate) sample file](<https://www.tldp.org/LDP/abs/html/sample-bashrc.html>).


One of the things to play with: command line prompt defined in 
[PS1](https://www.ibm.com/developerworks/linux/library/l-tip-prompt/).

```bash
PS1="[\d \t \u@\h:\w ] $ "
```

For special characters see PROMPTING at ``man bash``. To make it
permanent, should be added to *.bashrc* like ``export PS1``.

---

### Creating/editing/viewing file

* A *text editor* edits files as ASCII.  These are your best friend.
  In fact, text files are your best friend: rawest, most efficient,
  longest-lasting way of storing data.
* "pager" is a generic term for things that view files or data.

Linux command line *text editors* like:

- *nano* - simplest
- *vim* - minimal.  To save&quit, ``ESC :wq``
- *emacs* - or the simplest one *nano*.  To save&quit: ``Ctrl-x
  Ctrl-c``

To view contents of a file in a scrollable fashion: ``less``

Quick look at the text file ``cat filename.txt`` (dumps everything to
screen- beware of non-text binary files or large files!)

Other quick ways to add something to a file (no need for an editor)

``echo 'Some sentence, or whatever else 1234567!-+>$#' > filename.txt``

``cat > filename2.txt`` to finish typing and write written to the file, press enter, then Ctrl-d.

**The best text viewer ever** ``less -S``  (to open a file in your EDITOR, hit *v*, to search through type */search_word*)

**Watching files while they grow** ``tail -n 0 -f <file>``

Try: add above mentioned ``export PS1`` to *.bashrc*. Remember ``source .bashrc`` to enable changes


> ## Exercise: Shell configuration files
>
> - link *.bash_profile* to *.bashrc*. Tip: see ``ln`` command from the previous session.
> - open *~/.bashrc* for eiditng and add there CDPATH example from above, customize
>   it for your needs and test. Tip: remember ``source ~/.bashrc``.
> - add ``umask 027`` to *.bashrc*, try creating files. Tip: ``umask -S`` prints your current setting.
> - customize a prompt ``$PS1`` and add it to your *.bashrc*, make sure is has
>   a current directory name and the hostname in it in the format *hostname:/path/to/current/dir*.
>   Hint: save the original PS1 like ``oldPS1=$PS1`` to be able to recover it any time.
> - (Optional) Set some default options for the ``less`` program in your bashrc.
>   Examples: case-insensitive searching, long prompt, wrapping lines.
{: .challenge}

---

### Utilities: the building blocks of shell


 - wide range of all kind of utilities available in Linux
 - shell is a glue to bind them all together
 - commandline is often a long list of those utilities joint into pipe
   that pass output of each other further

```bash
cat; sort; tr; cut; head; date; tail; wc; grep; uniq; paste; find  # and many others
``` 
 
We catch many of them on the way.

---

### Input and output: redirect and pipes

* Programs can display something: ``echo this is some output`` or ``cat``
* Programs can take some input: e.g. ``less`` by default displays
  input if no filename given.

- ``cat /etc/bashrc`` dumps that file to *stardard output* (stdout)
- ``cat /etc/bashrc | less`` gives it to ``less`` on *standard input*
  (stdin)

Pipe: output of the first command as an input for the second one ``command_a | command_b``:

```bash
 # send man page to a default printer
man -t ls | lpr

# see what files/directories use the most space, including hidden ones
du -hs * .[!.]* | sort -h

# count a number of logged in users
w -h | wc -l

# to remove all carriage returns and Ctrl-z characters from a Windows file
cat win.txt | tr -d '\15\32' > unix.txt

# to list all matching commands
history | grep -w 'command name'

# print all non-printable characters as well
ls -lA | cat -A

# print the name of the newest file in the directory (non-dot)
ls -1tF | grep -v -E '*/|@' | head -1
``` 

Redirects:
 - Like pipes, but send data to/from files instead of other processes.
 - Replace a file: ``command > file.txt``
 - Append to a file: ``command >> file.txt`` (be careful you do not mix them up!)
 - Redirect file as STDIN: ``command < file``  (in case program accepts STDIN only)

```bash
echo Hello World > hello.txt

ls -lH >> current_dir_ls.txt

# join two files into one
cat file1 file2 > file3

# extract user names and store them to a file
getent passwd | cut -d: -f1,5 > users

# join file1 and 2 lines one by one using : as a delimiter
paste -s -d : file1 file2 > file3

# go through file1 and replace spaces with a new line mark, then output to file2
tr -s ' ' '\n' < file1 > file2
# -or- in more readable format
cat file1 | tr -s ' ' '\n' > file2
``` 

**This is the unix philosophy** and the true power of the shell.  The
**unix philosophy** is a lot of small, specialized, good programs
which can be easily connected together. The beauty of the cli are elegant one-liners
i.e. list of commands executed in one line.

To dump output of all commands at once: group them.

```bash
{ command1; command2; } > filename  # commands run in the current shell  as a group
( command1; command2; ) > filename  # commands run in external shell as a group
```
 
**Coreutils by GNU** You may find many other useful commands at
https://www.gnu.org/software/coreutils/manual/coreutils.html

---

### Pipelines: ;, &&, and ||

- You can put several commands on the same line using different
  separators.
- The shell term for this is *pipelines*.

Chaining: ``command_a ; command_b``: always runs both commands.

Remember exit codes?  In shell, 0=success and anything 1-255=failure.
Note that this is opposite of normal Boolean logic!

The ``&&`` and ``||`` are [short-circuit](https://en.wikipedia.org/wiki/Short-circuit_evaluation) (lazy)
boolean operators.  They can be used for quick conditionsals.

* ``command_a && command_b``

  * If ``command_a`` is successful, also run ``command_b``
  * final exit code is last evaluated one, which has the role of Boolean *and*.

* ``command_a || command_b``

  * If ``command_a`` is *not* successful, also run ``command_b``
  * final exit code is that of the last evaluated command, which has
    the role of Boolean *or*.

**Hint** ``command_a && command_b || command_c``

Try: ``cd /nonexistent_dir && ls /nonexistent_dir`` compare with ``cd /nonexistent_dir; ls /nonexistent_dir``

Try: ``ping -c 1 8.8.8.8 > /dev/null && echo online || echo offline``


### grep

Later on you'll find out that ``grep`` is one of the most useful
commands you ever discover on Linux (except for all the *other* most
useful commands ever)

```bash
grep <pattern> <filename>  # grep lines that match <pattern>
 -or- 
command | grep <pattern>  # grep lines from stdin
``` 

```bash
# search all the files in the dir/ and its subdirs, to match the word 'is', case insensitive
grep -R -iw 'is' dir/
 
# grep all lines from *command* output, except those that have 'comment' in it
*command* | grep -v comment
 
# displaying 2 extra lines before and after the match (-A just after, -B just before)
grep -C 2 'search word' file
 
# counts the number of matches
grep -c <pattern> file(s)
 
# shows only the matched part of the string (by default grep shows whole line)
grep -o <pattern> file(s)
 
# accepts way more advanced regular expressions as a search pattern
grep -E <extended_regexpr> file(s)
``` 

For details on what <pattern> could be, look for REGULAR EXPRESSIONS
at ``man grep``.  Some examples:

```bash
# grep emails to a list
grep -Eio "\b[a-z0-9._%+-]+@[a-z0-9.-]+\.[a-z]{2,6}\b" file.txt
 
# grep currently running firefox processes
ps auxw | grep firefox
 
# grep H1 and H2 header lines out of HTML file
grep "<[Hh][12]>" file.html
``` 

> ## Exercise: grep and pipelines
>
> - make a pipe that counts number of files/directories (including dot files) in your directory
> - grep directories out of ``ls -l``
> - grep all but blank lines in triton:/etc/bashrc
>
>   - expand the previous one to filter out commented lines also (line starts with #). Note that
>     lines may have spaces before # mark.
>
> - count unique logged in users on triton. Tip: ``w`` or ``users`` gives you
>   a list of all currently login users, many of them have several sessions open.
> - (Optional) Play with the commands grep, cut: find at least two ways to
>   extract IP addresses only out of /etc/hosts. Tip: *grep* has *-o* option, thus one can build
>   a regular expression that will grab exactly what you need.
> - (Optional) Using pipes and commands echo/tr/uniq, find doubled words out of 'My
>   Do Do list: Find a a Doubled Word'. Any easier way to do it?
{: .challenge}

---



### Built-in and external commands

There are two types of commands:

- shell built-in: ``cd``, ``pwd``, ``echo``, ``test``, etc.
- external: ``ls``, ``date``, ``less``, ``lpr``, ``cat``, etc.
- some can be both: e.g. ``test``.  Options not always the same!
- For the most part, these behave similarly, which is a good thing!
  You don't have to tell which is which.

**Hint:** type ``type -a`` to find what is behind the name.

- ``echo something to print out``: prints whatever you put after.

**Disable built-in command:** ``enable -n echo``, after this */usr/bin/echo*
becomes a default instead of built-in *echo*.

---