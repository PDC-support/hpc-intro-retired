---
layout: episode
title: "Overview"
teaching: 5
exercises: 5
questions:
  - "What is HPC?"
  - "How can you use HPC?"
objectives:
  - "Get an overview of what cluster and cloud computing is"
  - "Discuss benefits and challenges in using HPC systems"
keypoints:
  - "keypoint 1"
  - "keypoint 2"
---

# What is high performance computing

> High Performance Computing most generally refers to the practice of aggregating computing power in 
> a way that delivers much higher performance than one could get out of a typical desktop computer or
> workstation in order to solve large problems in science, engineering, or business. 

(G.Sravanthi *et al.*, IOSR J. Comp. Eng. 16, 36-43)

Using a cluster often has the following advantages for researchers:

- **Speed.** With many more CPU cores, HPC systems can offer significant speed up.
- **Volume.** Many HPC systems have both the processing memory (RAM) and disk storage to handle very large amounts of data. Terabytes of RAM and petabytes of storage are available for research projects.
- **Efficiency.** Many HPC systems operate a pool of resources that are drawn on by a many users. In most cases resources are used almost constantly.
- **Cost.** Bulk purchasing and government funding mean that the cost to the research community for using these systems is significantly less than it would be otherwise.
- **Convenience.** Maybe your calculations just take a long time to run or are otherwise inconvenient to run on your personal computer. There's no need to tie up your own computer for hours when you can use someone else's instead.


## What is a cluster?

The words "cloud", "cluster", and "high-performance computing" get thrown around a lot. So what do
they mean exactly? And more importantly, how do we use them for our work?

- *Cloud* refers to remote computing resources of any kind
  - webservers, remote storage, API endpoints, and more traditional "compute" resources.
- *Cluster* describes a network of computers sharing a common purpose
  - used to accomplish tasks that might otherwise be too big for any one computer.

![The cloud is made of Linux](../img/linux-cloud.jpg)

## The future of HPC

In our increasingly computerized world, there are many reasons to expect that the 
importance of HPC in many different sectors will only grow.

The competition between supercomputer hardware vendors is stiff. Intel Xeon processors have long
dominated the HPC processor market, but now Arm has entered the race, and AMD is re-entering the market.
At the same time, thanks to the hype around big data and artificial intelligence,
there is a large focus on developing specialized hardware (e.g. GPUs, TPUs) to handle such tasks 
efficiently. 

The [TOP500 list](https://top500.org/) ranks the 500 fastest supercomputers in the world, and 
the [GREEN500](https://top500.org/green500/) list ranks the top 500 supercomputers by energy efficiency.
There is considerable prestige involved in reaching the #1 spot, which is frequently 
held by the US or China. Lately, a lot of attention is focused on developing the first 
[exascale computing system](https://en.wikipedia.org/wiki/Exascale_computing), capable of reaching at 
least one exaFLOP. The US and China are expected to build their first exascale supercomputers in 
2020-2021, and in many ways the race to get there first reminds of the [space race](https://en.wikipedia.org/wiki/Space_Race)!


---

> ## How can you use HPC?
>
> Are you already using HPC in your work, and if so for what? Or if you're new to HPC, 
> how do you think using a cluster will be different from using your laptop?
> Talk to your neighbor about what you use (or want to start using) HPC for in your research,
> and what challenges you encounter (or expect you will encounter).
{: .challenge}

