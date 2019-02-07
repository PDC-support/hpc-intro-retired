---
layout: episode
title: "Final words"
teaching: 5
exercises: 0
questions:
  - "What best practices should I keep in mind when running on HPC?"
  - "How can I get help?"
objectives:
  - "Learn a few rules of thumb for more efficient HPC work"
---

# HPC best practices

- Work on improving your command line skills, this pays off in the long run.
- Be friends with the file system, rather write one large file than thousands of small files.
- Show good manners on the login node.
- Use the transfer nodes for transfering large amounts of data.
- Avoid massively parallel but very short jobs, this causes problems for the 
  scheduler.
- Avoid massive output to STDOUT.

- Estimate how much time your job will take before submitting it (shortens queue time)
- Some codes run faster when using fewer cores per node due to memory 
  requirements.
- Calibrate your jobs, like you would do with an experimental apparatus
  - Use a test case where you know what the correct answer should be.
- Perform parallel scaling tests of your jobs.
- If you have access to multiple clusters, run benchmark tests to see 
  where your code runs fastest.

- Compare different compilers (Intel, GNU, Cray...)
- Use recent compiler versions when building code.

- Keep all files associated with a project in a single folder.
- Consider using automated workflow management tools if you run 
  complicated workflows.  



# Getting help

- `man` pages
  - `$ man <some-command>`
- List of good resources:
  - [An HPC wiki for another HPC center containing tutorials](https://dccn-hpc-wiki.readthedocs.io/en/latest/index.html).
  - [HPC Carpentry](https://hpc-carpentry.github.io/).
  - [SNIC knowledge base with a list of training events](http://docs.snic.se/wiki/Training).
  - [List of software installed on SNIC centres along with links to documentation (poorly maintained)](http://docs.snic.se/wiki/Software).
- Send in a support request.
  - SNIC (and other national HPC providers) employs support staff and application experts who can help with basic and advanced problems.
- Include all relevant information when you send in a support request.



---

