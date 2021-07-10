---
title: 'Ising Model on the Square Lattice: Introduction'
date: 2021-03-08
permalink: /posts/2021/03/ising-model-1/
tags:
  - statistical mechanics
  - ising model
---

## Summary (TL;DR)

In the following series of 5 posts, I will take you on a gentle* journey to find the critical temperature of the Ising model on the square lattice.
This is a famous problem in statistical mechanics, first solved in the 1940's to much acclaim.
It is famously difficult, but following the procedure described in "Exactly Solvable Models in Statistical Mechanics" by R.J. Baxter, I will try and make the journey as straightforward and comprehensible as possible.

*Knowledge of some linear algebra and complex analysis will be necessary to understand all of the steps.
<div style="display: block; text-align:center"> 
<figure style="display: inline-block; text-align: center">
   <img src="/images/ising.gif" style="max-width: 300px; vertical-align:top;">
   <figcaption style="text-align: center;">Square Lattice Ising Model <br> Low Temperature Regime</figcaption>
</figure>
<figure style="display: inline-block; text-align: center">
   <img src="/images/ising_highT.gif" style="max-width: 300px; vertical-align:top;">
   <figcaption style="text-align: center;">Square Lattice Ising Model <br> High Temperature Regime</figcaption>
</figure>
</div>

## An Old Problem

Why write this series of posts?

During a statistical mechanics course in graduate school I was shown a plot of the magnetization of an iron bar as a function of temperature.
Famously, there is a phase transition, called the critical temperature, where the bar seems to suddently magnetize!

{%
  include image.html
  file="/images/ising_magnetization.png"
  max-width="500px"
  caption="Average Magnetization of a Square Lattice Ising Model as a Function of Temperature"
%}

I was then shown the following equation for the critical temperature:

$$
  T_C = \frac{2}{\log (1+\sqrt{2})}
$$

This equation looked simple enough, and I thought it would be a fun exercise trying to derive it.

Boy was I wrong. Not only is it difficult, but famously so. Looking around online for help, I only found derivations that either assumed a deep knowledge of physics, or would take giant logical steps that were hard to impossible to follow.

The most approachable derivation that I found was in "Exactly Solvable Models in Statistical Mechanics" by R.J. Baxter. Nonetheless, it took me a few weeks to fully grasp each step taken as Baxter's presentation is fast-paced and expects a significant amount of intuition.

## The Ising Model

### Configurations

Consider a tiny, microscopic magnet.
Restrict it to either point up or point down.
Now add some more magnets similarly restricted to point only up or down.
Label these magnets $$ j = 1,2,3,\ldots,N $$.
This set of magnets is referred to as the __system__.

{%
  include image.html
  file="/images/ising_spins.png"
  max-width="300px"
  caption="A 4-by-4 Ising Model Grid with Random Configuration of Spins"
%}

A __configuration__ of the spins is a particular assignment of values, for instance for the 16 spins shown, this configuration is,

$$
  \textbf{s} = \left[ \begin{array}{cccc} 
    -1 & +1 & -1 & -1\\
    +1 & +1 & -1 & +1\\
    -1 & -1 & +1 & -1\\
    -1 & -1 & +1 & +1 
  \end{array} \right]
$$

Rather than using arrows or $$ \pm 1 $$, configurations will be shown as white and black squares, like in the videos above.

{%
  include image.html
  file="/images/ising_spins_boxes.png"
  max-width="300px"
  caption="The Same Configuration as Above but with Black and White Squares"
%}

The configuration space, $$ \mathcal{S}_N $$, is the set of all possible configurations
As each spin can take one of two possible values, for $$ N $$ spins, there are $$ 2^N $$ possible configurations.

If $$ N = 3 $$, then the configuration space is,

$$
  \mathcal{S}_N = \left\{ \begin{array}{cc} 
    [-1,-1,-1] & [-1,-1,+1] \\ [-1,+1,-1] & [-1,+1,+1] \\ [+1,-1,-1] & [+1,-1,+1] \\ [+1,+1,-1] & [+1,+1,+1]
  \end{array} \right\}
$$

All configurations are not equally likely though.

