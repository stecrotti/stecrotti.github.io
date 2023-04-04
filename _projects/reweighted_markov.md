---
layout: distill
title: Reweighted Markov processes
description: 
img: assets/img/sirs.png
importance: 1
category: maths
giscus_comments: true
bibliography: reweighted_markov.bib
authors:
  - name: Stefano Crotti
    affiliations:
      name: Politecnico di Torino, Italy
---

When I first started working on reweighted stochastic processes, it was not clear to me why they should be computationally more challenging than their "free" version. 
Here is a tentative explanation of why and to which extent this is true. 
It was first conceived to be read by a friend who is a physicist but is not so used to manipulating probability distributions.

### Mini review of conditional probability

Any distribution over discrete random variables $$p(x_{1},x_{2},\ldots,x_{n})$$ can be written as

$$
\begin{equation}
p(x_{1},x_{2},\ldots,x_{n})=p(x_{1})p(x_{2}|x_{1})p(x_{3}|x_{1},x_{2})\cdots p(x_{n}|x_{1},x_{2},\ldots,x_{n-1})
\end{equation}
$$

by repeated application of the definition of conditional probability
$$p(A|B)=\frac{p(A,B)}{P(B)}.$$ 

We did nothing really, and indeed the RHS of $$(1)$$ is
just as complicated to treat as the LHS. However this can be the first
step towards significant simplifications, as will be clear in the
following.

### A word about notation

Probability is usually defined in terms of events: we call $$p(A)$$
the probability that event $$A$$ happens. When random variables come
into play we shift point of view and work with $$p(X=x)$$, the probability
of the event "variable $$X$$ takes value $$x$$". It is common practice
to use the same letter for the random variable and its value, upper
and lowercase respectively, and to simply write $$p(x)$$ for short.

## Markov processes

