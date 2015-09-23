---
Title:
---

A>## Exercises
A>
A>Load the following dataset:
A>
A>
```r
library(GSE5859Subset)
data(GSE5859Subset)
```
A>
A>And define the outcome and predictors. To make the problem more difficult, we will only consider autosomal genes:
A>
A>
```r
y = factor(sampleInfo$group)
X = t(geneExpression)
out = which(geneAnnotation$CHR%in%c("chrX","chrY"))
X = X[,-out]
```
A>
A>1. Use the `createFold` function in the `caret` package, set the seed to 1 `set.seed(1)` and create 10 folds. 
A>
A>    Question: What is the 2nd entry in the fold 3?
A>
A>
A>2. We are going to use kNN. We are going to consider a smaller set of predictors by using _filtering_ genes using t-tests. Specifically, we will perform a t-test and select the {$$}m{/$$} genes with the smallest p-values.
A>
A>    Let {$$}m=8{/$$} and {$$}k=5{/$$} and train kNN by leaving out the second fold `idx[[2]]`. How many mistakes do we make on the test set? Remember it is indispensable that you perform the t-test on the training data.
A>
A>
A>
A>3. Now run through all 5 folds. What is our error rate?
A>
A>
A>
A>4. Now we are going to select the best values of {$$}k{/$$} and {$$}m{/$$}. Use the expand grid function to try out the following values:
A>
A>    
    ```r
    ms=2^c(1:11)
    ks=seq(1,9,2)
    params = expand.grid(k=ks,m=ms)
    ```
A>
A>    Now use apply or a for-loop to obtain error rates for each of these pairs of parameters. Which pair of parameters minimizes the error rate?
A>
A>
A>5. Repeat exercise 4, but now perform the t-test filtering before the cross validation. Note how this biases the entire result and gives us much lower estimated error rates.
A>
A>
A>
A>6. Repeat exercise 3, but now, instead of `sampleInfo$group` , use 
A>
A>    
    ```r
    y = factor(as.numeric(format( sampleInfo$date, "%m")=="06"))
    ```
A>
A>    What is the minimum error rate now?
A>
A>
A>We achieve much lower error rates when predicting date than when predicting the group. Because group is confounded with date, it is very possible that these predictors have no information about group and that our lower 0.5 error rates are due to the confounding with date. We will learn more about this in the batch effect chapter.
A>
A>
A>
A>
A>
A>
A>
A>
