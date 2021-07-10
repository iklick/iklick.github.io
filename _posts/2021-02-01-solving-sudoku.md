---
title: 'Sudoku as an Integer Linear Program'
date: 2021-02-01
permalink: /posts/2021/02/solving-sudoku/
tags:
  - optimization
  - ilp
  - linear programming
---

## Outline

1. Description of Sudoku as a puzzle.
2. Mapping Sudoku to an Integer Linear Programming problem.
3. Some implementation details when using GLPK.
4. Script for solving Sudoku puzzles in C with GLPK.

# What is Sudoku?

Sudoku is a simple numbers problem that appears in most daily newspapers.
I spent many mornings during breakfast racing my parents or siblings to see who could solve the Boston Globe's sudoku first.

1. There is a 3-by-3 grid of blocks and each block is itself a 3-by-3 grid of cells.
2. Each cell either contains a digit between 1-9 or is empty.
3. The goal of the puzzle is to fill in the blank cells such that each row of 9 cells, each column of 9 cells, and each block of 9 cells contains a single copy of each digit 1-9.

{%
  include image.html
  file="/images/sudoku_example.png"
  max-width="500px"
  alt="/images/500x300.png"
  caption="An example of a typical Sudoku puzzle."
%}

The initial digits provided are the _clues_ and depending on the set of clues a problem may be easy or difficult for humans to solve.

When a human solves a Sudoku puzzle, they typically fill in each cell one at a time by finding a cell which only permits one of the digits 1-9 as the other 8 digits all appear in its row, column and block.

{%
  include image.html
  file="/images/sudoku_human.png"
  max-width="500px"
  alt="/images/500x300.png"
  caption="The first few cells filled in using process of elimination."
%}

Sometimes when solving a difficult problem though, after filling in some blanks, a human reaches a situation where no single cell can be filled in next, and instead two cells might have to be filled in simultaneously.

Anyone who solves Sudoku puzzles regularly knows the frustration of being stuck.

It was one of these mornings, unable to finish the puzzle by hand while I ate breakfast, that inspired me to write a Sudoku solver.

## Preparing Sudoku for a Programmatic Solution

How can we define a Sudoku puzzle so a computer can solve it?
Let's define each box for a number as a cell, indexed $$ (i,j) $$ for $$ i,j = 1,2,\ldots,9 $$.
Each cell can receive a number between $$ 1 $$ and $$ 9 $$.
One option might be to have each cell take a value from $$ 1 $$ to $$ 9 $$ but these numbers do not really have any intrinsic meaning. It is equivalent to play Sudoku with letters A thru I as well.

{%
  include image.html
  file="/images/sudoku_labels.png"
  max-width="500px"
  alt="/images/500x300.png"
  caption="The Sudoku grid with cells labeled according to row and column indices."
%}

Instead, for each cell, create $$ 9 $$ variables, indexed $$ c_{i,j,k} $$, for $$ i,j,k = 1,2,\ldots,9 $$, where $$ c_{i,j,k} = 1 $$ if cell $$ (i,j) $$ has value $$ k $$ and $$ c_{i,j,k} = 0 $$ otherwise.

{%
  include image.html
  file="/images/sudoku_variables.png"
  max-width="500px"
  alt="/images/500x300.png"
  caption="The top third of a Sudoku puzzle with the variables for cell (2,9) displayed."
%}

As these variables can only take values 1 or 0, they can also be thought of as true or false statements.

Thinking along this line helps convert the rules of Sudoku into equations.

### Rules of Sudoku as Equations

