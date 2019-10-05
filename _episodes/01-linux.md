---
layout: episode
title: "(Optional) Linux shell tutorial"
teaching: 30
exercises: 30
questions:
  - "How does the Linux shell work?"
  - "Why use it?"
objectives:
  - "Learn the basics of Linux shell"
  - "Prepare for work on Beskow and Tegner"
---

# Linux shell tutorial

## Overview


> This material is based on several excellent resources available online, 
> including:
> - [The Software Carpentry Unix Shell lesson](https://swcarpentry.github.io/shell-novice/)
> - [Course material developed at Aalto, Finland](https://scicomp.aalto.fi/training/linux-shell-tutorial.html)
> - [Documentation pages at the University of Oslo](https://www.mn.uio.no/geo/english/services/it/help/using-linux).

This material consists of the following parts: 
 1. [What is a shell?](#what-is-shell)
 2. [Bash basics](#bash-basics)
 3. [Processes](#processes)
 4. [File permissions](#file-permissions)
 5. [Hotkeys](#hotkeys)
 6. [Environmental variables](#envvars)
 7. [Configuration](#config)

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

## Bash basics {#bash-basics}

### Getting a bash shell

Set yourself up with a bash shell. 

- **Linux and Mac users:**  
  Just open a terminal window.

- **Windows users:**  
  For Windows10 users we recommend you try the new [WSL](https://docs.microsoft.com/en-us/windows/wsl/install-win10), and [set it up to log in to PDC](https://www.pdc.kth.se/support/documents/login/windows_login.html#wsl-approach). You can use the WSL terminal to follow 
  this tutorial.
  
  For other Windows systems, or if you want to go the traditional way, follow these instructions to [install PuTTY](https://www.pdc.kth.se/support/documents/login/windows_login.html).
  You will be working on Tegner right away.

---

### Your very first bash commands 

We are now going to get familiar with the bash shell.  
We start with two commands.

| command | Explanation |
| ------- | ----------- |
| `pwd`     | print work directory | 
| `ls`      | list contents | 


> ## Getting started
>
> - What do you get if you type ``pwd`` in the terminal?
> - What do you get if you type ``ls`` in the terminal?
{: .task}


> **Take home messages:**  
>   - Bash shell is a text command based tool to interact with the computer.
>   - You are always in the directory (folder) given by ``pwd``.

---

### Files and folders

When you work you typically want to
- manipulate files (ascii text, images, raw data etc)
- manage folders and their contents in some way

Some typical bash commands for these purposes are:

| command | Explanation |
| ------- | ----------- |
| `cd myproject` | change directory to `myproject` |
| `mkdir newdir`  | create a new directory called `newdir` |
| `cp file1.dat file2.dat` | make a copy of `file1.dat` called `file2.dat` |
| `mv file1.dat newdir/file2.dat` | move `file1.dat` into `newdir` and rename it `file2.dat` |


> ## Copying files and moving between folders
>
> - Create a directory called `example_hpc`. Verify that it was created.
> - Move into `example_hpc`. Obtain the name of the current location. Check contents.
> - Start a text editor in your usual way. Create a new text file named `file1.txt` and save it inside the folder `example_hpc`.
> - Use the terminal to copy this file to a new file named `file2.txt`. Check result.
{: .task}

> ## Text editors
>
> There are text editors with a graphical user interface and text editors 
> that allow you to edit text in the terminal. Some allow both modes of 
> operation. Choosing the right editor is a matter of taste and preferences. 
> Since we often spend significant portions of our days editing text and 
> source code, it can be valuable to invest time into learning your 
> favourite editor really well.  
> Here is a list of common editors:
> - `nano` - easiest to use but with minimal functionality
> - `vi/vim` - takes some effort to get started, but has more functionality than Nano
> - `emacs` - takes some effort and learning to get started and offers almost unlimited functionality  
> 
> Other alternatives, particularly for Windows:
> - Visual Studio Code
> - Atom
> - Notepad / Notepad++
> 
> As `nano` is the simplest one, it will be used in this tutorial.
{: .callout}

> ## File and directory names
>
>  - Do not use space as separators in names - use underscore instead!
>  - What happens if you use spaces in file/directory names?
{: .callout}


---

### Relative vs. absolute paths
You can specify a location by its *relative* (to current location) or *absolute* path.

| Relative | Absolute |
| ------- | ----------- |
| cd example_hpc | cd /home/tkl/example_hpc |
| cd ..      | cd /home/tkl/ |
| cd ../..   | cd /home/ |


> **Take home message:**  
>   - Absolute paths always work but relative paths are more convenient.

### File managers
 
On Beskow and Tegner we have installed GNU Midnight Commander 
which is a free cross-platform orthodox file manager. To use it, do:
```bash
$ module load midnightcommander
$ mc
```



---

### Learning some more commands


| command | Explanation |
| ------- | ----------- |
| `man ls`    | prints manual on the command ls (just an example) | 
| `rm file1`  | remove file1 (careful! no return in general)      |
| `ls -l`     | list files in long format                         |
| `history`   | prints the command line history in order.         |
| `cat file`  | prints all contents of file to stdout.            |
| `less file` | view contents of file in pager.                   |
| `head file` | prints first lines of file to stdout.             |
| `tail file` | prints last lines of file to stdout.              |
| `echo something` | prints from command line to stdout. |


- ``ls -l`` is an example of using a command with a *flag*. 
- These are usually options to customize the command. 
- Use `man <command>` to learn about options for commands you use.

> ## Reading the `man` page
> 
> - Type `man ls`.
> - Type `Up/Down` keys to scroll through the manpage, or `Enter` to jump a page.
> - Type `/` to search, and `n` to find next match or `N` for previous match.
> - Try searching for the `-F` flag.
> - Type `q` to exit.
{: .task}

> ## List dot files (files starting with . )
>
> - Try to find a way to list all files, including those with ``.`` as their first chracter.
{: .task}

---

### Input and output: redirect and pipes

- Programs can display something: `echo hello world`, `cat file.txt`
- Programs can take some input: e.g. `less` by default displays input if no filename given.
- `cat /etc/bashrc` dumps that file to stardard output (stdout)
- `cat /etc/bashrc | less` gives it to less on standard input (stdin)

#### Pipe

- Output of the first command as an input for the second one: `command_a | command_b`

```bash
# count a number of logged in users
w -h | wc -l

# to list all matching commands
history | grep -w 'command name'

# print the name of the newest file in the directory (non-dot)
ls -1tF | grep -v -E '*/|@' | head -1
```

#### Redirects

- Like pipes, but send data to/from files instead of other processes.
- Replace a file: command > file.txt
- Append to a file: command >> file.txt (be careful you do not mix them up!)
- Redirect file as STDIN: command < file (in case program accepts STDIN only)

```bash
echo Hello World > hello.txt

ls -lH >> current_dir_ls.txt

# join two files into one
cat file1 file2 > file3

# go through file1 and replace spaces with a new line mark, then output to file2
tr -s ' ' '\n' < file1 > file2
# -or- in more readable format
cat file1 | tr -s ' ' '\n' > file2
```

> ## Exercise
>
> - Type ``history``
> - Type ``history > history.txt``
> - Type ``ls -l`` and then check time stamp of ``history.txt``
> - Use ``cat`` to print all contents of this file. 
> - Print the last 4 lines of ``history.txt`` using the ``tail`` command (explore the 
>   manpage if needed)
> - Instead of creating an intermediate file, find a more clever way to print the 
>   last 4 commands by piping `history` into `tail`    
{: .task}


> ## (Advanced) Pipelines with `;`, `&&`, and `||`
>
> - You can put several commands on the same line using different
>   separators.
> - The shell term for this is *pipelines*.
> 
> Chaining: ``command_a ; command_b``: always runs both commands.
> 
> Remember exit codes?  In shell, 0=success and anything 1-255=failure.
> Note that this is opposite of normal Boolean logic!
> 
> The ``&&`` and ``||`` are [short-circuit](https://en.wikipedia.org/wiki/Short-circuit_evaluation) (lazy)
> boolean operators.  They can be used for quick conditionsals.
> 
> * ``command_a && command_b``
> 
>   * If ``command_a`` is successful, also run ``command_b``
>   * final exit code is last evaluated one, which has the role of Boolean *and*.
> 
> * ``command_a || command_b``
> 
>   * If ``command_a`` is *not* successful, also run ``command_b``
>   * final exit code is that of the last evaluated command, which has
>     the role of Boolean *or*.
> 
> **Hint** ``command_a && command_b || command_c``
> 
> Try: ``cd /nonexistent_dir && ls /nonexistent_dir`` compare with ``cd /nonexistent_dir; ls /nonexistent_dir``
> 
> Try: ``ping -c 1 8.8.8.8 > /dev/null && echo online || echo offline``
{: .challenge}

---

### Finding things

#### find

- Where is that file again? Use ``find``

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
# search for file.txt in home directory
find ~ -name file.txt
# one can search more than one dir at once
find ~ /cfs/klemming/nobackup/u/username -name file.txt  
``` 

> ## find on Lustre
> In your klemming directories it's usually more efficient to use ``lfs find``.  
> This uses a raw lustre connection to make it more efficient than 
> accessing every file. It has somewhat limited abilities as comparing
> to GNU find. For details ``man lfs`` on Tegner.
{: .callout}

> ## Fast find -- locate
> Another utility that you may find useful ``locate <pattern>``, but on
> workstations only.  This uses a cached database of all files, and
> just searches that database so it is much faster.
{: .callout}


#### grep
- In many cases you quickly want to find specific information in files. 
- Then ``grep`` is your command to use.

```bash
grep <pattern> <filename>  # grep lines that match <pattern>
 -or- 
command | grep <pattern>  # grep lines from stdin
``` 



> ## Searching for patterns with grep
>
> - Type ``grep mkdir <path>/history.txt``   
>   (replacing \<path\> as needed)
{: .task}

> ## Find location of a file
>
> - Type ``find . -name history.txt``
{: .task}

> ## Exercise: grep and pipes
>
> - make a pipe that counts number of files/directories (including dot files) in your directory
> - grep directories out of ``ls -l``
> - grep all but blank lines in Tegner's file /etc/bashrc
>
>   - expand the previous one to filter out commented lines also (line starts with #). Note that
>     lines may have spaces before # mark.
>
> - count unique logged in users on triton. Tip: ``w`` or ``users`` gives you
>   a list of all currently login users, many of them have several sessions open.
> - (Optional) Play with the commands grep, cut: find at least two ways to
>   extract IP addresses only out of /etc/hosts. Tip: `grep` has `-o` option, thus one can build
>   a regular expression that will grab exactly what you need.
> - (Optional) Using pipes and commands echo/tr/uniq, find doubled words out of 'My
>   Do Do list: Find a a Doubled Word'. Any easier way to do it?
{: .task}

> ## Advanced `find`
> Find syntax is actually an entire boolean logic language given on the
> command line: it is a single expression evaluated left to right with
> certain precedence.  There are match expressions and action
> expressions.  Thus, you can get amazingly complex if you want to.
> Take a look at the 'EXAMPLES' section in `man find` for the comprehensive list
> of examples and explanations.
> 
> **Too many arguments**  error solved with the ``find ... | xargs``
> 
> More examples:
> ```bash
> # look for jpeg files in the current dir only
> find . -maxdepth 1 -name '*.jpg' -type f
> 
> # find all files of size more than 10M and less than 100M
> find . -type -f -size +10M -size -100M
> 
> # find everything that does not belong to you
> find ~ ! -user $USER | xargs ls -ld
> 
> # open all directories to group members
> # tip: chmod applies x-bit to directories automatically
> find . -type d -exec chmod g+rw {} \;
> 
> # find all s-bitted executable binaries
> find /usr/{bin,sbin} -type f -perm -u+x,u+s
> 
> # find and remove all files older than 7 days
> find path/dir -type f -mtime +7 -exec rm -f {} \;
> ``` 
{: .challenge}

> ## Advanced `grep`
> ```bash
> # search all the files in the dir/ and its subdirs, to match the word 'is', case insensitive
> grep -R -iw 'is' dir/
>  
> # grep all lines from *command* output, except those that have 'comment' in it
> *command* | grep -v comment
>  
> # displaying 2 extra lines before and after the match (-A just after, -B just before)
> grep -C 2 'search word' file
>  
> # counts the number of matches
> grep -c <pattern> file(s)
>  
> # shows only the matched part of the string (by default grep shows whole line)
> grep -o <pattern> file(s)
>  
> # accepts way more advanced regular expressions as a search pattern
> grep -E <extended_regexpr> file(s)
> ``` 
> 
> For details on what <pattern> could be, look for REGULAR EXPRESSIONS
> at ``man grep``.  Some examples:
> 
> ```bash
> # grep emails to a list
> grep -Eio "\b[a-z0-9._%+-]+@[a-z0-9.-]+\.[a-z]{2,6}\b" file.txt
>  
> # grep currently running firefox processes
> ps auxw | grep firefox
>  
> # grep H1 and H2 header lines out of HTML file
> grep "<[Hh][12]>" file.html
> ``` 
{: .challenge}


> **Take home messages:**  
>   - Customize the command usage with flags.
>   - The manual is your friend - consult it before googling. 
>   - In the learning phase, use ``history`` regularly.
>   - ``commandA > fileA`` redirects output from commandA to text fileA
>   - ``commandA | commandB`` means that you* ***pipe*** the output from commandA to commandB.
>   - Most commands work well alone and you can cherry pick which you need to combine into a unique result. **Modularity**.


---

## Processes {#processes}

Uptil now we have only focused on how to handle files and folders.

But we typically also want to **run programs.**

- All running programs (and commands) are *processes*
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

> ## Exercise: Watching processes
>
> - type ``top`` in your terminal
> - open a new terminal and type ``nano``.
> - find the *pid* of nano
{: .task}

---

### Foreground and background processes

The shell has a concept of *foreground* and *background* processes:

**foreground**

When you used ``top`` you started a *foreground* process. Our keyboard is connected as input to it and the screen is its output. There can only be one of these active at a time.

To kill a foreground process: Ctrl-c

**background**

On the contrary, a *background* process does not have any input connected. You can have as many of these as your resources permit. 

If you add ``&`` right after the command it will send the process to background. 

To kill a background process: ``kill`` or ``pkill``, possible also from within ``top``.

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
>  - Find out with `man` how to use `top` / `pstree` / `ps` to list all the running processes that belong to you.  
>    Tip: `top` has both command line options and hotkeys.
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
{: .task}

---

## File/directory permissions {#file-permissions}

When you share resources with other people, it is important that your files have the correct access settings. 

- Permissions are a type of *file metadata*.

- They tell you who can: **r**ead, **w**rite, e**x**ecute a file/list directory.

Let's see an example, produced by ls -l

```bash
$ ls -l

-rw-r--r-- 1 tkl tkl  120 feb  3 19:51 file1.txt
-rwx------ 1 tkl tkl 9865 feb  2 08:51 my_secret_code.ex
drwx------ 2 tkl tkl 4096 feb  1 13:53 private_folder
drwxrw-r-- 2 tkl tkl 4096 feb  6 19:54 public_folder
```

- First character is specific for type of object. `d` is for directory.

- Next we have three triplets:  `user`, `group`, and `others`.


| command | Explanation |
| ------- | ----------- |
| `chmod u+rwx fileA`  | add **r**ead, **w**rite and e**x**ecution rights of fileA to **u**ser |
| `chmod o+r  fileA`   | add **r**ead permission of fileA to **o**thers |
| `chmod o-wx fileA`   | remove **w**rite and e**x**ecution rights of fileA for **o**thers | 

You may also have seen commands using numbers, e.g. ``chmod 644 fileA``.
The logic behind this is: r=4, w=2, x=1. Thus, 
```bash
chmod 700 <files>   # r=4, w=2, x=1
```
is equivalent to:
```bash
chmod u+rwx,g-rwx,o-rwx <files>   # u=user, g=group, o=others, a=all 
```

To change permissions for all the subdirectories and files at once,
use the recursive flag:
```bash
chmod -R <perm> <directory>
```

To change group ownership (you must be a group member):
```bash
chgrp group_name <file or directory>
```

To change owner of file/directory:
```bash
chown -R greys <file or directory>
```

> ## Exercise: File permission
>
>  - Create a textfile with some random word in your Public folder.
>  - Remove all access for `groups` and `others`, and ask a friend to read it.
>  - Add read permission so that your friend can read the file.
>
{: .task}

> ## Exercise: Folder and file permission
>
> This is part of the output from torkj's home. 
> ```bash
> -rw-r--r-- 1 torkj      30 262470 Dec 28 16:15 ompi_info.txt
> -rw-r--r-- 1 torkj      30      5 Jan 25 13:55 tmp.txt
> drwxr-xr-x 3 torkj nogroup   2048 Feb 11 15:30 Public
> ```
>
> Try to read tmp.txt - explain the outcome
>
> **Hint:**  
> Even though a file has read access, the top directory must be
> searchable before external user or group will be able to access
> it. Sometimes people do  
>   ``chmod -R o-rwx $SOME_DIR``  
>   ``chmod o+x $SOME_DIR``  
> Execute (``x``) without read (``r``) means that you can
> access files inside if you know the exact name, but not list the
> directory.  The permissions of the files themselves still matter.
{: .task}

> ## Exercise: Some more in-depth things
>
> What are the extra permission bits: ``s-bit`` and ``t-bit``?  
> Read up on ``umask`` [(see here)](https://www.computerhope.com/unix/uumask.htm).  
>
{: .task}

### Modifying permissions: advanced

Access Control Lists (ACLs) are advanced access permissions.  They
don't work everywhere, for example mostly do not work on NFS
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

**Advanced file status**   
To get file meta info: ``stat <file_or_dir>``


> ## Exercise: Permissions
>
> - Create a directory, use ``chmod`` to allow user and any group members
>   full access and no access for others
> - (Optional) Change that directory group ownership with ``chown`` or 
>   ``chgrp`` (any group that you belong to is fine), set s-bit for the 
>   group and apply t-bit to a directory, check that the upper directory 
>   has `o+x` bit set: now you should have a private working space for 
>   your group. Tip: see groups that you are a member of using ``id -Gn``
> - (Optional) Create a directory and a subdirectory in it and set their 
>   permissions to 700 with one command.
> - Create a directory (in /cfs/klemming/nobackup/u/user on Tegner or somewhere on your own system)
> - use ``setfacl`` to set its permissions so that only you and some
>   user/group of your choice would have access to it.
{: .task}


---

## How to make things faster: hotkeys {#hotkeys}

- Avoid typing every character in the shell - **use hotkeys!**
- These are **short cuts** to find command you are looking for in the shell.
- Most important key: **TAB**: autocomplete.

**You should never be  typing full filenames or command names. TAB can complete almost anything**

### Common hotkeys:

| Hotkey | Effect |
| ------ | ------ |
| TAB | autocompletion |
| Home ``or`` Ctrl-a | start of the command line |
| End ``or`` Ctrl-e | end |
| up/down arrows | traverse command history |
| Ctrl-l | clear the screen |
| Ctrl-Shift- -  | undo the last changes on cli |
| Ctrl-Shift-c | copy |
| Ctrl-Shift-v | paste |
| Alt-r | undo all changes made to this line |
| Ctrl-r | command history search: backward (hit Ctrl-r, then start typing the search word, hit Ctrl-r again to go through commands that have the search word in it) |
| Ctrl-s  | search command history furtherword (for this to work one needs to disable default suspend keys ``stty -ixon``) |
| Ctrl-u  | remove beginning of the line, from cursor |
| Ctrl-k | remove end of the line, from cursor |
| Ctrl-w | remove previous word |


> ## TAB autocompletion
>
> - Type ``find /home -name history.txt``
> - Type ``cat /home/`` and start pushing TAB. Add minimal characters and TAB. Repeat until you have full path to ``history.txt``.
{: .task}



> **Take home message:**
>    - Always use TAB


---

> ## Exercise: Looking around on the login node
>
> - Obtain the current location
> - Type ``top`` to view processes and info on the server
> - Type ``w`` to see which other users are logged in
> - Type ``last -n 15`` to get a list of the last 15 logins
{: .task}


---

## Environment variables {#envvars}

- In shell, variables define your environment. 
- Common practice is that environmental 
  variables are written in capital: `$HOME`, `$SHELL`, `$PATH`, `$PS1`, `$RANDOM`. 
- To list all defined variables use `printenv`. 
- All variables can be used or even redefined. 
- No error if you call an undefined variable, it is just considered to be empty.

> ## Exploring environment variables
>
> - Type ``echo $HOME``
> - Type ``echo $HOSTNAME``
> - Type ``echo $PATH``
> - Type ``echo $`` and then press TAB to see what happens
{: .task}

These give you further power to customize your session to fit your needs.

---

## Initialization and configuration {#config}

- When the shell first starts (when you login), it reads some files. These are normal shell files, and it evaluates normal shell commands to set configuration.
- You can always test things in your own shell and see if it works before putting it in the config files. Highly recommended!
- You customize your environment means setting or expanding aliases, variables, functions.
- The config files are located in $HOME and are called:
  - `.bashrc` (when SSH) and
  - `.bash_profile` (interactive login to a workstation)
  - they are often a symlink from one to another

> ## Customize your shell environment with .bashrc
> 
> - To get inspiration take a look at this [(very elaborate) sample file](<https://www.tldp.org/LDP/abs/html/sample-bashrc.html>).
> - Type ``source .bashrc`` to reload the information there.
{: .task}


> ## Customize your command line prompt
> Command line prompt defined in 
> [PS1](https://www.ibm.com/developerworks/linux/library/l-tip-prompt/).
> ```bash
> PS1="[\d \t \u@\h:\w ] $ "
> ```
> You might find some inspiration [here](https://www.maketecheasier.com/8-useful-and-interesting-bash-prompts/)
>
> For special characters see PROMPTING at ``man bash``.  
> If you want to save something permanently, add to `.bashrc` like ``export PS1``. But save the old ``PS1`` so that you can recover it if necessary!
>
{: .task}

> **Take home message:**
> 
>    - You can store names in environment variables
>    - Customize your session with configuration files
>    - .inputrc needs to be loaded with ``bind`` rather than ``source``.  
> 
> This is however not needed if the file is present at login, as it is automatically loaded by the system.

---

# Summary of commands used so far

Commands used so far

| command | Explanation |
| ------- | ----------- |
| `pwd`     | present work directory | 
| `ls`      | list contents |
| `cd example_hpc` | change directory to `example_hpc` |
| `mkdir new_dir`  | create a new directory called `new_dir` |
| `cp file1 file2` | make a copy of `file1` called `file2` |
| `mv file1 new_dir/file2` | move `file1` into `new_folder` and call that `file2` |
| `cd example_hpc` | cd /home/tkl/example_hpc |
| `cd ..`      | cd /home/tkl/ |
| `cd ../..`   | cd /home/ |
| `man ls`    | prints manual on the command ls (just an example) | 
| `rm file1`  | remove file1 (careful! no return in general)      |
| `ls -l`     | list files in long format                         |
| `history`   | prints the command line history in order.         |
| `cat file`  | prints all contents of file to stdout.            |
| `head file` | prints first lines of file to stdout.             |
| `tail file` | prints last lines of file to stdout.              |
| `find -name Objname` | looks for ``Objname`` recursively starting from current location | 
| `find /home -name Objname` | looks for ``Objname`` starting from ``/home``  |
| `grep pattern file`  | grabs all matches of `pattern` in `file` |
| `chmod u+rwx fileA`  | add **r**ead, **w**rite and e**x**ecution rights of fileA to **u**ser |
| `chmod o+r  fileA`   | add **r**ead permission of fileA to **o**thers |
| `chmod o-wx fileA`   | remove **w**rite and e**x**ecution rights of fileA for **o**thers |
| `echo $HOSTNAME`     | displays the string stored in environmental variable HOSTNAME
| `source .bashrc`     | loads the file .bashrc that contains envinromental variables |
| `w`	 	     | list which users are logged in |
| `last -n 10`	     | list the lastest 10 logins |

---

# GNU Screen   


### Exiting the shell, and the [GNU screen](https://www.gnu.org/software/screen/) utility

To exit the shell, type `logout` or press Ctrl-d.

However, quitting your shell can be annoying if you have customized 
your environment and would need to start over when reopening the shell.
Luckily there are programs to preserve your session, such as `screen`

With `screen`, you can:
 - Manage persistent terminal sessions, which survive if connection is abruptly cut.
 - Save screen processes when logging out and resume where you left off, 
 - Have multiple windows connected to the same terminal session.
 - Copy and paste (including block-copy) between different 
   screens without mouse.

**Screen commands short list**

| command | Explanation |
| ------- | ----------- |
| `screen` | start a session | 
| `screen -S [name]` | start a named session |
| `screen -ls` | list running sessions |
| `screen -x` | attach to a running session |
| `screen -r [name]` | attach to session with [name] |

The `screen` commands inside a screen session are prefixed by an 
escape key, by default `Ctrl-a` (`C-a`): 

| `screen` command | Explanation |
| ---------------- | ----------- |
| `C-a d` | detach from session |
| `exit (C-d)` | kill the window/session |
| `C-a c` | create new window |
| `C-a C-a` | change to last-visited window |
| `C-a <number>` | change to window by number |
| `C-a "` | see window list (and select) |

> ## Exercise: Trying out screen
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
{: .task}

Some people have their ``screen`` open forever, which just keeps
running and never gets closed.  Wherever they are, they ssh in,
connect, and resume right where they left off.

#### [tmux](https://github.com/tmux/tmux/wiki) 
- [tmux](https://github.com/tmux/tmux/wiki) is similar to screen 
   with some minor pros and cons, try it out to see which one you like better!
  
---

## File archiving 


``tar`` is the de-facto standard tool for saving many files or
directories into a single archive file.  
Archive files may have extenssions `.tar`, `.tar.gz` etc depending on compression.

```bash
# create tar archive gzipped on the way
tar -caf arhive_name.tar.gz directory_to_be_archived/

# extract files
tar -xaf archive_name.tar.gz -C path/to/directory
``` 

- `f` is for the filename
- `a` selects the compression method based on the archive file suffix (in
  this example gzip, due to the .gz suffix. 
- Without compression files/directories are simply packed as is. 

Other command line options: 
- `r` - append files to the end of an archive.
- `t` - list archive content.


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
{: .task}

---







---

