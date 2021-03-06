---
Title: Modeling Exercises
---

{pagebreak}

A>## Exercises
A>
A>
A>1. Suppose you have an urn with blue and red balls. If {$$}N{/$$} balls are selected at random with replacement (you put the ball back after you pick it), we can denote the outcomes as random variables {$$}X_1,\dots,X_N{/$$} that are 1 or 0. If the proportion of red balls is {$$}p{/$$} , then the distribution of each of these is {$$}\mbox{Pr}(X_i=1)=p{/$$}. 
A>
A>    These are also called Bernoulli trials. These random variables are independent because we replace the balls. Flipping a coin is an example of this with {$$}p=0.5{/$$}. 
A>
A>    You can show that the mean and variance are {$$}p{/$$} and {$$}p(1-p){/$$} respectively. The binomial distribution gives us the distribution of the sum {$$}S_N{/$$} of these random variables. The probability that we see {$$}k{/$$} red balls is given by:
A>
A>    {$$} 
A>    \mbox{Pr}(S_N=k) = {N \choose k} p^k (1-p)^{N-k} 
A>    {/$$}
A>
A>    In R, the function `dbimom` gives you this result. The function `pbinom` gives us {$$}\mbox{Pr}(S_N\leq k){/$$}.
A>
A>    This equation has many uses in the life sciences. We give some examples below.
A>
A>    The probability of conceiving a girl is 0.49. What is the probability that a family with 4 children has 2 girls and 2 boys (you can assume that the outcomes are independent)?
A>
A>
A>2. Use what you learned in Question 1 to answer these questions:
A>
A>    What is the probability that a family with 10 children has 6 girls and 4 boys (assume no twins)?
A>
A>
A>3. The genome has 3 billion bases. About 20% are C, 20% are G, 30% are T, and 30% are A. Suppose you take a random interval of 20 bases, what is the probability that the GC-content (proportion of Gs of Cs) is strictly above 0.5 in this interval?
A>
A>
A>
A>
A>4. The probability of winning the lottery is 1 in 175,223,510. If 20,000,000 people buy a ticket, what is the probability that more than one person wins?
A>
A>
A>5. We can show that the binomial approximation is approximately normal when {$$}N{/$$} is large and {$$}p{/$$} is not too close to 0 or 1. This means that:
A>
A>    {$$}
A>    \frac{S_N - \mbox{E}(S_N)}{ \sqrt{ \mbox{Var}(S_N)}}
A>    {/$$} 
A>    
A>    is approximately normal with mean 0 and SD 1. Using the results for sums of independent random variables, we can show that {$$}\mbox{E}(S_N) = Np{/$$} and {$$}\mbox{Var}(S_n)=Np(1-p){/$$}. 
A>
A>    The genome has 3 billion bases. About 20% are C, 20% are G, 30% are T, and 30% are A. Suppose you take a random interval of 20 bases, what is the exact probability that the GC-content (proportion of Gs of Cs) is greater than 0.35 and smaller or equal to 0.45 in this interval?
A>
A>
A>
A>6. For the question above, what is the normal approximation to the probability?
A>
A>
A>7. Repeat exercise 4, but using an interval of 1000 bases. What is the difference (in absolute value) between the normal approximation and the exact distribution of the GC-content being greater than 0.35 and lesser or equal to 0.45?
A>
A>
A>8. The Cs in our genomes can be [_methylated_](http://en.wikipedia.org/wiki/DNA_methylation) or _unmethylated_. Suppose we have a large (millions) group of cells in which a proportion {$$}p{/$$} of the Cs of interest are methylated. We break up the DNA of these cells and randomly select pieces and end up with {$$}N{/$$} pieces that contain the C we care about. This means that the probability of seeing {$$}k{/$$} methylated Cs is binomial:
A>
A>    
    ```r
    exact = dbinom(k,N,p)
    ```
A>
A>    We can approximate this with the normal distribution:
A>
A>    
    ```r
    a <- (k+0.5 - N*p)/sqrt(N*p*(1-p))
    b <- (k-0.5 - N*p)/sqrt(N*p*(1-p))
    approx = pnorm(a) - pnorm(b)
    ```
A>
A>    Compute the difference `approx - exact` for:
A>    
    ```r
    N <- c(5,10,50,100,500)
    p <- seq(0,1,0.25)
    ```
A>
A>    Compare the approximation and exact probability of the proportion of Cs being {$$}p{/$$}, {$$}k=1,\dots,N-1{/$$} plotting the exact versus the approximation for each {$$}p{/$$} and {$$}N{/$$} combination.
A>    
A>    - A) The normal approximation works well when {$$}p{/$$} is close to 0.5 even for small {$$}N=10{/$$}
A>    - B) The normal approximation breaks down when {$$}p{/$$} is close to 0 or 1 even for large {$$}N{/$$}
A>    - C) When {$$}N{/$$} is 100 all approximations are spot on.
A>    - D) When {$$}p=0.01{/$$} the approximation are terrible for {$$}N=5,10,30{/$$} and only OK for {$$}N=100{/$$}
A>
A>
A>9. We saw in the previous question that when {$$}p{/$$} is very small, the normal approximation breaks down. If {$$}N{/$$} is very large, then we can use the Poisson approximation. 
A>
A>    Earlier we computed 1 or more people winning the lottery when the probability of winning was 1 in 175,223,510 and 20,000,000 people bought a ticket. Using the binomial, we can compute the probability of exactly two people winning to be:
A>    
A>    
    ```r
    N <- 20000000
    p <- 1/175223510
    dbinom(2,N,p)
    ```
