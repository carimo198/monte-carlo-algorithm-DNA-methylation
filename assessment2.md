---
title: "Monte Carlo algorithm project: DNA methylation of cytosines"
author: "Mohammad R. Hosseinzadeh z5388543"
date: "Last updated: 01 August, 2021"
output:
  html_document:
    toc: true
    toc_depth: 3
    toc_float: true
    theme: default 
    highlight: tango
    keep_md: yes
    code_folding: show
    mathjax: default
fontsize: 12pt
spacing: double
line-height: 1.5
---

### Setup

Load useful packages to assist with completing the tasks.


```r
# Load required packages:
library(knitr) # Dynamic report generation 
library(tidyverse) # collection of packages useful for most data science tasks  
library(lubridate) # For dates and date-times
library(kableExtra) # For HTML and PDF tables
library(scales) # alpha function for changing colour transparency
library(spatstat) # for computing density quantiles

# set chunk options
knitr::opts_chunk$set(
  echo = TRUE,
  eval = TRUE,
  fig.width = 8, fig.height = 6,
  fig.align = "center",
  message = FALSE,
  warning = FALSE,
  comment = "") 
```


### Task 1: Plot the density $f(x)$  

Define $X$ a random variable describing the relative frequency of DNA methylation of cytosines in [0,1]. $X_1,..., X_n$ are i.i.d distributed according to f(x), a mixture of three beta distributions which is defines as:  

$$f(x) = \frac{1}{3} Be(\alpha_1 = 1, \beta_1 = 5) + \frac{1}{3} Be(\alpha_2 = 3, \beta_2 = 5) + \frac{1}{3} Be(\alpha_3 = 10, \beta_3 = 5)$$



```r
set.seed(1234)
# define function for mixture beta distribution f(x)
f <- function(x) {
  fx1 <- (1/3) * dbeta(x, 1, 5)
  fx2 <- (1/3) * dbeta(x, 3, 5)
  fx3 <- (1/3) * dbeta(x, 10, 5)
  fx <- fx1 + fx2 + fx3 
  return(fx)
}

#Plot the density
curve(f, col = "blue", lwd = 2,
      xlab = "f(x)", ylab = "Density",
      main = "Density of mixture beta distribution f(x)", 
      xlim = c(0,1), ylim = c(0, 2))
```

<img src="assessment2_files/figure-html/unnamed-chunk-1-1.png" style="display: block; margin: auto;" />



```r
set.seed(1234)
# plot of individual beta distribution from the mixture f(x)
curve(1/3*dbeta(x, 1, 5), col = "red", lwd = 2,
      lty = 3, xlab = "x", ylab = "Density",
      ylim = c(0, 2), xlim = c(0,1), n = 10000)

curve(1/3*dbeta(x, 3, 5), col = "darkgreen", lwd = 2,
      lty = 3, xlab = "x", ylab = "Density",
      ylim = c(0, 2), xlim = c(0,1), n = 10000, add = TRUE)

curve(1/3*dbeta(x, 10, 5), col = "orange", lwd = 2,
      lty = 3, xlab = "x", ylab = "Density",
      ylim = c(0, 2), xlim = c(0,1), n = 10000, add = TRUE)

#Plot the mixture density
curve(f, col = "blue", lwd = 2,
      xlab = "f(x)", ylab = "Density",
      ylim = c(0, 2), xlim = c(0,1), add = TRUE)

# add legend
legend("topright", 
       legend = c("Mixture density f(x)", "1/3Be(1, 5)",
                  "1/3Be(3,5)", "1/3Be(10,5)"),
  col = c("blue", "red", "green", "orange"), 
  lty = c(1, 3, 3, 3), lwd = 2, cex = 0.7)

title(main = "Mixture beta distribution f(x) density with its individual components")
```

<img src="assessment2_files/figure-html/unnamed-chunk-2-1.png" style="display: block; margin: auto;" />


### Task 2: Implement accept/reject algorithm

To implement the accept-reject sampling method we will be using the following accept-reject algorithm (version 2):

1. Generate $\theta$ from $g(\theta)$.
2. Generate $\psi$ from $U(0,Kg(\theta))$.
3. Accept $\theta$ if $\psi \le f(x)$.

The first step of our accept/reject algorithm is to define a sampling distribution $g(\theta)$. Let $g(\theta)$ be $g(x)$ and since the variable $X$ is defined in relative frequency [0,1], we can use a uniform proposal distribution, as our sampling distribution: 
$$g(x) = Unif(0,1)$$ 

We require the following conditions to be met: 

- $f(x) \le Kg(x)$ 
- with the acceptance probability $\frac{f(x)}{Kg(x)}$. 

In order to gain the maximal efficiency, we need to define $K = \max_x$[$\frac{f(x)}{Kg(x)}$]. Therefore, acceptance probability can be written as follows:

$$Pr(Accept|x)= f(x) \le Kg(x)=\frac{1}{K}$$  

