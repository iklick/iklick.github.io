---
title: 'Paper Review: Associative Memory'
date: 2021-02-22
permalink: /posts/2021/02/associative-memory/
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

This question is far beyond the scope of a blog post.
Instead, let's just think of a memory as information.
Specifically, let's say a memory consists of $$ n $$ bits (a variable that can take one of two possible values).
We can represent images and text as $$ n $$ bits, for instance, a black and white image with $$ n $$ pixels.

<br/>

For concreteness, let each memory be an array with $$ n $$ bits taking values either $$ +1 $$ or $$ -1 $$.
There are $$ 2^n $$ possible memories, which seems like a strange idea, to put a cap on the number of memories.
Note though that for just $$ n = 300 $$, there are more possible memories than atoms in the universe.
For $$ n = 10,000 $$ the number of possible memories is now the number of atoms if every atom in our universe was another entire universe.

<br/>

Geometrically, the memories are the corners of the $$ n $$-dimensional hyper-cube centered at the origin with side length two.
This suggests a continuous relaxation which I call the __memory space__, $$ \mathcal{M} $$, which consists of all vectors $$ \textbf{x} \in \mathbb{R}^n $$ such that $$ x_j \in [-1,1] $$, $$ j = 1,2,\ldots,n $$.


<br/>

Out of this enormous number of memories, let's pick $$ p $$ of them that we care about, and label them $$ \textbf{x}_k $$, $$ k = 1,2,\ldots,p$$.
Create the square symmetric matrix $$ C $$ as the sum of outer products of the memory.

\begin{equation}
  C = \frac{1}{n} \sum_{k=1}^p \textbf{x}_k \textbf{x}_k^T
\end{equation}

## Brief Summary of Gradient Dynamical Systems

Let $$ V : \mathbb{R}^n \mapsto \mathbb{R} $$ be a continuous, analytic, function and define the gradient dynamical system,

\begin{equation}
  \dot{\textbf{y}}(t) = f(\textbf{y}(t)) = - \nabla V(\textbf{y}(t))
  \label{eq:grad}
\end{equation}


{%
  include theorem.html
  title="Stability of Gradient Systems"
  text="Let \( V \) be a real, analytic, function in a neighborhood of \( \bar{\textbf{y}} \in \mathbb{R}^n \). Then \( \bar{\textbf{y}} \) is a stable fixed point of Eq. \eqref{eq:grad} if and only if it is a local minimum of \( V \)."
  proof="The proof is beyond the scope of this post but a nice discussion of this result can be found in this post <a>https://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.992.4772&rep=rep1&type=pdf</a>"
%}

Further restrict the function $$ V $$ in the following ways.

<ol>
  <li> It is lower bounded, \( \exists V_{\min} \) s.t. \( V(\textbf{y}) \geq V_{\min}, \quad \forall \textbf{y} \in \mathbb{R}^n \). </li>
  <li> There is a set of local minima, \( \bar{\textbf{y}} \), which satisfy for some \( \epsilon > 0 \), \( V(\bar{\textbf{y}}) \leq V(\bar{\textbf{y}} + \textbf{y}) \) and \( ||\bar{\textbf{y}} - \textbf{y}|| \leq \epsilon \). </li>
</ol>

Then, unless the initial, $$ \textbf{y}(0) $$, is pathologically located at a saddle point of the dynamical system, the dynamical system in Eq. \eqref{eq:grad} will evolve towards some stable fixed point.

## Dynamical System to Recover Memories

Define the function,

\begin{equation}
  V(\textbf{y}(t)) = - \sum_{i,j = 1}^n C_{i,j} \cos (y_j(t) - y_i(t)) - \frac{\epsilon}{2n} \sum_{i,j = 1}^n \cos 2(y_j(t) - y_i(t))
  \label{eq:potential}
\end{equation}

This function is clearly lower bounded as $$ -1 \leq \cos (y) \leq 1 $$.
The gradient dynamical system ,

\begin{equation}
  \dot{y}_i(t) = f_i(\textbf{y}(t)) = \sum_{j=1}^n C_{i,j} \sin (y_j(t) - y_i(t)) + \frac{\epsilon}{n} \sum_{j=1}^n \sin 2(y_j(t) - y_i(t))
  \label{eq:dynamics}
\end{equation}

The fixed points of this system correspond to states $$ \bar{\textbf{y}} $$ where every pair of elements differ by a multiple of $$ \pi $$.
Notice that the states appear as differences of each other so if $$ \bar{\textbf{y}} $$ is a fixed point, then so is $$ \bar{\textbf{y}} + c \textbf{1}_n $$ for any $$ c \in \mathbb{R} $$.

We know that the dynamical system will evolve towards a fixed point $$ \bar{\textbf{y}} $$ but we are not sure which one yet.
To determine whether the system evolves towards a memory or towards some other corner of the memory space.

We have to check the Jacobian of the dynamics.