A>    
A>    If we were to use the normal approximation, we would greatly underestimate this:
A>
A>    
    ```r
    a <- (2+0.5 - N*p)/sqrt(N*p*(1-p))
    b <- (2-0.5 - N*p)/sqrt(N*p*(1-p))
    pnorm(a) - pnorm(b)
    ```
A>
A>    To use the Poisson approximation here, use the rate {$$}\lambda= Np{/$$} representing the number of people per 20,000,000 that win the lottery. Note how much better the approximation is:
A>
A>    
    ```r
    dpois(2,N*p)
    ```
A>    
A>    In this case. it is practically the same because {$$}N{/$$} is very large and {$$}Np{/$$} is not 0. These are the assumptions needed for the Poisson to work. What is the Poisson approximation for more than one person winning?
A>
A>
A>10. Now we are going to explore if palindromes are over-represented in some part of the HCMV genome. Make sure you have the latest version of the `dagdata`, load the palindrome data from the Human cytomegalovirus genome, and plot locations of palindromes on the genome for this virus:
A>
A>
A>    
    ```r
    library(dagdata)
    data(hcmv)
    plot(locations,rep(1,length(locations)),ylab="",yaxt="n")
    ```
A>    These palindromes are quite rare, and therefore {$$}p{/$$} is very small. If we break the genome into bins of 4000 basepairs, then we have {$$}Np{/$$} not so small and we might be able to use Poisson to model the number of palindromes in each bin:
A>
A>    
    ```r
    breaks=seq(0,4000*round(max(locations)/4000),4000)
    tmp=cut(locations,breaks)
    counts=as.numeric(table(tmp))
    ```
A>    So if our model is correct, `counts` should follow a Poisson distribution. The distribution seems about right:
A>
A>    
    ```r
    hist(counts)
    ```
A>    So let {$$}X_1,\dots,X_n{/$$} be the random variables representing counts then {$$}\mbox{Pr}(X_i=k) = \lambda^k / k! \exp ( -\lambda){/$$} and to fully describe this distribution, we need to know {$$}\lambda{/$$}. For this we will use MLE. We can write the likelihood described in book in R. For example, for {$$}\lambda=4{/$$} we have:
A>    
A>    
    ```r
    probs <- dpois(counts,4)
    likelihood <- prod(probs)
    likelihood
    ```