### Energy of a Configuration

The likelihood we would find a system in a particular configuration is a function of its energy.
The energy of a particular configuration is,

$$
  E (\textbf{s}) = -J \sum_{(j,k)} s_j s_k - H \sum_{j} s_j
$$

The first summation is over all interacting pairs of spins, scaled by the interaction strength $$ J $$, and the second sum is over all individual spins, scaled by the field strength $$ H $$.

If $$ J > 0 $$, then pairs of adjacent spins with the same sign reduce the energy while pairs of adjacent spins with the opposite sign increases the energy.

If $$ H > 0 $$, then spins pointing up reduce the energy while spins pointing down increase the energy.

The other component of the probability of finding the system in a configuration is the temperature, $$ T $$.

The energy and the temperature are combined to compute the probability of the configurations, known as its __Boltzmann weight__.

$$
  P(\textbf{s}) = \frac{1}{Z_N} \exp \left[ -\frac{1}{k_B T} E(\textbf{s}) \right]
$$

where $$ Z_N $$ is the normalization constant to make the sum of probabilities equal to one and $$ k_B $$ is the Boltzman constant.

$$
  \sum_{\textbf{s} \in \mathcal{S}_N} P(\textbf{s}) = 1
$$

In the investigation of phase transitions, the __free energy__ of a system can offer insight into their locations.

$$
  f(T) = -k_B T N^{-1} \log Z_N
$$

In particular, phase transitions can only truly occur in the __thermodynamic limit__, that is, the limit as the number of sites in the lattice goes to $$ \infty $$ (in both directions).

While we can compute the partition function explicitly for small values of $$ N $$, it's not hard to see that as $$ N $$ grows, the number of possible configurations grows exponentially, and so computing the contribution of the energy for each term in the summation that makes up the partition function becomes impossible.

Evaluating the partition function in a way to remove the summation over all configuration makes up the majority of the derivation.

## Preliminaries

In the following sections, some important topics are reviewed that are required to understand  the derivation of the partition function.

### Hyperbolic Functions

Hyperbolic functions appear in many steps of the derivations.
The two main ones that appear are the $$ \sinh (z) $$ and $$ \cosh (z) $$.

$$
  \begin{aligned}
    2 \cosh (z) &= e^{z} + e^{-z} && 2 \sinh (z) &= e^{z} - e^{-z}
  \end{aligned}
$$

From the definitions, a number of identities can be derived immediately, for example,

$$
  \cosh^2 (z) - \sinh^2 (z) = 1
$$

### Matrices

Matrices will play a major role in the derivations of the free energy of the Ising model.

