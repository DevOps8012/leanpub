---
Title: Linear models and randomness
---


A>## Exercises
A>
A>The standard error of an estimate is the standard deviation of the sampling distribution of an estimate. In previous chapters, we saw that our estimate of the mean of a population changed depending on the sample that we took from the population. If we repeatedly sampled from the population, and each time estimated the mean, the collection of mean estimates would form the sampling distribution of the estimate. When we took the standard deviation of those estimates, that was the standard error of our mean estimate.
A>
A>In the case of a linear models written as:
A>
A>    {$$}
A>Y_i = \beta_0 + \beta_1 X_i + \varepsilon, i=1,\dots,n
A>    {/$$}
A>
A>    {$$}\varepsilon{/$$} is considered random. Every time we re-run the experiment we will see different {$$}\varepsilon{/$$} dichotomous. This implies that in different application {$$}\varepsilon{/$$} represents different things: measurement error or variability between individuals for example.
A>    
A>    If we were to re-run the experiment many times, and estimate linear model terms {$$}\hat{\beta}{/$$} each time, this is called the sampling distribution of the estimates. If we take the standard deviation of all of these estimates from repetitions of the experiment, this is called the standard error of the estimate. While we are not sampling individuals, you can think about the repetition of the experiment that we are "sampling" new errors in our observation of {$$}Y{/$$}.
A>
A>1. We have shown how to find the least squares estimates with matrix algebra. These estimates are random variables as they are linear combinations of the data. For these estimates to be useful we also need to compute the standard errors. Here we review standard errors in the context of linear models.
A>    To see this we can run a Monte Carlo simulation to imitate the collection of falling object data. Specifically we will generate the data repeatedly and compute the estimate for the quadratic term each time.
A>
A>    
    ```r
    g = 9.8 ## meters per second
    h0 = 56.67
    v0 = 0
    n = 25
    tt = seq(0,3.4,len=n) 
    y = h0 + v0 *tt - 0.5* g*tt^2 + rnorm(n,sd=1)
    ```
A>
A>    Now we act as if we didn't know h0, v0 and -0.5*g and use regression to estimate these. We can rewrite the model as {$$}y = \beta_0 + \beta_1 t + \beta_2 t^2 + \varepsilon{/$$} and obtain the LSE we have used in this class. Note that g = -2 {$$}\beta_2{/$$}.
A>
A>    To obtain the LSE in R we could write:
A>
A>    
    ```r
    X = cbind(1,tt,tt^2)
    A = solve(crossprod(X))%*%t(X)
    ```
A>
A>    Given how we have defined `A`, which of the following is the LSE of g, the acceleration due to gravity (suggestion: try the code in R)?
A>    
A>    - A) `9.8`  
A>    - B) `A %*% y`
A>    - C) `-2 * (A %*% y) [3]`
A>    - D) `A[3,3]`
A>    
A>
A>
A>2. In the lines of code above, there was a call to a random function `rnorm`. This means that each time the lines of code above are repeated, the estimate of g will be different.
A>
A>    Use the code above in conjunction with the function `replicate` to generate 100,000 Monte Carlo simulated datasets. For each dataset, compute an estimate of {$$}g{/$$} (remember to multiply by -2).
A>
A>    What is the standard error of this estimate?
A>
A>
A>
A>3. In the father and son height examples, we have randomness because we have a random sample of father and son pairs. For the sake of illustration let's assume that this is the entire population:
A>
A>    
    ```r
    library(UsingR)
    x = father.son$fheight
    y = father.son$sheight
    n = length(y)
    ```
A>
A>    Now let's run a Monte Carlo simulation in which we take a sample of size 50 over and over again. Here is how we obtain one sample:
A>
A>    
    ```r
    N = 50
    index = sample(n,N)
    sampledat = father.son[index,]
    x = sampledat$fheight
    y = sampledat$sheight
    betahat = lm(y~x)$coef
    ```
A>
A>    Use the function replicate to take 10,000 samples.
A>
A>    What is the standard error of the slope estimate? That is, calculate the standard deviation of the estimate from the observed values obtained from many random samples.
A>
A>
A>
A>
A>
A>4. Later in this chapter we will introduce a new concept: covariance. The covariance of two lists of numbers {$$}X=x_1,...,x_n{/$$} and {$$}Y=y_1,...,y_n{/$$} is 
A>
A>
A>
A>
