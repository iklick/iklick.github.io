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

## Motivation

A hospital needs to select some doctors to be on call for an ER who are capable of performing any common required treatment (bone setting, stitches, trauma surgery, etc).
Each doctor is capable of performing some of these tasks but not all of them. The hospital wishes to staff the ER such that there will be a doctor on call capable of performing any required treatment while not demanding too many unpopular on call shifts incurring unnecessary costs. 

A new distribution company needs to select sites to build their storage facilities such that each populated area of interest is within 2 day's travel.

You want to construct a surveillance system in a building with cameras watching every hallway such that the number of cameras is minimum.

The common trait of these problems is a set of statements which must be true (any treatment can be performed, every population center is within 2 days of a distribution center, every hallway surveilled) and a set of resources from which we can make our selection.
Each resource fulfills some of the requirements making some of the statements true, and we must continue to select resources until all of them are true.

These are the hallmarks of a _set cover problem_ which is discussed in this post.

Beyond these small examples, the set cover problem appears as a step in a large number of data analysis tools which can lead to enormous problem instances with thousands or millions of variables.

## The Set Cover Problem (SCP)

The _universe_ consists of \$ m \$ elements denoted

$$
  \mathcal{U} = \left\{ 0,1,\ldots,m-1 \right\}
$$

These are the therapies in the hospital example, the populated areas in the distribution center example, or the hallways in the surveillance example.

The potential resources are defined by the following set of sets,

$$
  \mathcal{S} = \left\{ \mathcal{S}_j \subseteq \mathcal{U} | j = 0,1,\ldots,n-1 \right\}
$$

Each set \$ \mathcal{S}_j \$ represents the set of treatments the j'th doctor could perform, or the set of populated areas a potential facility location could provide for, or the hallways capable of being surveilled by a potential camera location.

There is some cost associated with each selection, defined as \$ w : \mathcal{S}_j \mapsto \mathbb{R}^+ \$, or \$ w(\mathcal{S}_j) = w_j \$ for convenience.

The SCP attempts to find the subset \$ \mathcal{C} \subseteq \mathcal{S} \$ such that each element \$ \ell \in \mathcal{U} \$ appears in at least one of the subsets in \$ \mathcal{C} \$.

### Storage Scheme for the SCP

The data required to represent an instance of the SCP is the size of the universe \$ m \$, the number of sets in \$ \mathcal{S} \$, \$ n \$, the sets themselves, \$ \mathcal{S}_j \$, and the weights, \$ w_j \$.

To store the sets \$ \mathcal{S}_j \in \mathcal{S} \$, note that we could create a matrix \$ A \in \{0,1\}^{m \times n} \$ (a matrix of ones and zeros) where each row corresponds to an element in the universe \$ \mathcal{U} \$ and each column corresponds to a set in \$ \mathcal{S} \$.

For a concrete example consider the case \$ m = 6 \$ and \$ n = 8 \$ with sets

$$
  \begin{aligned}
    \mathcal{S}_0 &= \{0,3\} && \mathcal{S}_1 = \{0,1\}\\
    \mathcal{S}_2 &= \{0,4,5\} && \mathcal{S}_3 = \{2\}\\
    \mathcal{S}_4 &= \{1,2,4\} && \mathcal{S}_5 = \{1,5\}\\
    \mathcal{S}_6 &= \{0,2\} && \mathcal{S}_7 = \{3,4\}
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
The first approach to storing a sparse matrix is called _triplet form_, which splits the matrix into two integer arrays \$ c \$ and \$ r \$ which store the column and row indices of each nonzero element.

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

The amount of storage is now two  \$ n_{nz} \$ arrays of integers, which I assume require 4 bytes each so the total storage is \$ 8 n_{nz} \$ bytes.
For this storage scheme to be efficient, it should require less than the dense boolean matrix,

$$
  8 n_{nz} < nm \quad \rightarrow \quad n_{nz} < \frac{nm}{8}
$$

that is, the sparsity of the matrix should be less than an eighth.

The triplet format is good for iterating over the elements, but note that arbitrarily, iterating over the rows or columns require searching for the corresponding indices in either \$ r \$ or \$ c \$.
The triplet format written above is already sorted by rows which suggests a further memory reduction in storage.

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

With this storage scheme, the structure used to store an instance of an SCP can be defined,

```c
  typedef struct {
    int     m;  // Size of the universe
    int     n;  // Number of sets
    double *w;  // Weights for each set
    int    *p;  // Pointer to starting index of each set in r
    int    *r;  // Linear array for each of the sets
  } setcover_t;
```