A [Markov process](https://en.wikipedia.org/wiki/Markov_chain), also known as Markov chain, is a sequence $$X^{1},X^{2},\ldots,X^{T}$$
of random variables with the property that the distribution of $$X^{t}$$ depends only on the value of $$X^{t-1}$$.
Mathematically this means that $$p(x^{t}|x^{1},x^{2}\ldots,x^{t-1})=p(x^{t}|x^{t-1})$$
for all $$t=2,\ldots T$$. The starting point is some $$p(x^{1})$$.

Combining this property with the decomposition $$(1)$$ we get

$$
\begin{align}
p(x^{1})p(x^{2}|x^{1})p(x^{3}|x^{1},x^{2})\cdots p(x^{T}|x^{1},x^{2},\ldots,x^{T-1})= & p(x^{1})p(x^{2}|x^{1})p(x^{3}|x^{2})\cdots p(x^{T}|x^{T-1})
\end{align}
$$

Therefore the joint probability for a Markov process reads

$$\begin{align}
p(x^{1},x^{2},\ldots,x^{T})= & p(x^{1})\prod_{t=2}^{T}p(x^{t}|x^{t-1})
\end{align}$$

One tends to consider distribtions like this "tractable" because a bunch of operations of interest can be performed efficiently:
- Compute the normalization: this is trivial, the distribution is already
normalized

$$
\begin{equation}
\sum_{x^{1},x^{2},\ldots,x^{T}}p(x^{1})\prod_{t=2}^{T}p(x^{t}|x^{t-1})=\sum_{x^{1}}p(x^{1})\prod_{t=2}^{T}\sum_{x^{t}}p(x^{t}|x^{t-1})=1
\end{equation}
$$

- Compute marginals: this is done iteratively starting from $$p(x^{1})$$.
Then

$$
\begin{equation}
p(x^{t})=\sum_{x^{t-1}}p(x^{t-1},x^{t})=\sum_{x^{t-1}}p(x^{t}|x^{t-1})p(x^{t-1})
\end{equation}\quad\forall t \in 2,\ldots,T$$

with a computational cost of $$\mathcal{O}(qT)$$ where $$q$$ is the
number of values each $$X^{t}$$ can take.
- Sample exactly: the distribution is already written in a form that is almost begging you to sample from it

$$
\begin{equation}
\begin{aligned}x^{1}\sim & p(x^{1})\\
x^{2}\sim & p(x^{2}|x^{1})\\
\vdots\\
x^{T}\sim & p(x^{T}|x^{T-1})
\end{aligned}
\end{equation}
$$

again with cost $$\mathcal{O}(qT)$$.

### Example: 1D random walk

A one-dimensional random walk describes a particle hopping left or
right on a one-dimensional lattice at discrete time steps. Variable $$X^{t}$$ is the position of the particle at time $$t$$: it changes
by $$-1,+1$$ when the particle hops left or right, respectively. In
the simplest case where the probability of going left or right does
not depend on time we have

$$
\begin{equation}
p(x^{t}|x^{t-1})=p_{left}\delta(x^{t},x^{t-1}-1)+p_{right}\delta(x^{t},x^{t-1}+1)
\end{equation}
$$

with $$p_{left}+p_{right}=1$$. In general one could have different $$(p_{left}^{t},p_{right}^{t})$$ at each timestep.

## Reweighted Markov processes

We can now study distributions that are obtained from Markov processes by tilting the distribution with a reweighting factor. The most general object we consider is

$$
\begin{equation}
p(x^{1},x^{2},\ldots,x^{T})\propto W(x^{1},x^{2},\ldots,x^{T})\Phi(x^{1},x^{2},\ldots,x^{T})
\end{equation}
$$

where $$W$$ is a Markov process and $$\Phi$$ an arbitrary non-negative-valued
function. More explicitly,

$$
\begin{equation}
p(x^{1},x^{2},\ldots,x^{T})=\frac{w^{1}(x^{1}){ \prod_{t=2}^{T}w^{t}(x^{t}|x^{t-1})}\Phi(x^{1},x^{2},\ldots,x^{T})}{Z}
\end{equation}
$$

with 
$$\sum_{x^{t}}w^{t}(x^{t}|x^{t-1})=1$$
and $$Z$$ the normalization constant.
<!-- $$Z={\sum_{x^{1},x^{2},\ldots,x^{T}}}w^{1}(x^{1}){\prod_{t=2}^{T}w^{t}(x^{t}|x^{t-1})}\Phi(x^{1},x^{2},\ldots,x^{T})$$. -->

This is **not a Markov process** anymore!
Should you need to convince yourself, try computing $$p(x^{t}|x^{1},x^{2}\ldots,x^{t-1})$$
as, by definition, $$\frac{p(x^{1},x^{2},\ldots,x^{t})}{\sum_{x^{t}}p(x^{1},x^{2},\ldots,x^{t})}$$.
In the case without reweighting $$(1)$$ you will get back the Markov property $$p(x^{t}|x^{1},x^{2}\ldots,x^{t-1})=p(x^{t}|x^{t-1})$$.
The same procedure fails for a reweighted process.

It is not hard to show that **none of the 3 operations that
were easy for Markov processes are affordable anymore: normalizing,
computing marginals and sampling from $$(8)$$ all have
a cost that scales exponentially with $$T$$**.

### Example: constrained 1D random walk

A 1D random walk that has been constrained to land at a certain point $$x_{final}$$ at the final time $$T$$. We have

$$
\begin{equation}
\begin{cases}
w^{t}(x^{t}|x^{t-1}) & =p_{left}\delta(x^{t},L)+(1-p_{left})\delta(x^{t},R)\\
\Phi(x^{1},x^{2},\ldots,x^{T}) & =\delta(x^{T},x_{final})
\end{cases}
\end{equation}
$$

Already for such a simple example it is hopefully clear how things became much more involved
with respect to the case with no reweighting.
For instance, how does one go about sampling from
such a distribution? An idea is to draw trajectories from the free distribution $$w^{1}(x^{1}){ \prod_{t=2}^{T}w^{t}(x^{t}|x^{t-1})}$$
and then discard those samples that do not fulfill the constraint. This is an honest, unbiased sampler.
However, in cases where the probability of a trajectory satisfying the constraint is very small, the 
number of discarded samples before finding a good one can become huge, making the whole 
process a computational nightmare. 
Indeed, in more complex settings, one is often interested in realizations of the process which
are extremely rare <d-cite key="crotti2023large"></d-cite>. 


## Multivariate distributions

All of the above works just as well if, at each time step $$t$$, $$X^{t}$$ is a random vector, i.e. a set of random variables $$\boldsymbol{X}^{t}=(X_{1}^{t},X_{2}^{t},\ldots,X_{N}^{t})$$.
For example, $$\boldsymbol{X}^{t}$$ could describe the state of a system of $$N$$ interacting particles. The problem of computing marginals and sampling for a non-reweighted process is in general not easy anymore: the cost scales as $$\mathcal{O}(q^{N}T)$$ which quickly becomes prohibitive
as $$N$$ grows (here we renamed $$q$$ the number of values taken by
a single $$X_{i}^{t}$$, therefore $$\boldsymbol{X}^{t}$$ can take $$q^{N}$$values). 

The multivariate _and_ biased case is **doubly hard**: typical quantities of interest are the marginal distributions

$$
\begin{equation}
p(x_{i}^{t})=\sum_{\{x_{j}^{t}\}_{j\neq i}}p(\boldsymbol{x}^{t})=\sum_{\{x_{j}^{s}\}_{j\neq i,s\neq t}}p(x^{1},x^{2},\ldots,x^{T})
\end{equation}
$$

which require a **doubly exponential** $$\mathcal{O}(q^{NT})$$ number of operations to be computed.  

### Example: Epidemic spreading according to the SI model

The SI (Susceptible, Infectious) model of epidemic spreading considers a population of $$N$$ individuals each in a state $$X_{i}\in\{S,I\}$$. We use the notation $$\partial i$$ to define the set of individuals in contact with $$i$$. At any time instant $$t$$, an individual $$i$$ that is susceptible can be infected by one of its infected contacts $$j$$ with probability $$\lambda$$. We write the joint distribution as

$$\begin{equation}
p(x^{1},\ldots,x^{T})={\displaystyle \prod_{t}}w^{t}(x^{t}|x^{t-1})={\displaystyle \prod_{t}}\prod_{i}w_{i}^{t}(x_{i}^{t}|\{x_{j}^{t-1}\}_{j\in\partial i})
\end{equation}
$$

with

$$\begin{equation}
w_{i}^{t}(x_{i}^{t}=I|\{x_{j}^{t-1}\}_{j\in\partial i})=1-\prod_{j\in\partial i}\left(1-\lambda\delta(x_{j}^{t-1},I)\right).
\end{equation}
$$

The equations above describe the "free" evolution of the dynamics. What happens in the much realistic case where one introduces the observation that some individuals were tested
at some point and therefore their state at that time is known? Suppose
a set $$O=\{(j,s)\}_{j\in1:N,s\in1:T}$$ of observations: we know that
individual $$j$$ was tested at time $$s$$ and the result was $$y_{j}^{s}\in\{S,I\}.$$ 

By Bayes' formula (which is nothing but a corollary of the definition of conditional probability, really), the probability of an evolution $$\boldsymbol{x^{1}},\ldots,\boldsymbol{x^{T}}$$ given the set of statistically
independent observations $$Y=\{y_{j}^{s}\}_{j,s\in O}$$ is

$$
\begin{equation}
p(\boldsymbol{x}^{1}\boldsymbol{,}\ldots\boldsymbol{,\boldsymbol{\boldsymbol{x}}}^{T}|Y)=\frac{p(Y|\boldsymbol{x}^{1}\boldsymbol{,}\ldots\boldsymbol{,\boldsymbol{\boldsymbol{x}}}^{T})p(\boldsymbol{\boldsymbol{x}^{1}\boldsymbol{,}}\ldots\boldsymbol{\boldsymbol{,\boldsymbol{\boldsymbol{x}}}}^{T})}{\sum_{\boldsymbol{\boldsymbol{x}^{1}\boldsymbol{,}\ldots\boldsymbol{,\boldsymbol{\boldsymbol{x}}}^{T}}}p(Y|\boldsymbol{x}^{1}\boldsymbol{,}\ldots\boldsymbol{,\boldsymbol{\boldsymbol{x}}}^{T})p(\boldsymbol{\boldsymbol{x}^{1}\boldsymbol{,}}\ldots\boldsymbol{\boldsymbol{,\boldsymbol{\boldsymbol{x}}}}^{T})}
\end{equation}
$$

where $$p(\boldsymbol{x}^1,\ldots,\boldsymbol{x}^T)$$ is given by $$(12)$$ and
$$
p(Y|\boldsymbol{x}^{1}\boldsymbol{,}\ldots\boldsymbol{,\boldsymbol{\boldsymbol{x}}}^{T})=\prod_{(j,s)\in O}\delta(x_{j}^{s},y_{j}^{s})
$$.
Therefore, $$p(\boldsymbol{x}^{1}\boldsymbol{,}\ldots\boldsymbol{,\boldsymbol{\boldsymbol{x}}}^{T}|Y)$$ is of the form $$(8)$$.

## Now what?

It is clear at this point that exact computations on reweighted Markov processes are generally not feasible. However, approximations have proposed, some of which are listed below. In most cases the goodness of an approximation depends on the particular form of $$W$$ and $$\Phi$$.

- The most straightforward strategy is probably a Monte Carlo approach
known as importance sampling. Given some observable
of interest $$\mathcal{A}(x^{1},\ldots,x^{T})$$, consider its average
$$A=\mathbb{E}_{p}[\mathcal{A}]={\displaystyle \frac{1}{Z}\sum_{x^{1},x^{2},\ldots,x^{T}}}\mathcal{A}(x^{1},x^{2},\ldots,x^{T})W(x^{1},x^{2},\ldots,x^{T})\Phi(x^{1},x^{2},\ldots,x^{T})$$.
This can be reinterpreted as $$\frac{1}{Z}\mathbb{E}_{W}[\mathcal{A}\Phi]$$, the
expectation of the quantity $$\mathcal{A}\Phi$$ over distribution $$W$$.
Since $$W$$ is a Markov process we know how to sample from it. To build a Monte Carlo estimate for $$A$$, first draw $$M$$ independent samples $$\{\underline{x}^{(\mu)},\ldots,\underline{x}^{(M)}\}$$ from $$W$$ ($$\underline{x}$$ is short for $$x^{1},x^{2},\ldots,x^{T}$$). Then,
$$\begin{equation}
\hat{A}=\frac{\sum_{\mu=1}^{M}\mathcal{A}(\underline{x}^{(\mu)})\Phi(\underline{x}^{(\mu)})}{\sum_{\mu=1}^{M}\Phi(\underline{x}^{(\mu)})}
\end{equation}$$
To see that such approximation is a sensible choice, you can compute the expectation of the numerator and the denominator and verify that their ratio is equal to $$A$$ <d-footnote>In contrast, the expected value of the ratio 
$\mathbb{E}\hat{A}$ is not equal to $A$! This is something I learnt while reasoning on this topic: importance sampling for unnormalized distributions gives biased estimators</d-footnote>.
- Find an "effective" Markov process which is as close as possible to the biased one. An idea is to set up a variational problem: look among a class of Markov processes for the one that minimizes the KL divergence with the target distribution $$p\propto W\Phi$$ <d-cite key="causality"></d-cite>.
- And more...