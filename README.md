# Monte Carlo algorithms project: DNA methylation of cytosines
In biology, epigenetics is the study of heritable phenotype changes that do not involve alterations in the DNA sequence. One important epigenetic mechanism is DNA methylation of cytosines. This mechanism has been shown to be related to complex diseases, such as heart diseases, schizophrenia and different cancers, for example at early stages of cancer. For this reason, using large-scale sequencing to detect genome-wide methylation can be used to early detection of some forms of cancer.

The data usually come as relative frequencies of methylation. A relative frequency is a variable defined in [0,1], then a beta distributions can be considered a reasonable model. However data can come from different tissues, then we have different levels of methylation (usually categorised into three different broad categories - low, median, and high methylation), then a "mixture" of beta distributions can be more suitable. 

When analysing a methylation data, it may be needed to simulate values from mixtures of beta distributions. In this project, we simulate values from a mixture of beta distributions using accept/reject sampling and importance sampling. 

We define $X$ a random variable describing the frequency of methylation. $X_1,...,X_n$ are i.i.d. distributed according to $f(x)$. A mixture of three beta distributions are defined as follows:

$$f(x) = \frac{1}{3} Be(\alpha_1 = 1, \beta_1 = 5) + \frac{1}{3} Be(\alpha_2 = 3, \beta_2 = 5) + \frac{1}{3} Be(\alpha_3 = 10, \beta_3 = 5)$$
