--- 
title: "Power calculations" 
output: html_document 
layout: page 
--- 





## Power calculations 

### Introduction 

We have used the example of the effects of two different diets on the weight of mice. Because in this illustrative example we have access to the population we know that in fact there is a substantial (about 10%) difference between the average weights of the two populations: 


```r 
dat <- read.csv("mice_pheno.csv") ## We downloaded this file in a previous section 

controlPopulation <- filter(dat,Sex == "F" & Diet == "chow") %>% select(Bodyweight) %>% unlist 
``` 

``` 
## Error in eval(expr, envir, enclos): could not find function "%>%" 
``` 

```r 
hfPopulation <- filter(dat,Sex == "F" & Diet == "hf") %>% select(Bodyweight) %>% unlist 
``` 

``` 
## Error in eval(expr, envir, enclos): could not find function "%>%" 
``` 

```r 
mu_hf <- mean(hfPopulation) 
``` 

``` 
## Error in mean(hfPopulation): object 'hfPopulation' not found 
``` 

```r 
mu_control <- mean(controlPopulation) 
``` 

``` 
## Error in mean(controlPopulation): object 'controlPopulation' not found 
``` 

```r 
print(mu_hf - mu_control) 
``` 

``` 
## Error in print(mu_hf - mu_control): object 'mu_hf' not found 
``` 

We have also seen that in some cases, when we take a sample and perform a t-test we don't always get a p-value smaller than 0.05. For example, here is a case were we take sample of 5 mice and we don't achieve statistical significance at the 0.05 level: 


```r 
set.seed(1) 
N <- 5 
hf <- sample(hfPopulation,N) 
``` 

``` 
## Error in sample(hfPopulation, N): object 'hfPopulation' not found 
``` 

```r 
control <- sample(controlPopulation,N) 
``` 

``` 
## Error in sample(controlPopulation, N): object 'controlPopulation' not found 
``` 

```r 
t.test(hf,control)$p.value 
``` 

``` 
## Error in t.test(hf, control): object 'hf' not found 
``` 

Did we make a mistake? By not rejecting the null hypothesis are we saying the diet has no effect? The answer to this question is no. All we can say is that we did not reject the null. But this does not necessarily imply that the null true. The problem is that in this particular instance we don't have enough _power_, a term we are now going to define. If you are doing scientific research it is very likely that you will have to do a power calculation at some point. In many cases it is an ethical obligation as it can help you avoid sacrificing mice that do not need to be. Here we explain what this means. 


### Types of error 

Note that whenever we perform a statistical test we are well aware that we may make a mistake. This is why our p-values are not 0. Under the null, there is always a positive, perhaps super small, but a chance that we will reject the null when it is true. If the p-value is 0.05 it will happen 1 out of 20 times. This *error* is called _type I error_ by statisticians. 

A type I error is defined as rejecting the null when we should not. This is also referred to as a false positive. So why do we use 0.05 then? Shouldn't we use 0.000001 to be really sure? The reasone we don't use infinitesimal cut-offs to avoid type I error at all cost is that there is another error we can commit: not reject the null when we should. This is called a _type II error_ or a false negative. The R code analysis above shows an example of a false negative: we did not reject the null hypothesis (at the 0.05 level) and, because we peaked at the true population means, we know there is in fact a difference. Had we used a p-value cutoff of 0.25, we would not have made this mistake. However, in general, are we comfortable with a type I error rate of 1 in 4? Usually we are not. 


### The 0.05 and 0.01 cut-offs are arbitrary 

Most Journals and regulatory agencies many times insist that results be significant at the 0.01 or 0.05 levels. Note that there is nothing special about these numbers other than the fact that some of the first papers on p-values used these values as examples. Part of the goal of this book is to give readers a good understanding of what p-values and confidence intervals are so that these choices can be judged in an informed way. Unfortunately, in science, these cut-offs are applied somewhat mindlessly but that is topic is part of a complicated debate. 


### Power calculation 