We then need to compute the value of K, the maximum of the density and associate that with the corresponding $x$ value. From the graph we can see that $x_{\max}$ value is 0. Therefore, we can use our f(x) function to compute the maximum density associated with $x=0$:

$$f({\max}_x) = f(0) = 1.667$$



```r
set.seed(1234)
# compute K, the maximum value of the density f(x)
k <- f(0)

# display result
k
```

```
[1] 1.666667
```


We can now simulate 10,000 values from the beta-mixture distribution using our defined accept-reject algorithm and plot the result of the simulation for visual analysis. 


```r
# Accept reject algorithm
set.seed(1234) # set seed

# set number of samples
n <- 10000

# simulate values of x from proposal distribution
x <- runif(n, 0, 1)

# compute K, the maximum value of the density f(x)
k <- f(0)

# simulate psi values
psi <- runif(n, 0, k)

# acceptance indicator - logical vector
ind <- psi < f(x)

# simulate random values to be used in plotting theoretical target density
th <- seq(0,1, length = 10000)

# arrange graph to fit 2 plots
par(mfrow = c(1, 2))

# plot 1 - graph of simulation
plot(x, psi, pch = 20, cex = 0.8,
     col = ifelse(ind, "black", "red"),
     xlab = "x", ylab = "",
     main = "",
     xlim = c(0, 1), ylim = c(0, 1.7))

# add theoretical target density distribution line
lines(th, f(th), col = "blue", lwd = 4)

# plot 2 - graph of only accepted simulated values
plot(x[ind], psi[ind],
     pch = 20, cex = 0.8,
     xlab = "x", ylab = "", main = "",
     xlim = c(0, 1), ylim = c(0, 1.7))

# add theoretical target density distribution line
lines(th, f(th), col = "blue", lwd = 4)


# add legend
legend("topright", 
       legend = c("accepted values", "rejected values", "target density"),
       col = c("black", "red", "blue"), pch = c(20, 20, NA),
       lty = c(NA, NA, 1), lwd = 2, cex = 0.7)

# add title
title("Accept/reject sampling algorithm 2", line = -1.5, outer =T)       
```

<img src="assessment2_files/figure-html/unnamed-chunk-4-1.png" style="display: block; margin: auto;" />

### Task 3: Compare observed acceptance rate with theoritcal acceptance rate

The theoretical acceptance rate is defined as $\frac{1}{K}$ which is calculated to be 0.6. Therefore, theoretically, 60% of the simulated values should be accepted.


```r
set.seed(1234)
# compute the theoretical acceptance rate
theory_AR <- 1/k
theory_AR
```

```
[1] 0.6
```

We can compute the observed acceptance rate by dividing the total number of accepted values over the number of total number of simulated values.


```r
# compute observed acceptance rate
set.seed(1234)

# assign accepted x values
accepted_x <- x[ind]

# compute observed AR
observed_AR <- length(accepted_x) / n
observed_AR
```

```
[1] 0.5978
```

The observed acceptance rate is 59.78% compared with a 60% theoretical acceptance rate with a seed value 1234. 


### Task 4: Importance sampling algorithm

We will implement an importance sampling algorithm by using the same 10,000 values generated from the proposal $Unif(0,1)$ distribution from task 2.

The importance sampling algorithm requires the following:

1. generate $\theta^{(i)}$ from $g(\theta)$
2. give $\theta^{(i)}$ a weight $w^{(i)}$ $\propto$ $\frac{\pi(\theta^{(i)}|x)}{g(\theta^{(i)})}$ 

