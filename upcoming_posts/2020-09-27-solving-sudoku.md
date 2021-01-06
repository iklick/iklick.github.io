---
title: 'Personal Interest Problem: Sudoku as an Integer Linear Program'
date: 2020-09-27
permalink: /posts/2020/09/solving-sudoku/
tags:
  - optimization
  - ilp
  - linear programming
---

# What is Sudoku?

If you've ever spent time reading a newspaper or in an airport, you're probably at least slightly familiar with the Sudoku puzzle.
If not, the general premise is the following:

1. There is a 3-by-3 grid of blocks and each block is itself a 3-by-3 grid of cells.
2. Each cell either contains a digit between 1-9 or is empty.
3. The goal of the puzzle is to fill in the blank cells such that each row of 9 cells, each column of 9 cells, and each block of 9 cells contains a single copy of each digit 1-9.

The initial digits provided are the _clues_ and depending on the set of clues a problem may be easy or difficult for humans to solve.

When a human solves a Sudoku puzzle, they typically fill in each cell one at a time by finding a cell which only permits one of the digits 1-9 as the other 8 digits all appear in its row, column and block.

Sometimes when solving a difficult problem though, after filling in some blanks, a human reaches a situation where no single cell can be filled in next, and instead two cells might have to be filled in simultaneously.

Anyone who solves Sudoku puzzles often will have faced puzzles they could not solve by hand.

It was one of these mornings, unable to finish the puzzle by hand while I ate breakfast, that inspired me to write a Sudoku solver.

Before addressing my solution method, a brief sojourn through integer linear programming must be made.

## Integer Linear Programming

Consider an optimization problem that consists of \$ n \$ variables, denoted \$ x_i \$, \$ i = 0,1,\ldots,n-1 \$ with a linear cost function 

$$
  f = \sum_{i=0}^{n-1} c_i x_i
$$

and linear constraints

$$
  g_j^L \leq \sum_{i=0}^{n-1} A_{j,i} x_i \leq g_j^U, \quad j = 0,1,\ldots,m-1
$$

If the cost and constraints are all linear, then the optimization problem is a _linear programming_ problem for which there exist many specialized algorithms.
Combining the above, the generic linear programming problem can be written using the vectors \$ c \in \mathbb{R}^n \$, \$ x \in \mathbb{R}^n \$, \$ g^L \in \mathbb{R}^m \$, \$ g^U \in \mathbb{R}^m \$, \$ x^L \in \mathbb{R}^n \$, \$ x^U \in \mathbb{R}^n \$, and \$ A \in \mathbb{R}^{m \times n} \$.

$$
  \begin{aligned}
    \min && &f = c^T x\\
    \text{s.t.} && &g^L \leq Ax \leq g^U\\
    && &x^L \leq x \leq x^U
  \end{aligned}
$$

where the last line are the box constraints (which should be left out of the other constraints).

If we further restrict that the variables \$ x_i \$ must be _integer_, that is, \$ x \in \mathbb{Z}^n \$, then we call the problem an _integer linear program_ or ILP for short.

$$
  \begin{aligned}
    \min && &f = c^T x\\
    \text{s.t.} && &g^L \leq Ax \leq g^U\\
    && &x^L \leq x \leq x^U\\
    && &x \in \mathbb{Z}^n
  \end{aligned}
$$

Problem solving often consists of mapping your new problem into a recognizable form for which powerful computational tools exist.
This is especially true for ILPs which can be used to describe many many classic optimization problems.

A proper discussion of the computational complexity of ILPs is outside the scope of this post but I highly recommend the following resources for those interested.

A rough estimate of the difficulty of an ILP can be determined by the number of variables, the number of constraints, and the number of nonzeros in the constraint matrix \$ A \$.

## Transcription of Sudoku to an ILP

The first step is to decide what the variables should be. One thought might be to set each cell to be a variable which may takes values 1-9.
This is not a good choice though because the values 1-9 do not represent an arithmetic quantity, but merely a useful classification.
Instead, a membership type variable is introduced instead defined as follows.

```c
  typedef struct {
    glp_prob *ilp;  // The GLPK generic problem interface
    int       nvar; // The number of variables
    int       ncon; // The number of constraints
    int       nnz;  // The number of nonzeros in the constraint matrix
    int      *irow; // The row indices in triplet form
    int      *jcol; // The column indices in triplet form
    double   *val;  // The values in the triplet form
    int      *x;    // The board
  } sudoku_t;
```

The membership variable is defined to be \$ z_{i,j,k} = 1 \$ if cell \$ (i,j) \$ has value \$ k \$ and \$ z_{i,j,k} = 0 \$ otherwise.
More concretely, if cell \$ (3,5) \$ has value \$ 8 \$ then \$ z_{3,5,8} = 1 \$ while \$ z_{3,5,k} = 0 \$ for \$ k = 0,1,2,3,4,5,6,7 \$.

To map the three index variable \$ z_{j,k,\ell} \$ to the single index \$ x_i \$, define the following map,

$$
  i = \ell + 9 k + 81 j, \quad j,k,\ell = 0,1,\ldots,8
$$

