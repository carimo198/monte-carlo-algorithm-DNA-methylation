# Monte Carlo algorithms project: DNA methylation of cytosines
## [Link to project](https://carimo198.github.io/monte-carlo-algorithm-DNA-methylation/)
In biology, epigenetics is the study of heritable phenotype changes that do not involve alterations in the DNA sequence. One important epigenetic mechanism is DNA methylation of cytosines. This mechanism has been shown to be related to complex diseases, such as heart diseases, schizophrenia and different cancers, for example at early stages of cancer. For this reason, using large-scale sequencing to detect genome-wide methylation can be used to early detection of some forms of cancer.

The data usually come as relative frequencies of methylation. A relative frequency is a variable defined in [0,1], then a beta distributions can be considered a reasonable model. However data can come from different tissues, then we have different levels of methylation (usually categorised into three different broad categories - low, median, and high methylation), then a "mixture" of beta distributions can be more suitable. 

When analysing a methylation data, it may be needed to simulate values from mixtures of beta distributions. In this project, we simulate values from a mixture of beta distributions using accept/reject sampling and importance sampling. 
