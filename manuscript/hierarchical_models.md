---
layout: page
title: Hierarchical Models
---



## Hierarchical Models

Now we're going to show how we can use the hierarchical model described
in the previous module to improve our estimates of the variance.
So, here on the bottom we have the various estimates
obtained from the sample.
And you can see there's a few that are very, very small.
And a few that are quite big.
Once we apply the hierarchical model and we
obtain a new estimate for that variance that
incorporates the variability that we see across all genes,
everything start shrinking towards a center that's at the somewhere
around here.
And you can see that a standard deviation estimate that
was practically zero, and made a t-statistic very big
is now moving towards a positive number, that's
going to make the t-statistic M become smaller.
So what's going on here is that we start out
with the standard deviation estimates for all genes.
We have a standard deviation for the average gene.
All the standard deviations get shrunken towards this value, which
makes the t-statistic statistic get shrunken from the observed t-statistic
to something that's not as big.
So here's the volcano plot before applying
hierarchical model, the t-test that we described.
And we have again, several false positives
that are there because we have a small estimate of the standard deviation.
And once we apply this hierarchical a model
and obtain what is usually call a moderated t-statistic,
things improve quite a lot.
So now we see all the blue points still up there, still very high.
But now the red points are no longer here.
The red points have moved down to the non-significant area.
So this is a clear example of how using hierarchical models
can greatly improve your downstream results when
it comes to things like the detecting differentiation expressed.
And this approach that we showed for differential expression
is actually applicable in a wide range of cases.



```r
 ##rafalib::install_bioc("SpikeInSubset")
library(SpikeInSubset)
```

```
## Loading required package: Biobase
## Loading required package: BiocGenerics
## Loading required package: methods
## Loading required package: parallel
## 
## Attaching package: 'BiocGenerics'
## 
## The following objects are masked from 'package:parallel':
## 
##     clusterApply, clusterApplyLB, clusterCall, clusterEvalQ,
##     clusterExport, clusterMap, parApply, parCapply, parLapply,
##     parLapplyLB, parRapply, parSapply, parSapplyLB
## 
## The following object is masked from 'package:stats':
## 
##     xtabs
## 
## The following objects are masked from 'package:base':
## 
##     anyDuplicated, append, as.data.frame, as.vector, cbind,
##     colnames, do.call, duplicated, eval, evalq, Filter, Find, get,
##     intersect, is.unsorted, lapply, Map, mapply, match, mget,
##     order, paste, pmax, pmax.int, pmin, pmin.int, Position, rank,
##     rbind, Reduce, rep.int, rownames, sapply, setdiff, sort,
##     table, tapply, union, unique, unlist, unsplit
## 
## Welcome to Bioconductor
## 
##     Vignettes contain introductory material; view with
##     'browseVignettes()'. To cite Bioconductor, see
##     'citation("Biobase")', and for packages 'citation("pkgname")'.
## 
## Loading required package: affy
```

```r
data(rma95)
library(genefilter)
```

```
## 
## Attaching package: 'genefilter'
## 
## The following object is masked from 'package:base':
## 
##     anyNA
```

```r
fac <- factor(rep(1:2,each=3))
tt <- rowttests(exprs(rma95),fac)
mask <- with(tt, abs(dm) < .2 & p.value < .01)
spike <- rownames(rma95) %in% colnames(pData(rma95))
cols <- ifelse(mask,"red",ifelse(spike,"dodgerblue","black"))

with(tt, plot(-dm, -log10(p.value), cex=.8, pch=16,
     xlim=c(-1,1), ylim=c(0,5),
     xlab="difference in means",
     col=cols))
abline(h=2,v=c(-.2,.2), lty=2)
```

![plot of chunk unnamed-chunk-1](images/hierarchical_models-unnamed-chunk-1-1.png) 

```r
tt$s <- apply(exprs(rma95), 1, function(row) sqrt(.5 * (var(row[1:3]) + var(row[4:6]))))
with(tt, plot(s, -log10(p.value), cex=.8, pch=16,
              log="x",xlab="estimate of standard deviation",
              col=cols))
```

![plot of chunk unnamed-chunk-1](images/hierarchical_models-unnamed-chunk-1-2.png) 

Now with limma


```r
library(limma)
```

```
## 
## Attaching package: 'limma'
## 
## The following object is masked from 'package:BiocGenerics':
## 
##     plotMA
```

```r
fit <- lmFit(rma95, model.matrix(~ fac))
ebfit <- ebayes(fit)
limmares <- data.frame(dm=coef(fit)[,"fac2"], p.value=ebfit$p.value[,"fac2"])
with(limmares, plot(dm, -log10(p.value),cex=.8, pch=16,
     col=cols,xlab="difference in means",
     xlim=c(-1,1), ylim=c(0,5)))
abline(h=2,v=c(-.2,.2), lty=2)
```

![plot of chunk unnamed-chunk-2](images/hierarchical_models-unnamed-chunk-2-1.png) 

Shrink


```r
n <- 40
qs <- seq(from=0,to=.2,length=n)
idx <- sapply(seq_len(n),function(i) which(as.integer(cut(tt$s^2,qs)) == i)[1])
idx <- idx[!is.na(idx)]
par(mar=c(5,5,2,2))
plot(1,1,xlim=c(0,.21),ylim=c(0,1),type="n",
     xlab="variance estimates",ylab="",yaxt="n")
axis(2,at=c(.1,.9),c("before","after"),las=2)
segments((tt$s^2)[idx],rep(.1,n),
         ebfit$s2.post[idx],rep(.9,n))
```

![plot of chunk unnamed-chunk-3](images/hierarchical_models-unnamed-chunk-3-1.png) 
