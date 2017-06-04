# Introduction

In metagenomics, information about micro-organisms in an environment can be extracted with two main techniques:

- Amplicon sequencing, which sequence only on the rRNA/rDNA of organisms
- Whole-genome sequencing (WGS), which sequence full genomes of the micro-organisms in the environment

Data generated from these two techniques must be treated differently. In this tutorial, we will focus on the analysis of whole-genome sequencing. 

> ### :nut_and_bolt: Comments
> If you want to learn how to analyze amplicon data, please check our dedicated tutorials
{: .comment}

From both amplicon and WGS metagenomics raw data, we can extract information about which micro-organisms are present in the studied environment. But, contrary to amplicon, WGS metagenomics data contain also full genome information about the micro-organisms. It is then possible to identify genes associated to functions, to reconstruct metabolic pathways and then determine which functions are done by the micro-organisms in the studied environment. It is even possible to go further and determine which micro-organisms are involved in a given function or pathways.

> ### Agenda
>
> However, extraction of useful information from raw WGS metagenomics sequences is a complex process
with numerous bioinformatics steps and tools to use. These steps can be get together in 4 main steps we will deal with in the following tutorial:
>
> 1. [Pretreatments](#pretreatments)
> 2. [Taxonomic analyses](#taxonomic_analyses)
> 3. [Functional analyses](#functional_analyses)
> 4. [Combination of taxonomic and functional results](#taxonomic_functional_analyses)
> {: .agenda}

In this tutorial, we will work on a sample from ..., sequenced with ... This sample has already been analyzed with the [EBI Metagenomics' pipeline](), which use slightly different tools than the ones we will use here. But, we could compare our results with the ones obtained with EBI Metagenomics.

# Pretreatments

Before any extraction of information about the community, raw sequences have to be pre-processed with quality control of the raw sequences and sequence sorting. But, first, we need in get our data in Galaxy.

## Data upload

The original data are available at EBI Metagenomics under run number [...](...).

> ### :pencil2: Hands-on: Data upload
>
> 1. Create a new history for this WGS metagenomics exercise
> 2. Import the FASTQ file pair from [Zenodo]()
>
>    > ### :bulb: Tip: Importing data via links
>    >
>    > * Copy the link location
>    > * Open the Galaxy Upload Manager
>    > * Select **Paste/Fetch Data**
>    > * Paste the link into the text field
>    > * Press **Start**
>    {: .tip}
>
>    As default, Galaxy takes the link as name. It also do not link the dataset to a database or a reference genome.
>
{: .hands_on}

## Quality control and treatment

For quality control, we use similar tools as described in [the Quality Control tutorial](../../NGS-QC/tutorials/dive_into_qc): [FastQC](http://www.bioinformatics.babraham.ac.uk/projects/fastqc/) and [Trim Galore](http://www.bioinformatics.babraham.ac.uk/projects/trim_galore/).

> ### :pencil2: Hands-on: Quality control
>
> 1. **FastQC** :wrench:: Run FastQC on both FastQ files to control the quality of the reads
>
>    > ### :question: Questions
>    >
>    > 1. What is the read length?
>    > 2. Is there anything what you find striking when you compare both reports?
>    >
>    >    <details>
>    >    <summary>Click to view answers</summary>
>    >    <ol type="1">
>    >    <li>The read length is 37 bp</li>
>    >    <li>Both reports for GSM461177_untreat_paired_chr4_R1 and for GSM461177_untreat_paired_chr4_R2 are quite ok. For GSM461177_untreat_paired_chr4_R1, there is several warnings and an issue on the Kmer content. For GSM461177_untreat_paired_chr4_R2, the quality in the 2nd tile is bad (maybe because of some event during sequencing). We need to be careful for the quality treatment and to do it with paired-end information</li>
>    >    </ol>
>    >    </details>
>    {: .question}
>
> 2. **Trim Galore** :wrench:: Treat for the quality of sequences by running Trim Galore on the paired-end datasets to eliminate sequences smaller than 60 bp, with a mean quality score inferior to 15 or with more than 2% of N bases and to trim sequences on right end when the mean quality score over a window of 5 bp is inferior to 20
>
>    > ### :question: Questions
>    >
>    > Why is Trim Galore run once on the paired-end dataset and not twice on each dataset?
>    >
>    > <details>
>    > <summary>Click to view answers</summary>
>    > Trim Galore can remove sequences if they become too short during the trimming process. For paired-end files Trim Galore! removes entire sequence pairs if one (or both) of the two reads became shorter than the set length cutoff. Reads of a read-pair that are longer than a given threshold but for which the partner read has become too short can optionally be written out to single-end files. This ensures that the information of a read pair is not lost entirely if only one read is of good quality.
>    > </details>
>    {: .question}
>
> 3. **FastQC** :wrench:: Re-run FastQC on Trim Galore's outputs and inspect the differences
>
>    > ### :question: Questions
>    >
>    > 1. How are the changes in the read length?
>    > 2. Is there any characteristics impacted by Trim Galore?
>    >
>    >    <details>
>    >    <summary>Click to view answers</summary>
>    >    <ol type="1">
>    >    <li>The read length is then now from 20 to 37 bp</li>
>    >    <li>For GSM461177_untreat_paired_chr4_R1, the per base sequence content is now red. For GSM461177_untreat_paired_chr4_R2, the per tile sequence quality is still bad but now also the per base sequence content and the Kmer Content</li>
>    >    </ol>
>    >    </details>
>    {: .question}
>
{: .hands_on}

> ### :question: Questions
>
> 1. How many sequences have been conserved here? 
> 2. And with EBI Metagenomics' pipeline?
>
> <details>
> <summary>Click to view answers</summary>
> <ol type="1">
> <li></li>
> <li></li>
> </ol>
> </details>
> {: .question}

> ### :nut_and_bolt: Comments
> 
> One sequence file is expected for the next steps. In case of paired-end sequence data, the paired sequences must be assembled, with FastQJoin for example
{: .comment}

# Dereplication

During sequencing, one sequence must have been added to the dataset in multiple exact copy. Removing such duplicates reduce the size of the dataset without loosing information, with the dereplication (identification of unique sequences in a dataset).

> ### :pencil2: Hands-on: Dereplication
>
> 1. **VSearch dereplication** :wrench:: Run VSearch dereplication on the quality controlled sequences (output of Trim Galore!)
>
>    > ### :question: Questions
>    >
>    > 1. How many sequences are removed with the dereplication?
>    > 2. And with EBI Metagenomics' pipeline?
>    >
>    >    <details>
>    >    <summary>Click to view answers</summary>
>    >    <ol type="1">
>    >    <li></li>
>    >    <li></li>
>    >    </ol>
>    >    </details>
>    {: .question}
>
{: .hands_on}

# Sequence sorting

With WGS metagenomics data, full genome information can be accessed: information corresponding to CDS of the micro-organisms, sequences corresponding to ribosomal sequences (rDNA or rRNA) of the micro-organisms, ... Useful functional information are present in sequences corresponding to CDS, and some taxonomic information in sequences corresponding to ribosomomal sequences (like the amplicon). To reduce the dataset size for the extraction of functional information, we can remove rRNA/rDNA sequences from the original dataset. 

This task is also useful to inspect the rRNA/rDNA sequences. And as in EBI Metagenomics' pipeline, these sequences can be used for taxonomic analyses as any amplicon data

> ### :nut_and_bolt: Comments
> If you want to learn how to analyze amplicon data, please check our dedicated tutorials
{: .comment}

For this task, we use SortMeRNA (kopylova_sortmerna:_2012). This tool filter RNA sequences based on local sequence alignment (BLAST) against 8 rRNA databases (2 Rfam databases for 5.8S and 5S eukarya sequences and 6 SILVA datasets for 16S (archea and bacteria), 18S (eukarya), 23S (archea and bacteria) and 28S (eukarya) sequences.

> ### :pencil2: Hands-on: Sequence sorting
>
> 1. **SortMeRNA** :wrench:: Run SortMeRNA on the dereplicated sequences with the selection of all rRNA databases (to extract all the rRNA sequences)
>
>    > ### :question: Questions
>    >
>    > 1. Which percentage of the original data are assigned to rRNA/rDNA sequences?
>    > 2. How can you explain with low percentage?
>    > 3. How many sequences are identified as 16S sequences? And 18S sequences?
>    > 4. What are the values for EBI Metagenomics?
>    >
>    >    <details>
>    >    <summary>Click to view answers</summary>
>    >    <ol type="1">
>    >    <li></li>
>    >    <li></li>
>    >    </ol>
>    >    </details>
>    {: .question}
>
> 2. (Optional) **SortMeRNA** :wrench:: Run SortMeRNA on the selected rRNA sequences with the selection of 16S rRNA databases
> 3. (Optional) **SortMeRNA** :wrench:: Run SortMeRNA on the selected rRNA sequences with the selection of 18S rRNA databases
{: .hands_on}

# Extraction of taxonomic information

The first important information to extract from any metagenomics sample is which micro-organisms are present in the sequenced sample and in which proportion are they present. So we want to extract information about the structure of the community of micro-organisms in the environment.

To identify the community structure, several approaches can be used. With amplicon or rRNA data, the sequences are clustered into Operational Taxonomic Units (OTU) and one representative sequence of each OTU is assigned to the most plausible microbial lineage. This approach is possible because of rRNA data: data that evolved quite slowly compared to other part of genomes, rRNA sequences data (particularly 16S and 18S) are then well conserved and good taxonomic markers.

However, for WGS data, applying such approaches implies using a really small proportion of sequences for the taxonomic assignation, which can induce statistical bias. Other approaches have been developed to cope with WGS data. For example, MetaPhlAn2 (segata_metagenomic_2012,truong_metaphlan2_2015) uses a database of ~1M unique clade-specific marker genes (not only the rRNA genes) identified from ~17,000 reference (bacterial, archeal, viral and eukaryotic) genomes.

> ### :pencil2: Hands-on: Taxonomic assignation
>
> 1. **MetaPhlAN2** :wrench:: Run **MetaPhlAN2** on the dereplicated sequences
>
>    > ### :question: Questions
>    >
>    > 1. What does the main output file contain?
>    >
>    >    <details>
>    >    <summary>Click to view answers</summary>
>    >    <ol type="1">
>    >    <li></li>
>    >    <li></li>
>    >    </ol>
>    >    </details>
>    {: .question}
>
> 2. **Format MetaPhlAn2 output** :wrench:: Run **Format MetaPhlAn2 output** on the MetaPhlAn2 output to extract information about each taxonomic level
>
>    > ### :question: Questions
>    >
>    > 1. Which species is the most represented?
>    > 2. Can you compare to the results of EBI Metagenomics?
>    >
>    >    <details>
>    >    <summary>Click to view answers</summary>
>    >    <ol type="1">
>    >    <li></li>
>    >    <li></li>
>    >    </ol>
>    >    </details>
>    {: .question}
>
{: .hands_on}

The generated information remains difficult to inspect. Krona (ondov_interactive_2011) is a visualization tool for intuitive exploration of relative abundances of taxonomic classifications. It produces an interactive HTML file

> ### :pencil2: Hands-on: Interactive visualization with KRONA
>
> 1. **Format MetaPhlAn2 output for Krona** :wrench:: Run **Format MetaPhlAn2 output for Krona** to format MetaPhlAn2 output for KRONA
> 2. **KRONA** :wrench:: Run **KRONA** on the formatted MetaPhlAn2 output
>
>    > ### :question: Questions
>    >
>    > 1. What are the main species found for the bacteria?
>    > 2. Is the proportion of ... similar to the one found with EBI Metagenomics?
>    >
>    >    <details>
>    >    <summary>Click to view answers</summary>
>    >    <ol type="1">
>    >    <li></li>
>    >    <li></li>
>    >    </ol>
>    >    </details>
>    {: .question}
>
{: .hands_on}

Alternatively, [GraPhlAn](https://bitbucket.org/nsegata/graphlan/wiki/Home>) is a tool for producing circular static representation of taxonomic analyses, easily exportable.

> ### :pencil2: Hands-on: Static visualization with GraPhlAn
>
> 1. **export2graphlan** :wrench:: Run **export2graphlan** on MetaPhlAn2 output to extract a tree with parameter
> 
>    - Levels to annotate in the tree: 5
>    - Levels to annotate in the external legend: 6,7
>    - Title font size: 15
>    - Default size for clades not found as biomarkers: 10
>    - Minimum value of biomarker clades: 0
>    - Maximum value of biomarker clades: 250
>    - Font size: 10
>    - Minimum font size: 8
>    - Maximum font size: 12
>    - Font size for the annotation legend: 11
>    - Minimum abundance value for a clade to be annotated: 0

>    > ### :nut_and_bolt: Comments
>    > We decide to display the maximum of clade (100, here). If you want more or less, you can modulate the number of clades to highlight. And if you want to change displayed annotations, you can change levels to annotate.
>    {: .comment}
>
>    - Number of clades to highlight: 100
>    - Row number contaning the names of the features: 0
>    - Row number containing the names of the samples: 0
>
> 2. **Modify an input tree for GraPhlAn** :wrench:: Run **Modify an input tree for GraPhlAn** on the export2graphlan's outputs to combine them into a PhyloXML file
> 3. **GraPhlAn** :wrench:: Run **GraPhlAn** on the previous step's outputs
>
>    > ### :question: Questions
>    >
>    > 1. What are the main species found for the bacteria?
>    > 2. Is the proportion of ... similar to the one found with EBI Metagenomics?
>    >
>    >    <details>
>    >    <summary>Click to view answers</summary>
>    >    <ol type="1">
>    >    <li></li>
>    >    <li></li>
>    >    </ol>
>    >    </details>
>    {: .question}
>
{: .hands_on}

With our dataset, we obtain a nice graphical representation of taxonomic diversity inside our sample, with circle radius being proportional to relative abundance of the corresponding clade.

> ### :question: Questions
>
> 1. Is the main species the same as the one observed with KRONA? 
>
> <details>
> <summary>Click to view answers</summary>
> <ol type="1">
> <li></li>
> <li></li>
> </ol>
> </details>
> {: .question}

# Functional analyses

Investigation of sample composition give an insight on "What organisms are present in our sample". We now want to know "What are they doing in that sample ?", with metabolic analyses.

## Metabolic analysis

For this investigation, we need to affiliate sequences to a protein database. We choose for this task to use `HUMAnN2 <http://huttenhower.sph.harvard.edu/humann2>`_. HUMAnN profiles the presence/absence and abundance of gene families and microbial pathways in a community from metagenomic or metatranscriptomic sequencing data.

HUMAnN2 is available in `Analyze metabolism` (`Functional assignation` section). We execute HUMAnN2 on non rRNA sequences:

.. _humann2_param:

.. figure:: /assets/images/framework/tutorial/humann2_param.png

3 output files are generated:

- A file with abundance of found UniRef50 gene families
- A file with coverage of found Metacyc pathways
- A file with abundance of found Metacyc pathways

These 3 files give detailed insights into gene families and pathways. This is interesting when we want to look to a particular pathway or to check abundance of a given gene families. However, when we want a broad overview of metabolic processes in a community, we need tools to regroup gene families or pathways into global categories.

## Broad overview of metabolic analysis

To get global categories from HUMAnN2 outputs, we decide to use `Gene Ontology <http://geneontology.org/>`_. Gene ontology project is a collaborative project to get a 3 structures ontologies to describe gene products in terms of their associated biological processes, cellular components and molecular functions. 

HUMAnN2 gives opportunity to regroup UniRef50 gene family abundances into GO abundances. However, these GO terms are still too precise to get a good overview of metabolic processes. 

Gene Ontology Consortium proposes `GO slim <http://geneontology.org/page/go-slim-and-subset-guide>`_, which are cut-down versions of the GO ontologies to give a broad overview of the ontology content. In our case, we use metagenomic GO slim terms developed by Jane Lomax and the InterPro group. 

To regroup HUMAnN2 output containing UniRef50 gene family abundances into abundances of metagenomic GO slim term, we use `Group humann2 uniref50 abundances to Gene Ontology (GO) slim terms <https://github.com/ASaiM/group_humann2_uniref_abundances_to_GO>`_ :cite:`batut_group_humann2_uniref_abundances_to_go:_2016`. This tool uses `GoaTools <https://github.com/tanghaibao/goatools>`_ :cite:`tang_goatools:_2016` to map GO terms to GO slim terms, HUMAnN2 to regroup abundances of UniRref50 gene families into abundances of metagenomc GO slim terms and custom Python scripts.

Tool to group HUMAnN2 UniRef50 abundances to Gene Ontology (GO) slim terms is available in `Analyse metabolism` (`Functional assignation` section). We execute it on HUMAnN2 output containing UniRef50 gene family abundance:

.. _group_humann2_uniref_abundances_to_go_param:

.. figure:: /assets/images/framework/tutorial/group_humann2_uniref_abundances_to_go_param.png

This tool generates 3 tabular outputs:

- A file with abundances of GO terms corresponding to molecular functions
- A file with abundances of GO terms corresponding to biological processes
- A file with abundances of GO terms corresponding to cellular components

## Visualization of metabolic analysis

The 3 previously generated tabular files with relative abundances of Gene Ontology slim terms can be visualised with barplots. A tool `Plot barplot with R` is available in `Visualize data` (`Post treatments` section):

.. _plot_barplot:

.. figure:: /assets/images/framework/tutorial/plot_barplot.png

Several graphical options are available such as margins, labels, bar color, ...

For our dataset, we execute this tool 3 times to obtain the following 3 graphics 

.. _cellular_component_abundance:

.. figure:: /assets/images/framework/tutorial/cellular_component_abundance.png
    :scale: 50 %

    Relative abundance of GO slim terms corresponding to cellular components
    

.. _biological_process_abundance:

.. figure:: /assets/images/framework/tutorial/biological_process_abundance.png
    :scale: 50 %

    Relative abundance of GO slim terms corresponding to biological processes


.. _molecular_function_abundance:

.. figure:: /assets/images/framework/tutorial/molecular_function_abundance.png
    :scale: 50 %

    Relative abundance of GO slim terms corresponding to molecular fonctions

Our analyses are done, you may want to :ref:`download the generated files <framework-tutorial-download>` and to :ref:`extract these numerous step into a workflow to reproduce it <framework-tutorial-transform>`. When you are done, do not forgot to :ref:`stop the Galaxy instance and clean the environment <framework-tutorial-clean>`. 

.. bibliography:: /assets/references.bib
   :cited:
   :style: plain
   :filter: docname in docnames

# Combination of taxonomic and functional results

.. _framework-tutorial-taxonomic-functional-analysis:

# Combination of taxonomic and functional analyses

# Conclusion

Conclusion about the technical key points. And then relation between the technics and the biological question to end with a global view.