### Parsing SCP Input Files

To test the following solution methods the datasets from the paper "An algorithm for set covering problems" by J.E. Beasley are collected from the the paper's author's website.

These instances are stored in files with the following format:

1. Line 1 stores the size of the universe and the number of sets, `m` and `n`.
2. The next set of lines stores the set weights `w[0]` through `w[n-1]`.
3. The remainder of the file alternates between lines with a single integer which represents the number of sets in which each element appears and then a series of lines with the corresponding set indices.

In other words, the matrix \$ A \$ is stored in the input files in CSR format.

Reading in a problem instance can be broken into 2 parts: 

1. Parse the input file into `m`, `n`, `w`, and a temporary CSR matrix.
2. Convert the CSR matrix to a CSC matrix stored in `p` and `r`.



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

#### Using GLPK

The Gnu Linear Programming Kit (GLPK) is a readily available, albeit not the most efficient, linear programming (and integer linear programming) solver.
GLPK is used to find the exact solution of the ILP form of the SCP written above.
The exact solution and the time it takes to compute it is used to measure the efficiency of the greedy algorithm.
The following function is the call to GLPK


```c
  int setcover_ilp (setcover_t *SCP, set_t *C, double *obj) {
    
    glp_prob *ILP;      // The datatype used by GLPK
    int *irow, *jcol;   // Row and column indices for the triplet matrix
    double *val;        // Values in the triplet matrix
    int idx, nnz;
    int retval;
    
    // Create the GLPK object
    ILP = glp_prob_create ();
    
    // Set the number of constraints and variables
    glp_add_rows (ILP, SCP->m);
    glp_add_cols (ILP, SCP->n);
    
    // Define the variables
    for (int k = 0; k < SCP->n; k++) {
      glp_set_col_bnds (ILP, k, GLP_DB, 0.0, 1.0);
      glp_set_obj_coef (ILP, k, SCP->w[k]);
      glp_set_col_kind (ILP, k, GLP_BV);
    }
    
    // Define the constraints
    for (int k = 0; k < SCP->m; k++) {
      glp_set_row_bnds (ILP, k, GLP_LO, 1.0, 0.0);
    }
    
    // Build the constraint matrix in triplet format
    idx = 1;
    nnz = SCP->p[SCP->n];
    irow = (int*) calloc (nnz+1, sizeof(int));
    jcol = (int*) calloc (nnz+1, sizeof(int));
    val  = (double*) calloc (nnz+1, sizeof(double));
    for (int k = 0; k < SCP->n; k++) {
      for (int j = SCP->p[k]; j < SCP->p[k+1]; j++) {
        irow[idx] = SCP->r[j];  // Row index
        jcol[idx] = k;          // Column index
        val[idx]  = 1.0;        // All nonzeros are = 1
        idx += 1;               // Increase the counter
      }
    }
    glp_load_matrix (ILP, nnz, irow, jcol, val);
    
    // Solve the relaxed LP
    retval = glp_simplex (ILP, NULL);
    if (retval != 0) {
      printf("GLPK failed solving the relaxed LP!\n");
      free (irow); free (jcol); free (val);
      return 1;
    }
    
    // Solve the integer program
    retval = glp_intopt (ILP, NULL);
    if (retval != 0) {
      printf("GLPK failed solving the integer program!\n");
      free (irow); free (jcol); free (val);
      return 1;
    }
    
    // Load the solution to the output set
    set_empty (C);
    for (int k = 0; k < SCP->n; k++) {
      if (glp_mip_col_val (ILP, k) == 1.0) {
        set_add (C, k);
      }
    }
    
    // Get the objective value
    *obj = glp_mip_obj_val (ILP);
    
    // Free local memory
    free (irow); free (jcol); free (val);
    return 0;
  }
```


### The Greedy Approximation Algorithm

A greedy algorithm is one where a sequence of (easy) optimization problems are solved in a finite number of steps.
Define the function to maximize (or equivalently minimize) as \$ f : 2^{\mathcal{S}} \mapsto \mathbb{R} \$ so that each step in the greedy algorithm solves the problem,

$$
  \begin{aligned}
    \max\limits_{\mathcal{C}^{(k)} \in \bar{\mathcal{C}}(\mathcal{C}^{(k-1)})} && &f(\mathcal{C}^{(k)})\\
    \text{s.t.} && &\bar{C}(\mathcal{C}^{(k-1)}) \subseteq 2^{\mathcal{S}}
  \end{aligned}
$$