Each cell in the Sudoku board can only take a single number, obviously.
If cell $$ (i,j) $$ has value $$ 4 $$ for instance, it cannot take any other value (otherwise you'd be writing 2 numbers on top of each other...)
This can be thought of as saying,

> Either $$ c_{i,j,1} $$ or $$ c_{i,j,2} $$ or $$ c_{i,j,3} $$ or $$ \ldots $$ or $$ c_{i,j,9} $$ must be true

(These 'or's are exclusive 'or's, that is, only one can be true at a time.)

Requring only a single true/false variable to be true out of a set of true/false variables can be represented algebraically by restricting their sum to be one.

$$
  \sum_{k=1}^9 c_{i,j,k} = 1, \quad i,j = 1,2,\ldots,9
$$

This summation exactly recreates the either ... or ... or statement above.
By restricting the summation to be one, and since each variable can only be 1 or 0, then only one variable can be 'true'.

Along the same lines, summations to represent the row and column constraints such that each row and column can only have a single instance of each value.

$$
  \begin{equation}
  \sum_{j=1}^9 c_{i,j,k} = 1, \quad i = 1,2,\ldots,9, \quad k = 1,2,\ldots,9
  \end{equation}
$$

$$
  \sum_{i=1}^9 c_{i,j,k} = 1, \quad j = 1,2,\ldots,9, \quad k = 1,2,\ldots,9
$$

Finally, each 3-by-3 box can only have one instance of each number.
Define $$ \mathcal{B}_{\ell} $$ be the row/column indices of box $$ \ell $$.

$$
  \sum_{(i,j) \in \mathcal{B}_{\ell}} c_{i,j,k} = 1, \quad k = 1,2,\ldots,9
$$

With the rules of Sudoku now expressed as equations, the rules of Sudoku can be expressed as the following system of equations.

$$
  \left\{ \begin{array}{lll}
    \sum_{i=1}^9 c_{i,j,k} = 1 & j = 1,2,\ldots,9, & k = 1,2,\ldots,9\\
    \sum_{j=1}^9 c_{i,j,k} = 1 & i = 1,2,\ldots,9, & k = 1,2,\ldots,9\\
    \sum_{k=1}^9 c_{i,j,k} = 1 & i = 1,2,\ldots,9, & k = 1,2,\ldots,9\\
    \sum_{(i,j) \in \mathcal{B}_{\ell}} c_{i,j,k} = 1, & k = 1,2,\ldots,9
  \end{array} \right.
$$

With the rules of Sudoku now encoded as equations over the variables, all that remains in writing the problem for the computer is inputting the available clues.
If cell $$ (i,j) $$ is equal to $$ k $$, then define $$ c_{i,j,k} = 1 $$ and $$ c_{i,j,\ell} = 0 $$ for $$ \ell \neq k $$.

The first step to solving a system of equations like this is to count the number of variables and equations.
As $$ i,j,k $$ can all vary from 1 to 9 freely so there are $$ n_{var} = 9^3 $$ variables.
There are 9 row constraints for each of the 9 variables making 81 row constraints.
Similarly, there are 81 column constraints and 81 cell constraints.
Also, there are 81 block constraints, making $$ n_{con} = 4 * 81 $$ constraints in total.

## Picking the Right Tool

The key aspects to this problem are

1. all of the variables must take an integer value, $$ c_{i,j,k} \in \{0,1\} $$, and
2. all of the constraints are __linear__ equations of the variables.

This type of problem is called an Integer Linear Program (ILP).
Generally, ILPs are optimization problems, that is, along with a set of linear constraints like the ones derived above for the rules of Sudoku.

$$
  \begin{aligned}
    \min && &\textbf{f}^T \textbf{x}\\
    \text{s.t.} && &\textbf{c}^L \leq A \textbf{x} \leq \textbf{c}^U\\
    && &\textbf{x}^L \leq \textbf{x} \leq \textbf{x}^U\\
    && &\textbf{x} \in \mathbb{Z}^{n_{var}}
  \end{aligned}
$$

There are a few ILP solving software programs that are available for free. The one I chose is GLPK since it is readily available across platforms and is relatively easy to use.

## Implementation Details

The next step to solving the Sudoku puzzle with GLPK is converting the variables and constraints into the form required by the software.
The variables are triple indexed, $$ c_{i,j,k} $$, but the variable array required by GLPK is single indexed, $$ x_{\ell} $$.

$$
  \textbf{x} = \left[ \begin{array}{c}
    c_{1,1,1} \\ c_{1,1,2} \\ \vdots \\ c_{1,1,9} \\ c_{1,2,1} \\ \vdots \\ c_{1,9,9} \\ c_{2,1,1} \\ \vdots \\ c_{9,9,9}
  \end{array} \right]
$$

The equation to take a triplet $$ (i,j,k) $$ to the linear index $$ \ell $$ is,

$$
  \ell = k + 9 j + 81 i
$$

As the constraints are linear, this means that the system of equations can be written in the usual form,

$$
  A \textbf{x} = \textbf{b}
$$

where $$ A $$ is a $$ n_{con} \times n_{var} $$ matrix.
To use GLPK, the matrix $$ A $$ must be provided.
Typically, this matrix $$ A $$ is very __sparse__ and so $$ A $$ is provided in __triplet format__.
To provide a matrix in triplet format, the number of nonzeros must be determined, which is straightforward for the Sudoku puzzle as each constraint has 9 nonzeros, so $$ n_{nz} = 9 n_{con} $$.

## Code

The code is written to accept input files with a 9x9 grid of characters where the clues are written from 1-9 and the blank squares can be any non-numeric character.

For example, the following datafile can be used as an input.

```
xxxxxxxxx
xxxxx3x85
xx1x2xxxx
xxx5x7xxx
xx4xxx1xx
x9xxxxxxx
5xxxxxx73
xx2x1xxxx
xxxx4xxx9
```

Assuming the header file ``glpk.h`` is located in ``/usr/include`` and the datafile is named ``example.sudoku``, the executable can be built with the following commands using gcc:
```
isaac:~$ gcc -I/usr/include/ -c sudoku.c
isaac:~$ gcc -o sudoku.out sudoku.o -lglpk
isaac:~$ ./sudoku.out ./example.sudoku
```

where ``sudoku.c`` is shown if you expand the section below.

```c
  #include <stdlib.h> 
  #include <stdio.h>
  #include <string.h>
  #include "glpk.h"
  
  int triplet2single (int i, int j, int k) {
    return k + 9*j + 81*i;
  }
  
  int main (int argc, char **argv) {
    
    /* Variables */
    int nvar, ncon, nnz;  // Problem Sizes
    int i, j, k, l, m;    // Indices
    int bi, bj;           // Block indices
    glp_prob *P;          // GLPK Problem
    int *irow, *jcol;     // Triplet format indices
    double *val;          // Values in triplet matrix
    FILE *fin;            // File storing the problem
    char nums[10] = "123456789";
    char line[100];       // For reading the input
    char *ptr;
    
    /* Check Input */
    if (argc < 2) {
      printf("USAGE: %s <input file>", argv[0]);
    }
    
    /* Open the File for Reading */
    fin = fopen(argv[1], "r");
    if (fin == NULL) {
      printf("Failed to open %s\n", argv[1]);
      return 1;
    }
    
    /* Poblem Dimension */
    nvar = 9*9*9;
    ncon = 4*9*9;
    nnz  = 9*ncon;
    
    /* Allocate Memory to Build the Problem */
    irow = (int*)    calloc (nnz+1, sizeof(int));
    jcol = (int*)    calloc (nnz+1, sizeof(int));
    val  = (double*) calloc (nnz+1, sizeof(double));
    
    /* Create the GLPK Interface */
    P = glp_create_prob ();
    
    /* Set the ILP Size */
    glp_add_cols (P, nvar);  // Columns are variables
    glp_add_rows (P, ncon);  // Rows are constraints
    
    /* Read in the Problem */
    for (i = 0; i < 9; i++) {
      fgets(line, sizeof(line), fin);
      for (j = 0; j < 9; j++) {
        ptr = strchr (nums, line[j]);
        if (ptr != NULL) {
          for (k = 0; k < 9; k++) {
            if (k == ptr[0]-1) {
              glp_set_col_bnds (P, triplet2single(i,j,k)+1, GLP_FX, 1.0, 1.0);
            }
            else {
              glp_set_col_bnds (P, triplet2single(i,j,k)+1, GLP_FX, 0.0, 0.0);
            }
            glp_set_col_kind (P, triplet2single(i,j,k)+1, GLP_BV);
          }
        }
        else {
          for (k = 0; k < 9; k++) {
            glp_set_col_bnds (P, triplet2single(i,j,k)+1, GLP_DB, 0.0, 1.0);
            glp_set_col_kind (P, triplet2single(i,j,k)+1, GLP_BV);
          }
        }
      }
    }
    fclose(fin);
    
    /* Set Constraint Bounds */
    for (i = 0; i < ncon; i++) {
      glp_set_row_bnds (P, i+1, GLP_FX, 1.0, 1.0);
    }
    
    /* Build Constraint Matrix    */
    /*   l - current matrix entry */
    /*   m - current constraint   */
    
    l = 1; m = 1;
    
    /* Add the Cell Constraints */
    for (i = 0; i < 9; i++) {
      for (j = 0; j < 9; j++) {
        for (k = 0; k < 9; k++) {
          irow[l] = m; jcol[l] = triplet2single (i,j,k)+1, val[l] = 1.0;
          l += 1;
        }
        m += 1;
      }
    }
    
    /* Add the Row Constraints */
    for (i = 0; i < 9; i++) {
      for (k = 0; k < 9; k++) {
        for (j = 0; j < 9; j++) {
          irow[l] = m; jcol[l] = triplet2single (i,j,k)+1, val[l] = 1.0;
          l += 1;
        }
        m += 1;
      }
    }
    
    /* Add the Column Constraints */
    for (k = 0; k < 9; k++) {
      for (j = 0; j < 9; j++) {
        for (i = 0; i < 9; i++) {
          irow[l] = m; jcol[l] = triplet2single (i,j,k)+1, val[l] = 1.0;
          l += 1;
        }
        m += 1;
      }
    }
    
    /* Add the Block Constraints */
    for (bi = 0; bi < 9; bi += 3) {
      for (bj = 0; bj < 9; bj += 3) {
        for (k = 0; k < 9; k++) {
          for (i = bi; i < bi+3; i++) {
            for (j = bj; j < bj+3; j++) {
              irow[l] = m; jcol[l] = triplet2single(i,j,k)+1; val[l] = 1.0; 
              l += 1;
            }
          }
          m += 1;
        }
      }
    }
    
    /* Set the Constraint Matrix */
    glp_load_matrix (P, nnz, irow, jcol, val);
    
    /* Solve the LP Relaxation */
    m = glp_simplex (P, NULL);
    if (m != 0) {
      printf("LP Relaxation failed with return code: %i\n", m);
      free (irow); free (jcol); free (val);
      glp_delete_prob (P);
      return 1;
    }
    
    /* Solve the Integer Program */
    m = glp_intopt (P, NULL); 
    if (m != 0) {
      printf("Integer Optimization failed with return code: %i\n", m);
      free (irow); free (jcol); free (val);
      glp_delete_prob (P);
      return 1;
    }
    
    /* Display the Result */
    for (i = 0; i < 9; i++) {
      printf("+-+-+-+-+-+-+-+-+-+\n");
      printf("|");
      for (j = 0; j < 9; j++) {
        for (k = 0; k < 9; k++) {
          if ((int)glp_mip_col_val (P, triplet2single(i,j,k)+1) == 1.0) {
            printf("%i|", k+1);
          }
        }
      }
      printf("\n");
    }
    printf("+-+-+-+-+-+-+-+-+-+\n");
    
    
    
    /* Free Memory */
    free (irow);
    free (jcol);
    free (val);
    glp_delete_prob (P);
    
    return 0;
  }
```
