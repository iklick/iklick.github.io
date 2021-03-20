---
title: 'Ising Model on the Square Lattice: Free Energy'
date: 2021-01-05
permalink: /posts/2021/01/ising-eigenvalues/
tags:
  - statistical mechanics
  - ising model
---

# The Ising Model

I enjoy exploring equations on lattices and one of the most famous lattice problems is the Ising model.

## Model Definition

Let the undirected graph $$ \mathcal{G} = (\mathcal{V},\mathcal{E}) $$ have $$ n $$ nodes and $$ m $$ edges.
Each node is assigned a __spin__, denoted $$ s_j $$, which takes values in the set $$ \{ -1,1 \} $$.

A configuration, $$ \textbf{s} $$, is a particular assignment of spins to the nodes in the graph.
The energy associated with a particular configuration is,

$$
  E(\textbf{s}) = - \sum_{(j,k) \in \mathcal{E}} J_{j,k} s_j s_k - \sum_{j \in \mathcal{V}} h_j s_j
$$

The probability of finding the graph in a particular configuration is determined using the Boltzmann distribution,

$$
  P(\textbf{s}) = \frac{1}{Z} e^{-E(\textbf{s} \beta}
$$

where $$ \beta = \frac{1}{k_B T} $$ can be thought of as an inverse temperature.

## Partition Function

To solve for many quantites of interest of the Ising model, evaluating the partition function in terms of an integral or elementary functions is very useful.
This is not, in general, a simple task and many open problems remain.
The first graph for which the Ising model was solved is the __ring graph__.

$$
  \mathcal{V} = \{1,2,\ldots,N\} \quad \text{and} \quad \mathcal{E} = \{(1,2),(2,3), \ldots, (N-1,N),(N,1)\}
$$

Assuming uniform coupling strength $$ J_{j,k} = J $$ and uniform field strength $$ h_j = h $$

$$
  E(\textbf{s}) = -J \sum_{(j,j+1)} s_j s_{j+1} - h \sum_j s_j
$$

and the partition function,

$$
  \begin{aligned}
  Z &= \sum_{j_1}^2 \sum_{j_2}^2 \ldots \sum_{j_N}^2 e^{-E(\textbf{s}) \beta}\\
  &= \sum_{\textbf{s} \in \mathcal{S}} \text{exp} \left[ J\beta \sum_{j=1}^N s_j s_{j+1} + h\beta \sum_{j=1}^n s_j \right]\\
  &= \sum_{\textbf{s} \in \mathcal{S}} \prod_{j=1}^N \text{exp} \left[ J \beta s_j s_{j+1} + h\beta s_j \right]\\
  &= \sum_{\textbf{s} \in \mathcal{S}} \prod_{j=1}^N \text{exp} \left[ J \beta s_j s_{j+1} + \frac{1}{2} h\beta (s_j + s_{j+1}) \right]
  \end{aligned}
$$

Define the function $$ v(s_j, s_{j+1}) = J\beta s_j s_{j+1} + \frac{1}{2} h\beta (s_j + s_{j+1}) $$ which can be collected into the matrix

$$ 
  V = \left[ \begin{array}{cc}
    e^{J\beta + h\beta} & e^{-J\beta} \\ e^{-J\beta} & e^{J\beta - h \beta}
  \end{array} \right]
$$

and so the partition function can be written as follows,

$$
  Z = \sum_{\textbf{i} \in \mathcal{S}} \prod_{j=1}^N V_{i_j,i_{j+1}}
$$

In this form, Ernst Ising used the __transfer matrix method__ to solve for $$ Z $$ in terms of the eigenvalues of $$ V $$.

### Transfer Matrix

{%
  include theorem.html
  title="Transfer Matrix Identity"
  text="Let \( \mathcal{A} \) be a set of \( N \) square matrices, \( A^{(1)}, A^{(2)}, \ldots, A^{(N)} \) of dimension \( n \) each.
  Then 
  
  $$
    \sum_{i_1 = 1}^n \sum_{i_2 = 1}^n \cdots \sum_{i_N=1}^n \prod_{j=1}^N A^{(j)}_{i_j,i_{j+1}} = \text{Tr}\left( \prod_{j=1}^N A^{(j)} \right)
  $$
  
  The \( N \) summations can equivalently be thought of as a single summation over all \( n^N \) configurations \( \textbf{i} \) such that each element \( i_j  \in \{1,2,\ldots,n\} \)."
  proof="The proof is based on the definition of matrix multiplication.
  The \( (i,k) \)'th element of the matrix product \( AB \) where \( A \) and \( B \) are both square \( n \)-by-\( n \) matrices can be determined using the summation,
  
  $$
    (AB)_{i,k} = \sum_{j=1}^n A_{i,j} B_{j,k}
  $$
  
  The \( (i,k) \)'th element of the matrix product \( ABC \) can then be found using the first result.
  
  $$
    (ABC)_{i,k} = \sum_{\ell=1}^n (AB)_{i,\ell} C_{\ell,k} = \sum_{\ell=1}^n \sum_{j=1}^n A_{i,j} B_{j,\ell} C_{\ell,k}
  $$
  
  This pattern continues for any product of matrices so that,
  
  $$
    \left( \prod_{j=1}^N A^{(j)} \right)_{i,k} = \sum_{j_1 = 1}^n \sum_{j_2 = 1}^n \ldots \sum_{j_{N-1} = 1}^n A_{i,j_1}^{(1)} A^{(2)}_{j_1,j_2} \ldots A^{(N)}_{j_{N-1},k}
  $$
  
  The definition of the trace of a square matrix is the sum of its diagonal elements,
  
  $$
    \text{Tr}(A) = \sum_{j=1}^n A_{i,i}
  $$
  
  Combining the definition of a matrix product and the trace of a matrix yields,
  
  $$
    \text{Tr} \left(\prod_{j=1}^N A^{(j)} \right) = \sum_{i=1}^N \left( \prod_{j=1}^N A^{(j)} \right)_{i,i} = \sum_{i=1}^N \left( \sum_{j_2 = 1}^n \sum_{j_3 = 1}^n \ldots \sum_{j_N = 1}^n A_{i,j_1}^{(1)} A^{(2)}_{j_1,j_2} \ldots A^{(N)}_{j_{N},i} \right)
  $$
  
  Simply substituting the summation index notation \( i \) with \( j_1 \) finishes the proof."
%}

As an example, consider the case $$ n = 2 $$ and $$ N = 4 $$.

### Partition Function ctn'd

The partition function can be rewritten as a matrix trace now,

$$
  Z = \text{Tr} \left( \prod_{j=1}^N V \right) = \text{Tr} V^N = \lambda_1^N + \lambda_2^N
$$

where the eigenvalues $$ \lambda_1 $$ and $$ \lambda_2 $$ for a symmetric 2-by-2 matrix are found from the characteristic equation,

$$
  \lambda^2 - (A_{1,1} + A_{2,2}) \lambda + A_{1,1} A_{2,2} - A_{1,2}^2 = 0
$$

so that,

$$
  \lambda_{1,2} = \frac{A_{1,1} + A_{2,2}}{2} \pm \frac{1}{2} \sqrt{(A_{1,1}+A_{2,2})^2 - 4 A_{1,1}A_{2,2} + 4 A_{1,2}^2}
$$

where the elements,

$$
  \begin{aligned}
    A_{1,1} &= e^{J \beta + h \beta}\\
    A_{1,2} &= e^{-J \beta}\\
    A_{2,2} &= e^{J \beta - h \beta}
  \end{aligned}
$$

Simplifying some of the expressions,

$$
  A_{1,1} + A_{2,2} = e^{J \beta} (e^{h \beta} + e^{-h \beta}) = 2 e^{J \beta} \cosh h \beta
$$

$$
  (A_{1,1} + A_{2,2})^2 - 4A_{1,1} A_{2,2} = (A_{1,1} - A_{2,2})^2 = 4 e^{2J\beta} \sinh^2 h \beta
$$

$$
  A_{1,2}^2 = e^{-2J \beta}
$$

so the eigenvalues are,

$$
  \lambda_{1,2} = e^{J \beta} \cosh h \beta \pm \sqrt{e^{2J \beta} \sinh^2 h \beta + e^{-2J\beta}}
$$

A number of important quantities of the system are determined from the __free energy__ of the system in the __thermodynamic__ limit,

$$
  f(\beta) = -\frac{1}{\beta} \lim\limits_{N \rightarrow \infty} \ln Z(\beta)
$$
