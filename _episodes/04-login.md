---
layout: episode
title: "Authentication, login and file transfer"
teaching: 10
exercises: 0
questions:
  - "What is Kerberos and why does PDC use it?"
  - "How do I log in and copy files to/from PDC?"
objectives:
  - "Set up access to PDC"
  - "Learn to log in and copy files to PDC"
keypoints:
  - "Kerberos offers a secure mechanism for connecting to PDC, and has pros and cons when it comes to usability."
  - "Login nodes are a limited shared resource and should be used with care."
---

# Kerberos Authentication Protocol

PDC needs to protect the individual work and data of researchers who use PDC's systems, 
as well as protecting PDC's own hardware resources from security threats and attacks.
For this reason, PDC uses the Kerberos authentication protocol for secure login to PDC's clusters.

### What is it?

- Kerberos is a computer network authentication protocol with mutual authentication.
- PDC uses the Heimdal version of the open-source Kerberos software.
- Both the users of PDC and the PDC servers verify each other's identities using Kerberos. 
- Connecting using Kerberos protects against 
  [eavesdropping](https://en.wikipedia.org/wiki/Eavesdropping#Network_attacks) and 
  [replay](https://en.wikipedia.org/wiki/Replay_attack) attacks.

<img src="../img/kerberos_login.png" alt="SNIC" width="600" align="middle"> 

### Ticket

- Proof of users identity.
- Users use passwords to obtain tickets.
- Tickets are cached on the user's computer for a specified duration.
- Tickets **should be created on your local computer**.
- No passwords are required during the ticket's lifetime.

### Realm

- Sets boundaries within which an authentication server has authority (`NADA.KTH.SE`).

### Principal

- Refers to the entries in the authentication server database  (`username@NADA.KTH.SE`).
- Expires typically every two years, after which it needs to be reactivated.

### Kerberos commands

- `kinit` generates ticket
- `klist` lists kerberos tickets
- `kdestroy` destroys ticket file
- `kpasswd` changes password

Generating a ticket and listing it:
```bash
$ kinit --forwardable username@NADA.KTH.SE
$ klist -f

Credentials cache : FILE:/tmp/krb5cc_500
    Principal: username@NADA.KTH.SE
Issued       Expires     Flags  Principal
Mar 25 09:45 Mar 25 19:45 FI krbtgt/NADA.KTH.SE@NADA.KTH.SE
Mar 25 09:45 Mar 25 19:45 FA afs/pdc.kth.se@NADA.KTH.SE
```

---

# Secure shell login 

**To log in to PDC clusters** you need to use the `ssh` command (secure shell).

- Your SSH implementation needs to support GSS-API with Kerberos key exchange.
- For Linux, Mac and Windows computers, some [installation and configuration steps](https://www.pdc.kth.se/support/documents/login/login.html#step-by-step-login-tutorial) of SSH need to be performed
- After obtaining a Kerberos ticket, there is no need to enter a password when using `ssh`.

> If you haven't already installed and configured Kerberos and SSH at this point, please notify an instructor.

**To copy files to and from PDC file systems**, you should use `scp` (secure copy) 
which copies files between hosts over a network. 
- `scp` uses SSH for transfer, is based on the same authentication and provides the same security as `ssh`. 
- Before using `scp`, you need a valid forwardable Kerberos tickets on your local machine and a working SSH setup.

### Login and transfer nodes

|Cluster  | Type	| Address                                        |
| ------- | ----------- | ---------------------------------------------- |
|Beskow	  | Primary	| beskow.pdc.kth.se                              |
|Tegner	  | Primary	| tegner.pdc.kth.se (tegner-login-1.pdc.kth.se)  |
|Tegner	  | Secondary	| tegner-login-2.pdc.kth.se                      |
|Tegner	  | Transfer 1	| t04n27.pdc.kth.se                              |
|Tegner	  | Transfer 2	| t04n28.pdc.kth.se                              |

> ## Generate a 3-day Kerberos ticket
> 
> Check the documentation of the `kinit` command to see how to specify the lifetime 
> of the Kerberos ticket (on Linux and Mac, check the `man` pages: `$ man kinit`).
> Then generate a 3-day ticket and list it.
{: .challenge}

> ## Now log in to Tegner
> 
> Assuming that you have your laptop set up for access to PDC, now log in to Tegner using `ssh`:
> ```
> $ ssh <username>@tegner.pdc.kth.se
> ```
> {: .bash}
{: .challenge}

> ## Being Certain Which System your Terminal is connected to
>
> If you ever need to be certain which system a terminal you are using is connected to then use the
> following command: 
> ```
> $ hostname
> ```  
> In the following, we will indicate which system a terminal is logged in to using 
> ```bash
> [cluster]$ some-command
> ``` 
> where `cluster` can be `tegner` or `beskow`
{: .callout}


> ## Exploring the Tegner login node
>
> Let's explore the login node a bit. 
> First, where are we? The command
> ``` 
> $ hostname
> ```
> {: .bash}
> tells you the name of the login node (note that you have been redirected from `tegner.pdc.kth.se`).  
> To find out your current directory, type
> ```
> $ pwd
> ```
> {: .bash}
> This is your PDC home directory on the AFS file system (more about that later).  
> To find the number of processors, run:
>
> ```
> $ nproc --all
> ```
> {: .bash}
>
> or
>
> ```
> $ cat /proc/cpuinfo
> ```
> {: .bash}
>
> to see full details.
> 
> How about memory? Try running: 
>
> ```
> $ free -m
> ```
> {: .bash}
>
> or for more details: 
>
> ```
> $ cat /proc/meminfo free -m
> ```
> {: .bash}
{: .challenge}

> *Take home message:* The login node is a shared and limited resource with many concurrent users.
> It should not be used for any demanding calculations!


> ## Transfering files to and from PDC
>
> Small files (e.g. input files, submit scripts) can be copied to PDC file systems 
> via the login node. Larger files (more than several MB) should instead be copied 
> via one of the dedicated transfer nodes, 
> either `t04n27.pdc.kth.se` or `t04n28.pdc.kth.se`.
> To copy a file from your computer to the cluster, do:
> ``` 
> $ scp mydata.dat <username>@t04n27.pdc.kth.se:~/
> ```
> {: .bash}
> or if you need to transfer a whole directory, add the `-r` flag:
> ``` 
> $ scp -r job-directory/ <username>@t04n27.pdc.kth.se:~/
> ```
> {: .bash}
> Note that after the hostname we have `:~/`, which stands for your PDC home directory.  
> To copy a file from PDC to your computer, do:
> ```
> $ scp <username>@t04n27.pdc.kth.se:~/my_big_output.dat .
> ```
> {: .bash}
> This copies a file from your PDC home directory called `my_big_output.dat` to the 
> current directory on your computer. If 
> you instead want to copy a file directly from your Lustre nobackup directory 
> ([more about that in the next section](../05-filesystems)), you can provide the 
> explicit path:
> ```
> $ scp <username>@t04n27.pdc.kth.se:/cfs/klemming/nobackup/<initial>/<username>/my_big_output.dat .
> ```
> {: .bash}
{: .challenge}
