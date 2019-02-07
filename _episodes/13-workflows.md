---
layout: episode
title: "(Optional) Automating HPC workflows"
teaching: 10
exercises: 10
questions:
  - "How can I automatize my HPC workflows?"
objectives:
  - "Learn to automatize workflows with Snakemake"
---

# HPC workflows

> This material is based on [a CodeRefinery lesson](https://coderefinery.github.io/reproducible-research/).

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

Automated workflows increase the reproducibility of computational 
research, and enable you to figure out 
precisely what data and what code were used to generate a result.
Such workflows:

 - Provide a historical record (provenance) of data, its origins and causal relationships.
 - Can be used to ensure quality of data based on ancestral data, or find sources of errors.
 - Allow automated recreation of data.
 - Are implemented in many workflow management tools.

But what are workflow management tools?

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

### Simple workflow example with [Snakemake](https://snakemake.readthedocs.io/en/stable/index.html)

Snakemake is a workflow management tool with a number 
of desirable properties:

- Gentle learning curve, based on Python.
- Free, open-source, and installs easily via pip.
- Cross-platform (Windows, MacOS, Linux) and **compatible with all HPC schedulers**
  - same workflow works without modification and scales appropriately whether on a laptop or cluster .
- Heavily used in bioinformatics, but is completely general.

<img src="../img/snakemake.png" style="height: 250px;"/>

#### Setup

First log in to Tegner, then load an Anaconda module 
and activate the snakemake environment:

```bash
$ module add anaconda/py36/5.0.1
$ source activate snakemake
```

To follow along in this exercise, go to your klemming nobackup directory 
and **clone** this [repository](https://github.com/coderefinery/word-count):

```shell
$ git clone https://github.com/coderefinery/word-count.git
```

#### About the project

The example project is about counting the frequency distribution of words in a given text, plotting bar charts and testing 
[Zipf's law](https://en.wikipedia.org/wiki/Zipf%27s_law).

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

Note that the project includes a README file, a requirements file with software dependencies, and a license file.

The texts that we want to analyze for the project is in the `data/` directory (four books in plain text), 
along with LICENSE_TEXTS.md which contains the license for the texts and their origins. 

The data directory is like this:
```bash
word_count/
|-- data/
|   |--LICENSE_TEXTS.md
|   |--abyss.txt
|   |--isles.txt
|   |--last.txt
|   |--sierra.txt
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

and compute the ratio between the frequencies of the two most common words (Zipf's law predicts this ratio to be 2):
```bash
$ python source/zipf_test.py processed_data/abyss.dat > results/results.txt
```

and finally create a tarball of the results:
```bash
$ tar -czvf zipf_analysis.tar.gz results/abyss.dat processed_data/abyss.dat results/results.txt
```

Have a look at the generated output files!

- In simple cases it's easy to figure out what the input is and how results are computed from it.
- As projects grow, it becomes more difficult to keep track of all steps of a workflow.
- The complete workflow for the Zipf test project looks like:

<img src="../img/snakemake_dag.png" style="height: 300px;"/>

---

#### Automating the workflow

We will now use a Snakefile to automatically run all the steps of the 
workflow, taking care of the dependencies between each step. The 
Snakefile looks like this:

```python
# a list of all the books we are analyzing
DATA = glob_wildcards('data/{book}.txt').book

# localrules are for HPC workflows - these rules will not be run as batch jobs
localrules: all, clean, make_archive

# the default rule (first rule to appear in snakefile)
rule all:
    input:
        'zipf_analysis.tar.gz'

# delete everything so we can re-run things
rule clean:
    shell:
        '''
        rm -rf source/__pycache__
        rm -f zipf_analysis.tar.gz processed_data/* results/*
        '''

# count words the books
# logfiles from each run are put in .log files"
rule count_words:
    input:
        wc='source/wordcount.py',
        book='data/{file}.txt'
    output: 'processed_data/{file}.dat'
    log: 'processed_data/{file}.log'
    shell:
        '''
        echo "Running {input.wc} with {threads} cores on {input.book}." &> {log} &&
            python {input.wc} {input.book} {output} >> {log} 2>&1
        '''

# create plots for each book
rule make_plot:
    input:
        plotcount='source/plotcount.py',
	book='processed_data/{file}.dat'
    output: 'results/{file}.png'
    shell: 'python {input.plotcount} {input.book} {output}'

# generate summary table
rule zipf_test:
    input:
        zipf='source/zipf_test.py',
        books=expand('processed_data/{book}.dat', book=DATA)
    output: 'results/results.txt'
    shell:  'python {input.zipf} {input.books} > {output}'

# create an archive with all of our results
rule make_archive:
    input:
        expand('results/{book}.png', book=DATA),
        expand('processed_data/{book}.dat', book=DATA),
        'results/results.txt'
    output: 'zipf_analysis.tar.gz'
    shell: 'tar -czvf {output} {input}'

# success/error reporting, from https://twitter.com/khanaziz84/status/1082203240752734208
onsuccess:
    print("Success, have a cake!")
    shell("mail -s 'Snakemake workflow completed: Have a beer!' your-email@somewhere.com < {log}")

onerror:
    print("Error, Snakemake aborted!")
    shell("mail -s 'Snakemake workflow aborted: Have a coffee and see logs inside!' your-email@somewhere.com < {log}")
```

- Let's discuss this Snakefile!
  - rules, special rules
  - `input` (dependencies), `output` (targets), `shell` (command)
  - wildcards (`{input}`)
  - named dependencies (`{input.book}`)
  - pattern rules (`{file}`)
  - python functions from snakemake.io (`glob_wildcards()`, `expand()`)

We copy-paste this Snakefile into a file called Snakefile, 
and run all steps of our workflow with a single command:
```bash
$ snakemake
```

We see lots of output, and hopefully all steps complete successfully.

#### Running the workflow in batch jobs

All steps of this example workflow finish within seconds on a single core, 
but you might be dealing with heavy calculations that need to be run 
on a cluster. 

To let Snakemake execute the rules on compute nodes, we need to 
specify SLURM flags in a configuration file (json or yaml):

```bash
{
    "__default__" :
    {
        "account" : "edu19.intropdc",
        "time" : "00:05:00",
        "n" : 1,
        "partition" : "main"
    },
    "zipf_test" :
    {
        "account" : "edu19.intropdc",
        "time" : "00:10:00",
        "n" : 24,
        "partition" : "main"
    }
}
```

- Each rule in the Snakefile can have a unique cluster configuration (#cores, time, etc.)
- If a rule is not found in the cluster config file, the `__default__` rule is used for it.

We copy-paste the config file to a file `cluster.json`, clean all output:
```bash
$ snakemake clean
```

and run the workflow as batch scripts:

```bash
$ snakemake -j 50 --cluster-config cluster.json --cluster "sbatch -A {cluster.account} -p {cluster.partition} -n {cluster.n}  -t {cluster.time}"
```

- The `-j` flag determines how many jobs Snakemake is allowed to have running at the same time.
- Try monitoring the SLURM queue.
- Check that all output files are generated, and whether any SLURM output files have been created.


