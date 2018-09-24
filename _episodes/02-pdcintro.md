---
layout: episode
title: "The PDC Center for HPC"
teaching: 20
exercises: 0
questions:
  - "What services does PDC provide?"
  - "How can I start running my research at PDC?"
objectives:
  - "Get an understanding of how PDC operates"
keypoints:
  - "1"
  - "2"
---

# Swedish National Infrastructure for Computing (SNIC) 

SNIC is a national research infrastructure which: 
 - Provides a balanced and cost-efficient set of resources.
 - Provides user support for large scale computation and data storage.
 - Aims to meet the needs of researchers from all scientific disciplines and from all over Sweden (universities, university colleges, research institutes, etc).
 
<img src="../img/SNIC_SUPR_map.png" alt="SNIC" width="450" align="middle"> 


## SNIC user and project repository (SUPR)

- SUPR ([supr.snic.se](https://supr.snic.se/)) is the SNIC database of persons, projects, proposals and more.
- Most allocations for computing time are managed via SUPR.
- After being allocated computing time at a SNIC cluster, users can apply for user accounts at the corresponding SNIC centers.

---

# PDC Center for HPC

## A peak back in time

| **Year**  | **Rank**  | **Procs.** | **Peak TFlops** | **Vendor** | **Name** | **Specs.** |
| --------- | --------- | ---------- | --------------- | ---------- | -------- | ---------- |
| 1994 | 341 | 256   | 0.0025 | Thinking Machines | Bellman | CM-200/8k |
| 1996 | 64  | 96    | 0.0172 | IBM | Strindberg | SP2/96 |
| 1998 | 60  | 146   | 0.0934 | IBM | Strindberg | SP P2SC 160 MHz |
| 2003 | 196 | 180   | 0.6480 | HP | Lucidor | Cluster Platform 6000 rx2600 Itanium2 900 MHz Cluster, Myrinet |
| 2005 | 65  | 886   | 5.6704 | Dell | Lenngren | PowerEdge 1850 3.2 GHz, Infiniband |
| 2010 | 89  | 9800  | 86.024 | Dell | Ekman | PowerEdge SC1435 Dual core Opteron 2.2GHz, Infiniband |
| 2010 | 76  | 11016 | 92.534 | Cray | Lindgren | XT6m 12-core 2.1 GHz |
| 2011 | 31  | 36384 | 305.63 | Cray | Lindgren | XE6 12-core 2.1 GHz |
| 2014 | 32  | 53632 | 1973.7 | Cray | Beskow | XC40 16-core 2.3GHz |
| 2017 | 36  | 67456 | 2440.0 | Cray | Beskow | XC40 18-core 2.1GHz |


## Clusters at PDC

### Beskow - a Cray XC40 system

<img src="../img/Beskow.png" alt="Beskow" width="450" align="middle"> 

Beskow is currently the fastest machine in Scandinavia (until late 2018 when Tetralith takes over)

 - Lifetime: Q4 2019
 - 11 racks, 2060 nodes
 - Intel Haswell processor 2.3 GHz / Intel Broadwell processor 2.1 GHz
 - 67,456 cores - 32(36) cores/node
 - Aries Dragonfly network topology
 - 156.4 TB memory - 64(128) GB/node

### Tegner - pre/post processing for Beskow and GPU jobs

<img src="../img/k420.png" alt="Tegner" width="250" align="middle"> 

- Used for pre/post processing data 
- Has large RAM nodes
- Has nodes with GPUs
- Has two transfer nodes
- Lifetime: Q4 2019

**5 x 2 TB Fat nodes**
- 4 x 12 core Ivy Bridge, 2 TB RAM 
- 2 x Nvidia Quadro K420
  
**5 1 TB Fat nodes**
 - 4 x 12 core Ivy Bridge, 1 TB RAM 
 - 2 x Nvidia Quadro K420
  
**46 Thin Nodes**
- 2 x 12 core Haswell, 512 GB RAM 
- Nvidia Quadro K420 GPU

**9 K80 Nodes**
- 2 x 12 core Haswell, 512 GB RAM 
- Nvidia Tesla K80 GPU

## Support and system staff at PDC

**First-line support**
- Provide assistance to PDC users related to accounts, login, allocations etc.

**System staff**
- System administrators ensure that computing and storage resources run smoothly and securely.

**Application Experts**
- Assist researchers across different disciplines in developing and using scientific codes.
- Provide training.

<a href="https://www.pdc.kth.se/hpc-services/application-experts-1.737439">
   <img src="../img/AEs.png" alt="Application experts" width="550" align="middle"> 
</a>

## Training and other activites at PDC

**Summer School**  
- Introduction to HPC held in August every year (MPI, OpenMP, GPU, HPC architecture, profiling...)

**Various courses and workshops** 
- Programming with GPGPU
- Distributed and Parallel Computing 
- Cloud Computing
- Software Development Tools
- CodeRefinery workshops

**PDC Pub and Open House**
- Held in spring every year
- Users are invited to PDC to chat with the staff and see the clusters over drinks and snacks

**PDC/SeRC seminar series**
- Monthly lunch-time seminars given by leading e-Science researchers on a wide range of topics

All events can be found on the [events calendar](https://www.pdc.kth.se/about/events)

---

