# acyeh2022

Scripts are used to calculate "selective expanders" (TCRs from a single donor pool that differentially expand between 2 recipients) in Yeh AC et al, 2022.
Please read below for overview of how to run scripts

# Data preparation
Input files needed to run scripts include:
1) donor TCR sequences
2) recipient 1 TCR sequences
3) recipient 2 TCR sequences

File structure for each of the above must contain the following named columns:
1) "rearrangement" - a unique string containing CDR3 nucleotides (e.g. "CTCTGCAGCCTGGGAATCAGAACGTGCGAAGCAGAAGACTCAGCACTGTACTTGTGCTCCAGCAGTCAAAGGGGTGACACCCAGTAC")
2) "templates" - is the number of counts identified from sequencing (e.g. "11")
3) "frame_type" - is either "In" or "Out" - all frame_types set to "Out" are omitted from calculation (this column is derived from Adaptive's TCRB (v3) assay). If want to use all TCRs in file, set all templates to "frame_type" equal to "In"

# Scripts
There are 2 scripts provided (run sequentially):
1) Priors Generation Script: This is used to generate a log-log plot of TCR count vs. clonality of the donor pool, which is used as a Bayesian prior.  The output of this file named "exp_coeff_matrix.csv" will be used as input for the next Script.
2) Selective Expanders Script: This is used to calculate a p-value for each TCR specified from the input files used in the manuscript. Significant p-values represent clones that are "selectively expanded". The output of this file will have the suffix "- final probabilities.csv".

Notes:
1) The first script should run fairly quickly on a typical computing desktop (a few minutes for a file with ~1M naive TCRs)
2) The second script typically takes several hours (and potentially up to several days for pools larger clone sizes) when run on donor and recipient files with ~100-200k TCR counts.
3) The second script is  described in Supplementary Materials and Methods - "Probabilistic Simulation of Post-transplant TCR Expansion"

# Quick Start
Let's first download donor data file (see Zenodo link under samples folder). These should include the following file: `1901T-donor-spleen-all-supermerge.csv`.

Next, download the script "[Cluster] Publication - Priors Generation.R", change the working directory in the script to where the donor data file is located, and make sure the proper donor file is referred to:
```setwd("C:/...") #Specify file directory```
```donor_file <- fread("1901T-donor-spleen-all-supermerge.csv") #Specify file name```