In their simplest form, a matrix can be thought of as a function that takes elements from two countable sets and maps it to a value in some other set.
Here, we restrict the discussion to square matrices with entries that are a function of two configurations (meaning every matrix is $$ 2^N $$-by-$$ 2^N $$.

$$
  A : \mathcal{S}_N \times \mathcal{S}_N \mapsto \mathcal{A}
$$

where $$ \mathcal{A} $$ is some set, for instance real numbers, complex numbers, or booleans.

### Matrix Product

{%
  include definition.html
  title="Matrix Product"
  text="Let \( A^{(1)} \) and \( A^{(2)} \) be two matrices of dimension \( n \)-by-\( m \) and \( m \)-by-\( p \), respectively.
  The product of these two matrices, called \( B \), will be of dimension \( n \)-by-\( m \) and have entries,
  
  $$
    B_{j,k} = \sum_{\ell = 1}^m A_{j,\ell} B_{\ell,k}
  $$
  
  Now introduce a third matrix, \( A^{(3)} \), of dimension \( p \)-by-\( q \) and let \( B = A^{(1)} A^{(2)} A^{(3)} \) now be the product of these three matrices so its dimension is \( n \)-by-\( q \).
  Its entries are,
  
  $$
    B_{j,k} = \sum_{\ell_1=1}^m \sum_{\ell_2=1}^p A^{(1)}_{j,\ell_1} A^{(2)}_{\ell_1,\ell_2} A^{(3)}_{\ell_2,k}
  $$
  
  In general, an entry of a product of \( N \) matrices will consist of \( N-1 \) summations over the entries of adjacent matrices.
  Assuming all matrices are square and of dimension \( n \)-by\( n \), the result can be written,
  
  $$
    \left(\prod_{\ell=1}^N A^{(\ell)} \right)_{j,k} = \sum_{\ell_1=1}^n \sum_{\ell_2 = 1}^n \ldots \sum_{\ell_{N-1} = 1}^n A^{(1)}_{j,\ell_1} \left( \prod_{q = 1}^{N-2} A^{(q)}_{\ell_{q-1},\ell_q} \right) A_{\ell_{N-1},k}
  $$
  "
%}

The definition of the product of $$ N $$ square matrices written out as nested summations is intimidating at first if you're not familiar with it.
Try writing out an element of a matrix product of four matrices to reassure yourself that the expression for general $$ N $$ matrices makes sense.

### Eigenvalues

{%
  include definition.html
  title="Eigenvalues of a Matrix"
  text="Let \( A \) be a square matrix of dimension \( n \).
  A complex scalar \( \lambda \in \mathbb{C} \) and vector \( \textbf{x} \in \mathbb{C}^n \) is an eigenvalue and eigenvector pair of the matrix \( A \) if they satisfy the equation,
  
  $$
    A \textbf{x} = \lambda \textbf{x}
  $$
  "
%}

If $$ \lambda $$ is an eigenvalue of a matrix $$ A $$, then $$ \lambda^N $$ is an eigenvalue of the matrix $$ A^N $$.

The eigenvalues can be found as the roots of the characteristic polynomial,

$$
  \det (\lambda I - A ) = 0
$$ 

For a 2-by-2 matrix,

$$
  A = \left[ \begin{array}{cc}
    a_{11} & a_{12} \\ a_{21} & a_{22}
  \end{array} \right]
$$

its eigenvalues are the roots of the polynomial,

$$
  \left| \begin{array}{cc}
    \lambda - a_{11} & -a_{12} \\ -a_{21} & \lambda - a_{22} 
  \end{array} \right| = \left(\lambda - a_{11} \right) \left( \lambda - a_{22} \right) - a_{12} a_{21} = \lambda^2 - (a_{11} + a_{22} ) \lambda + a_{11} a_{22} - a_{12} a_{21} = 0
$$

which can be found using the quadratic formula.

If an eigenvalue is __simple__, that is, it appears as root of the characteristic polynomial only once, then it has a unique corresponding eigenvector $$ \textbf{x} $$ up to a scalar multiple.

### Trace of a Matrix

{%
  include definition.html
  title="Trace of a Matrix"
  text="Let \( A \) be a square matrix of dimension \( n \).
  The trace of a matrix is the sum of its diagonal elements.
  
  $$
    \text{Tr} (A) = \sum_{j=1}^n A_{j,j}
  $$
  
  Equivalently, the trace of a matrix is the sum of the eigenvalues.
  
  $$
    \text{Tr} (A) = \sum_{j=1}^n \lambda_j
  $$
  "
%}

### Commuting Matrices

{%
  include definition.html
  title="Commuting Matrices"
  text="Let \( A \) and \( B \) be two square matrices of equal dimension.
  These matrices commute if
  
  $$
    AB = BA
  $$
  "
%}

Commuting matrices share eigenvectors.

{%
  include theorem.html
  title="Eigenvectors of Commuting Matrices"
  text="Let \( A \) and \( B \) be commuting matrices and let \( \lambda,\textbf{x} \) be a simple eigenvalue with its eigenvector of \( A \).
  Then \( \textbf{x} \) is also an eigenvector of \( B \)."
  proof="Start by using the commuting property of \( A \) and \( B \) and post-multiply by the eigenvector \( \textbf{x} \).
  
  $$
    AB \textbf{x} = BA \textbf{x} = \lambda B \textbf{x}
  $$
  
  This relation implies that \( B\textbf{x} \) is also an eigenvector of \( A \) with eigenvalue \( \lambda \) but by assumption \( \lambda \) is simple.
  Thus \( B \textbf{x} = c \textbf{x} \) for some scalar \( c \) which then implies that \( \textbf{x} \) is also an eigenvector \( B \) with eigenvalue \( c \)."
%}

If $$ \lambda $$ is __not__ a simple eigenvalue of $$ A $$ (implying its corresponding eigenvectors form a linear subspace of $$ \mathbb{C}^n $$), then there is an eigenvector of $$ B $$ that lies in this subspace.
This will not be necessary in the derivation herein though.

## Transfer Matrix Identity

The transfer matrix identity is the key to expressing the summation in the partition function as a matrix operation.

{%
  include theorem.html
  title="Transfer Matrix Identity"
  text="Let there be \( N \) square matrices, denoted \( A^{(j)} \), \( j = 1,2,\ldots,N \), each of dimension \( n \)-by-\( n \).
  Define all arithmetic to be cycle, that is, \( n+1 = 1 \).
  
  $$
    \sum_{\ell_1 = 1}^n \sum_{\ell_2 = 1}^n \ldots \sum_{\ell_N = 1}^n \prod_{j=1}^N A^{(j)}_{\ell_j,\ell_{j+1}} = \text{Tr} \left( \prod_{j=1}^N A^{(j)} \right)
  $$
  "
  proof="The proof uses the definition of the matrix product and the matrix trace.
  A diagonal element of the product of \( N \) matrices is,
  
  $$
    \left( \prod_{j=1}^N A^{(j)} \right)_{k,k} = \sum_{\ell_1 = 1}^n \sum_{\ell_2 = 1}^n \ldots \sum_{\ell_{N-1} = 1}^n A^{(1)}_{k,\ell_1} \left( \prod_{j=1}^{N-1} A^{(j)}_{\ell_{j-1},\ell_j} \right) A^{(N)}_{\ell_{N-1},k}
  $$
  
  Then, the trace of this matrix product is,
  
  $$
    \begin{aligned}
    \text{Tr} \left( \prod_{j=1}^N A^{(j)} \right) &= \sum_{k=1}^n \left( \prod_{j=1}^N A^{(j)} \right)_{k,k}\\ 
    &= \sum_{k=1}^n \left( \sum_{\ell_1 = 1}^n \ldots \sum_{\ell_{N-1} = 1}^n A^{(1)}_{k,\ell_1} \left( \prod_{j=1}^{N-1} A^{(j)}_{\ell_{j-1},\ell_j} \right) A^{(N)}_{\ell_{N-1},k} \right)\\
    &= \sum_{\ell_1 = 1}^n \ldots \sum_{\ell_N=1}^n \prod_{j=1}^N A^{(j)}_{\ell_j,\ell_{j+1}} 
    \end{aligned}
  $$
  
  which completes the proof.
  "
%}

Consider the case $$ N = 4 $$ and $$ n = 2 $$.

## 1D Ising Model

Before addressing the Ising model on the square lattice, a discussion about the one dimensional Ising model helps layout the steps that must be taken.

Let there be $$ N $$ spins, indexed $$ s_1,s_2,\ldots,s_N $$, such that each spin is connected to its two neighbors with cyclic boundary conditions so that $$ S_N $$ and $$ s_1 $$ are neighbors, that is, $$ s_{N+1} = s_1 $$.

The energy of a configuration can then be written,

$$
  E(\textbf{s}) = -J \sum_{j=1}^N s_j s_{j+1} - H \sum_{j=1}^N s_j
$$

The summations can be combined in the following way by splitting each term in the second summation into halves.

$$
  E(\textbf{s}) = - \sum_{j=1}^N \left( J s_j s_{j+1} + H \frac{s_j + s_{j+1}}{2} \right)
$$

To simplify the following computation, make the following substitutions,

$$
  K = \frac{J}{k_B T} \quad \text{and} \quad R = \frac{H}{k_B T}
$$

Using the variables $$ K $$ and $$ R $$ and the single summation form of the energy above, the partition function becomes,

$$
  \begin{aligned}
    Z_N &= \sum_{\textbf{s} \in \mathcal{S}} \exp \left[ \sum_{j=1}^N K s_j s_{j+1} + R \frac{s_j + s_{j+1}}{2} \right]\\
    &= \sum_{\textbf{s} \in \mathcal{S}} \prod_{j=1}^N \exp \left[ K s_j s_{j+1} + R \frac{s_j + s_{j+1}}{2} \right]
  \end{aligned}
$$

The trick is to recognize the expression inside of the product is a pair-wise function of spins, so it can be treated as entries of a matrix.
The matrix will have elements $$ V_{-1,-1} $$, $$ V_{-1,+1} $$, $$ V_{+1,-1} $$, and $$ V_{+1,+1} $$ for each possible pair of spins.
Define the $$ 2 $$-by-$$ 2 $$ matrix $$ V $$ as having elements,

$$
  V = \left[ \begin{array}{cc} e^{K-R} &  e^{-K} \\  e^{-K} & e^{K+R} \end{array}  \right]
$$

The partition function can now be written in a form reminiscent of the transfer matrix identity.

$$
  Z_N = \sum_{s_1 = \pm 1} \sum_{s_2 = \pm 1} \ldots \sum_{s_N = \pm 1} \prod_{j=1}^N V_{s_j,s_{j+1}} = \text{Tr} \left( \prod_{j=1}^N V \right) = \text{Tr} V^N
$$

Using the eigenvalue form of the trace the partition function can now be written as an algebraic expression of the eigenvalues.

$$
  \text{Tr} V^N = \lambda_1^N + \lambda_2^N = \lambda_1^N \left( 1 + \frac{\lambda_2^N}{\lambda_1^N} \right)
$$

The eigenvalues are found from $$ V $$'s characteristic polynomial.

$$
  \begin{aligned}
  &\lambda^2 - \left( e^{K-R} + e^{K+R} \right) \lambda + e^{K-R}e^{K+R} - e^{-2K}\\
  &= \lambda^2 - 2e^K\cosh(R) \lambda + e^{2K} - e^{-2K} = 0
  \end{aligned}
$$

Using the quadratic formula, the eigenvalues are,

$$
  \begin{aligned}
  \lambda_{1,2} &= \frac{1}{2} \left( 2e^{K} \cosh R \pm \sqrt{4 e^{2K} \cosh^2 R - 4 e^{2K} + 4 e^{-2K}} \right)\\
  &= e^K \cosh R \pm \sqrt{e^{2K} \sinh^2 R + e^{-2K}}
  \end{aligned}
$$

The free energy is 

$$
  \begin{aligned}
  f_N &= -k_B T \frac{1}{N} \log Z_N\\
  &= -k_B T \frac{1}{N} \left[ N \log \lambda_1 + \log \left(1 + \left( \frac{\lambda_2}{\lambda_1} \right)^N\right)\right]
  \end{aligned}
$$

To determine the behavior of the free energy in the thermodynamic limit, bounds can be proven for the ratio $$-1 \leq \frac{\lambda_2}{\lambda_1} \leq 1 $$ which is a straightforward algebraic process.
This means that in the thermodynamic limit, $$\lim\limits_{N \rightarrow \infty} \left[ 1 + \left( \frac{\lambda_2}{\lambda_1} \right)^N \right] = 1 $$ so the free energy approaches a constant.

$$
  f = \lim\limits_{N \rightarrow\infty} f_N = -k_B T \log \lambda_1
$$

The free energy is an analytic function for $$ T > 0 $$.
As $$ T \rightarrow 0^+ $$ though, $$ f \rightarrow \infty $$ suggesting a `phase transition', but this is not the type of behavior of interest.

## Conclusion of Part 1

This post addressed the Ising model on the Square Lattice, laid out some useful identities involving matrices, and solved the free energy expression for the Ising model on the Ring.
The steps taken to find the free energy of the Ising model on the ring are,

1. Manipulate the energy expression for a configuration to make it appear matrix-like.
2. Use the modified energy expression to write the partition function as the trace of a matrix power using the transfer matrix identity.
3. Determine the eigenvalues of the matrix that appears in the new partition function form.
4. Compute the free energy and search for discontinuities.

For the Ising Model on the ring each of these steps was quite straightforward.
For the Ising Model on the square lattice, these steps require creativity, mathematical dexterity, and some special functions.