```c
  int sudoku_triplet2single (int *i, int j, int k, int l) {
    if (j < 0 || k < 0 || l < 0 || j > 8 || k > 9 || l > 8) {
      *i = -1;
      return 1;
    }
    
    *i = l + 9*k + 81*j;
    
    return 0;
  }
```

which maps each triplet \$ (j,k,\ell) \$ to the single index \$ i = 0,1,\ldots,9^3-1 \$ which lets us equivalently refer the variables as either in terms of single index or triplet index.

For completion, the reverse map makes use of the floor function,

$$
  j = \lfloor \frac{i}{81} \rfloor, \quad k = \lfloor \frac{i - 81 j}{9} \rfloor, \quad \ell = i - 81 j - 9 k
$$

With the variables defined, we can move to representing the constraints.
The constraints come in 4 sets,
1. Each cell can only contain a single digit
2. Each row can only contain each digit once
3. Each column can only contain each digit once
4. Each block can only contain each digit once

### Cell Constraints

Each cell can only contain a single digit, which in terms of our variables, requires that for each pair \$ (i,j) \$, only a single value of \$ k \$ can have \$ z_{i,j,k} = 1 \$ and all others must be zero.
With our definition of the binary variables, this can be represented by the summation,

$$
  \sum_{\ell=0}^{8} z_{j,k,\ell} = 1, \quad j,k = 0,1,\ldots,8
$$

There are 81 cell constraints in total (one for each cell) and each cell contraint has 9 nonzeros.

```c
  for (j = 0; j < 9; j++) {
    for (k = 0; k < 9; k++) {
      for (l = 0; l < 9; l++) {
        sudoku_triplet2single (&i, j, k, l);
        S->irow[idx] = con; S->jcol[idx] = i+1; S->val[idx] = 1.0;
        idx++;
      }
      con++;
    }
  }
```

### Row Constraints

Each row can only contain a single copy of each digit,

$$
  \sum_{k = 0}^{8} z_{j,k,\ell} = 1, \quad j,\ell = 0,1,\ldots,8
$$

There are 81 row constraints in total (one for each row and each digit) and each row constraint has 9 nonzeros.

```c
  for (j = 0; j < 9; j++) {
    for (l = 0; l < 9; l++) {
      for (k = 0; k < 9; k++) {
        sudoku_triplet2single (&i, j, k, l);
        S->irow[idx] = con; S->jcol[idx] = i+1; S->val[idx] = 1.0;
        idx++;
      }
      con++;
    }
  }
```

### Column Constraints

Each column can only contain a single copy of each digit,

$$
  \sum_{j = 0}^8 z_{j,k,\ell} = 1, \quad k,\ell = 0,1,\ldots,8
$$

There are 81 column constraints in total (one for each column and each digit) and each column constraint has 9 nonzeros.

```c
  for (k = 0; k < 9; k++) {
    for (l = 0; l < 9; l++) {
      for (j = 0; j < 9; j++) {
        sudoku_triplet2single (&i, j, k, l);
        S->irow[idx] = con; S->jcol[idx] = i+1; S->val[idx] = 1.0;
        idx++;
      }
      con++;
    }
  }
```

### Block Constraints

Each block can be represented by a set of 3 row indices and 3 column indices starting from \$ o_j,o_k \in \{0,3,6\} \$.

$$
  \sum_{j = o_j}^{o_j + 2} \sum_{k = o_k}^{o_k + 2} z_{j,k,\ell} = 1, \quad \ell = 0,1,\ldots,8, \quad o_j,o_k =0,3,6
$$

There are 81 block constraints in total (one for each block and each digit) and each block constraint has 9 nonzeros.

```c
  for (oj = 0; oj < 9; oj += 3) {
    for (ok = 0; ok < 9; ok += 3) {
      for (l = 0; l < 9; l += 1) {
        for (j = oj; j < oj+3; j += 1) {
          for (k = ok; k < ok+3; k += 1) {
            sudoku_triplet2single (&i, j, k, l);
            S->irow[idx] = con; S->jcol[idx] = i+1; S->val[idx] = 1.0;
            idx++;
          }
        }
        con++;
      }
    }
  }
```

### The Sudoku ILP

$$
  \begin{aligned}
    \min && &\sum_{j=0}^8 \sum_{k=0}^8 \sum_{\ell = 0}^8 c_{j,k,\ell} z_{j,k,\ell}\\
    && &\sum_{\ell = 0}^8 z_{j,k,\ell} = 1, \quad j,k = 0,1,\ldots,8\\
    && &\sum_{k = 0}^8 z_{j,k,\ell} = 1, \quad j,\ell = 0,1,\ldots,8\\
    && &\sum_{j = 0}^8 z_{j,k,\ell} = 1, \quad k, \ell = 0,1,\ldots, 8\\
    && &\sum_{j = o_j}^{o_j+2} \sum_{k=o_k}^{o_k+2} z_{j,k,\ell} = 1, \quad \ell = 0,1,\ldots,8, \quad o_j,o_k = 0,3,6\\
    && &z_{j,k,\ell} \in \{0,1\}, \quad j,k,\ell = 0,1,\ldots,8
  \end{aligned}
$$

## Solving using GLPK

## Similar Problems

## Further Reading