Power is the probability of rejecting the null when the null is false. Now, "when the null is false" is a complicated statement because it can be false in many ways. {$$}\delta \equiv mu_hf - mu_control {/$$}could be anything and power actually depends on this parameter. It also depends on the standard error of your estimates which depends on sample size and the population standard deviations. In practice we don't know these, so we usually report power for several plausible values. Statistical theory gives us formulas to calculate power. The `pwr` package performs these calculations for you. Here we will illustrate the concepts behind power by coding up simulations in R. 

Suppose our sample size is 


```r 
N <- 12 
``` 

and we will reject the null hypothesis at 


```r 
alpha <- 0.05 
``` 

What is our power with this particular data? We will compute this probability by re-running the exercise many times and calculating the proportion of times reject. Specifically, we will run 


```r 
B <- 2000 
``` 

simulations. The simulation is as follows: we take a sample of size {$$}N {/$$}from both control and treatment groups, we perform a t-test comparing these two, and report if the p-value is less than `alpha` or not. We write a function that does this: 


```r 
reject <- function(N, alpha=0.05){ 
hf <- sample(hfPopulation,N) 
control <- sample(controlPopulation,N) 
pval <- t.test(hf,control)$p.value 
pval < alpha 
} 
``` 

Here is an example of one simulation for a simple size of 12. The call to `reject` answers the question "Did we reject?" 

```r 
reject(12) 
``` 

``` 
## Error in sample(hfPopulation, N): object 'hfPopulation' not found 
``` 

Now we can use the `replicate` function to do this `B` times. 


```r 
rejections <- replicate(B,reject(N)) 
``` 

``` 
## Error in sample(hfPopulation, N): object 'hfPopulation' not found 
``` 

Our power is just the proportion of times we correctly reject. So with {$$}N=12 {/$$}our power is only: 


```r 
mean(rejections) 
``` 

``` 
## Error in mean(rejections): object 'rejections' not found 
``` 

For those that were confused as to why the t-test was rejecting when we knew the null was false, this explains it. With a sample size of just 12 our power is about 9%. To guard against false posties at the 0.05 level, we had to be this careful and ended up with making many type II error. 

Let's see how power improves with N. We will use the function `sapply` which applies a function to each of the elements of a vector. We want to repeat the above for the following sample size: 


```r 
Ns <- seq(5, 50, 5) 
``` 

So we use `apply` like this: 

```r 
power <- sapply(Ns,function(N){ 
rejections <- replicate(B, reject(N)) 
mean(rejections) 
}) 
``` 

``` 
## Error in sample(hfPopulation, N): object 'hfPopulation' not found 
``` 

Note that for each of the three simulation it just returns the proportion of times we reject. Not surprisingly power increases with N: 


```r 
plot(Ns, power, type="b") 
``` 

``` 
## Error in xy.coords(x, y, xlabel, ylabel, log): 'x' and 'y' lengths differ 
``` 

Similarly if we change the level `alpha` at which we reject, power changes. The smaller I want the chance of type I error to be, the less power I will have. We can see this by writing similar code but keeping {$$}N {/$$}fixed and considering several values of `alpha` 


```r 
N <- 30 
alphas <- c(0.1,0.05,0.01,0.001,0.0001) 
power <- sapply(alphas,function(alpha){ 
rejections <- replicate(B,reject(N,alpha=alpha)) 
mean(rejections) 
}) 
``` 

``` 
## Error in sample(hfPopulation, N): object 'hfPopulation' not found 
``` 

```r 
plot(alphas, power, xlab="alpha", type="b", log="x") 
``` 

``` 
## Error in xy.coords(x, y, xlabel, ylabel, log): 'x' and 'y' lengths differ 
``` 

Note that the x-axis in this last plot is in the log scale. 

There is no "right"" power or "right" alpha level. But it is important that you understand what each means. 

<b>Optional homework</b> Create a plot with curves of power versus N. Show several curves in the same plot with color representing alpha level. 
