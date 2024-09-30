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

### Functors
Adding a `__call__` method to a class will enable instances to behave as functions.
This is what is called "functor" behavior in Julia.

### Context manager
A context manager is a Python construct that calls `__enter__` when the object is created in a `with` clause, and `__exit__` at the end of the with clause. For instance, this is how Python handles the with `open(...) as f`: construct that you'll often see for opening files without requiring an explicit `close(f)` at the end.

### Generators and iterators
Generators in Python are iterators, just like those in Julia, i.e. lazy iterators over iterables, except in Python once you can't iterate more than once. Iterating a second time returns nothing

```
>>> mygenerator = (x*x for x in range(3))
>>> for i in mygenerator:
...    print(i)
0
1
4
>>> for i in mygenerator:
...    print(i)
```
Python iterators are stateful, meaning that an iterator is an object containing a state which is modified every time an iteration is performed. Instead, Julia's [`iterate`](https://docs.julialang.org/en/v1/base/collections/#Base.iterate) accepts an iterator and a state, and returns a `(item , newstate)` tuple.

In Python, iterators are managed using the [`yield`](https://www.geeksforgeeks.org/use-yield-keyword-instead-return-keyword-python/) keyword. Placed instead of a `return` statement, it transforms the return of the parent function into an iterator which, once iterated over, executes the code until the next `yield` statement. 
Automatically, enough information is stored in the iterator object such that it's possible to resume execution at each iteration.

### File explorer
`os.walk`: generates the file names in a directory tree by walking the tree either top-down or bottom-up. For each directory in the tree rooted at directory top (including top itself), it yields a 3-tuple (dirpath, dirnames, filenames).


## Comparisons with Julia
### Closures
`partial` is Python's equivalent of a closure in Julia.