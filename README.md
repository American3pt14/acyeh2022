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
Let's first download donor data file (see Zenodo link under samples folder). This should include the following donor file: `1901T-donor-spleen-all-supermerge.csv`.

Next, download the script "[Cluster] Publication - Priors Generation.R", change the working directory in the script to where the donor data file is located, and make sure the proper donor file is referred to:
```
setwd("C:/...") #Specify file directory
donor_file <- fread("1901T-donor-spleen-all-supermerge.csv") #Specify file name
```
The output of the above file should be named `exp_coeff_matrix.csv` and represents the coefficients for the log-log plot of the TCR clonality sizes vs. frequency. This is necessary to generate a "prior probability" for downstream Bayesian analysis

Now, let's download 2 recipient data file (see Zenodo link under samples folder). To begin with, we can download the following files: `1901T-Gp1-1-spleen.tsv` and `1901T-Gp1-2-spleen.tsv`.  Place these two files in the same directory as `exp_coeff_matrix.csv` and '1901T-donor-spleen-all-supermerge.csv'.

Next, download the script "[Cluster] Publication - Selective Expanders.R", change the working directory in the script to where the donor data file is located, and make sure the proper donor file is referred to:
```
analysis_dir <- "/..."
outputdir <- paste0(analysis_dir,"/Analysis") # Output files will be sent to "/Analysis" subdirectory
```
Make sure under the File Input Requirements heading, the appropriate file are referred to:
```
donor_pool <- fread("1901T-donor-spleen-all-supermerge.csv")  
# File structure must contain the following named columns: 1) "rearrangement", 2) "templates", 3) "frame_type", where:
# 1) "rearrangement" is a unique string containing CDR3 nucleotides (e.g. "CTCTGCAGCCTGGGAATCAGAACGTGCGAAGCAGAAGACTCAGCACTGTACTTGTGCTCCAGCAGTCAAAGGGGTGACACCCAGTAC")
# 2) "templates" is the number of counts identified from sequencing (e.g. "11")
# 3) "frame_type" is either "In" or "Out" - we omit all frame_types that are set to "Out"

## 2) Sequenced recipient pool 1
recipient1 <- read.table(file = "1901T-Gp1-1-spleen.tsv", sep = '\t', header = TRUE)  
# File structure must contain the following named columns: 1) "rearrangement", 2) "templates", 3) "frame_type" as above

## 3) Sequenced recipient pool 2
recipient2 <- read.table(file = "1901T-Gp1-2-spleen.tsv", sep = '\t', header = TRUE)  
# File structure must contain the following named columns: 1) "rearrangement", 2) "templates", 3) "frame_type" as above

## 4) Coefficient matrix: (Generated from donor sequences using the script "[Cluster] Publication - Priors Generation")
# This file contains output for 3 variables: "a","b","x_c" as seen in Figure 1B that describe the rate of clone size decay y=a*x^(-b), with x_c being the x-intercept
coefficient_matrix <- "exp_coeff_matrix.csv"
```
