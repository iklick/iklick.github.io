---
title: 'Paper Review: Associative Memory'
date: 2021-03-01
permalink: /posts/2023/03/associative-memory/
tags:
  - Nonlinear Dynamics
  - Numerical Methods
  - Stability
  - Computational Efficiency
---

## Outline

<ol>
  <li> Definition of memories </li>
  <li> The nonlinear dynamical model </li>
  <li> Stability </li>
  <li> Computational Efficiency </li>
</ol>

## What is a Memory?

## The Nonlinear Dynamical Model

## Stability of a Memory

## Computational Efficiency

Matrix-vector products are ubiquitous in numerical methods.
In general, computing $$ A \textbf{x} = \textbf{y} $$ for an $$ n $$-dimensional system requires $$ n^2 $$ operations.
While typically not a problem, if $$ n $$ is large or if many matrix vector operations must be performed, this may become prohibitively costly.
Exploiting any special structure of the matrix $$ A $$ can help reduce the amount of time it takes to compute the matrix vector product.

The matrix that appears in the dynamics of the associative memory is the sum of $$ p $$ outer products.

$$
  C = \frac{1}{n} \sum_{k=1}^p \textbf{x}^{k} \textbf{x}{k^T}
$$

The matrix vector product with this matrix and arbitrary vector $$ \textbf{v} $$ can be expressed as a weighted sum of vectors.

$$
  C \textbf{v} = \frac{1}{n} \sum_{k=1}^p \textbf{x}^k \textbf{x}^{k^T} \textbf{v} = \frac{1}{n} \sum_{k=1}^p \left( \textbf{v}^T \textbf{x}^k \right) \textbf{x}^k 
$$

Each product $$ \textbf{v}^T \textbf{x}^k $$ requires $$ n $$ operations and adding each of the $$ p $$ vectors requires another $$ n $$ operations.
In the end, there is on the order $$ p n $$ operations that must be performed, so if $$ p \ll n $$, a considerable amount of time has been saved.

### Dynamical Equations

The dynamics that govern the associative memory model are

\begin{equation}
  \dot{\textbf{y}}_i = \sum_{j=1}^n C_{i,j} \sin (y_j - y_i) + \frac{\epsilon}{n} \sum_{j=1}^n \sin (2 (y_j - y_i))
\end{equation}

This requires about $$ n^2 $$ sine function evaluations, which for large $$ n $$, can become computationally expensive.
Luckily, the sine function of a difference can be decomposed into trigonometric expressions for each term independently.

\begin{equation}
  \begin{array}{l}
  \sin (y_j - y_i) = \sin y_j \cos y_i - \cos y_j \sin y_i\newline
  \cos (y_j - y_i) = \cos y_j \cos y_i + \sin y_j \sin y_i
  \end{array}
\end{equation}

Additionally, using the double angle formula,

\begin{equation}
  \sin 2\theta = 2 \sin \theta \cos \theta
\end{equation}

the dynamical equation can be rewritten,

\begin{equation}
  \dot{y}_i = \sum_{j=1}^n C_{i,j} (\sin y_j \cos y_i - \cos y_j \sin y_i) + \frac{2\epsilon}{n} \sum_{j=1}^n \left( (\cos^2 y_i - \sin^2 y_i) \cos y_j \sin y_j + \cos y_i \sin y_i (\sin^2 y_j - \cos^2 y_j) \right)
\end{equation}

Collecting like terms and pulling everything not dependent on $$ j $$ outside the summation.

\begin{equation}
  \dot{y}_i = \cos y_i \sum_{j=1}^n C_{i,j} \sin y_j - \sin y_i \sum_{j=1}^n C_{i,j} \cos y_j + \frac{2\epsilon}{n} (\cos^2 y_i - \sin^2 y_i) \sum_{j=1}^n \cos y_j \sin y_j + \frac{2\epsilon}{n} \cos y_i \sin y_i \sum_{j=1}^n (\sin^2 y_j - \cos^2 y_j)
\end{equation}

This is now a sum of four weighted vectors after defining

\begin{equation}
  \begin{array}{cc}
    S_1 = \sum_{j=1}^n \cos y_j \sin y_j & S_2 = \sum_{j=1}^n \sin^2 y_j - \cos^2 y_j
  \end{array}
\end{equation}

\begin{equation}
  \dot{\textbf{y}} = \textbf{c} \circ C \textbf{s} - \textbf{s} \circ C \textbf{c} + \frac{2\epsilon}{n} S_1 (\textbf{c}^2 - \textbf{s}^2) + \frac{2\epsilon}{n} S_2 \textbf{c} \circ \textbf{s}
\end{equation}

where the operation $$ \textbf{c} \circ \textbf{s} $$ is defined as the element-wise vector product and $$ C \textbf{s} $$ and $$ C \textbf{c} $$ are computed using the matrix-vector product described above.

### Jacobian-vector Product

### Largest Eigenvalue of the Jacobian
