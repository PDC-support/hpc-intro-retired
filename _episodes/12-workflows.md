---
layout: episode
title: "Automating HPC workflows"
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


## Documenting and automating your workflow

Reproducible workflows enable you to figure out precisely what data and what code were used to generate a result:

 - Provide a historical record (provenance) of data, its origins and causal relationships.
 - Can be used to ensure quality of data based on ancestral data, or find sources of errors.
 - Allow automated recreation of data.
 - Implemented in many [workflow management tools](https://github.com/common-workflow-language/common-workflow-language/wiki/Existing-Workflow-systems).

### Snakemake

WRITEME

---

