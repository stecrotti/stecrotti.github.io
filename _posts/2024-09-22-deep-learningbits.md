---
layout: distill
title: Deep Learning bits
date: 2024-09-22 16:20:16
description: 
tags:
categories: 


---

## GPU
### When to transfer data
It is usually a good idea to keep the data on the cpu and only load a batch to the gpu to compute the gradient of the loss at training time. This is because gpus typically have limited memory. However, gpus can also be used for their original application: image processing! If a lot of it is required, e.g. to do data augmentation, it can be advantageous to directly work with the dataset living on the gpu.

## Batch normalization
Pictorial explanation: with batchnorm each layer is less sensitive to the modifications in the parameters of earlier layers due to training. In this way each layer learns a bit more "independently", effectively speeding up the process (see [video](https://www.youtube.com/watch?v=nUUqwaxLnWs&t=473s) by Andrew Ng).

### Alternatives to batchnorm
- Group Normalization: small batch size can lead to poor accuracy in the mean and variance estimates -> split not in batches but in channels, i.e. normalize with the mean and stdev for each channel, taken on all the training data.
- Instance normalization: while batchnorm applies the same normalization to all data in a batch, this one applied the normalization to each single datapoint. From what i understand the statistics is taken over internal dimension (rows, columns)
- Layer normalization: (you guessed it!) for a single data point, use avg and stdev from the whole layer. This eliminates dependence on batch size and apparently is more generalizable to recurrent nets
- RMSNorm: a variant of LayerNorm, scale invariant, more efficient to compute for large layers

Question: batchnorm comprises, after standardization to mean 0 and stdev 1, a further re-scaling and offsetting. Isn't this last step redundant as it could be absorbed into the next operation in the pipeline, which is a linear layer?

Answer 1: the next operation could very well be a non-linear layer (e.g. ReLU).

Answer 2: even if the next operation is linear, and the two could in principle be composed into a single linear layer, keeping the two separate is apprently good for stability during training, helps with vanishing/exploding gradients. Lastly, and this is the more convincing argument to me, in the finetuning phase of transfer learning, the scaling and offset parameters of batchnorm can be tuned to match the statistics of new data, while keeping the rest of the parameters frozen.

### Covariate shift
It's a situation where the statistics of the input data change with respect to those of the training set.
Basic solution: standardize the input.
Observation: this exactly what batchnorm is doing, just not only at the input but in correspondence of each layer.

### Regularization effect of batchnorm
It somehow adds noise, which is known (e.g. dropout) to help with generalization. I'm not convinced about how there is more noise wrt regular batch training.

### Batchnorm at test time
During training, you take statistics over the batch. At test time, one typically processes data one at the time. Solution: use means and variances computed during training and averaged across batches, reduced over iterations using an exponential moving average.

## CNNs
### Flexibility of convolutional kernels
When instantiating a convolutional layer (e.g. with `torch.nn.Conv2d`), one doesn't need to specify the size of the images! Indeed, the only parameters stored there are the entries of the kernel, whose size independent from the image's.

### Fully Convolutional Networks
No fully connected layers, only convolutions.

**Uses**: As opposed to classification, for tasks where the output itself is high-dimensional, such as pixel-wise classification (Segmentation).

**Advantages**: Flexibility, as the sizes of the layers never depend on the input size.
Less parameters, as convolutional kernels are typically much smaller than the input size.

**Implementation**: here, to get a scalar at the end, just average over the `height, width` dimensions.

### 1x1 layer
A convolution layer with kernel of 1-by-1 pixels. It aggregates information from different channels, without mixing pixels.

Uses:
- Change the number of channels without affecting spatial resolution
- Obvious: learn relationship between channels. Typically a non-linearity right after achieves this.


## Momentum
Consider a damped system governed by Newton's law

$$
m\frac{d^2x}{dt^2}+\gamma\frac{dx}{dt}=-\nabla V
$$

where the force is given by a viscous term proportional to the velocity $\frac{dx}{dt}$, and a space-dependent potential $V$.

First, define the negative momentum $q=-m\frac{dx}{dt}$ and substitute to get a system of two differential equations

$$
\begin{cases}
\frac{dq}{dt}=-\frac\gamma m q+\nabla V\\
\frac{dx}{dt}=-\frac qm
\end{cases}
$$

Discretizing in the most naive way (I think this is called Euler's method) gives

$$
\begin{cases}
q_{t+\Delta t}=\left(1-\frac\gamma m \Delta t\right)q_t+\Delta t\nabla V(x_t)\\
x_{t+\Delta t}=x_t-\frac{\Delta t}{m}q_{t+\Delta t}
\end{cases}
$$

Upon reparametrizing $z=\frac{q}{\Delta t}$, we get

$$
\begin{cases}
z_{t+\Delta t}=\left(\Delta t-\frac\gamma m\right)z_t+\nabla V(x_t)\\
x_{t+\Delta t}=x_t-\frac{\Delta t^2}{m}z_{t+\Delta t}
\end{cases}
$$

and finally, defining $\beta=\Delta t-\frac\gamma m$ and $\alpha=\frac{\Delta t^2}{m}$, we get

$$
\begin{cases}
z_{t+\Delta t}=\beta z_t+\nabla V(x_t)\\
x_{t+\Delta t}=x_t-\alpha z_{t+\Delta t}
\end{cases}
$$

which is the update rule for gradient descent with momentum.


## ResNet
### Residual Nets
IDEA: learn the difference (residual) between input and output of a layer, instead of the transformation between them.

Imagine starting from a trained 20-layer CNN. You are not satisfied with the results, want to go deeper. One thing you can do is to add another, say, 36 trainable layers and initialize them to do nothing (in a specific way), i.e. their transfer function is an identity. Now, fine-tune the parameters of those 36 layers. It looks like you either improve or at worst do nothing and performance stays the same as for the 20-layer net.
This sounds convincing enough, but does it solve the vanishing gradient problem?  

Maybe the idea is that fitting zero is easier than fitting a complicated mapping $H(x)$. So, let the net learn $F(x)=H(x)-x$, then add $x$ back: evaluate $F(x)+x$.

## Scaling of parameters at initialization
Consider inputs $x$ standardized with mean zero and std one. These are passing through a linear layer $w$ and then a ReLU layer.
The output of the linear layer is
$$a^i=\sum_{j=1}^{n_H}x^{(i)}_j w_j$$
where $n_H$ is the size of the layer and $x^{(i)}_j$ is the $j$-th component of the $i$-th training point.
After the ReLU layer,
$$b^i = relu(a^i).$$

Suppose we initialize $w$ with gaussian random numbers of mean zero and standard deviation $\sigma$. One sensible thing to say is to pick $\sigma$ such that the output of the layer has the same variance ($1$) as the input.
The variance for the first layer is

$$
\begin{align}
\langle (a^i) ^2\rangle =& \sum_{j,k}\langle x^{(i)}_j x^{(i)}_k\rangle\langle w_j w_k \rangle\\
=&\sum_{j=1}^{n_H}\langle (x^{(i)}_j)^2 \rangle\langle (w_j)^2 \rangle\\
=& \sum_{j=1}^{n_H}\sigma^2\\
=&n_H\sigma^2.
\end{align}
$$

Now, $a^i$ is a gaussian with mean zero and variance $n_H\sigma^2$.
What is the variance of $b^i=relu(a^i)$? We have

$$
\begin{align}
\langle b^i\rangle =& \int_0^\infty da a \mathcal N(0,\sqrt{n_H}\sigma)\\
=& \frac{\sqrt{n_H}\sigma}{2}
\end{align}
$$

and

$$
\begin{align}
\langle (b^i)^2\rangle =& \int_0^\infty da a^2 \mathcal N(0,\sqrt{n_H}\sigma)\\
=& \frac{n_h\sigma}{2}.
\end{align}
$$

So the variance is

$$Var(b^i)=\langle (b^i)^2\rangle-\langle b^i\rangle^2=\frac{n_H\sigma^2}{4.}$$

For it to be equal to one, we pick

$$\sigma=\sqrt{\frac{2}{n_H}}.$$

## Old-style Machine Learning
### Decision trees and random forests
- Decision trees are quick to train and easy to interpret.
- Random forests are trained on subset of rows and cols in the dataset. Idea: outputs from trees are uncorrelated -> their errors cancel in the average.
- Train a RF to predict whether an item will be in the train or valid set, then look for the most relevant features, try removing them and see if accuracy stays ok. If so, the new validation set won't contain out-of-domain data!
- Embeddings: important to encode relationship between predictors. Can be useful to first train NN on embeddings, then use embeddings instead of the actual data on a simpler model, e.g. RF.


## Useful concepts
### Learning rate finder
As a preliminary step before training, one can do this to find a good learning rate: start with a very small one, do a step of gradient descent w.r.t a batch, then increase the learning rate (usually geometrically), and repeat while tracking the loss. 
Continue until the loss stops decreasing.

### Receptive field
It's the set of pixels in the original image that contribute to the value of a certain pixel at intermediate or output layers. Reminds me of the powers of the adjacency matrix of a graph which store information about nodes (pixels here) being reachable from others using paths of a certain maximum length.

### 1-cycle training
Learning rate schedule from small to large, to small again. Cool idea: in the epochs with large learning rate, the optimizer doesn't get trapped in narrow local minima, instead is attracted by large basins, which are better for generalization. (cool idea, but is it true? High-dimensional landscapes can be deceptive)

### Cosine annealing
It's a type of learning rate schedule that has the effect of starting with a large learning rate that is relatively rapidly decreased to a minimum value before being increased rapidly again. The resetting of the learning rate acts like a simulated restart of the learning process and the re-use of good weights as the starting point of the restart is referred to as a "warm restart" in contrast to a "cold restart" where a new set of small random numbers may be used as a starting point.