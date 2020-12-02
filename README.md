## BIO634 Next-Generation Sequencing 2 – Transcriptomes, Variant Calling and Biological Interpretation

## December 3-4th 2020


### University of Zurich
### URPP Evolution in Action
![URPP logo](Logo_URPP_kl2.png)

Carla Bello (carla.bello@ieu.uzh.ch) & Gregor Rot (gregor.rot@uzh.ch)

## Table of Content

### Day 1
&nbsp; | &nbsp; | &nbsp;
-------- | --- | --- 
9.30 - 9.40 | **Welcome & Introduction** | CB & GR
9.40 - 10:45 | **QC and Mapping** <br /> [Presentation](https://github.com/carlalbc/BIO634_2020/blob/master/Day1_QC_and_mapping/BIO634_Day1_DataQC_and_BWAmapping.pdf) \| [Hands-on](https://github.com/carlalbc/BIO634_2020/blob/master/Day1_QC_and_mapping/Day1_DataQC_and_mapping.md) | CB
10:45 - 11.00 | *Coffee break*
11.00 - 12.00 | **QC and Mapping: Continuation** | CB & GR
12.00 - 13.30 | *Lunch break*
13.30 - 14.30 | **Variant Calling 2** <br /> [Presentation](variant_calling/variant_calling_presentation.pdf)  \| [Hands-on](variant_calling/variant_calling_exercises.md) | GR & CB
14.30 - 14.45 | *Coffee break*
14.45 - 16.00 | *Talk:* Dr. Jean-Claude Walser (ETH): <br /> RNA-seq in ecology and evolutionary biology [pdf](https://github.com/carlalbc/BIO634_2019/blob/master/UniZH_Bio634_JCW_190603.pdf) -CANCELLED-

### Day 2
&nbsp; | &nbsp; | &nbsp;
-------- | --- | --- 
9.30 - 10.45 | **RNA-seq** <br /> [Presentation](https://github.com/carlalbc/BIO634_2020/blob/master/Day2_RNAseq/BIO634_Day2_RNAseq.pdf) \| [Hands-on](https://github.com/carlalbc/BIO634_2020/blob/master/Day2_RNAseq/Day2_RNAseq.md) | CB 
10.45 - 11:00 | *Coffee break*
11.00 - 12.00 |  **Continuation: RNA-seq**  | CB & GR
12.00 - 13.30 | *Lunch break* 
13.30 - 14.30 |  **Continuation: RNA-seq**  | CB & GR
14.30 - 14.45 | *Coffee break* |
14:45 - 16:00 |  **Making sense of gene lists** <br /> [Presentation](gene_lists/gene_lists_presentation.pdf)  \| [Hands-on](gene_lists/gene_lists_exercises.md) | GR & CB

## Prerequisites for the course

- Basic command line 
- Basic knowledge about NGS data structure: reads, alignments (BAM), quality scores or attendance of 'BIO609 Introduction to Linux and Bash Scripting' and `BIO610 Next-Generation Sequencing 1 – Introductory Course: Assembly, Mapping, and Variant Calling`

## Directory structure

```
(Unix System hierarchical tree)

/home/student
            /software
            /variantcalling2
            /rnaseq
            /data
                /data_ngs2
```

## Installation Instructions for Docker

Install [Docker](https://www.docker.com/). Download the [bio634.tar](https://bioinfo.evolution.uzh.ch/teaching/bio634.tar) file. Load the Docker image to the file:

```
docker load -i bio634.tar
```

Create a storage folder on your local computer. Then you can start the docker container by running:

```
docker run -v /storage:/home/student/storage --hostname ubuntu --user student --workdir="/home/student" -ti bio634 bash --login
```
In the case above, the local folder is `/storage` (replace with your own local folder), which will be mounted on `/home/student/storage`.

If you are using Windows, please put the local folder in quotes, an example would be:

```
docker run -v "C:\Users\username\storage":/home/student/storage --hostname ubuntu --user student --workdir="/home/student" -ti bio634 bash --login
```

We will run most exercises in this Docker container, which has all required software pre-installed.

## Recommended books (Practical Computing Skills)

- [Haddock & Dunn. Practical Computing for Biologists. Sinauer Associates 2011.](http://practicalcomputing.org)  
  A good book that covers the shell/command line, programming in python & bash, databases, regular expressions. 
  Suitable for self-study and as a reference book.

- [Vince Buffalo. Bioinformatics Data Skills. O'reilly 2015](http://shop.oreilly.com/product/0636920030157.do)  
  This practical book teaches the skills that scientists need for turning large sequencing datasets into reproducible and robust biological findings.
  Also covers methods on Sequence and Alignment Data. 
  More advanced than Haddock & Dunn and progresses with faster pace.


## Recommended websites

**General**  
- <http://software-carpentry.org/>  
  Scientific Computing Resources for learning bash shell, programming in python, R, …]  
- [SEQanswers](http://seqanswers.com/) the NGS community (Questions&Answers, protocols, software lists, news)   
- [BioStars](https://www.biostars.org/) for questions about biocomputing and scripting for biologists  
- [stackoverflow](http://stackoverflow.com/) for questions related to coding

**Linux/Shell**  
- [Cheatsheet intermediate](http://www.cheatography.com/davechild/cheat-sheets/linux-command-line/pdf/)  
- [SIB e-learning: UNIX fundamentals](http://edu.isb-sib.ch/pluginfile.php/2878/mod_resource/content/3/couselab-html/content.html)
