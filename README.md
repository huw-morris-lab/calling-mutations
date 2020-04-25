# calling-mutations
Steps to call known pathogenic variants/mutations

Date: April 2020

Last updated: 25/04/2020

Authors: Manuela Tan

## General description and purpose

QC and data cleaning for SNP array data (e.g. NeuroChip)

This covers:
1. Extracting a candidate list of variants from a SNP array dataset after QC
2. Filtering in R to generate a list of individuals who carry variants of interest

This should be done after general QC and probably imputation as well (depending on whether your variants of interest have been directly genotyped or need to be imputed).

If you are interested in rare variants, you may want to change the allele frequency filter in the QC steps. Also you should probably impute using the TOPMed reference panel as this has better imputation for rare variants, as well as African and admixed American samples.
https://twitter.com/umimpute/status/1248365777843085313?ref_src=twsrc%5Etfw%7Ctwcamp%5Eembeddedtimeline%7Ctwterm%5Eprofile%3Aumimpute&ref_url=https%3A%2F%2Fimputationserver.sph.umich.edu%2Findex.html%23!
https://imputation.biodatacatalyst.nhlbi.nih.gov/


# 1. Extract list of variants


