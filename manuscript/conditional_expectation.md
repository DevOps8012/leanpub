---
layout: page
title: Conditional probabilities and expectations
---



## Conditional probabilities and expectations

Prediction problems can be divided into categorical and continuous outcomes. However, many of the algorithms can be applied to both due to the connection between conditional probabilities and conditional expectations. 

In categorical problems, for example binary outcome, if we know the probability of {$$}Y{/$$} being a 1 given that we know the value of the predictors {$$}X=(X_1,\dots,X_p)^\top{/$$} then we can optimize our predictions. We write this probability like this {$$}f(x)=\mbox{Pr}(Y=1 \mid X=x){/$$}. Note that {$$}Y{/$$} is a random variable which implies we are not guaranteed perfect prediction (unless these probabilities are 1 or 0). You can think of this probability as the proportion of the population with covariates {$$}X=x{/$$} that is a 1.

Now, given that the expectation is the average of all the values of {$$}Y{/$$}, in this is equivalent to the proportion of 1s which in this case is the probability. So for this case {$$}f(x) \equiv \mbox{E}(Y \mid X=x)=\mbox{Pr}(Y=1 \mid X=x){/$$}. The expected value has another attractive mathematical property and it is that it minimized the expected distance between the predictor {$$}\hat{Y}{/$$} and {$$}Y{/$$}: {$$}\mbox{E}\{ (\hat{Y} - Y)^2  \mid  X=x \}{/$$}. 

Here, we start by describing linear regression in the context of prediction. We use the son and father height example to illustrate. In our example we are trying to predict the son's height {$$}Y{/$$} based on the father's {$$}X{/$$}. Note that here we have only on predictor. Note that if we are asked to predict the height of a randomly selected son we would go with the mean:



```r
library(rafalib)
mypar(1,1)
library(UsingR)
data("father.son")
x=round(father.son$fheight) ##round to nearest inch
y=round(father.son$sheight)
hist(y,breaks=seq(min(y),max(y)))
abline(v=mean(y),col=2)
```

![plot of chunk unnamed-chunk-1](images/conditional_expectation-unnamed-chunk-1-1.png) 

In this case we can also approximate the distribution of {$$}Y{/$$} as normal which implies the mean maximizes the probability density. 

Now imagine we are given more information. We are told the the father of this randomly selected son has height 71 inches (1.2 SDs taller than the average) What is our prediction now? 



```r
plot(x,y,xlab="Father's height in inches",ylab="Son's height in inches",main=paste("correlation =",signif(cor(x,y),2)))
abline(v=c(-0.35,0.35)+71,col="red")
```

![plot of chunk unnamed-chunk-2](images/conditional_expectation-unnamed-chunk-2-1.png) 

```r
hist(y[x==71],xlab="Heights",nc=8,main="",xlim=range(y))
```

![plot of chunk unnamed-chunk-2](images/conditional_expectation-unnamed-chunk-2-2.png) 

<a name="regression"></a>

## Stratification
The best guess is still the expectation, but our strata has changed from all the data to only the {$$}Y{/$$} with {$$}X=71{/$$}. So we can stratify and take the average which is the conditional expectations. Out prediction for any {$$}x{/$$} is therefore:
{$$}
f(x) = E(Y \mid X=x)
{/$$}

It turns that because this data is approximated by a bivariate normal distribution we can, using calculus, show that 
{$$}
f(x) = \mu_Y + \rho \frac{\sigma_Y}{\sigma_X} (X-\mu_X)
{/$$}
and if we estimate these five parameters from the sample we get the regression line:


```r
plot(x,y,xlab="Father's height in inches",ylab="Son's height in inches",main=paste("correlation =",signif(cor(x,y),2)))
abline(v=c(-0.35,0.35)+71,col="red")
abline(lm(y~x),col=1)
```

![plot of chunk unnamed-chunk-3](images/conditional_expectation-unnamed-chunk-3-1.png) 

```r
hist(y[x==71],xlab="Heights",nc=8,main="",xlim=range(y))
```

![plot of chunk unnamed-chunk-3](images/conditional_expectation-unnamed-chunk-3-2.png) 

In this particular case the regression line provides an optimal prediction function for {$$}Y{/$$}. But this is not generally true.

