---
layout: distill
title: PyTorch bits
date: 2024-09-22 16:40:16
description: 
tags:
categories: 


---

## Tensors

### Broadcasting
Tensor operations in PyTorch: the rules for what happens depending on the size of the input tensors to `torch.matmul` is found [here](https://pytorch.org/docs/stable/generated/torch.matmul.html).
See also the explanation at the [NumPy docs](https://numpy.org/doc/stable/user/basics.broadcasting.html).

## Misc
### `view`
PyTorch's `torch.Tensor.view(*size)` method accepts -1 as (at most) one of the provided dimensions to be inferred. Example: `x.view(-1)` returns a flattened version of `x`, whatever the original size.
### `chunk`
`torch.chunk`==`torch.Tensor.chunk` splits a big array into chunks. Useful when you need to do linalg (matmul) operations on a set of matrices: first stack them together, then perform the operation which takes advantage of parallelism, fast underlying code etc. Finally retrieve each part using `chunk`.
### Refactoring
Apparently it's a good habit to wrap pairs of (conv_layer, relu_layer) in a `torch.nn.Sequential` object, to keep things tidy and debug dimension inconsistencies.

### `item`
To retreive the content of a tensor with one element, use `Tensor.item()`

### Storage viewer
The bare content of a `torch.Tensor` can be inspected via `tensor.untyped_storage`. For instance, when PyTorch expands a tensor for broadcasting, it will behave as the same row repeated three times, but that row is stored in memory only once.

### `stride`
PyTorch has a `stride` method for tensors:

> stride(dim) -> tuple or int
<br>
Returns the stride of :attr:`self` tensor.
<br>
Stride is the jump necessary to go from one element to the next one in the
specified dimension :attr:`dim`. A tuple of all strides is returned when no
argument is passed in.

### Use of ellipsis `...`
When indexing a tensor, can use ellipsis `...` to indicate "all preceding/subsequent dimensions"

### Backpropagation
The way PyTorch does backprop is by storing inputs and outputs at the time of the call to each intermediate function.
The backward step is then computed by using those values.

To define custom differentiable functions, write a class for the function, inheriting from `torch.autograd.Function`.