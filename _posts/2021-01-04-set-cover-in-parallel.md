---
title: 'A Greedy Algorithm in Parallel using MPI'
date: 2021-01-04
permalink: /posts/2021/01/set-over-in-parallel/
tags:
  - Combinatorial Optimization
  - MPI
  - Parallel Programming
  - Set Cover
---

## Purpose of this Post:

This post addresses the well known Set Cover Problem, one of Karp's original 21 NP-complete problems.
I will solve it 3 ways:

1. Finding an exact solution using GLPK
2. Approximately solving it with a greedy algorithm on a single processor
3. Approximately solving it with a parallel greedy algorithm using MPI

Along with the above topics, a discussion of efficient storage of sparse matrices, a description of approximation of algorithms, and an introduction to programming with MPI.

## The Set Cover Problem (SCP)

Let \$ \mathcal{U} = \{0,1,\ldots,m-1\} \$ be the _universe_ consisting of \$ m \$ elements, generically indexed starting from 0. There exists a set of subsets,

$$
  \mathcal{S} = \left\{ \mathcal{S}_j \subseteq \mathcal{U} | j = 0,1,\ldots,n-1 \right\}
$$

Along with each subset \$ \mathcal{S}_j \$ there is a positive weight assigned, \$ w : \mathcal{S} \mapsto \mathbb{R}^+ \$, with the notation convention \$ w(\mathcal{S}_j) = w_j \$.

The SCP attempts to find the subset \$ \mathcal{C} \subseteq \mathcal{S} \$ such that each element \$ \ell \in \mathcal{U} \$ appears in at least one set \$ \mathcal{S}_j \in \mathcal{C} \$ that minimizes the cost \$ \sum_{\mathcal{S}_j \in \mathcal{C}} w_j \$.

### Storage Scheme for the SCP

The data required to represent an instance of the SCP is the size of the universe \$ m \$, the number of sets in \$ \mathcal{S} \$, \$ n \$, the sets themselves, \$ \mathcal{S}_j \$, and the weights, \$ w_j \$.

To store the sets \$ \mathcal{S}_j \in \mathcal{S} \$, note that we could create a matrix \$ A \in \{0,1\}^{m \times n} \$ (a matrix of ones and zeros) where each row corresponds to an element in the universe \$ \mathcal{U} \$ and each column corresponds to a set in \$ \mathcal{S} \$.

For a concrete example consider the case \$ m = 6 \$ and \$ n = 8 \$ with sets

$$
  \begin{aligned}
    \mathcal{S}_0 = \{0,3\} && \mathcal{S}_1 = \{0,1\}\\
    \mathcal{S}_2 = \{0,4,5\} && \mathcal{S}_3 = \{2\}\\
    \mathcal{S}_4 = \{1,2,4\} && \mathcal{S}_5 = \{1,5\}\\
    \mathcal{S}_6 = \{0,2\} && \mathcal{S}_7 = \{3,4\}
  \end{aligned}
$$

Expressing these sets in matrix form yields

$$
  A = \left[ \begin{array}{cccccccc}
    1 & 1 & 1 & 0 & 0 & 0 & 1 & 0\\
    0 & 1 & 0 & 0 & 1 & 1 & 0 & 0\\
    0 & 0 & 0 & 1 & 1 & 0 & 1 & 0\\
    1 & 0 & 0 & 0 & 0 & 0 & 0 & 1\\
    0 & 0 & 1 & 0 & 1 & 0 & 0 & 1\\
    0 & 0 & 1 & 0 & 0 & 1 & 0 & 0
  \end{array} \right]
$$

If we were to store this matrix as an \$ n \times m \$ array of booleans which require 1 byte each, then (ignoring overhead) the matrix would require \$ nm \$ bytes to store.

As the matrix consists mostly of zeros though, the matrix can be treated as a _sparse matrix_ which stores only non-zero elements.
Let \$ n_{nz} \$ be the number of nonzero elements in \$ A \$.
The first approach to storing a sparse matrix is called _triplet form_, which splits the matrix into two integer arrays \$ c \$ and \ r \$ which store the column and row indices of each nonzero element.

The matrix above is represented in triplet form here:

$$
  \begin{aligned}
    r = \left[ \begin{array}{c}
      0 \\ 0 \\ 0 \\ 0 \\ 1 \\ 1 \\ 1 \\ 2 \\ 2 \\ 2 \\ 3 \\ 3 \\ 4 \\ 4 \\ 4 \\ 5 \\ 5
    \end{array} \right] && c = \left[ \begin{array}{c}
      0 \\ 1 \\ 2 \\ 6 \\ 1 \\ 4 \\ 5 \\ 4 \\ 5 \\ 6 \\ 0 \\ 7 \\ 2 \\ 4 \\ 7 \\ 2 \\ 5
    \end{array} \right]
  \end{aligned}
