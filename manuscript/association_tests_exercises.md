A>## Exercises
A>
A>We showed how to calculate a Chi-square test from a table. Here we will show how to generate the table from data which is in the form of a dataframe, so that you can then perform an association test to see if two columns have an enrichment (or depletion) of shared occurances.
A>
A>Download the [https://studio.edx.org/c4x/HarvardX/PH525.1x/asset/assoctest.csv](assoctest.csv) file into your R working directory, and then read it into R:
A>
A>
```r
d = read.csv("assoctest.csv")
```
A>
A>
A>1. This dataframe reflects the allele status (either AA/Aa or aa) and the case/control status for 72 individuals. Compute the Chi-square test for the association of genotype with case/control status (using the table() function and the chisq.test() function). Examine the table to see if it look enriched for association by eye. What is the X-squared statistic?
A>
A>
A>
A>
A>2. Compute the Fisher's exact test `fisher.test`  for the same table. What is the p-value?
A>
A>
