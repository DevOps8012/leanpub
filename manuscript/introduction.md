{line-numbers=off}
{frontmatter}

# Introduction

The unprecedented advance in digital technology during the second half of the 20th century has produced a measurement revolution that is transforming science. 
In the life sciences, data analysis is now part of practically every research project. Genomics in particalt is being driven by new measurement technologies that permit us to observe molecular entities for the first time that are leading to discoveries analogous to discovering the microorganisms and many others permitted by the invention and improvement of the microscope. Choice examples of these technologies are microarrays and next generation sequencing technology.  

Scientific fields that have traditionally relied upon simple data analysis techniques have been turned on their heads by these technologies. For example,  in the past, researchers would measure the transcription levels of a single gene of interest. Today, it is possible to measure all 20,000+ genes at once.  Advances such as these have brought about a shift from hypothesis to discovery-driven research.  However, interpreting information extracted from these massive and complex datasets requires sophisticated statistical skills as one can easily be fooled by patterns arising by chance. This has greatly elevated the importance of statistics and data analysis in this field.

## What does this book cover?

This book will cover several of the statistical concepts and data analytic skills needed to succeed in data-driven life science research. We go from relatively basic concepts related to computed p-values to advanced topics relted to analyzing high-throughput data.

We start with one of the most important topics in statistics and in the life sciences: statistical inference. Inference is the use of probability to learn population features from data. A typical example is deciphering if two groups (for example, cases versus controls) are different on average. Specific topics covered include the t-test, confidence intervals, association tests, Monte Carlo methods, permutation tests and statistical power. We make use of approximations made possible by mathematical theory, such as the Central Limit Theorem, as well as techniques made possible by modern computing. We will learn 
how to compute p-values and confidence intervals and implement basic data analyses. Throughout the book we will describe visualization techniques in R that are useful for exploring new data sets. For example, we will use these to learn when to apply robust statistical techniques.

We will then move on to an introduction to linear models and matrix algebra. We will explain why it is beneficial to use linear models to analyze differences across groups, and why matrices are useful to represent and implement linear models. We continue with a review of matrix algebra, including matrix notation and how to multiply matrices (both on paper and in R). We will then apply what we covered on matrix algebra to linear models. We will learn how to fit linear models in R, how to test the significance of differences, and how the standard errors for differences are estimated. Furthermore, we will review some practical issues with fitting linear models, including collinearity and confounding. Finally, we will learn how to fit complex models, including interaction terms, how to contrast multiple terms in R, and the powerful technique which the functions in R actually use to robustly fit linear models: the QR decomposition.

In the third part of the book we cover topics related to high-dimensional data. Specifically,  we describe multiple testing, error rate controlling procedures, exploratory data analysis for high-throughput data, p-value corrections and the false discovery rate. From here we move on to covering statistical modeling. In particular, we will discuss parametric distribution, including binomial and gamma distributions. Next, we will cover maximum likelihood estimation. Finally, we will discuss hierarchical models and empirical bayes and how they are applied in genomics.

We then cover the concepts of distance and dimension reduction. We will introduce the mathematical definition of distance and then use this to motivate the use of the singular value decomposition (SVD) for dimension reduction and multi-dimensional scaling. Once we learn this, we will be ready to cover hierarchical and k-mean clustering. We will follow this with basic introduction to machine learning.

We end by learn about batch effects and how component and factor analysis are used to deal with this callenge. In particular, we will examine confounding, show examples of batch effects, make the connection to factor analysis, and describe surrogate variable analysis. 

## How is this book different?

While statistics textbooks focus on mathematics this book focuses on using a computer to perform data analysis. Instead of explaining the mathematics and theory and then showing examples, we start by statiting a practical data related challenge. We then provide computer code that provide a solution to the problem and also helps illustrate the concepts behind the solution. By running the code yourself, and seeing data generation and analysis happen live, you will get a better intuition for the concepts, the mathematics, and the theory.

We focus on the practical challenges faced by data analysits in the life science and introduce the mathematics as a tool that can help us achieve the scientific goal. Furthemore, throughout the book we show the R code that performs this analysis and connect the lines of code to the statistical and matheatical concepts we explaing. All sections of this book are reproducible as they were produced by R markdown documents that include R code used to produce the figures, tables and results shown in the book. The code is shown in different font like this:


```r
x <- 2
y <- 3
print(x+y)
```

and the results in different colors and preceeded two hashtag:


```
## [1] 5
```

We will provide links that will give you access to the raw R makrdown code so you can easilty follow along the book by programming in R.










