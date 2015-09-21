---
layout: page
title: Bayesian Statistics
---




## Bayesian Statistics

The R markdown document for this section is available [here](https://github.com/genomicsclass/labs/tree/master/modeling/bayes.Rmd).

One distinguishing characteristic of high-throughput data is that we make many measures of related outcomes. For example, we measure the expression of thousands of genes, or the height of thousands of peaks representing protein binding, or the methylation levels across several CpGs. However, most of the statistical inference approaches we have shown here treat each feature independently and pretty much ignores data from other features. We will learn how using statistical models we can gain power by modeling features jointly. The most successful of these approaches are what we refer to as hierarchical models, which are best explained in the context of Bayesian statistics.

#### Bayes theorem

We start by reviewing Bayes theorem. We do this using a hypothetical cystic fibrosis test as an example. Suppose a test for cystic fibrosis has an accuracy of 99%. We will use the following notation:

{$$}
\mbox{Prob}(+ \mid D=1)=0.99, \mbox{Prob}(- \mid D=0)=0.99 
{/$$}

with {$$}+{/$$} meaning a positive test and {$$}D{/$$} representing if you actually have (1) the disease or not (0).

Suppose we select a random person and they test positive, what is the probability that they have the disease?  We write this as {$$}\mbox{Prob}(D=1 \mid +)?{/$$} The cystic fibrosis rate is 1 in 3,900 which implies that  {$$}\mbox{Prob}(D=1)=0.0025{/$$}. To answer this question we will use Bayes Theorem, which in general tells us that:

{$$}
\mbox{Pr}(A \mid B)  =  \frac{\mbox{Pr}(B \mid A)\mbox{Pr}(A)}{\mbox{Pr}(B)} 
{/$$}

This equation applied to our problem becomes:

{$$}
\begin{align*}
\mbox{Prob}(D=1 \mid +) & =  \frac{ P(+ \mid D=1) \cdot P(D=1)} {\mbox{Prob}(+)} \\
& =  \frac{\mbox{Prob}(+ \mid D=1)\cdot P(D=1)} {\mbox{Prob}(+ \mid D=1) \cdot P(D=1) + \mbox{Prob}(+ \mid D=0) \mbox{Prob}( D=0)} 
\end{align*}
{/$$}

Plugging in the numbers we get:

{$$}
\frac{0.99 \cdot 0.0025}{0.99 \cdot 0.0025 + 0.01 \cdot (.9975)}  =  0.02 
{/$$}

This says that despite the test having 0.99 accuracy, the probability of having disease given a positive test in 0.02. This may appear counterintuitive to some. The reason this is the case is because we have to factor in the very rare probability that a person, chosen at random, has the disease. To illustrate this we run a Monte Carlo simulation.

#### Simulation

The following simulation is meant to help you visualize Bayes Theorem. We start by randomly selection 1500 people from a population in which the disease in question has a 5% prevalence.


```r
set.seed(3)
prev <- 1/20
##Later, we are arranging 1000 people in 80 rows and 20 columns
M <- 50 ; N <- 30
##do they have the disease?
d<-rbinom(N*M,1,p=prev)
```

Now each person gets the test which is correct 90% of the time.

```r
accuracy <- 0.9
test <- rep(NA,N*M)
##do controls test positive?
test[d==1]  <- rbinom(sum(d==1), 1, p=accuracy)
##do cases test positive?
test[d==0]  <- rbinom(sum(d==0), 1, p=1-accuracy)
```

Because there are so many more controls than cases, even with low false positive rate, we get enough so that we have more controls than cases in the tested-positive group:


```r
cols <- c("grey","red")
people <- expand.grid(1:M,N:1)
allcols <- cols[d+1] ##Cases will be red

positivecols <- allcols
positivecols[test==0] <- NA ##remove non-positives

negativecols <- allcols
negativecols[test==1] <- NA ##remove non-positives

library(rafalib)
mypar()
layout(matrix(c(1,2,1,3),2,2),width=c(0.35,0.65))
###plot of all people
plot(people,col=allcols,pch=16,xaxt="n",yaxt="n",xlab="",ylab="",
     main=paste0("Population: ",round(mean(d)*100),"% are red"))

plot(people,col=positivecols,pch=16,xaxt="n",yaxt="n",xlab="",ylab="",
     main=paste("Tested Positive:",round(mean(d[test==1])*100),"% are red"))

plot(people,col=negativecols,pch=16,xaxt="n",yaxt="n",xlab="",ylab="",
     main=paste("Tested Negative:",round(mean(d[test==0])*100,1),"% are red"))
```

![Simulation demonstrating Bayes theorem. Top plot shows every individual with red denoting cases. Each one takes a test and with 90% gives correct answer. Those called positive (either correctly or incorrectly) are put in the bottom left pane. Those called negative in the bottom right.](images/R/bayes-tmp-simulation-1.png) 

The proportions of red in the top plot shows {$$}\mbox{Pr}(D=1){/$$}. The bottom left shows {$$}\mbox{Pr}(D=1 \mid +){/$$} and the bottom right shows {$$}\mbox{Pr}(D=0 \mid +){/$$}.


#### Bayes in practice


<!-- ![iglesias](http://upload.wikimedia.org/wikipedia/commons/thumb/9/98/Jos%C3%A9_Iglesias_on_September_28%2C_2012.jpg/902px-Jos%C3%A9_Iglesias_on_September_28%2C_2012.jpg) -->


José Iglesias is a professional baseball player. In April 2013, when he was starting his career he was performing rather well:

| Month | At Bats | H | AVG |
|-------|---------|---|-----|
| April | 20      | 9 | .450   |

Note that no one has finished a 
season batting .400 since 
Ted Williams in 1941! We have only seen 20 at bats when, so what is our prediction at the end of the season when he has accumulated about 500.


Let's explore the distribution of batting averages for all players with more than 500 at bats during the previous three seasons:




![Batting average histograms for 2010, 2011, and 2012.](images/R/bayes-tmp-batting_averages-1.png) 

We note that the average player had an average of .275 and the standard deviation of the population of players was 0.027. So we can see already that .450 would be quite an anomaly as it is over six SDs away from the mean. So is José lucky or good? If we become convinced that he is luck we should trade him to a team that trusts the .450 observation and thinks this is not just luck.

First, note that we can think of outcomes from hitting as a binomial with a success rate of {$$}p{/$$}. So if the success rate is indeed .450 the standard error of just 20 at bats is:

{$$}
\sqrt{\frac{.450 (1-.450)}{20}}=.111
{/$$}

This means that the confidence interval is .450-.222 to .450+.222 or .228 to .672

### Hierarchical Model

We saw that if we pick a random player, and were asked to predict their batting average. So first we pick a player at random, then we observe 20 random outcomes. The model thus includes two stages:

{$$}
\begin{align*}
\theta &\sim N(\mu, \tau^2) \mbox{ describes randomness in picking a player at random}\\
Y \mid \theta &\sim N(\theta, \sigma^2) \mbox{ describes randomness in his performance after we pick the player}
\end{align*}
{/$$}

Two levels of variability: 1) Player to player variability and 2) variability due to luck when batting. In a Bayesian framework the first level is called a _prior distribution_ and the second the _sampling distribution_

