# Making sense of gene lists

### URPP Evolution in Action

We will practise the 3 different ways of making sense of a gene list we have discussed in the presentation.

Please clone this repository into the storage folder with:

```
cd /home/student/data
git clone https://github.com/carlalbc/BIO634_2020
```

and work in the ***gene_lists*** folder.

## Exercise 1: Over-Representation / Enrichment Analysis

Over-representation analysis is simply a 2x2 contigency table separately for each gene. 

There are many online tools available for enrichment analysis, but many tools are not recommendable. Before you use one, make sure the tool  
- lets you define the background (i.e. all measured genes)  
- uses an up-to-date annotation  
- does multiple testing correction (e.g. FDR)  

A good choice is e.g. [GOrilla](http://cbl-gorilla.cs.technion.ac.il/), and for R/Bioconductor, we recommend the [topGO](http://www.bioconductor.org/packages/release/bioc/html/topGO.html) package. 
 
**Some tips**  
Generally ignore categories  
- with very few genes  
- with large overlap with other categories 

Often you get back a huge list of significantly enriched terms which is time-consuming to digest. [REViGO](http://revigo.irb.hr/) 
([PMID:21789182](http://www.ncbi.nlm.nih.gov/pubmed/21789182)) takes long lists of Gene Ontology terms and summarizes them by
removing redundant GO terms. It also makes summary plots.


### Exercise

Try out GOrilla with the list of genes overexpressed in Adult mice relative to Newborns, stored in the diffExp_N-A.txt file.  
  
First, we need to prepare the input files (1 gene name per line). We take the 734 genes overexpressed in Adult relative to Newborn mice
(from the RNA-seq tutorial) with a FDR < 5%. 
```
awk '$5<1 && $8<=0.05 {print $1}' diffExp_N-A.txt  | sed 's/"//g' > diffExp_N_smallerThan_A.FDR5perc.txt
awk 'NR>1 {print $1}' diffExp_N-A.txt  | sed 's/"//g' > diffExp_N-A.allGenes.txt
```

Run an over-representation analyis using [GOrilla](http://cbl-gorilla.cs.technion.ac.il/). Use the basic functionality <Two unranked lists of genes (target and background lists)> and upload the 2 files we just prepared. In step 4 choose <Process> and run the analysis.

Look at the table output below the figure. Click at some significantly enriched processes for the full description. It is well visible from the graph the parents of highly significantly GO categories often also become significant (Parents contain all the genes of their children).  
  
Try out GOrilla with the list of differentially expressed genes from the RNA-seq session.

### Network analysis with STRING

As an example for network analysis we try out STRING.  
  
STRING stands for *S*earch *T*ool for the *R*etrieval of *I*nteracting *G*enes/Proteins. It is a webtool and database for retrieving networks of interacting genes. The interactions include direct (physical) and indirect (functional) associations. STRING integrates previous knowledge from known experimental interactions and pathway databases, it does automated text-mining of the literature but it also covers the less well understood portion of gene interactions as predicted *de novo* by a number of algorithms using genomic information. Importantly, each association has a probabilistic confidence score.  
 
The current version v10.5 comprises more than 2'000 species while interaction information is transferred between species.


### Exercise

The gene list we use here is a list of significantly up-regulated genes in the *met1* background in *Arabidopsis thaliana*
defective in methylation maintenance (PMID:18423832).  
(obtain a list from Table S2 from the publication [PMID:23146178](http://onlinelibrary.wiley.com/doi/10.1111/tpj.12070/abstract)
The gene list is not significantly enriched in any Gene Ontology terms or in a KEGG pathway.

------------

1. Open http://string-db.org in your browser
2. STRING accepts different types of input, often we want to lookup a gene list, but sometimes only a single gene.
  Here we have a gene list so click on `search/multiple proteins` and copy & paste the gene list into the search field.
3. Type Arabidopsis in the `organism` and choose `Arabidopsis thaliana`
  Press the `GO!` button.
4. Next you see a page with suggested matches. Press `Continue ->` to accept all suggestions.
5. Now we get to a page with the result network. Each dot is a gene/protein of the input list. The lines connecting genes denote
known or predicted interactions with different line colors representing the types of evidence for the association. 
6. Click on a gene to get more information about it (and links out to other databases). 
  Hover and click on some lines and find out where the evidence comes from. Right-Click on <Show> to see the origin.
  Click on some green lines and right-click on <Show> to show the relevant publications co-mentioning the gene pair.  
7. If you scroll down further you see "Your input", the input gene(s) with descriptions.
  Below the network there is a "Settings" tab where you can switch on/off individual prediction methods and change
the required minimum confidence score.
  Change the confidence, press <Update> and check the effect on the resulting networks.
8. Below the network is a taskbar with icons "+" and "-" Here you can grow the network by 10 more partners. By pressing the "+ (more)" button 10 genes not present in your list are added to the network so that your input genes are maximally connected. These newly added genes (in white color) are candidates for working in the same process/pathway as your input genes (Find more information about them in the <Predicted Functional Partners:> window at the bottom of the page) 
Press the "+ (more)" button multiple times and see how the network grows (the same functionality you can get from the "Settings" tab: "max number of interactors to show").
9. In the tab "Analysis", you can see enrichment analysis with Gene Ontology and KEGG pathways.   
10. In the tab "Clusters", you can apply different cluster algorithms on your network.    

Make an Enrichment analysis with "GO Biological Processes" and "KEGG pathways"
Remove genes not connected to any other genes by going to "Settings", "hide disconnected nodes in the network".

## Gene Set Enrichment Analysis (GSEA)

Gene Set Enrichment Analysis (GSEA) is a more sensitive and robust alternative to Over-Representation Analysis. It does not require a (somewhat
arbitrary) significance cut-off but it uses the ranked list of all genes. Here we only mention GSEA as an option. Please feel free to explore this approach at [GSEA web page](https://www.gsea-msigdb.org/gsea/index.jsp). Additionally, many derivatives of the GSEA methods are now available in Bioconductor with more precise statistics, a [comprehensive list is here](http://www.bioconductor.org/packages/release/BiocViews.html#___Pathways).

## Tips

- Often you get back a huge list of significantly enriched terms which is difficult to digest. [REViGO](http://revigo.irb.hr/) ([PMID:21789182](http://www.ncbi.nlm.nih.gov/pubmed/21789182)) takes long lists of Gene Ontology terms and summarizes them by removing redundant GO terms. It also makes summary plots.
 
- Interesting tool seen during the preparation of the course: https://github.com/tanghaibao/goatools