The key to solving this problem efficiently is how the set of feasible solutions \$ \bar{\mathcal{C}} \$ is constructed.

For instance, in additive greedy algorithms, the initial set is empty, \$ \mathcal{C}^{(0)} = \emptyset \$, and each subsequent set of feasible solutions is defined as,

$$
  \bar{\mathcal{C}} (\mathcal{C}^{(k-1)}) = \left\{ \mathcal{C}^{(k)} \subseteq \mathcal{S} | \mathcal{C}^{(k)} = \mathcal{C}^{(k-1)} \cup \{ \ell \}, \ell \in \mathcal{S} \backslash \mathcal{C}^{(k-1)} \right\}
$$

In subtractive greedy algorithms, the initial set is full, \$ \mathcal{C}^{(0)} = \mathcal{S} \$, and each subsequent set of feasible solutions is defined as,

$$
  \bar{\mathcal{C}} (\mathcal{C}^{(k-1)}) = \left\{ \mathcal{C}^{(k)} \subset \mathcal{S} | \mathcal{C}^{(k)} = \mathcal{C}^{(k-1)} \backslash \{ \ell \}, \quad \ell \in \mathcal{C}^{(k-1)} \right\}
$$

Sometimes an additive and subtractive algorithm is useful where each set of feasible solutions consists of all sets after removing or adding an element.

The main point is that \$ \left| \bar{\mathcal{C}} (\mathcal{C}^{(k-1)}) \right| \ll |2^{\mathcal{S}}| \$ ensuring the local optimization problem can be solved using a brute force search.

Along with the function \$ f \$, we define a function \$ g : 2^{\mathcal{S}} \times \mathbb{N} \mapsto \mathbb{R} \$ that represents the exit criteria based on the current set and the iteration index.
For instance, if we are search for a \$ \mathcal{C} \subseteq \mathcal{S} \$ such that \$ |\mathcal{C}| = \ell \$ with an additive greedy algorithm, then \$ g (\mathcal{C},k) = \ell - |\mathcal{C}| \$ is a good choice.
Sometimes the exit criteria is solely based on the iteration count so \$ g(\mathcal{C},k) = k^{\max} - k \$.

1. Initialize with \$ \mathcal{C}^{(0)} \$, \$ k = 0 \$.
2. While \$ g (\mathcal{C}^{(k)},k) \neq 0 \$ 
 1. Set jstar = -1 and fstar = 0.0
 2. Iterate over all \$ \mathcal{C}'_j \in \bar{\mathcal{C}}^{(k)} \$ 
  1. If \$ f(\mathcal{C}'_j) > fstar \$ set jstar = j and fstar = \$ f(\mathcal{C}'_j) \$
 3. If \$ j^* = -1 \$, then no improvement was found and we break out of the while loop.
 4. Update \$ \mathcal{C}^{(k)} \gets \mathcal{C}'_{j^*} \$ and \$ k \gets k+1 \$
3. Return \$ \mathcal{C} \gets \mathcal{C}^{(k)} \$ 

```c
  int setcover_greedy (setcover_t *SCP, set_t *C, double *obj) {
    
    set_t E;
    double cost_best;
    double cost_j;
    int j;
    int selected_j;
    int *Sj;
    
    // Ensure proper initial state for C
    set_resize (C, SCP->n);
    set_remove_all (C);
    
    // Local storage keeps track of which elements are uncovered
    set_init (&E, SCP->m);
    set_add_all (&E);
    
    // Initial objective is 0
    *obj = 0.0;
    
    // While there are uncovered elements in the universe...
    while (!set_is_empty (&E)) {
      
      // Re-initialize the best found solution
      cost_best = 0.0;
      selected_j = -1;
      
      for (j = 0; j < SCP->n; j++) {
        if (!set_is_in (C, j)) {
        
          // Compute the cost to add Sj
          Sj = setcover_get_set (SCP, j);
          cost = (double) set_card_intersect_with_array (&E, Sj);
          cost /= setcover_get_weight (SCP, j);
          
          // If this cost is bigger, set it as the current winner
          if (cost > cost_best) {
            cost_best = cost;
            selected_j = j;
          }
        }
      }
      
      // Take the best found set and add it
      set_add (C, selected_j);
      Sj = setcover_get_set (SCP, selected_j);
      set_remove_some (&E, Sj);
      *obj += setcover_get_weight (SCP, j);
      
    }
    
    set_free (&E);
    return 0;
  }
```

<img src="/images/scp_comparison.png" alt="Comparison" align="center" />
