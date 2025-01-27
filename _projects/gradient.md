---
layout: distill
title: Natural gradients
description: 
img: assets/img/grad.png
importance: 1
category: math
giscus_comments: true
bibliography: gradient.bib
authors:
  - name: Stefano Crotti
    affiliations:
      name: Politecnico di Torino, Italy
---

Something about gradient descent has been bugging me. When you have a function $$L$$ of some multivariate parameter $$\boldsymbol{\theta}$$ and do gradient descent with learning rate $$\eta$$ to minimize it
$$
\begin{align}
\boldsymbol{\theta} \leftarrow \boldsymbol{\theta} - \eta \nabla L(\boldsymbol{\theta})
\end{align}
$$
something about the units of measurement isn't quite right. Suppose that $$L$$ is a dimensionless quantity and the $$\boldsymbol{\theta}$$'s are all lengths, measured, say, in meters. The components of $$\nabla L=\{\frac{\partial L}{\partial \theta_1},\ldots, \frac{\partial L}{\partial \theta_d}\}$$ have units of meters$$^{-1}$$. But then the update $$\theta_i \leftarrow \theta_i -\eta\frac{\partial L}{\partial \theta_1}$$ is adding up meters with meters$$^{-1}$$. Horror! My elementary school teacher would say it's like adding up potatoes with carrots. 

Notice that this is not just pointless mathematical meticolousness. Say that now you rescale the parameters so that they are measured in centimeters, do one iteration of gradient descent, then rescale back to meters.
The overall result is <d-footnote>This ultimately comes from the fact that whenever units are rescaled, gradient components get rescaled in the same direction (they are co-variant) while vector components get rescaled in the opposite direction (they are contra-variant).</d-footnote>
$$
\begin{align}
\boldsymbol{\theta} \leftarrow \boldsymbol{\theta} - \frac{\eta}{10000} \nabla L(\boldsymbol{\theta})
\end{align}
$$
which to me looks convincing enough that the landscape in which gradient descent moves is quite sensitive to the way your parameters are measured.
It could very well happen that some directions look "flat" when in reality they are varying, just on a much wider length scale. Will gradient descent arrive at the end of the flat region? Eventually, yes, but we clearly don't want to wait too long.

Anyway, maybe there is a solution: it could be that the learning parameter $$\eta$$ is implicitly addressing the problem. If $$\eta$$ has units meters$$^2$$ everything is under control again. We could interpret this as: $$\eta$$ is defining a **scale** for the parameters. If in the example above we had also rescaled $$\eta$$ by the right amount, then updates $$(1)$$ and $$(2)$$ would have been equivalent.
Cool, but what if now units are heterogeneous across the components of $$\boldsymbol{\theta}$$? After all there is only one $$\eta$$ and it can't have the right units for all of them.

Let's try to fix this by picking a different $$\eta_i$$ in each direction to account for the scale of each parameter. Then the update would look something like
$$
\begin{align}
\theta_i \leftarrow \theta_i - \eta_i \frac{\partial L}{\partial \theta_i}
\end{align}
$$
This can't work because the vector we are using for the update is not parallel to the gradient anymore. And we need to move in a direction parallel to the gradient in order to get to the minimum: we know that the gradient is the direction of steepest descent.

It is, right?

## Steepest descent
Let's review why one decides to move along the gradient in the first place.
Starting from an initial point $$\boldsymbol{\theta}$$, the goal is to make a small step $$\Delta\boldsymbol{\theta}$$ in a direction that makes $$L$$ decrease as much as possible. For simplicity let's call $$\Delta\boldsymbol{\theta}=\varepsilon \boldsymbol{x}$$ with $$\varepsilon$$ some small constant. Let's also fix the length of $$x$$ since we are only interested in comparing directions: $$||\boldsymbol{x}||^2=1$$.
We are looking for
$$
\begin{align}
\min_{||\boldsymbol{x}||^2=1} L(\boldsymbol{\theta}+\varepsilon \boldsymbol{x})
\end{align}
$$
Since the step is small, $$L$$ is well approximated by a first-order expansion
$$
\begin{align}
L(\boldsymbol{\theta}+\varepsilon \boldsymbol{x}) \approx L(\boldsymbol{\theta})+\varepsilon \sum_i x_i\frac{\partial L}{\partial \theta_i}
\end{align}
$$
The optimization problem can be solved using a Lagrange multiplier $$\lambda$$ that fixes the norm of $$\boldsymbol x$$
$$
\begin{align}
0 &= \frac{\partial}{\partial x_i}\left[L(\boldsymbol{\theta})+\varepsilon \sum_j x_j\frac{\partial L}{\partial \theta_j} + \lambda\left(1-\sum_k x_k^2\right)\right]\\
&= \varepsilon \frac{\partial L}{\partial \theta_i}-2\lambda x_i
\end{align}
$$
which gives the old familiar result
$$
\begin{align}
x = \frac{\nabla L(\boldsymbol{\theta})}{||\nabla L(\boldsymbol{\theta})||}.
\end{align}
$$