$$

The amount of storage is now 2 \$ n_{nz} \$ arrays of integers, which I assume require 4 bytes each so the total storage if \$ 8 n_{nz} \$ bytes.
For this storage scheme to be efficient, it should require less than the dense boolean matrix,

$$
  8 n_{nz} < nm \quad \rightarrow \quad n_{nz} < \frac{nm}{8}
$$

or in words, the sparsity of the matrix should be less than an eighth.

The triplet format is good for iterating over the elements, but note that arbitrarily, iterating over the rows or columns require searching for the corresponding indices in either \$ r \$ or \$ c \$.
The triplet format written above is already sorted by rows which suggests a further improvement in storage.

Compressed Sparse Row (CSR) format compresses the \$ r \$ array into just information about the starting elements in \$ c \$, often called the pointers to each row (do not confuse this with pointers in the c programming language).

$$
  \begin{aligned}
    p = \left[ \begin{array}{c}
      0 \\ 4 \\ 7 \\ 10 \\ 12 \\ 15 \\ 17
    \end{array} \right] && c = \left[ \begin{array}{c}
      0 \\ 1 \\ 2 \\ 6 \\ 1 \\ 4 \\ 5 \\ 4 \\ 5 \\ 6 \\ 0 \\ 7 \\ 2 \\ 4 \\ 7 \\ 2 \\ 5
    \end{array} \right]
  \end{aligned}
$$ 

Similarly, the Compressed Sparse Column (CSC) format compresses the \$ c \$ array in a similar fashion.

$$
  \begin{aligned}
    p = \left[ \begin{array}{c}
      0 \\ 2 \\ 4 \\ 7 \\ 8 \\ 11 \\ 13 \\ 15 \\ 17
    \end{array} \right] && r = \left[ \begin{array}{c}
      0 \\ 3 \\ 0 \\ 1 \\ 0 \\ 4 \\ 5 \\ 2 \\ 1 \\ 2 \\ 4 \\ 1 \\ 5 \\ 0 \\ 2 \\ 3 \\ 4
    \end{array} \right]
  \end{aligned}
$$

The CSR and CSC storage schemes are useful if we need to operate on the nonzero rows or columns of \$ A \$, respectively.
For instance, using the CSC storage scheme allows us to efficiently loop over the columns in \$ A \$, which represent the sets in \$ \mathcal{S} \$.

```c
  for (int j = 0; j < n; j++) {
    int nj  = p[j+1] - p[j];  // number of elements in Sj
    int *Sj = &r[j];          // pointer to the elements in Sj
  }
```

With this storage scheme, the struct used to store an instance of the setcover problem can be defined,

```c
  typedef struct {
    int     m;  // Size of the universe
    int     n;  // Number of sets
    double *w;  // Weights for each set
    int    *p;  // Pointer to starting index of each set in r
    int    *r;  // Linear array for each of the sets
  } setcover_t;
```

#### Parsing SCP Input Files

To test the following solution methods the datasets from the paper "An algorithm for set covering problems" by J.E. Beasley are collected from the the paper's author's website.

These instances are stored in files with the following format:

1. Line 1 stores the size of the universe and the number of sets, `m` and `n`.
2. The next set of lines stores the set weights `w[0]` through `w[n-1]`.
3. The remainder of the file alternates between lines with a single integer which represents the number of sets in which each element appears and then a series of lines with the corresponding set indices.

In other words, the matrix \$ A \$ is stored in the input files in CSR format.

### Framed as an Integer Linear Program (ILP)

The SCP can efficiently be expressed as an ILP by defining the boolean variables \$ x_j \$, \$ j = 0,1,\ldots,n-1 \$, where

$$
  x_j = \left\{ \begin{array}{ll}
    1 & \text{if } \mathcal{S}_j \in \mathcal{C}\\
    0 & \text{if } \mathcal{S}_j \notin \mathcal{C}
  \end{array} \right.
$$

Define the \$ \{0,1\} \$-matrix 
The SCP can now be expressed as,

$$
  \begin{aligned}
    \min && &\sum_{j=0}^{n-1} w_j x_j\\
    \text{s.t.} && &\sum_{j=0}^{n-1} a_{i,j} x_j \geq 1 && i = 0,1,\ldots,m-1\\
    && &x_j \in \{0,1\} && j = 0, 1, \ldots, n-1
  \end{aligned}
$$



### The Greedy Approximation Algorithm


