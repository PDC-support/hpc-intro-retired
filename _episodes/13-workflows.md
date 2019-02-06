---
layout: episode
title: "(Optional) Automating HPC workflows"
teaching: 20
exercises: 10
questions:
  - "How can I automatize my HPC workflows?"
objectives:
  - "Learn to automatize workflows with Snakemake"
---

# HPC workflows

## Directory structure for projects

- It is good to keep all files associated with a project in a single folder.
- Different projects should have separate folders.
- Use consistent and informative directory structure.
- Add a README file to describe the project and instructions on reproducing the results.
- Code, scripts, documentation, important input/output data, figures etc. 
  should be tracked in a version control system, but not generated files.
- But your mileage may vary, it's not a one-size-fits-all.

A project directory can look something like this:
```bash
project_name/
|-- data/                        contains input data files of the project
|   |-- README.md                describes where input data came from
|   |-- sub-folder/              may contain subdirectories as well
|   |-- ...
|-- processed_data/              will contain intermediate files from the analysis
|-- manuscript/                  will contain the manuscript describing the results
|-- results/                     will contain the results of the analysis (including tables and figures)
|-- source/                      will contain all code
```

---

## Automating your workflow

Automated reproducible workflows enable you to figure out 
precisely what data and what code were used to generate a result:

 - Provide a historical record (provenance) of data, its origins and causal relationships.
 - Can be used to ensure quality of data based on ancestral data, or find sources of errors.
 - Allow automated recreation of data.
 - Implemented in many workflow management tools.

What are workflow management tools?

- Orchestrated and repeatable pattern for a series of computational or data manipulation steps.
- Typical homemade workflows: series of scripts that read data and input, call programs and produce outputs.
- [Many specialized frameworks exist](https://github.com/common-workflow-language/common-workflow-language/wiki/Existing-Workflow-systems) 
  for managing scientific workflows
  - environments to create and execute workflows and monitor results
  - enable sharing and reusing workflows
  - enable tracking the provenance of workflow results 
  - can enable scaling across nodes, clusters, cloud
  - each has its own specialities, benefits and user communities
  - the [common workflow language (CWL)](http://www.commonwl.org) tries to 
    overcome "vendor lock-in"

---

### Simple workflow with [Snakemake](https://snakemake.readthedocs.io/en/stable/index.html)

Snakemake is a workflow management tool with a number 
of desirable properties:

- Gentle learning curve, based on Python.
- Free, open-source, and installs easily via pip.
- Cross-platform (Windows, MacOS, Linux) and compatible with all HPC schedulers
  - same workflow works without modification and scales appropriately whether on a laptop or cluster .
- Heavily used in bioinformatics, but is completely general.

<img src="../img/snakemake.png" style="height: 250px;"/>

> To follow along, **clone** this [repository](https://github.com/coderefinery/word-count):
> ```shell
> $ git clone https://github.com/coderefinery/word-count.git
> ```

The example project directory is like this:
```bash
word_count/
|-- data/                                
|-- processed_data/                                
|-- manuscript                           
|-- results/                             
|-- source/
|-- README.md
|-- requirements.txt
|-- license.md
|-- ...                              
```

#### Generating results

In the `source` directory  we have three scripts:
 - `wordcount.py`, finds the frequency distribution of words used in a text 
 - `plotcount.py`, plots a bar chart of the results
 - `zipf_test.py`, calculates the ratio between the counts of the two most common words

We count the number of times each word appears by:

```bash
$ python source/wordcount.py data/abyss.txt  processed_data/abyss.dat
```

and generate a plot by:
```bash
$ python source/plotcount.py processed_data/abyss.dat results/abyss.png
```

and finally compute the ratio between the frequencies of the two most common words (Zipf's law predicts this ratio to be 2)
```bash
$ python source/zipf_test.py processed_data/abyss.dat > results/results.txt
```

- In simple cases it's easy to figure out what the input is and how results are computed from it.
- As projects grow, it becomes more difficult to keep track of all steps of a workflow.
- The complete workflow for this example project looks like this:

<img src="../img/snakemake_dag.png" style="height: 300px;"/>

---

