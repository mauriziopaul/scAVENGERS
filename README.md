# scAVENGERS

## Overview
scAVENGERS demultiplexes snATAC-seq data by genotype, referring to the variant information.

The pipeline is composed of four stages: calling variants, making count matrices for each cell barcode and loci, clustering the cell barcodes, and detecting doublets. The stages are implemented in a snakemake pipeline.

The generative model is based on data generating process of snATAC-seq reads: the attachment of the reads to barcode sequences and the following PCR amplification of the reads.

## Requirements
scAVENGERS is intended to run at Linux with bash support. The program is successfully tested under the environment below. These requirements are provided in a conda environment and a shell script.
|name|version|remarks|
|---|---|---|
|**External tools**|
|freebayes|1.3.5|
|strelka2|2.9.10|Automatically downloaded by the shell script when the pipeline is excuted.|
|vartrix|1.1.22|
|bcftools|1.12|
|souporcell|2.0|Automatically downloaded and compiled by the shell script when the pipeline is executed. Troublet program will only be used among the whole pipeline.|
|snakemake|7.3.8|
|**Python and its packages**|
|python|3.8.12|
|numba|0.54.1|
|numpy|1.20.3|
|pandas|1.4.0|
|scikit-learn|1.0.2|
|scipy|1.7.3|
|**Rust**|
|rust|1.59.0|


## Execution
### 1. Installation

#### Running snakemake pipeline in Anaconda environment
The required softwares and packages are provided as a conda environment. Conda (https://www.anaconda.com/products/distribution) helps users to manage multiple packages easily. Running below will create an environment named 'scavengers.'
```
git clone https://github.com/kaistcbfg/scAVENGERS
cd scAVENGERS
conda env create --file environment.yaml
conda activate scavengers
```
### 2. Preparing data
Alignment file in bam format and line-seperated list of barcodes in text file are required to run scAVENGERS.

You can acquire alignment file and barcodes in ease by using Cellranger ATAC count (https://github.com/10XGenomics/cellranger-atac). The tool aligns fastq files by using bwa mem-like algorithm. The alignments are stored in `outs/possorted_bam.bam`, and the barcodes are stored in `outs/filtered_peak_bc_matrix/barcodes.tsv`.

### 4. Setting parameters
Via config file in yaml format, you can set parameters for the execution of pipeline. The parameters include path of the input and output data and settings for each program in the pipeline. The format is provided in `config.yaml`. 

### 4. Running scAVENGERS
The below is a command to run scAVENGERS with 10 jobs. To note, you must also set `THREADS` argument in config file in adequate value to run with intended number of jobs. With the arguments, the whole pipeline for demultiplexing will be executed. scAVENGERS require an indexed alignment file, an indexed reference genome file and a line-seperated list of barcode sequences in a text file.
```
cd scAVENGERS
snakemake --configfile config.yaml -j 10
```


### 4. Accessing the result
The result file `clusters.tsv` is structured like below. To note, the format is compatitible with the cluster result file generated from souporcell(https://github.com/wheaton5/souporcell).

|column name|description|
|---|---|
|barcode|barcode sequence|
|status|status of the barcode sequence: singlet or doublet|
|assignment|cluster number where the barcode is assigned. Doublets are expressed in a form {n}/{n}.|
|log_prob_singleton|log singleton probability calculated by troublet|
|log_prob_doublet|log doublet probabilty calculated by troublet|
|cluster{n}|log likelihood of the assignment on cluster n.|