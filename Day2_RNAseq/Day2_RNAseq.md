# BIO634 - Next generation sequencing (NGS) II. Transcriptomes, Variant Calling and Biological Interpretation
## June 3-4th 2021 
![alt text](https://github.com/carlalbc/URPP_tutorials/blob/master/img/Logo_URPP_kl2.png)
### University of Zürich (UZH) & URPP Evolution in action
----------
## Day 2.- RNA-seq and gene expression analyses

### I. Analyzing RNA-seq data with Salmon

*(Tutorial modified from the [Salmon](https://combine-lab.github.io/salmon/) official documentation)*

Today we will use **[Salmon](https://combine-lab.github.io/salmon/)** to align a transcriptome :fish:

[Salmon](https://combine-lab.github.io/salmon/) is a tool for quantifying the expression of transcripts using RNA-seq data. Salmon uses new algorithms (specifically, coupling the concept of *[quasi-mapping](https://academic.oup.com/bioinformatics/article/32/12/i192/2288985)* with a two-phase inference procedure) to provide accurate expression estimates very quickly (i.e. *wicked-fast*) and while using little memory. In Salmon it's all about quantification! 

:information_source: You could also use the STAR aligner, it is particularly good for all genomes where there are no alternatives alleles. For genomes such as hg38 that have alt alleles, hisat2 should be used as it handles the alts correctly and STAR does not yet. Use Tophat2 only if you do not have enough RAM available to run STAR (about 30 GB). The documentation for STAR is available [here](https://github.com/alexdobin/STAR/raw/master/doc/STARmanual.pdf).

### a) Obtaining the transcriptome and building the index 

In order to quantify transcript-level abundances, Salmon requires a target transcriptome. 

This transcriptome is given to Salmon in the form of a (possibly compressed) multi-FASTA file, with each entry providing the sequence of a transcript. 

For this example, we’ll be analyzing some *Arabidopsis thaliana* data, so we’ll download and index the *A. thaliana* transcriptome. 
- First, create a directory where we’ll do our analysis, let’s call it `salmon`: 

```sh
# Make a working directory on your ~/storage directory and go to it
mkdir ~/data/salmon
cd ~/data/salmon
```

- Download the transcriptome:

```sh
wget ftp://ftp.ensemblgenomes.org/pub/plants/release-28/fasta/arabidopsis_thaliana/cdna/Arabidopsis_thaliana.TAIR10.28.cdna.all.fa.gz -O athal.fa.gz
```

Here, we’ve used a reference transcriptome for Arabadopsis. However, one of the benefits of performing quantification directly on the transcriptome (rather than via the host genome), is that one can easily quantify assembled transcripts as well (obtained via software such as StringTie for organisms with a reference or Trinity for de novo RNA-seq experiments).

Next, we’re going to build an index on our transcriptome. The index is a structure that salmon uses to quasi-map RNA-seq reads during quantification. The index need only be constructed once per transcriptome, and it can then be reused to quantify many experiments. 

- Now we use the index command of salmon to build our index:

```
salmon index -t athal.fa.gz -i athal_index
```
More info on parameters for indexing [here](https://salmon.readthedocs.io/en/latest/)

### b) Obtaining sequencing data

In addition to the index, salmon obviously requires the RNA-seq reads from the experiment to perform quantification. In this tutorial, we’ll be analyzing data from this [4-condition experiment](https://www.ebi.ac.uk/ena/data/view/PRJDB2508)[accession PRJDB2508]. You can use the following shell script to obtain the raw data and place the corresponding read files in the proper locations. Here, we’re simply placing all of the data in a directory called `data`, and the left and right reads for each sample in a sub-directory labeled with that sample’s ID (i.e. `DRR016125_1.fastq.gz` and `DRR016125_2.fastq.gz` go in a folder called `data/DRR016125`).

```sh
#!/bin/bash
mkdir RNAseq
cd RNAseq
for i in `seq 25 28`; 
do 
  mkdir DRR0161${i}; 
  cd DRR0161${i}; 
  wget https://bioinfo.evolution.uzh.ch/share/data/bio634/RNAseq/DRR0161${i}/DRR0161${i}_1.fastq.gz; 
  wget https://bioinfo.evolution.uzh.ch/share/data/bio634/RNAseq/DRR0161${i}/DRR0161${i}_2.fastq.gz; 
  cd ..; 
done
cd .. 
```
- Place this commands in a script called `download_reads.sh`. You can use nano or gedit:
`nano download_reads.sh`
- Copy and paste the contents above, save the file with `Ctrl+S` and Exit with `Ctrl+X`
- To download the data, run the script and wait for it to complete:
```
bash download_reads.sh
```

### c) Quantifying the samples

Now that we have our index built and all of our data downloaded, we’re ready to quantify our samples. Since we’ll be running the same command on each sample, the simplest way to automate this process is, again, a simple shell script (`quant_samples.sh`):

```sh
#!/bin/bash
for fn in RNAseq/DRR0161{25..28};
do
samp=`basename ${fn}`
echo "Processing sample ${samp}"
salmon quant -i athal_index -l A \
         -1 ${fn}/${samp}_1.fastq.gz \
         -2 ${fn}/${samp}_2.fastq.gz \
         -p 2 --validateMappings -o quants/${samp}_quant
done 
```

- Place this commands in a script called `quant_samples.sh`. You can use nano:
`nano quant_samples.sh`
- Copy and paste the contents above, save the file with `Ctrl+S` and Exit with `Ctrl+X`
- To download the data, run the script and wait for it to complete:
```sh
bash quant_samples.sh
```

This script simply loops through each sample and invokes `salmon` using fairly barebone options. The `-i` argument tells salmon where to find the index `-l A` tells salmon that it should automatically determine the library type of the sequencing reads (e.g. stranded vs. unstranded etc.). The `-1` and `-2` arguments tell salmon where to find the left and right reads for this sample (notice, salmon will accept gzipped FASTQ files directly). Finally, the `-p 2` argument tells salmon to make use of 2 threads and the `-o` argument specifies the directory where salmon’s quantification results sould be written. Salmon exposes *many* different options to the user that enable extra features or modify default behavior. However, the purpose and behavior of all of those options is beyond the scope of this introductory tutorial. You can read about salmon’s many options in the [documentation](http://salmon.readthedocs.io/en/latest/).

After the salmon commands finish running, you should have a directory named `quants`, which will have a sub-directory for each sample. These sub-directories contain the quantification results of salmon, as well as a lot of other information salmon records about the sample and the run. The main output file (called `quant.sf`) is rather self-explanatory. For example, take a peek at the quantification file for sample `DRR016125` in `quants/DRR016125/quant.sf` and you’ll see a simple TSV format file listing the name (`Name`) of each transcript, its length (`Length`), effective length (`EffectiveLength`) (more details on this in the documentation), and its abundance in terms of Transcripts Per Million (`TPM`) and estimated number of reads (`NumReads`) originating from this transcript.

### d) After quantification

That’s it! Quantifying your RNA-seq data with salmon is that simple (and fast). Once you have your quantification results you can use them for downstream analysis with differential expression tools like [DESeq2](https://bioconductor.org/packages/DESeq2), [edgeR](https://bioconductor.org/packages/edgeR), [limma](https://bioconductor.org/packages/limma), or [sleuth](http://pachterlab.github.io/sleuth/). Using the [tximport](http://bioconductor.org/packages/tximport) package, you can import salmon’s transcript-level quantifications and optionally aggregate them to the gene level for gene-level differential expression analysis. You can read more about how to import salmon’s results into DESeq2 by reading the tximport section of the excellent [DESeq2 vignette](https://bioconductor.org/packages/DESeq2). For instructions on importing for use with edgeR or limma, see the [tximport vignette](http://bioconductor.org/packages/tximport). For preparing salmon output for use with sleuth, see the [wasabi](https://github.com/COMBINE-lab/wasabi) package.

### I. Importing transcript abundance datasets with tximport:

#### - Bioconductor - tximport

- You can use [Tximport](https://bioconductor.org/packages/release/bioc/vignettes/tximport/inst/doc/tximport.html#import-transcript-level-estimates) to import the quantification files obtained

#### - Install these packages in R:

```R
#Open R by typing R in the terminal:
R
# then run:
if (!requireNamespace("BiocManager", quietly = TRUE))
    install.packages("BiocManager")

BiocManager::install(c("TxDb.Hsapiens.UCSC.hg19.knownGene", "readr", "apeglm"))
```

#### - Load the tximport library in R:

**Note:** You can change `system.file`, for the `/path/to/dir` (Here we use `system.file` to locate the package directory, but for a typical use, we would just provide a path, e.g. `/path/to/dir`.)

```R
#Load the library
library(tximportData)
dir <- system.file("extdata", package = "tximportData")
list.files(dir)
```

Continue to follow the steps in the [workflow](https://bioconductor.org/packages/release/bioc/vignettes/tximport/inst/doc/tximport.html#import-transcript-level-estimates) of **Tximport** with the default dataset, or any dataset of your interest.


#### - Personal installation of tximport package in case you work with RNAseq data in the future (skip for today):

To install this package, start R (version "3.6") and enter ( This is for future usage, you already have this installed on your Docker installation):

```R
if (!requireNamespace("BiocManager", quietly = TRUE))
    install.packages("BiocManager")

BiocManager::install(c("tximport", "tximportData", "rhdf5", "csaw"))
```

### II. Analyzing RNA seq data with DESeq2:

If you already worked with Tximport on step I, you can start doing differential analyses on your quantified samples. Alternatively you can follow this guideline from the start and skip the previous exercise.

This [guideline](http://bioconductor.org/packages/devel/bioc/vignettes/DESeq2/inst/doc/DESeq2.html) explains the steps to follow when doing differential expression analyses with RNAseq data.

**NOTE**: Skip the Tximport part on this guideline and start from the **Count matrix input** section if you already did exercise I.


### III. Exploration of airway library (OPTIONAL): 

- To start let's install some R packages. In the terminal write the following:

#### Step 1: Install R packages (already installed in your Docker image)

```R
# Type the following on the R command line
if (!requireNamespace("BiocManager", quietly = TRUE))
    install.packages("BiocManager")
    BiocManager::install(c("VennDiagram","edgeR", "Matrix", "airway", "Rsamtools", "pasilla", "GenomicFeatures", "GenomicAlignments","BiocParallel", "Rsubread", "DESeq2"))
```
#### Step 2: Open **R**  

`$ R`

Now go to the following [link](https://www.bioconductor.org/help/course-materials/2016/CSAMA/lab-3-rnaseq/rnaseq_gene_CSAMA2016.html
) to where it says *"Locating BAM files and the sample table"* and start from there.


# Useful workflows for RNA-seq data analyses

- [Analyzing RNA-seq data with DESeq2, 2021](http://bioconductor.org/packages/devel/bioc/vignettes/DESeq2/inst/doc/DESeq2.html)
- [Differential Gene Expression using RNA-Seq. A Workflow](https://github.com/twbattaglia/RNAseq-workflow/blob/master/README.md)
- [RNA-seq workflow: gene-level exploratory analysis and differential expression, 2018](https://www.bioconductor.org/packages/devel/workflows/vignettes/rnaseqGene/inst/doc/rnaseqGene.html)
- [Importing transcript abundance datasets with Tximport, 2019 workflow](https://bioconductor.org/packages/release/bioc/vignettes/tximport/inst/doc/tximport.html).
-  [Analysis of Genomics Data with R/Bioconductor](https://ivanek.github.io/analysisOfGenomicsDataWithR/06_RNAseqFromFASTQtoCountData_html.html)
- [ARMOR (Automated Reproducible MOdular RNA-seq) a 2019 Snakemake RNA-seq workflow](https://github.com/csoneson/ARMOR).
- [RNA-seq workflow - gene-level exploratory analysis and differential expression, 2016](https://www.bioconductor.org/help/course-materials/2016/CSAMA/lab-3-rnaseq/rnaseq_gene_CSAMA2016.html).
