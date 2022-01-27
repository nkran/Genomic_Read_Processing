# Genomic Read Processing
### Overview
This pipeline was developed to process genomic reads through the trimming and mapping stages. This repository is paired with the GATK variant calling pipeline (*under development*). The pipeline was developed using [Nextfow](https://www.nextflow.io/) version 20.10.0 (version on Imperial HPC; date: 25/01/2022). I assume reads will have already been assessed using [FastQC](https://www.bioinformatics.babraham.ac.uk/projects/fastqc/), so trimming paramaters will be known. The quality of the reads I've used so far in the Crisanti lab only need simple trimming (adapter and random primer removal, and a simple trailing quality trim) , so if you need more complex paramaters please contact me and I can update the pipeline to better reflect these needs.

### Trimming 
There are two options for trimming: [Trim Galore](https://www.bioinformatics.babraham.ac.uk/projects/trim_galore/) and [Trimmomatic](http://www.usadellab.org/cms/?page=trimmomatic).

### Mapping
This is conducted using [BWA-MEM](https://github.com/lh3/bwa). If you require a different genomic mapping tool, please let me know and I can add more options to the tool for versitility. 

### Usage
To use this pipeline follow these instructions:

  1. Run FastQC to identify appropraite trimming paramaters.
  2. Clone this repository using `git clone` into the project working directory. I would recommend using a scratch directory such as the `ephemeral` Imperial HPC directory due to the generation of numerous sizeable files.
  3. Place all paired-end reads into a directory called `02_Raw_Reads`. The pipeline expects reads are gzipped paired-end fastqs with naming convention that can be detected with the regex `*_R{1,2}*q.gz`.  
  4. Update the `nextflow.config` file appropraitely. The paramaters that are required to be updated are highlighted.
  5. Submit the pipeline using `qsub NF_Mapping.sh`

*NB.* You can provide trimmed reads, just place them in the `03_Trimmed` directory and use the `--Skip_Trim` option in the `NF_Mapping.sh` file.


Below is the help message from `Mapping.nf`:
```
Usage:
	You'll first need to update the paths and config file to reflect your environment and ensure you are in the same directory as the scripts, then:
	qsub NF_Mapping.sh
	
	If you require more advanced trimming options, you can skip the trimming steps and place trimmed gzipped fastqs into the 03_Trimmed directory and run:
	nextflow run Mapping.nf -c nextflow.config --profile imperial --Skip Trim 1

	The pipeline expects paired-end gzipped fastqc files that can be detected with the regex "*_R{1,2}*q.gz". 
	To check, use "ls -1 /path/to/02_Raw_Reads/*_R{1,2}*q.gz"
	
	Directory Structure:
	  /Project_dir/                                                Project Directory - Exectute scripts from here
	    | - Nextflow_Submit.sh                                     Pipeline submission script
	    | - Mapping.nf                                             Nextflow script
	    | - nextflow.config                                        Nextflow config - Update to reflect environment and computational requirements
	    | - 01_FastQC/                                             
	    | - 02_Raw_Reads/                                          Place all raw paired end read in this directory
	          | - 01_FastQC/                                       Optional post-trimming FastQC directory
	    | - 03_Trimmed/
	    | - 04_Mapped/
	
	Optional arguments:
	  --help                                                       Show this message
    --version                                                    Shows versions of all software included 
	  --init                                                       To be run first and only once - sets up conda environments
	  --Skip_Trim                                                  Skips trimming step
	  --FastQC                                                     Runs FastQC after trimming alongside mapping (Cannot be used with --Skip_Trim; off by default)
	  --Skip_IndexRef                                              Skips the index reference step. Reference genome and bwa index files need to be in the same directory.    
	  --mode trim_galore                                           Choice of which trimming software (trim_galore/trimmomatic; default: trim_galore)
```
