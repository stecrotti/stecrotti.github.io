---
layout: distill
title: Python bits
date: 2024-09-22 16:30:16
description: 
tags:
categories: 

# toc:
#   - name: Equations
#     subsections:
#       - name: Subsection1
#   - name: Citations

---

## Performance
### Vectorized operations
Vectorized operations are quicker than loops in Python. 
That's because loops are executed in Python, while vectorized operations done by NumPy call C routines under the hood, and C is overwhelmingly faster than Python



## Misc
### Indexing
Python's way of indexing vectors is weird coming from Julia, but look at this: to split a list l before and after index i, you just do `before=l[:i]; after=l[i:]`.

### Dynamic addition of attributes
Turns out in Python you can add attributes to an object at runtime, attributes that were not in the class definition! Things like `obj.new_attribute = 3`.
It sounds pretty dangerous to me, but ok.

### Functors in Python
Adding a `__call__` method to a class will enable instances to behave as functions.
This is what is called "functor" behavior in Julia.

### Context manager
A context manager is a Python construct that calls `__enter__` when the object is created in a `with` clause, and `__exit__` at the end of the with clause. For instance, this is how Python handles the with `open(...) as f`: construct that you'll often see for opening files without requiring an explicit `close(f)` at the end.


## Comparisons with Julia
### Closures
`partial` is Python's equivalent of a closure in Julia.