# iDASH2021 Track3 dataset

The dataset is simulated by [PheWAS](https://github.com/PheWAS/PheWAS), the R package for phenome wide association study.

In the sample input data **phewas5k_data_orig.csv, phewas10k_data_orig.csv**, each row represents one individual sample with id 1~5000/10000 (listed in the first column).
The second to 1828th column shows whether an individual has a specific disease (True/False), labeled by International Classification of Disease, 9th edition (ICD9) code.
The 1829th column displays the number of variants (0/1/2) an individual carries on SNP "rsEXAMPLE" (genotype). The last column "sex" indicates the individual's biological gender (F:female, M:male).  Please note that "NA" indicates missing data.
The following code will import the input dataset into R:
```
data=read.csv(file = 'phewas5k_data_orig.csv')
```
Alternatively, more simulated data could be generated by [PheWAS](https://github.com/PheWAS/PheWAS).
```
install.packages("devtools")
library(devtools)
install_github("PheWAS/PheWAS")
library(PheWAS)
set.seed(1)
ex=generateExample()
id.vocab.code.count=ex$id.vocab.code.count
genotypes=ex$genotypes
id.sex=ex$id.sex
phenotypes=createPhenotypes(id.vocab.code.count, aggregate.fun=sum, id.sex=id.sex)
data=inner_join(inner_join(phenotypes,genotypes),id.sex)
```
In the next step,the association between a specific phenotype and the SNP is estimated by [PheWAS](https://github.com/PheWAS/PheWAS) using logistic regression, where "sex" is treated as covariate:
```
results=phewas_ext(data,phenotypes=names(data)[2:1828],genotypes=c("rsEXAMPLE"), covariates=c("sex"), cores=1)
```
The participants are expected to submit results in a output file (**phewas5k_result_rank.csv, phewas10k_result_rank.csv**), which includes the phenotypes (ICD9 code), SNP, covariates, beta, standard error (SE), odd ratio (OR), p-value, the number of samples (n_total), the number of case samples (n_cases), the number of control samples (n_controls), the minor allele frequency (allele_freq).
The output file should sort the phenotypes by p-value and only list the phenotypes with p-value < 0.05.

###### Reference: Denny JC, Ritchie MD, Basford M, Pulley J, Bastarache L, Brown-Gentry K, Wang D, Masys DR, Roden DM, Crawford DC. PheWAS: Demonstrating the feasibility of a phenome-wide scan to discover gene-disease associations. Bioinformatics. 2010 Mar 24. PMID: 20335276
