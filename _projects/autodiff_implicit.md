---
layout: page
title: Differentiation of implicit functions
description: 
img: assets/img/autodiff_implicit_2.png
importance: 1
category: math
---

<!-- Automatic Differentiation (AutoDiff, AD) is a quite cool set of tricks used to compute derivatives
numerically up to machine precision. You write a program $$f$$ that given an input $$x$$ returns 
$$f(x)$$, then turn to your favourite AutoDiff library which will magically compute $$f'(x)$$. -->

We want to compute the derivative of a function $$f$$. What happens when $$f$$ is defined implicitly, for example by a fixed-point equation?
Say that you have another function $$g(x, y)$$ and you want to solve for $$x$$ such that $$g=0$$ at fixed $$y$$.
You can define $$f$$ such that $$g\left(x,f(x)\right)=0$$. So now what is $$f'(x)$$?.

Assuming that all the functions at play are sufficiently smooth, we can write
$$
\begin{align}
0 =& \frac{d}{dx} g\left(x,f(x)\right) \\
  =&   \frac{\partial g}{\partial x} + \left.\frac{\partial g}{\partial y}\right\rvert_{y=f(x)} f'(x)
\end{align}
$$
which gives
$$
\begin{equation}
f'(x) = - \frac{\frac{\partial g}{\partial x}}{\left.\frac{\partial g}{\partial y}\right\rvert_{y=f(x)}}
\end{equation}
$$

Of course my favorite fixed-point equations are the Belief Propagation equations.
For an Ising model on a regular graph of degree $$k$$, the self-consistency equation for the cavity field $$u$$ is
$$
\begin{equation}
u = (k-1)\tanh^{-1}\left[\tanh(u)\tanh(J)\right]
\end{equation}
$$
where $$J$$ is the coupling strength.

For any value of $$J$$, one computes $$u$$ as the fixed point of the above equation. 
Then, the magnetization is given by
$$
\begin{equation}
m = \tanh\left(\beta \frac{k}{k-1}u\right) = 
\end{equation}
$$

Here is what the $$(m,J)$$ plot looks like for $$k=3$$
<div class="row justify-content-sm-center">
    <div class="col-sm-8">
        {% include figure.html path="assets/img/autodiff_implicit_1.png" title="u(J)" class="img-fluid rounded" %}
    </div>
</div>
<!-- <div class="caption">
    This image can also have a caption. It's like magic.
</div> -->

What is $$\frac{dm}{dJ}$$?

To make contact with the notation above, let's define 
$$
\begin{equation}
g(J,u) = (k-1)\tanh^{-1}\left[\tanh(u)\tanh(J)\right] - u
\end{equation}
$$
and $$f:g\left(J,f(J)\right)=0$$.

We are now able to compute the derivative of $$u$$ with respect to $$J$$ using $$(3)$$ (as long as the denominator is nonzero).
Just a final touch of chain rule
$$
\begin{equation}
\frac{dm}{dJ} = \frac{d}{dJ} \tanh\left(\beta \frac{k}{k-1}f(J)\right)  
\end{equation}
$$
and we are done:

<div class="row justify-content-sm-center">
    <div class="col-sm-8">
        {% include figure.html path="assets/img/autodiff_implicit_3.png" title="u(J)" class="img-fluid rounded" %}
    </div>
</div>

Not bad!

Here is the Julia code:
```
import ForwardDiff: derivative
using Plots, LaTeXStrings

pgfplotsx()
Plots.default(grid=:false, label="", msc=:auto, box=:on, labelfontsize=14)

k = 3
J = 1.0
β = 1.0

f(u, J) = (k-1)/β *atanh(tanh(β*u)*tanh(β*J))
g(u) = tanh(β*k/(k-1)*u)

function iterate_fixedpoint(f, init=1.0; maxiter=10^3, tol=1e-16)
    x = init
    err = Inf
    for _ in 1:maxiter
        xnew = f(x)
        abs(xnew) < tol && return 0.0
        err = abs((x-xnew)/x) 
        err < tol && return x
        x = xnew
    end
    error("Fixed point iterations did not converge. err=$err")
end

Js = 0.3:1e-3:1.0
ms = zeros(length(Js))
dmdJs = zeros(length(Js))

for (i,J) in pairs(Js)
    ustar = iterate_fixedpoint(u->f(u,J), maxiter=10^5)
    dfdJ = derivative(J->f(ustar,J), J)
    dfdu = derivative(u->f(u,J), ustar)
    dudJ = dfdJ / (1 - dfdu)
    ms[i] = g(ustar)
    dmdJs[i] = dudJ * derivative(g, ustar)
end

pl = plot(Js, ms, xlabel="J", ylabel="m", ylims=(-0.2, 1.2))
pl2 = plot(Js, dmdJs, xlabel="J", ylabel=L"\frac{dm}{dJ}", ylims=(-2,20))
```