Now, let's use this model for José's data. Suppose we want to predict his innate ability in the form of his _true_ batting average {$$}\theta{/$$}. This would be the hierarchical model for our data:

{$$}
\begin{align*}
\theta &\sim N(.275, .027^2) \\
Y \mid \theta &\sim N(\theta, .110^2) 
\end{align*}
{/$$}

So now we are ready to compute a posterior distribution to summarize our prediction of {$$}\theta{/$$}. The continuous version of Bayes rule can be used here:

{$$}
\begin{align*}
f_{ \theta \mid Y} (\theta\mid Y) &=
\frac{f_{Y\mid \theta}(Y\mid \theta) f_{\theta}(\theta)
}{f_Y(Y)}\\
&= \frac{f_{Y\mid \theta}(Y\mid \theta) f_{\theta}(\theta)}
{\int_{\theta}f_{Y\mid \theta}(Y\mid \theta)f_{\theta}(\theta)}
\end{align*}
{/$$}

We are particularly interested in the {$$}\theta{/$$} that maximizes the posterior probability {$$}f_{\theta\mid Y}(\theta\mid Y){/$$}. In our case, our {$$}f{/$$} s are normal and we can compute the  {$$}\mbox{E}(\theta\mid y){/$$} and {$$}\mbox{var}(\theta\mid y){/$$}. Specifically, we can show the average of this distribution is the following:

{$$}
\begin{align*}
\mbox{E}(\theta\mid y) &= B \mu + (1-B) Y\\
&= \mu + (1-B)(Y-\mu)\\
B &= \frac{\sigma^2}{\sigma^2+\tau^2}
\end{align*}
{/$$}

In the case of José Iglesias, we have:

{$$}
\begin{align*}
\mbox{E}(\theta \mid Y=.450) &= B \times .275 + (1 - B) \times .450 \\
&= .275 + (1 - B)(.450 - .260) \\
B &=\frac{.110^2}{.110^2 + .027^2} = 0.943\\
\mbox{E}(\theta \mid Y=450) &\approx .285
\end{align*}
{/$$}

The variance can be shown to be:

{$$}
\mbox{var}(\theta\mid y) = \frac{1}{1/\sigma^2+1/\tau^2}
= \frac{1}{1/.110^2 + 1/.027^2}
{/$$}

In our example the SD is 0.026


So we started with a _frequentist_ 95% confidence interval that ignored data from other players and summarized just José's data: .450 {$$}\pm{/$$} 0.220. Then we used an Bayesian approach to obtain a posterior probability. This is actually referred to as empirical Bayes approach because we used data to construct the prior. From the posterior we can report what is called a 95% credible interval by reporting a region, centered at the mean, with a 95% chance of occurring. In our case this turns out to be: .285 {$$}\pm{/$$} 0.052

Here are the José Iglesias batting averages for the next five months. 

|Month|At Bat| Hits| AVG |
|-----|------|-----|-----|
|April|20|9|.450|
|May|26|11|.423|
|June|86|34|.395|
|July|83|17|.205|
|August|85|25|.294|
|September|50|10|.200|
|Total w/o April|330|97|.293|

Although both intervals included the final batting average, the Bayesian credible interval provided a much more precise prediction. In particular it predicted that we would not be as good the remainder of the season.