We can then obtain samples, $(\theta^{(1)}, w^{(1}),..., (\theta^{(N)}, w^{(N)})$, which are weighted samples from the target distribution $\pi(\theta|x)$. 

```r
set.seed(1234)
# assign weight - using the same 10,000 x values as before
w <- f(x) 

# importance weight  - weighted values of x
IS_weighted <- w / sum(w)

# compute importance sampling density
d_IS <- density(x, weights = IS_weighted, from = 0, to = 1)

# simulate samples from the weighted values of x
x_IS <- sample(x, size = n, prob = IS_weighted, replace = TRUE)

# Importance sampling mean approximation
IS_mean <- sum(w * x) / sum(w)

# display importance sampling mean approximation
IS_mean
```

```
[1] 0.4048396
```


### Task 5: Compare target density, accpeted values density and density of weighted values


```r
set.seed(1234)
# compare densities
par(mfrow = c(1,1))

# plot density of accepted values from accept/reject algorithm - histogram
hist(accepted_x, probability = TRUE, xlab = "x", ylab = "Density",
     xlim = c(0, 1), ylim = c(0, 1.7), col = alpha("darkgrey", 0.2),
     border = alpha("black", 0.7), main = "")

# add importance sampling density approximation
lines(d_IS, col = "red", lty = 2, lwd = 2)

# add importance sampling density from simulating samples of the weighted x
lines(density(x_IS), col = "orange", lty = 2, lwd = 2)

# add theoretical target distribution density line - blue line
lines(th, f(th), col = "blue", lwd = 2)

# add legend
legend(
  "topright", 
  legend = c("A/R sampling density", "IS density", 
             "IS W simulation", "Target density"),
  col = c("darkgray", "red", "darkorange", "blue"), lty = c(NA, 2, 2, 1), 
  pch = c(15, NA, NA, NA), lwd = 2, cex = 0.75)

# add plot title
title(main = "Historgram of accept-reject sampling accepted values",
      sub = "Comparison of target, accept-reject sampling and importance sampling densities")
```

<img src="assessment2_files/figure-html/unnamed-chunk-8-1.png" style="display: block; margin: auto;" />


### Task 6: Which of the two algorithms approximate the target distribution better


This is dependent on the type of problem we need to solve, since upon visual inspection both algorithms have adequately approximated the target distribution. 

To evaluate the performance of the algorithms, we can compute the expected value (mean) and compare with the expected value of the target distribution. We will use Monte-Carlo approximation error to compute the approximate expected value of our target density as well as computing intervals for the expected value by applying the central limit theorem. 

However, it should be noted that due to the multi-modal properties of the target distribution, the mean is not representative as oppose to uni-modal distributions. Therefore, in future analysis, comparison of probability distributions with methods such as Kullback-Leibler divergence or computing interval quantiles will be more appropriate.


```r
# true mean approximation and intervals
set.seed(1234)

# set simulation size
N <- seq(1, 10000)

# create custom function to generate random values from our beta-mixt dist
random_f <- function(x) {
  r_f1 <- (1/3) * rbeta(x, 1, 5)
  r_f2 <- (1/3) * rbeta(x, 3, 5)
  r_f3 <- (1/3) * rbeta(x, 10, 5)
  r_f <- r_f1 + r_f2 + r_f3 
  return(r_f)
}

# generate random theta values 
theta <- random_f(10000000)

# assign empty vector for theta mean 
mth <- c()

# assign empty vector for theta mean lower interval  
ic_l <- c()

# assign empty vector for theta mean upper interval 
ic_u <- c()

# for loop to generate Monte-Carlo approximation error
for(j in 1:length(N)) {
  mth[j] <- mean(theta[1:N[j]])
  ic_l[j] <- mth[j] - 1.96 * sqrt(var(theta[1:N[j]])/N[j])
  ic_u[j] <- mth[j] + 1.96 * sqrt(var(theta[1:N[j]])/N[j])
}

# compute true mean approximation
true_mean <- mean(mth, na.rm = TRUE)

# compute 95% CI - lower interval
tm_lower <- mean(ic_l, na.rm=TRUE)

# compute 95% CI - upper interval
tm_upper <- mean(ic_u, na.rm=TRUE)

# Display true mean approximation
true_mean
```

```
[1] 0.4018095
```

```r
# Display 95% CI
c(tm_lower, tm_upper)
```

```
[1] 0.3986628 0.4049756
```




```r
# accept/reject sampling mean approximation and intervals
set.seed(1234)

# compute accept/reject sampling mean approximation
mx <- mean(accepted_x)

# compute 95% CI - lower
mx_lower <- mx - 1.96 * sqrt(var(accepted_x)/n)

# compute 95% CI - upper
mx_upper <- mx + 1.96 * sqrt(var(accepted_x)/n)

# display A/R mean approximation
mx
```

```
[1] 0.4017751
```

```r
# display 95% CI
c(mx_lower, mx_upper)
```

```
[1] 0.3969323 0.4066179
```


```r
# importance sampling mean approximation and intervals
set.seed(1234)

# compute importance sampling mean approximation
mIS <- IS_mean

# compute 95% CI - lower
mIS_lower <- mIS - 1.96 * sqrt(var(w)/n)

# compute 95% CI - upper
mIS_upper <- mIS + 1.96 * sqrt(var(w)/n)

# display IS mean approximation
mIS
```

```
[1] 0.4048396
```

```r
# display 95% CI
c(mIS_lower, mIS_upper)
```

```
[1] 0.3966106 0.4130687
```

Our results demonstrate that the expected values and their 95% equal-tailed credible intervals are as follows:

- True mean: 
  - mean = **0.40181**
  - 95% CI = **[0.39866, 0.40498]**

- Accept-reject sampling:
  - mean = **0.40177**
  - 95% CI = **[0.39693, 0.40662]**

- Importance sampling:
  - mean = **0.40484**
  - 95% CI = **[0.39661, 0.41307]**
  
As we can see, the accept-reject algorithm performed slightly better in approximating the target mean. Therefore, I would use the accept-reject algorithm over the importance sampling algorithm.  

In our particular case, the accept-reject algorithm was able to perform an optimal estimation of the target mean with an ideal 60% acceptance rate. This is due to having known the type of proposal distribution needed to allow us to compute the maximum K value. However, this will not always be the case and importance sampling might be better suited in those situations. 