\begin{equation}
  A_{i,j}(\textbf{y}(t)) = C_{i,j} \cos (y_j(t) - y_i(t)) - \delta_{i,j} \sum_{k=1}^n C_{i,k} \cos (y_k(t) - y_i(t)) + \frac{2\epsilon}{n} \cos 2(y_j(t) - y_i(t)) - \delta_{i,j} \frac{2\epsilon}{n} \sum_{k=1}^n \cos 2 (y_k(t) - y_i(t))
  \label{eq:jacobian}
\end{equation}

At a fixed point, $$ \bar{\textbf{y}} $$, each pair of states differs by a multiple of $$ \pi $$.

### Mapping from $$ \mathbb{R}^n $$ to $$ \mathcal{M} $$

Given a vector $$ \textbf{y} $$, we can map it injectively to the memory space using the following procedure.

<ol>
  <li> Add or subtract $$ 2\pi $$ to each element $$ \bar{y}_j $$, $$ j  1,2,\ldots,n $$ and call the result $$ \bar{x}_j $$. Note that adding or subtracting $$ 2\pi $$ to $$ y_i $$ does not affect Eq. \eqref{eq:dynamics}. </li>
  <li> Find the maximum and minimum values $$ \bar{x}_j $$ and call them $$ \bar{x}_{\max} $$ and $$ \bar{x}_{\min} $$, respectively. </li>
  <li> Finally, perform the scale and shift $$ x_j = 2 \frac{\bar{x}_j - \bar{x}_{\min}}{\bar{x}_{\max} - \bar{x}_{\min}} - 1 $$. The resulting vector $$ \textbf{x} \in \mathcal{M} $$ and, when $$ \textbf{y} $$ is a fixed point (i.e. all pairs differ by a multiple of $$ \pi $$), the resulting vector $$ \textbf{x} $$ is a corner of $$ \mathcal{M} $$. </li>
</ol>

### Expressing the Jacobian in Memory Space

At a fixed point $$ \bar{\textbf{y}} $$, each pair $$ \bar{y}_i $$ and $$ \bar{y}_j $$ either

<ul>
  <li> differ by an odd multiple $$ \bar{y}_j - \bar{y}_i = (2k+1) \pi $$, or </li>
  <li> differ by an even multiple $$ \bar{y}_j - \bar{y}_i = 2k \pi $$. </li>
</ul>

After the mapping to the memory space, the vector $$ \textbf{x} $$ has all elements equal to positive or negative one, where if two states differ by an even multiple of $$ \pi $$, they have the same sign, and vice versa if they differ by an odd multiple of $$ \pi $$.

<br/>

If two states differ by an even multiple of $$ \pi $$, then $$ \cos (y_j - y_i) = \cos (2k\pi) = 1 $$, which is equal to $$ x_i x_j $$ regardless of the sign of $$ x_i $$ and $$ x_j $$.
If two states differ by an odd multiple of $$ \pi $$, then $$ \cos (y_j - y_i) = \cos ((2k+1)\pi) = -1 $$, which again is equal to $$ x_i X_j$$ regardless of their signs.

The Jacobian, at a fixed point, can be written in that fixed point's representation in memory space by additionally noting that $$ \cos 2(y_j-y_i) = \cos (2k\pi) = 1 $$ regardless of whether $$ \bar{y}_j $$ and $$ \bar{y}_i $$ differ by an odd or even multiple of $$ \pi $$.

\begin{equation}
  A(\bar{\textbf{y}}) = A (\textbf{x}) = \frac{2\epsilon}{n} E_n - 2 \epsilon I_n + J(\textbf{x})
\end{equation}

where the matrix $$ J(\textbf{x}) $$ has elements,

\begin{equation}
  J_{i,j}(\textbf{x}) = C_{i,j} x_i x_j - \delta_{i,j} \sum_{k=1}^n C_{i,k} x_i x_k
\end{equation}

### Eigenvalues of the Jacobian

The first step is to prove the vector $$ \textbf{1}_n $$ is an eigenvector of the Jacobian.
This can be done in a straightforward manner where it is found the corresponding eigenvalue is $$ 0 $$.
Then, every other eigenvector, $$ \textbf{v}_j $$, is orthogonal to the vector of all ones, $$ \textbf{v}_j^T \textbf{1}_n = 0 $$.

Investigating the other eigenvalues of $$ A(\textbf{x}) $$,

\begin{equation}
  A(\textbf{x}) \textbf{v}_j = \frac{2\epsilon}{n} E_n \textbf{v}_j - 2\epsilon I_n \textbf{v}_j + J(\textbf{x}) \textbf{v}_j = \lambda_j \textbf{v}_j
\end{equation}

Note that 

<ol>
  <li> $$ E_n \textbf{v}_j  = \textbf{0}_n $$ as each row of $$ E_n $$ is the vector of all ones and </li>
  <li> The eigenvector of $$ A(\textbf{x}) $$ is also an eigenvector of $$ J(\textbf{x}) $$ </li>
</ol>