A>    Notice that it's a tiny number. It is usually more convenient to compute log-likelihoods:
A>
A>    
    ```r
    logprobs <- dpois(counts,4,log=TRUE)
    loglikelihood <- sum(logprobs)
    loglikelihood
    ```
A>    Now write a function that takes {$$}\lambda{/$$} and the vector of counts as input and returns the log-likelihood. Compute this log-likelihood for `lambdas = seq(0,15,len=300)` and make a plot.  What value of `lambdas` maximizes the log-likelihood? 
A>
A>
A>11. The point of collecting this dataset was to try to determine if there is a region of the genome that has a higher palindrome rate than expected. We can create a plot and see the counts per location:
A>
A>    
    ```r
    library(dagdata)
    data(hcmv)
    breaks=seq(0,4000*round(max(locations)/4000),4000)
    tmp=cut(locations,breaks)
    counts=as.numeric(table(tmp))
    binLocation=(breaks[-1]+breaks[-length(breaks)])/2
    plot(binLocation,counts,type="l",xlab=)
    ```
A>    What is the center of the bin with the highest count?
A>
A>
A>12. What is the maximum count? 
A>
A>
A>13. Once we have identified the location with the largest palindrome count, we want to know if we could see a value this big by chance. If {$$}X{/$$} is a Poisson random variable with rate:
A>
A>    
    ```r
    lambda = mean(counts[ - which.max(counts) ])
    ```
A>    What is the probability of seeing a count of 14 or more?
A>
A>
A>14. So we obtain a p-value smaller than 0.001 for a count of 14. Why is it problematic to report this p-value as strong evidence of a location that is different?
A>    - A) Poisson in only an approximation.
A>    - B) We selected the highest region out of 57 and need to adjust for multiple testing.
A>    - C) {$$}\lambda{/$$} is an estimate, a random variable, and we didn't take into account its variability.
A>    - D)  We don't know the effect size.
A>
A>
A>15. Use the Bonferonni correction to determine the p-value cut-off that guarantees a FWER of 0.05. What is this p-value cutoff?
A>
A>
A>16. Create a qq-plot to see if our Poisson model is a good fit:
A>
A>    
    ```r
    ps <- (seq(along=counts) - 0.5)/length(counts)
    lambda <- mean( counts[ -which.max(counts)])
    poisq <- qpois(ps,lambda)
    plot(poisq,sort(counts))
    abline(0,1)
    ```
A>    How would you characterize this qq-plot
A>    - A) Poisson is a terrible approximation.
A>    - B) Poisson is a very good approximation except for one point that we actually think is a region of interest.
A>    - C) There are too many 1s in the data.
A>    - D) A normal distribution provides a better approximation.
A>
A>
A>17. Load the `tissuesGeneExpression` data library
A>    
A>    
    ```r
    library(tissuesGeneExpression)
    ```
A>    Now load this data and select the columns related to endometrium: 
A>
A>    
    ```r
    library(genefilter)
    y = e[,which(tissue=="endometrium")]
    ```
A>    This will give you a matrix `y` with 15 samples. Compute the across sample variance for the first three samples. Then make a qq-plot to see if the data follow a normal distribution. Which of the following is true?
A>    - A) With the exception of a handful of outliers, the data follow a normal distribution.
A>    - B) The variance does not follow a normal distribution, but taking the square root fixes this.
A>    - C) The normal distribution is not usable here: the left tail is over estimated and the right tail is underestimated.
A>    - D) The normal distribution fits the data almost perfectly.
A>
A>
A>18. Now fit an F-distribution with 14 degrees of freedom using the `fitFDist` function in the `limma` package:
A>
A>
A>19. Now create a qq-plot of the observed sample variances versus the F-distribution quantiles. Which of the following best describes the qq-plot?
A>    
A>    - A) The fitted F-distribution provides a perfect fit.
A>    - B) If we exclude the lowest 0.1% of the data, the F-distribution provides a good fit.
A>    - C) The normal distribution provided a better fit.
A>    - D) If we exclude the highest 0.1% of the data, the F-distribution provides a good fit.
A>
A>
