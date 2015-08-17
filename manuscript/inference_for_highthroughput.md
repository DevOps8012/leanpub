---
layout: page
title: Basic inference for high-throughput data
---




```r
library(rafalib)
```

## Inference in practice

Suppose we were given highthroughput gene expression data that was measured for several individuals in two populations. We are asked to report which genes have different average expression levels in the two populations. Note that if, instead thousands of genes, we were handed data from just one gene we could simply apply  the inference techniques that we have learned before. We could, for example, use a t-test or some other test. Here we review what changes when we consider high-throughput data.

### Thousands of tests

In this data we have two groups denoted with 0 and 1:

```r
library(GSE5859Subset)
data(GSE5859Subset)
g <- sampleInfo$group
g
```

```
##  [1] 1 1 1 1 1 1 1 1 1 1 1 1 0 0 0 0 0 0 0 0 0 0 0 0
```

If we were interested in a particular gene, let's arbitrarily pick the one on the 25th row, we would simply compute a t-test; assuming the data is well approximated by normal:


```r
e <- geneExpression[25,]
mypar2(1,2)
```

```
## Error in eval(expr, envir, enclos): could not find function "mypar2"
```

```r
qqnorm(e[g==1])
qqline(e[g==1])
```

![plot of chunk unnamed-chunk-3](images/inference_for_highthroughput-unnamed-chunk-3-1.png) 

```r
qqnorm(e[g==0])
qqline(e[g==0])
```

![plot of chunk unnamed-chunk-3](images/inference_for_highthroughput-unnamed-chunk-3-2.png) 

The qq-plots show that the data is well approximated by the normal approximation so apply a t-test. The t-test does not find this gene to be statistically significant:


```r
t.test(e[g==1],e[g==0])
```

```
## 
## 	Welch Two Sample t-test
## 
## data:  e[g == 1] and e[g == 0]
## t = 0.28382, df = 21.217, p-value = 0.7793
## alternative hypothesis: true difference in means is not equal to 0
## 95 percent confidence interval:
##  -0.1431452  0.1884244
## sample estimates:
## mean of x mean of y 
##  10.52505  10.50241
```

To answer the question for each gene we simply do this for every gene. Here we will define our own function and use `apply`:


```r
myttest <- function(x) t.test(x[g==1],x[g==0],var.equal=TRUE)$p.value
pvals <- apply(geneExpression,1,myttest)
```

We can now see which genes have p-values less than, say, 0.05. For example right away we see that:


```r
sum(pvals<0.05)
```

```
## [1] 1383
```

genes had p-values less than 0.05

However, as we will describe in more detail below, we have to be careful in interpreting this result because we have performed over  8,000 test. Note that if we performed the same procedure on random data, for which the null hypothesis is true for all feature, we obtain the following results:


```r
set.seed(1)
m <- nrow(geneExpression)
n <- ncol(geneExpression)
randomData <- matrix(rnorm(n*m),m,n)
nullpvals <- apply(randomData,1,myttest)
sum(nullpvals<0.05)
```

```
## [1] 419
```

As we will explain in the chapter, this is to be expected. Note that 419 is roughly 0.05*8192 and we will describe the theory that tells us why this prediction works.

### Faster implementation of t-test

Before, we continue, we should note that the above implementation is very inefficient. There are several faster implementations that perform t-test for high throughput data. For example


```r
library(genefilter)
results <- rowttests(geneExpression,factor(g))
max(abs(pvals-results$p))
```

```
## [1] 6.528111e-14
```

Note that `genefilter` is available from the Bioconductor projects. In a later section we will say more about this project but here is how one installs it:
 

```r
source("http://www.bioconductor.org/biocLite.R")
biocLite("genefilter")
```

Note that we get practically the same answer and much faster performance.


