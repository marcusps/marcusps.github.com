---
layout: post
title: "How long should your quantum benchmarking experiments be?"
date: 2019-12-13
usemathjax: true
author: 'Shelby Kimmel (Assistant Professor, Middlebury College) and Marcus Silva (Principal Researcher, Quantum Systems, Microsoft)'
tags: randomized-benchmarking rb cross-entropy-benchmarking xeb uncertainty
---

**TLDR** We give a simple intuitive (and only semi-rigorous) argument 
  for how to choose the length of
  experiments used to benchmark quantum computers, and also point how
  accurate the resulting noise level estimates can be vs. othe methods.

(see more of Prof. Kimmel's work [here](http://www.shelbykimmel.com))

## *Introduction*

Quantum computers are complex devices that will one day unlock
tremendous computational power -- but only once they are sufficiently
large and error rates are sufficiently low.  Towards that end, many
benchmarking protocols have been devised to help experimentalists
assess the quality of currently availabel prototypical devices, and
guide their development towards much more powerful machines.

One of the most popular benchmarking protocols is called _randomized
benchmarking_ , and is simply referred to as _RB_ [[Knill et al., 2007](https://arxiv.org/abs/0707.0963), 
[Magesan et. al, 2011](https://arxiv.org/abs/1109.6887)]. There are
many variants of RB that focus on benchmarking different aspects of a
quantum computer, including Google's _cross-entropy benchmarking_
(referred to as _XEB_), which was used to help demonstrate the power
of a 53 qubit quantum computer just recently [[Arute et. al, 2019](https://www.nature.com/articles/s41586-019-1666-5)].

These families of protocols, as well as older protocols used to asses
the life-time of quantum states, all rely on fitting experimental data
to exponential decays. The fitting function will be of the form
\begin{equation}
f(k) = A p^k + B,
\end{equation}
where $$k$$ is an
integer corresponding to the length of experimental sequences that are
sampled, and $$p$$ is the quantity we really care about -- it generaly
corresponds to a measure of how good the operations in the quantum computer are,
and the higher $$p$$ is, the better the quantum computer.

### *What do these parameters mean?*

The decay parameter $$p$$ can, in extreme cases, be negative, but in most 
cases it is between $$0$$ and $$1$$, and most often these days, very close to $$1$$
($$p\approx0.99$$ is not uncommon, and some experiments have
demonstrated $$p\approx0.9999$$).

$$A$$ and $$B$$ also tell us something about the quality of the quantum
computer -- namely, how well we can initialize the quantum computer, and how we
can readout the information in the quantum computer.

For the purposes of this discussion, we will take $$A$$ and $$B$$ to be known 
-- they can be estimated largely independently $$p$$, and these estimates don't 
depend on the length of the experiments.

Equivalently, we can think of fitting curves through three different
sequence lengths:

   + $$k=0$$, or a sequence with only preparation and measurement, which yields $$f_0=A+B$$
   + $$k=k_*$$, some finite sequence length with low variance (what we are interested in optimizing here)
   + $$k=\infty$$ (or some very large number), which yields $$f_\infty=B$$.

The last sequence may seem experimentally daunting, but we can also
address that by replacing an infinitely long sequences with the
preparation of the maximally mixed state.

## *Estimating $$p$$ without fitting*

One intuitive approach to estimating $$p$$ is to estimate $$f(k)$$ for
some $$k$$, and then invert the relationship between $$f(k)$$ and $$p$$
(recall we assume we know what $$A$$ and $$B$$ are). More explicitly, our
estimate of $$p$$ will be given by
\begin{equation}
p = \sqrt[k]{\frac{f(k)-B}{A}},
\end{equation}
which is straightforward to compute.

This being a quantum computer, there are many sources of fundamental
uncertainty, and $$f(k)$$ corresponds to the expectation value of a
random distribution of measurement outcomes. We can improve this
estimate by sampling from this distribution (i.e., running the
experiment) more times.

Interestingly, there is a trade-off between sampling $$f(k)$$ more times
to obtain a better estimate, and choosing $$k$$ ( the length of the
experiment ) so that we obtain $$p$$ with greater accuracy for the same 
number of experiments.

It is known that RB (and related experiments) can yield estimate of
$$p$$ that have very small uncertainties. This has been verified
empirically in too many experiments to mention, but this also a result that
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
and requires computing the derivative of $$p$$ with
respect to $$f(k)$$
\begin{equation}
\frac{\partial p}{\partial f(k)} = \frac{1}{A} \frac{1}{k} p^{1-k}
\end{equation}
from which we can deduce that
\begin{equation}
\sigma_p^2 \approx \left| \frac{\partial p}{\partial f(k)} \right|^2~{\sigma_f^2} 
\end{equation}

the quantity we care about minimizing with respect to the sequence
length $$k$$.

## *The optimal sequence length*

Note that $$\sigma_f^2$$ also depends on $$k$$, and taking that dependence
on $$k$$ into account makes things a bit messy whne we look for the minimal 
variance sequence. 

Instead, we use $$\frac{1}{4N}$$ as an
upper bound to $$\sigma_f^2$$, which is independent $$k$$, allowing us to
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
which is remakably similar to the rigorous result for RB 
[[Granade, Ferrie, and Cory](https://arxiv.org/abs/1404.5275)],
but also applicable to more general decay experiments.


## *What do we gain?*

It is instructive to look at how much are we gaining by looking at these 
longer sequences?

We can estimate this by comparing the variance of our estimate using
$$k=k_*$$ versus the variance of taking the shortest sequence $$k=1$$. In
this comparisson we will include the $$k$$ dependence of the uncertainty in our 
estimate of the survival probability $$f_k$$ -- we neglected it in the derivation 
of $$k_*$$, but it is good to make a consistency check. 

In the neighbourhood of $$A=B=\frac{1}{2}$$ (relevant for single qubit
RB), the estimate obtained from the long experiments with sequence
length $$k_*$$ is approximately $$\frac{1}{3}k_*$$ times smaller than the
variance obtained from the shortest sequences. Recall that $$k_*\approx\frac{1}{1-p}$$,
and can be quite large for high quality experiments.

In the regime where $$p\approx 0.999$$, this corresponds to a factor of
$$300$$ improvement in accuracy with the same effort! Trying to obtain
such an improvement by increasing the number of samples would require
the number of samples to be increased by a factor of $$90000$$!

## *But we don't know what $$p$$ is*!

In practice we don't know what $$p$$ is, so we cannot choose
the optimal $$k_*$$.

One approach to get around this is to choose $$k$$ adaptively, 
starting from some initial estimate of $$p$$, and modifying $$k$$ 
as we get better estimates. This can be a bit awkward to automate.

What we can do instead is simply choose multiple $$k$$ corresponding to
$$1-p$$ (a proxy for the error rate) of different orders of magnitude. 
One can then obtain independent estimates for each point, and choose 
the one with smallest uncertanity. Alternatively, one can use non-linear 
least squares fitting through all the points, or other more sophisticated
estimation methods that uses all the data for the estimate, and expect 
improvements over the independent estimates.

It turns out that the uncertainty is not strongly dependent on $$p$$, so a
single choice of $$k_*$$ can estimate a wide range of $$p$$ competently. For 
example, choosing $$k=\{0,100,1000,10000,\infty\}$$, should allow for good 
estimates over the full range $$p\in[0.99,0.9999]$$, but also beyond it.

## *Summary*

Although we have swept some details under the rug (bias of the
estimates, etc.), we hope this gives a intuitive illustration of why
RB and related protocols can yield such high accuracy estimates of
error rates in quantum computers. 
