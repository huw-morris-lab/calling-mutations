# calling-mutations
Steps to call known pathogenic variants/mutations or other variants of interest.

Date: April 2020

Last updated: 25/04/2020

Authors: Manuela Tan

## General description and purpose

QC and data cleaning for SNP array data (e.g. NeuroChip)

This covers:
1. Extracting a candidate list of variants from a SNP array dataset after QC
2. Filtering in R to generate a list of individuals who carry variants of interest

This should be done after general QC and probably imputation as well (depending on whether your variants of interest have been directly genotyped or need to be imputed). The variant lists I have curated are from the non-imputed data, so if you have imputed your data you will be working with different SNP names.

If you are interested in rare variants, you may want to change the allele frequency filter in the QC steps. Also you should probably impute using the TOPMed reference panel as this has better imputation for rare variants, as well as African and admixed American samples.

https://twitter.com/umimpute/status/1248365777843085313?ref_src=twsrc%5Etfw%7Ctwcamp%5Eembeddedtimeline%7Ctwterm%5Eprofile%3Aumimpute&ref_url=https%3A%2F%2Fimputationserver.sph.umich.edu%2Findex.html%23!

https://imputation.biodatacatalyst.nhlbi.nih.gov/


# 1. Extract list of variants

Extract SNPs of interest with your text file of variants you want to extract. Note that this list should match the SNP names in your bim file. These may be different if you are working with imputed data. You can also extract SNPs by position, rather than name. See http://zzz.bwh.harvard.edu/plink/dataman.shtml - your text file will have CHR,  BP1 (start position), BP2 (end position - can be the same as the start position), LABEL.
```
plink --bfile $FILENAME \
 --extract pd_snps.txt \
 --make-bed \
 --out $FILENANE.PD_snps
```

Recode in raw format (minor allele count - 0, 1 or 2) for R. The recodeA means in additive format so it counts the number of minor alleles.
```
plink --bfile $FILENANE.PD_snps \
--recodeA \
--out $FILENANE.PD_snps.recodeA
```

# 2. Use R to filter just for individuals who carry at least one mutation

This step is up to you. I wrote a little script to make it easier to find the individuals who carry at least one variant of interest (if you are looking through lots of individuals).

```
R

#---Load libraries---####
library(data.table)
library(tidyverse)
library(readxl)

#---Read in plink raw file---####
plink_data <- fread("$FILENANE.PD_snps.recodeA.raw")

#---Rename variants/column names in plink file---####

#I did these steps to name my variants understandable names e.g. GBA_N370S, as the SNP names in NeuroChip are not really interpretable.
#I created Excel spreadsheets with the variant names in NeuroChip and then an edited name - see files in variant-lists folder.

#Read in list of mutation names
mutation_names <- read_excel(choose.files(), sheet = "mut_names", col_names = TRUE)

#Rename column headers (NeuroChip SNP ID) with mutation names
names(plink_data) <- mutation_names$final_mutname[match(names(plink_data), mutation_names$snp_name)]

plink_data <- as_tibble(plink_data)

#Make column names unique (duplicate probes for N370S etc.)
names(plink_data) <- make.unique(names(plink_data), sep="_")


#---Select only columns that have individuals carrying mutations---####
#Select columns that have value of 1 or more
plink_data %>% 
  select_if(funs(sum(.) >=1))

#---Select individuals carrying any variant of interest---####
any_vars <- filter_at(plink_data, vars(-FID, -IID, -PAT, -MAT, -SEX, -PHENOTYPE), any_vars(.>=1))


#---Export data to CSV format---####
write_excel_csv(any_vars, "summary_results.csv")
```
