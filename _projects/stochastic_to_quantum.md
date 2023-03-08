---
layout: distill
title: From stochastic processes to quantum mechanics
description: 
img: assets/img/diffusion_heisenberg.png
importance: 1
category: physics
bibliography: stochastic_to_quantum.bib
authors:
  - name: Stefano Crotti
    affiliations:
      name: Politecnico di Torino, Italy
---


Consider a system of $$N$$ $$q$$-ary variables $$x=x_1,\ldots,x_N$$ evolving according to a stochastic process governed by a master equation
$$
\begin{equation}
\frac{d}{dt}p(x,t)=\sum_{x'}\left[p(x',t)w(x'\to x) - p(x,t)w(x\to x')\right]
\end{equation}
$$
where $$w(x\to x')$$ is the probability of transitioning from state $$x$$ to state $$x'$$.

As explained in <d-cite key="henkel1997reaction"></d-cite>, define an abstract vector $$\ket{x}=\ket{x_1,\ldots,x_N}$$ element of a $$N$$-dimensional 
Hilbert space, and its dual $$\bra{x}$$ such that 
$$
\begin{equation}
\braket{x'|x}=\delta(x',x)
\end{equation}
$$
Define also the abstract vector $$\ket{p(t)}$$ as
$$
\begin{equation}
    \ket{p(t)}=\sum_x p(x,t)\ket{x}
\end{equation}
$$
which implies 
$$
\begin{equation}
    p(x,t)=\braket{x|p(t)}.
\end{equation}
$$

Now, the time evolution for $$\ket{p(t)}$$, is given by
$$
\begin{align}
    \frac{d}{dt}\ket{p(t)} &= \sum_x \frac{d}{dt}p(x,t)\ket{x}\\
    &= \sum_{x,x'} \left[p(x',t)w(x'\to x) - p(x,t)w(x\to x')\right]\ket{x}\\
    &= \sum_{x,x'}\left[w(x'\to x)\braket{x'|p(t)} - w(x\to x')\braket{x|p(t)}\right]\ket{x}\\
    &= \sum_{x,x'}\left[w(x'\to x)\ket{x}\bra{x'} - w(x\to x')\ket{x}\bra{x}\right]\ket{p(t)}\\
    &= \sum_{x,x'}\left[w(x\to x')\ket{x'}\bra{x} - w(x\to x')\ket{x}\bra{x}\right]\ket{p(t)}\\
    &= \sum_{x,x'}w(x\to x')\left(\ket{x'}\bra{x} - \ket{x}\bra{x}\right)\ket{p(t)}\\
\end{align}
$$

where to go from $$(8)$$ to $$(9)$$ we renamed $$x\to x'$$, $$x'\to x$$ in the first term.
Finally
$$
\begin{align}
    \frac{d}{dt}\ket{p(t)} &= -\mathcal{H} \ket{p(t)}
\end{align}
$$
where we defined
$$
\begin{equation}
    \mathcal{H} = \sum_{x,x'}w(x\to x')\left(\ket{x}\bra{x}-\ket{x}\bra{x'}\right)
\end{equation}
$$

Now $$(11)$$ is an eigenvalue problem, formally solved by
$$
\begin{equation}
    \ket{p(t)} =e^{-t\mathcal H}\ket{p(0)}
\end{equation}
$$
but intractable in general since the size is exponential in $$N$$.

At this point, we notice that $$(11)$$ somehow resembles a Schrodinger
equation for the so-called "stochastic Hamiltonian" $$\mathcal{H}$$.
There are some differences with a Schrodinger equation, namely $$\mathcal H$$ is not 
Hermitian in general, $$p(t)$$ is
a probability density instead of a wavefunction, there is no imaginary unit.
However, it turns out that for a variety of stochastic processes, there exists an equivalent
quantum system whose Hamiltonian is precisely $$\mathcal H$$.
In such cases, any technique for the diagonalization of the quantum Hamiltonian can be
transferred directly to the time evolution of $p$. 
For example, the steady state of the stochastic process coincides with the eigenvector relative to the
smallest eigenvalue of $$\mathcal H$$.
This means that if there exists an equivalent quantum system whose Hamiltonian is $$\mathcal H$$,
then knowlegde of the ground state of the quantum system directly implies knowledge of the
steady state of $$p(x,t)$$. Neat!

Even when exact results are not available for such quantum problems, efficient approximation
techniques such as the [Density Matrix Renormalization Group](https://en.wikipedia.org//wiki/Density_matrix_renormalization_group) can be employed.



## A simple case: exclusion process with symmetric transitions<d-cite key="alexander1978lattice"></d-cite> 
Consider a system of $$N$$ sites and a bunch a particles hopping from site to site.
Variable $$x_i$$ is $$1$$ if site $$i$$ is occupied, $$0$$ otherwise. 
The term "exclusion" refers to the impossibility of having more than one
particle occupying a site.
The only allowed transition is: a particle can hop from an occupied site $$i$$ to an empty site $$j$$ with probability $$w_{ij}$$.
More formally, the transitions are $$ w(x\to x') = \sum_{i=1}^N w_i(x\to x')$$ with
$$
\begin{align}
    w_i(x\to x') = \sum_{j=i}^N w_{ij}\delta(x_i,1)\delta(x_j,0)\delta(x'_i,0)\delta(x'_j,1)
    \prod_{k\neq i,j} \delta(x_k,x'_k)
\end{align}
$$
For this choice of transitions, the Hamiltonian $$(10)$$ reads
$$
\begin{align}
    \mathcal H &= \sum_{i\neq j}\sum_{\substack{x_i,x_j\\x'_i,x'_j}}w_{ij}\delta(x_i,1)\delta(x_j,0)\delta(x'_i,0)\delta(x'_j,1)
    \left(\ket{x_i x_j}\bra{x_i x_j}-\ket{x_i x_j}\bra{x'_i x'_j}\right)\\
    &= \sum_{i\neq j} w_{ij} \left(\ket{1_i0_j}\bra{1_i0_j}-\ket{0_i1_j}\bra{1_i0_j}\right)\\
    &= \sum_{i<j} w_{ij} \left(\ket{1_i0_j}\bra{1_i0_j}-\ket{0_i1_j}\bra{1_i0_j}\right) +w_{ji} \left(\ket{1_j0_i}\bra{1_j0_i}-\ket{0_j1_i}\bra{1_j0_i}\right) \\
\end{align}
$$
If transition rates are symmetric $$w_{ij}=w_{ji}$$,
$$
\begin{align}
    \mathcal H &= \sum_{i<j} w_{ij} \left(\ket{1_i0_j}\bra{1_i0_j}-\ket{0_i1_j}\bra{1_i0_j}+\ket{1_j0_i}\bra{1_j0_i}-\ket{0_j1_i}\bra{1_j0_i}\right) \\
    &= \sum_{i<j} w_{ij} \mathcal T^{(ij)}
\end{align}
$$
On the standard $$4$$-dimensional basis $$B_i\otimes B_j$$, 
with $$B_\alpha=\left\{\begin{pmatrix}1\\0\end{pmatrix}=\ket{1_\alpha}, \begin{pmatrix}0\\1\end{pmatrix}=\ket{0_\alpha}\right\}$$,
the action of $$\mathcal T^{(ij)}$$ is
$$
\begin{align}
    \mathcal T^{(ij)} &= \begin{pmatrix}
        0 & 0 & 0 & 0\\
        0 & -1 & 1 & 0 \\
        0 & 1 & -1 & 0 \\
        0 & 0 & 0 & 0\\
    \end{pmatrix}
\end{align}
$$
An educated eye (not mine!) will notice that 
$$
\begin{align}
    \mathcal T^{(ij)} &=\sigma^x_i\sigma^x_j+\sigma^y_i\sigma^y_j+\sigma^z_i\sigma^z_j-\mathbb{1}\\
    &= \vec{\sigma}_i\cdot\vec{\sigma}_j-\mathbb{1}
\end{align}
$$
where $$\vec{\sigma}_\alpha=(\sigma_\alpha^x, \sigma_\alpha^y, \sigma_\alpha^z)$$ are the 
[Pauli matrices](https://en.wikipedia.org/wiki/Pauli_matrices) .
The notation $$\sigma^x_i\sigma^x_j$$ means $$\sigma^x_i$$ acting on $$\ket{x_i}$$, $$\sigma^x_j$$ acting on $$\ket{x_j}$$.
Finally we get
$$
\begin{align}
    \mathcal H &= \sum_{i<j} w_{ij} \left(\vec{\sigma}_i\cdot\vec{\sigma}_j-\mathbb{1}\right)
\end{align}
$$
which is the Hamiltonian of a fully-connected, anti-ferromagnetic quantum Heisenberg model.