Notice though, that I made an arbitrary choice for what the "length", or the "norm" of $$\boldsymbol x$$ was.
In general, if parameters live in some space $$\mathcal M$$, such space can be equipped with an operation that measures the length of vectors tangent to $$\mathcal M$$. Mathematicians call it a **metric**. At a point $$\boldsymbol \theta$$, the action of a metric $$G$$ on a pair of vectors $$\boldsymbol x, \boldsymbol y$$ tangent to $$\mathcal M$$ at $$\boldsymbol \theta$$ is given by
$$
\begin{align}
G_\theta(\boldsymbol x, \boldsymbol y)=\sum_{ij}g_{ij}(\boldsymbol \theta)x_i y_j
\end{align}
$$
where $$g_{ij}, x_i, y_j$$ are the components of $$G, \boldsymbol x, \boldsymbol y$$ with respect to some basis.
The length of a vector $$\boldsymbol x$$ is $$||\boldsymbol x||_G=G_\theta(\boldsymbol x, \boldsymbol x)=\sum_{ij}g_{ij}(\boldsymbol\theta)x_i x_j$$.

Picking $$g(\boldsymbol \theta)_{ij}=\delta_{ij}\forall\boldsymbol \theta$$ gives back the norm in Euclidean space. 

## Natural gradient
If we re-do the optimization problem $$(4)$$ using an arbitrary metric $$G$$ to measure vectors, we get
$$
\begin{align}
0 &= \frac{\partial}{\partial x_i}\left[L(\boldsymbol{\theta})+\varepsilon \sum_j x_j\frac{\partial L}{\partial \theta_j} + \lambda\left(1-\sum_{kl} g_{kl}(\boldsymbol\theta)x_kx_l\right)\right]\\
&= \varepsilon \frac{\partial L}{\partial \theta_i}-2\lambda \sum_k x_k g_{ki}(\boldsymbol\theta)
\end{align}
$$
which gives
$$
\begin{align}
\boldsymbol x = \frac{G(\boldsymbol\theta)^{-1}\nabla L(\boldsymbol{\theta})}{||\nabla L(\boldsymbol{\theta})||_G}
\end{align}
$$
which is known as the "natural gradient".

So there it is: **the direction of steepest descent in a space with a non-trivial metric is given by the natural gradient**.

It must be pointed out that the computational cost with respect to regular gradient descent is quite larger: it scales as the cube of the number of parameters. This becomes challenging when your problem has millions of parameters.

## Which metric?
This is a neat piece of theory, but what does one do in practice? Is there a good criterion to pick a metric $$G$$?

One choice can be a diagonal metric: for each parameter $$i$$, manually provide some characteristic quantity giving the scale of that parameter. Raise it to the power $$-2$$ and set it as the diagonal element $$g_{ii}$$. Off-diagonal terms are all zero.
This recovers the update proposed heuristically in $$(3)$$.
Since inverting a diagonal matrix is trivial, using such a metric gives essentially no computational overhead.

There is a case where a choice for the metric comes up quite naturally: variational (Bayesian) inference.
Given an intractable distribution $$p(\boldsymbol x)$$, propose a family of distributions $$q_{\boldsymbol \theta}(\boldsymbol x)$$ parametrized by $$\theta$$. Then look for the choice of parameters that minimizes the Kullback-Leibler divergence, or relative entropy, between $$q_{\boldsymbol \theta}$$ and $$p$$:
$$
\begin{align}
D(q_{\boldsymbol \theta}||p)=\sum_{\boldsymbol x}q_{\boldsymbol \theta}(\boldsymbol x)\log\frac{q_{\boldsymbol \theta}(\boldsymbol x)}{p(\boldsymbol x)}
\end{align}
$$
So, the scenario is: we have a space of parameters and want to define a function measuring the distance between two nearby points in this space.
Since each choice of $$\boldsymbol \theta$$ corresponds to a probability distribution $$q_{\boldsymbol \theta}$$, we could define the distance between two points in parameter space as some distance between the distributions they parametrize.
The most natural choice <d-footnote>Natural is of course a matter of taste, but if you like physics and information theory, you'll probably agree with me here.</d-footnote> is again the KL divergence.
The only problem is that, as the name suggests, KL is not a well-defined distance function. For example it's not symmetric $$D(p||q)\neq D(q||p)$$.
However, if we limit ourselves to measuring the distance between points that are close to one another, we get, to second order
$$
\begin{align}
D(q_{\boldsymbol \theta}||q_{\boldsymbol \theta+\Delta\boldsymbol \theta})\approx \sum_{ij}g_{ij}(\boldsymbol \theta)\Delta\theta_i\Delta\theta_j
\end{align}
$$
where
$$
\begin{align}
g_{ij}(\boldsymbol \theta)= \sum_{\boldsymbol x}q_{\boldsymbol \theta}(\boldsymbol x)\left[ \frac{\partial \log q_{\boldsymbol \theta}(\boldsymbol x)}{\partial \theta_i} \frac{\partial \log q_{\boldsymbol \theta}(\boldsymbol x)}{\partial \theta_j}\right]
\end{align}
$$
is quite a famous object: the **Fisher informatrion metric**.

## Summary

Minimizing a function via gradient descent can be done using the natural gradient induced by a certain metric.
This is expected to give better results than regular gradient descent, albeit at the possibly considerable cost of computing and inverting $$g_{ij}(\boldsymbol \theta)$$.

Personally I see this as a nice piece of theory, I'm not expecting to actually make use of natural gradients anytime soon. That said, learning about it made me meditate first on units of measurement and then on how one can make the Fisher information metric pop up naturally.

The reference for this is mostly <d-cite key="amari1998natural"></d-cite>.