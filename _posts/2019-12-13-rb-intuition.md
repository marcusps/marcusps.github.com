---
layout: post
title: "How long should your quantum benchmarking experiments be?"
date: 2019-12-13
usemathjax: true
author: 'Shelby Kimmel (Assistant Professor, Middlebury College) and Marcus Silva (Principal Researcher, Quantum Systems, Microsoft)'
tags: randomized-benchmarking rb cross-entropy-benchmarking xeb uncertainty
---

**TLDR** We give a simple, intuitive, semi-rigorous argument 
  for how to choose the length of
  experiments used to benchmark quantum computers.
  If you 
  think your results will look like $$A~p^k+B$$ for $$p\lessapprox 1$$, 
  then you should choose $$k$$ to be
  around $$\frac{1}{1-p}$$.

(see more of Prof. Kimmel's work [here](http://www.shelbykimmel.com))

## *Introduction*

Quantum computers are complex devices that will one day unlock
tremendous computational power -- but only once they are sufficiently
large and error rates are sufficiently low.  Towards that end, many
benchmarking protocols have been devised to help experimentalists
assess the quality of currently available prototypical devices, and
guide their development towards more powerful machines.

One of the most popular benchmarking protocols is called _randomized
benchmarking_ , (RB) [[Knill et al., 2007](https://arxiv.org/abs/0707.0963), 
[Magesan et. al, 2011](https://arxiv.org/abs/1109.6887)]. There are
many variants of RB that benchmark different aspects of
quantum computers, including Google's _cross-entropy benchmarking_
(referred to as _XEB_), which was recently used to help demonstrate the power
of a 53 qubit quantum computer [[Arute et. al, 2019](https://www.nature.com/articles/s41586-019-1666-5)].

These RB protocols, as well as older protocols used to asses
the life-time of quantum states, all rely on fitting experimental data
to exponential decays. The fitting function will be of the form
\begin{equation}
f(k) = A p^k + B,
\end{equation}
where $$k$$ is an
integer corresponding to the length of experimental sequences that are
sampled, $$p$$ is the quantity we really care about -- it generaly
corresponds to a measure of how good the operations in the quantum computer are,
and the higher $$p$$ is, the better the quantum computer -- and $$f(k)$$ is the expectation of 
binomial random variable that we can take a sample of by running a single experiment.

### *What do these parameters mean?*

The decay parameter $$p$$ can, in extreme cases, be negative, but in most 
cases it is between $$0$$ and $$1$$.
(A value of $$p=1$$ means no errors have occurred; in recent experiments $$p\approx0.99$$ is not uncommon, and some experiments have
demonstrated $$p\approx0.9999$$, signalling extremely small error rates).

$$A$$ and $$B$$ provide information about
how well we can initialize the quantum computer, and how well we
can read out the information in the quantum computer.

For the purposes of this discussion, we will take $$A$$ and $$B$$ to be known, as 
they can be estimated largely independently $$p$$ and in a way that is independent of experiment length (
see the post script below).

## *Estimating $$p$$ without fitting*

One intuitive approach to estimating $$p$$ is to estimate $$f(k)$$ for
some $$k$$, and then invert the relationship between $$f(k)$$ and $$p$$
(recall we assume we know what $$A$$ and $$B$$ are). More explicitly, our
estimate of $$p$$ will be given by
\begin{equation}
p = \sqrt[k]{\frac{f(k)-B}{A}},
\end{equation}
which is straightforward to compute.

Since $$f(k)$$ corresponds to the expectation value of a
random distribution of measurement outcomes, we can improve this
estimate by taking more samples from this distribution (i.e., running the
experiment more times).

It is known that RB (and related experiments) can yield an estimate of
$$p$$ that has very small uncertainties. This has been verified
empirically in too many experiments to mention, but this is also a result that
has also been proven mathematically [[Epstein et. al, 2013](https://arxiv.org/abs/1308.2928), 
[Granade et. al, 2014](https://arxiv.org/abs/1404.5275)].

The details of these proofs may be somewhat opaque for those not
familiar with theoretical statistics, so here we give an argument that
requires minimal knowledge of statistics, and only cursory familiarity
with uncertainty propagation.

## *Quantifying uncertainty, and how it propagates*

First, let's look at how we can calculate the uncertainty in our final 
estimate of $$p$$.

One way to estimate uncertainty in an estimate of a random quantity is
to compute its *variance*. Recall that $$f(k)$$ is the mean of a random
distribution, and we are only able to sample from that distribution,
so our estimate of $$f(k)$$ will also be random.

Although we can empirically measure the uncertainty in our estimate of
$$f(k)$$, as well as the uncertainty in our estimate of $$p$$, we are more
interested in *predicting* the uncertainty in $$p$$ based on $$k$$, so we
can choose $$k$$ before running an experiment.

We do that be assuming the uncertainty in $$f(k)$$ is small, and
linearizing $$p$$ around $$f(k)$$ to estimate what the resulting
$$\sigma_p^2$$ would be. This is known as *uncertainty propagation*
[[Wikipedia](https://en.wikipedia.org/wiki/Propagation_of_uncertainty)],
and gives us the following relationship between $$\sigma_f^2$$ and $$\sigma_f^2$$:
\begin{equation}
\sigma_p^2 \approx \left| \frac{\partial p}{\partial f(k)} \right|^2~{\sigma_f^2} .
\end{equation}
Using this equation requires computing the derivative of $$p$$ with
respect to $$f(k)$$:
\begin{equation}
\frac{\partial p}{\partial f(k)} = \frac{1}{A} \frac{1}{k} p^{1-k}.
\end{equation}

Our goal is to minimize $$\sigma_p$$ with respect to the sequence
length $$k$$.

## *The optimal sequence length*

Note that $$\sigma_f^2$$ also depends on $$k$$, and taking that dependence
on $$k$$ into account makes things a bit messy as we look for the minimal 
variance sequence length. 
Instead, we use $$\frac{1}{4N}$$ as an
upper bound on $$\sigma_f^2$$, which is independent $$k$$, allowing us to
ignore the contribution from the survival probability, and reducing
the minimization of $$\sigma_p^2$$ to the minimization of
$$\left|\frac{\partial p}{\partial f(k)}\right|^2$$. 

If we look at $$\left|\frac{\partial p}{\partial
f(k)}\right|^2$$, we find
\begin{equation}
\left|\frac{\partial p}{\partial
f(k)}\right|^2 = \frac{p^2}{A^2} \frac{1}{k^2 p^{2k}},
\end{equation}
which can be minimized by maximizing $$k~p^{k}$$.

After a derivative and some light algebra we find the optimal sequence length to be
\begin{equation}
k_* = - \frac{1}{\log p} \approx \frac{1}{1-p},
\end{equation}
where the second approximation is in the $$p\approx 1$$ limit. This conclusion is remarkably similar to the rigorous result for RB 
[[Granade, Ferrie, and Cory](https://arxiv.org/abs/1404.5275)],
but also applicable to more general decay experiments.


## *What do we gain?*

It is instructive to look at how much are we gaining by looking at these 
longer sequences. We can estimate this by comparing the variance of our estimate using
$$k=k_*$$ versus the variance of taking the shortest sequence $$k=1$$. In
this comparison we will include the $$k$$ dependence of the uncertainty in our 
estimate of the survival probability $$f_k$$ -- we neglected it in the derivation 
of $$k_*$$, but it is good to make a consistency check. 

In the neighborhood of $$A=B=\frac{1}{2}$$ (relevant for single qubit
RB), the estimate obtained from the long experiments with sequence
length $$k_*$$ is approximately $$\frac{1}{3}k_*$$ times smaller than the
variance obtained from the shortest sequences. Recall that $$k_*\approx\frac{1}{1-p}$$,
which can be quite large for high quality devices.

For example, in the regime where $$p\approx 0.999$$, this corresponds to a factor of
$$17$$ improvement in accuracy (the standard deviation) with the same effort! Trying to obtain
such an improvement by increasing the number of samples would require
the number of samples to be increased by a factor of $$300$$!

## *But we don't know what $$p$$ is*!

In practice we don't know what $$p$$ is, so we cannot choose
the optimal $$k_*$$.

One approach to get around this is to choose $$k$$ adaptively, 
starting from some initial estimate of $$p$$, and modifying $$k$$ 
as we get better estimates. This can be a bit awkward to automate.

What we can do instead is simply choose multiple $$k$$ of different orders of magnitude. 
It turns out that the uncertainty is not strongly dependent on $$p$$, so a
single choice of $$k_*$$ can estimate a wide range of $$p$$ competently. For 
example, choosing $$k=\{0,100,1000,10000,\infty\}$$, should allow for good 
estimates over the full range $$p\in[0.99,0.9999]$$, but also beyond it.

Given a set of experimental data for such a range of $$k$$, 
one can then obtain independent estimates for each point, and choose 
the one with smallest uncertainty. Alternatively, one can use non-linear 
least squares fitting through all the points, or other more sophisticated
estimation methods that use all the data for the estimate, and expect 
improvements in accuracy over the independent estimates.


## *Summary*

Although we have swept some details under the rug (e.g. bias of the
estimates), we hope this gives a intuitive illustration of why
RB and related protocols with long sequence lengths can yield such high accuracy estimates of
error rates in quantum computers. 

### Estimating A and B

In this post, we mentioned that it is straightforward to esimate $$A$$ and $$B$$. 
To do this, one can run a zero-length RB sequence and an infinite length RB sequence:

   + $$k=0$$, or a sequence with only preparation and measurement, which yields $$f_0=A+B$$
   + $$k=\infty$$ (or some very large number), which yields $$f_\infty=B$$.

The last sequence may seem experimentally daunting, but we can also
address that by replacing an infinitely long sequences with the
preparation of the maximally mixed state.
