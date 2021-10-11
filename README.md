# Monte Carlo algorithms project: DNA methylation of cytosines
## [Link to project](https://carimo198.github.io/monte-carlo-algorithm-DNA-methylation/)
This repository belongs to my university assignment for Bayesian Inference and Computation subject in Master of Data Science course.

In biology, epigenetics is the study of heritable phenotype changes that do not involve alterations in the DNA sequence. One important epigenetic mechanism is DNA methylation of cytosines. This mechanism has been shown to be related to complex diseases, such as heart diseases, schizophrenia and different cancers, for example at early stages of cancer. For this reason, using large-scale sequencing to detect genome-wide methylation can be used to early detection of some forms of cancer.

The data usually come as relative frequencies of methylation. A relative frequency is a variable defined in [0,1], then a beta distributions can be considered a reasonable model. However data can come from different tissues, then we have different levels of methylation (usually categorised into three different broad categories - low, median, and high methylation), then a "mixture" of beta distributions can be more suitable. 

When analysing a methylation data, it may be needed to simulate values from mixtures of beta distributions. In this project, we simulate values from a mixture of beta distributions using accept/reject sampling and importance sampling. 

For this assignment we were required to complete the following tasks:

1. Plot the density f(x) for the defined beta mixture distributions.
2. Implement an accept/reject algorithm, with Unif(0,1) proposal distribution:
  - Set the seed in R at 1234.
  - Define the value K, i.e. the maximum of the density and associate that with the corresponding x value.
  - Simulate 10,000 values for a uniform distribution.
  - Define the acceptance probability.
  - Accept or reject the simulated value according to the acceptance probability.
3. Compute the observed acceptance rate and compare it with the theoretical one. 
4. Implement an importance sampling algorithm. Compute the importance weights
5. Compare on the same plot the target density, the density of the accepted values for the accept/reject algorithm, and the density of the values weighted by importance sampling.
6. Which of the two algorithms seems to approximate the target distribution better? Which one you would use and why?

My grade for this assignment was 30/30.
