# scAVENGERS
[![Documentation Status](https://readthedocs.org/projects/scavengers/badge/?version=latest)](https://scavengers.readthedocs.io/en/latest/?badge=latest)

## Overview
scAVENGERS demultiplexes snATAC-seq data by genotype, referring to the variant information.

## Execution
### 1. Installation
The command below clones the repository and install dependencies.
```
git clone https://github.com/kaistcbfg/scAVENGERS
conda env create -f scAVENGERS/envs/environment.yml
```

### 2. Setting parameters for scAVENGERS pipeline
Via config file in yaml format, you can set parameters for the execution of pipeline. The parameters include path of the input and output data and settings for each program in the pipeline. The format is provided in `config.yaml`. 

### 3. Running scAVENGERS pipeline
The below is a command to run whole pipeline for demultiplexing. scAVENGERS require an indexed alignment file, an indexed reference genome file and a line-seperated list of barcode sequences in a text file.
```
conda activate scavengers
scAVENGERS pipeline --configfile config.yaml -j $THREADS
```

### 4. Accessing the result
Running scAVENGERS pipeline results to a tab-delimited file. This result file `clusters.tsv` is structured like below. To note, the format is compatitible with the cluster result file generated from souporcell (https://github.com/wheaton5/souporcell).

|column name|description|
|---|---|
|barcode|barcode sequence|
|status|status of the barcode sequence: singlet or doublet|
|assignment|cluster number where the barcode is assigned. Doublets are expressed in a form {n}/{n}.|
|log_prob_singleton|log singleton probability calculated by troublet|
|log_prob_doublet|log doublet probabilty calculated by troublet|
|cluster{n}|log likelihood of the assignment on cluster n.|