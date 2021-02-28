---
layout: single
title:  "Case Study: Cauchy Matrix"
date: 2021-02-01
permalink: /posts/2021/02/cauchy-matrix/
header:
  teaser: "unsplash-gallery-image-2-th.jpg"
categories: 
  - software
tags:
  - gramian
  - floating point
  - numerical methods
---

# A Plateau-ing Curve

In graduate school, I was shown a figure generated like the one below.

{% include image.html max-width="500px" file="/images/cauchy-doubleprec.png" alt="/images/500x300.png" caption="Control energy growth decreases with uncertainty." %}

The y-axis shows the average and standard deviation of the control energy arising from a particular optimal control problem,

$$
  E(n) = \textbf{1}_n^T W^{-1} \textbf{1}_n
$$

where $$ n $$ is the number of systems, $$ \textbf{1}_n $$ is a vector of all ones of length $$ n $$ and $$ W $$ is a square $$ n $$-by-$$ n $$ matrix drawn from a probability distribution.

<details>
  <summary><b>Why is this equation called Control Energy?</b></summary>
  This matrix arises from an optimal control problem.
  
  $$
    \begin{aligned}
      \min && &\frac{1}{2} \int_0^{t_f} u^2(t) dt\\
      \text{s.t.} && &\dot{x}_k(t) = -a_k x_k(t) + u(t), \quad k = 1,2,\ldots,n\\
      && &x_k(0) = 0, \quad x_k(t_f) = 1
    \end{aligned}
  $$
  
  The values $$ a_k $$ is drawn from some probability distribution that is strictly positive.
  This problem represents the optimal control of a collection of stable single integrator systems.
  The control energy is defined as,
  
  $$
    E = \int_0^{t_f} u^2(t) dt 
  $$
  
  which is useful to represent the amount of <i>effort</i> required by a particular control signal to perform a task.
  
  The control energy to satisfy the contraints in this optimal control problem is lower bounded by its solution,
  
  $$
    E \geq E^* = \textbf{1}^T_n W^{-1} \textbf{1}_n
  $$
  
  where the matrix $$ W $$ has elements 
  
  $$
    W_{j,k} = \frac{1}{a_j+a_k}
  $$
  
  This can be proven using Pontryagin's Minimum Principle which is beyond the scope of this post but is presented in detail in other posts.
  <hr size="5" noshade>
</details>

The figure seems to show that, for at least the probability distribution used to generate the data, as the number of systems grows, the rate of energy growth seems to slow down.

This was a surprising result! But it was also slightly suspicious.

## Analytic Investigation

The matrix $$ W $$ that appears in this control energy expression is a __Cauchy matrix__.

{% 
  include definition.html 
  title='Cauchy Matrix' 
  text="Let \( \textbf{x} \) and \( \textbf{y} \) be two vectors of length \( n \) each with unique elements ( \( x_j \neq x_k \) and \( y_j \neq y_k \) for \( j \neq k \) ) and \( x_j - y_k \neq 0 \) for any pair of values.
  The \( n \)-by-\( n \) Cauchy matrix created from these two vectors is defined as \( C \) with elements $$ C_{j,k} = \frac{1}{x_j - y_k} $$."
%}

A number of properties about Cauchy matrices are known such as its determinant and its inverse.

A lesser known property is the sum of the elements of its inverse.

$$
  \textbf{1}^T C^{-1} \textbf{1} = \sum_{j=1}^n (x_j - y_j)
$$

The proof of this is not straightforward and requires a fair amount of preliminary results on polynomials.

<details>
  <summary><b>The Proof of the Sum of Elements of the Inverted Cauchy Matrix</b></summary>
  Before presenting the proof, some definitions and other theorems are required.
  
  {%
    include definition.html
    title="Polynomial in Factored Form"
    text="A monic polynomial of order \( n \) can be expressed as $$ p(z) = \prod_{j=1}^n (z - x_j) $$ This polynomial can equivalently be expressed in summation form as $$ p(z) = \sum_{j=0}^n a_j x^j $$
    The coefficient in front of the highest order power is clearly \( a_n = 1 \) and the coefficient in front of the second highest order power is $$ a_{n-1} = -\sum_{k=1}^n x_k $$ which is straightforward to show."
  %}
  
  The first theorem is a result of Euler from 1762 and appears in Donald Knuth's first volume.
  
  {%
    include theorem.html
    title="Summation of Powers over Product of Differences"
    text="Let \( \textbf{x} \) be a vector of length \( n \). Then the following identity holds 
    $$
      S_n(m) = \sum_{j=1} \frac{x_j^m}{\prod_{\substack{k=1 \\ k\neq j}}^n (x_j -x_k)} = \left\{ \begin{array}{ll} 0, & m = 0,1,\ldots,n-2\\ 1, & m = n-1\\ \sum_{j=1}^n x_j, & m = n \end{array} \right.
    $$"
    proof="The proof uses mathematical induction on the dimension of the vector \( n \).
    The base case is proved for \( n = 2 \).
    
    $$
      S_2(m) = \frac{x_1^m}{x_1-x_2} + \frac{x_2^m}{x_2-x_1} = \frac{x_1^m - x_2^m}{x_1 - x_2}
    $$
    
    For \( m = 0 \), clearly \( S_2(0) = 0 \). For \( m = 1 \), 
    
    $$
      S_2(1) = \frac{x_1 - x_2}{x_1 - x_2} = 1
    $$
    
    For \( m = 2 \), 
    
    $$
      S_2(2) = \frac{x_1^2 - x_2^2}{x_1 - x_2} = \frac{(x_1 - x_2)(x_1+x_2)}{x_1-x_2} = x_1 + x_2
    $$
    
    With the base case proven, the inductive hypothesis is stated.
    
    $$
      S_n(m) = \sum_{j=1} \frac{x_j^m}{\prod_{\substack{k=1 \\ k\neq j}}^n (x_j -x_k)} = \left\{ \begin{array}{ll} 0, & m = 0,1,\ldots,n-2\\ 1, & m = n-1\\ \sum_{j=1}^n x_j, & m = n \end{array} \right.
    $$
    
    Before moving to the inductive step, the summation is re-expressed as a difference,
    
    $$
      \begin{aligned}
        S_n(m) &= \frac{1}{x_n - x_{n-1}} \sum_{j=1}^n \frac{x_j^m (x_n - x_{n-1})}{\prod_{\substack{k=1 \\ k \neq j}}^n (x_j-x_k)}\\
        &= \frac{1}{x_n - x_{n-1}} \left[ \sum_{j=1}^n \frac{x_j^m (x_j - x_{n-1})}{\prod_{\substack{k=1 \\ k \neq j}}^n (x_j - x_k)} - \sum_{j=1}^n \frac{x_j^m (x_j - x_n)}{\prod_{\substack{k=1 \\ k \neq j}}^n (x_j - x_k)} \right]
      \end{aligned}
    $$
    
    Note that the \( j = n-1 \) term in the first summation and the \( j = n \) term in the second summation are equal to zero.
    
    $$
      \begin{aligned}
        S_n(m) = \frac{1}{x_n - x_{n-1}} \left[ \sum_{\substack{j=1 \\ j \neq n-1}}^n \frac{x_j^m (x_j - x_{n-1})}{\prod_{\substack{k=1 \\ k \neq j}}^n (x_j - x_k)} - \sum_{j=1}^{n-1} \frac{x_j^m (x_j - x_n)}{\prod_{\substack{k=1 \\ k \neq j}}^n (x_j - x_k)} \right]
      \end{aligned}
    $$
    
    In the product in the first summation, the \( k = n-1 \) term cancels the difference in the numerator while the same happens for the \( k = n\) term in the product in the second summation.
    
    $$
      S_n(m) = \frac{1}{x_n-x_{n-1}} \left[ \sum_{\substack{j=1 \\ j \neq n-1}}^n \frac{x_j^m }{\prod_{\substack{k=1 \\ k \neq j \\ k \neq n-1}}^n (x_j - x_k)} - \sum_{j=1}^{n-1} \frac{x_j^m }{\prod_{\substack{k=1 \\ k \neq j}}^{n-1} (x_j - x_k)} \right]
    $$
    
    With this form, the inductive step is taken.
    
    $$
      \begin{aligned}
      S_{n+1}(m) &= \frac{1}{x_{n+1}-x_n} \left[ \sum_{\substack{j=1 \\ j \neq n}}^{n+1} \frac{x_j^m }{\prod_{\substack{k=1 \\ k \neq j \\ k \neq n}}^{n+1} (x_j - x_k)} - \sum_{j=1}^{n} \frac{x_j^m }{\prod_{\substack{k=1 \\ k \neq j}}^{n} (x_j - x_k)} \right]\\
      &= \frac{1}{x_{n+1}-x_n} \left[ \bar{S}_n(m) - S_n(m) \right]
      \end{aligned}
    $$
    
    where \( \bar{S}_n(m) \) is of the same form as \( S_n(m) \) except the \( x_n \) is replaced by \( x_{n+1} \). Note that this replacement does not affect the results of the inductive hypothesis which is independent of the particular values of \( x_j \), just how many there are.
    
    For \( m = 0,1,\ldots,n-2 \), using the inductive hypothesis, we have 
    
    $$
      S_{n+1}(m) = \frac{1}{x_{n+1}-x_n} \left[ \bar{S}_n(m) - \bar{S}_n(m) \right] = \frac{1}{x_{n+1} - x_n} \left[ 0 - 0 \right] = 0
    $$
    
    For \( m = n-1 \), using the inductive hypothesis, we have
    
    $$
      S_{n+1}(n-1) = \frac{1}{x_{n+1}-x_n} \left[ \bar{S}_n(n-1) - S_n(n-1) \right] = \frac{1}{x_{n+1} - x_n} \left[1 - 1 \right] = 0
    $$
    
    For \( m = n \), using the inductive hypothesis, we have
    
    $$
      \begin{aligned}
      S_{n+1}(n) &= \frac{1}{x_{n+1} - x_n} \left[ \bar{S}_n(n) - S_n(n) \right]\\
      &= \frac{1}{x_{n+1} - x_n} \left[ \sum_{\substack{j=1 \\ j \neq n}}^{n+1} x_j - \sum_{j=1}^n x_j \right]\\
      &= \frac{x_{n+1} - x_n}{x_{n+1}-x_n}\\
      &= 1
      \end{aligned}
    $$
    
    For the last case, \( m = n+1 \), we cannot use the inductive hypothesis directly as it only holds up to \( m = n \). Instead we turn to the definition of monic polynomials presented above.
    Note that for any \( j = 1,2,\ldots,n \) we have, 
    
    $$
      p(x_j) = \prod_{k=1}^n (x_j - x_k) = 0
    $$
    
    and from the definition of monic polynomials, we can write,
    
    $$
      p(x_j) = \prod_{k=1}^n (x_j - x_k) = x_j^n - \left[ \sum_{\ell = 1}^n x_{\ell} \right] x_j^{n-1} + \sum_{\ell = 0}^{n-2} a_{\ell} x_j^{\ell}
    $$
    
    Dividing this expression by \( \prod_{\substack{k=1 \\ k \neq j}}^n (x_j - x_k) \) and summing over \( j \) yields,
    
    $$
      \begin{aligned}
      \sum_{j=1}^n \frac{p(x_j)}{\prod_{\substack{k=1 \\ k \neq j}}^n (x_j - x_k)} &= \sum_{j=1}^n \frac{x_j^n}{\prod_{\substack{k=1 \\ k \neq j}}^n (x_j - x_k)}\\
      &- \left[ \sum_{\ell = 1}^n x_{\ell} \right] \sum_{j=1}^n \frac{x_j^{n-1}}{\prod_{\substack{k=1 \\ k \neq j}}^n (x_j - x_k)}\\
      &+ \sum_{\ell = 1}^{n-2} a_{\ell} \sum_{j=1}^n \frac{x_j^{\ell}}{\prod_{\substack{k=1 \\ k \neq j}}^n (x_j - x_k)}\\
      &= 0
      \end{aligned}
    $$
    
    From the inductive hypothesis, for any term \( \ell = 0,1,\ldots,n-2 \) the summation over \( j \) is equal to zero.
    Also from the inductive hypothesis, for \( x_j^{n-1} \), the summation is equal to one.
    As the whole expression is equal to zero, we have the result,
    
    $$
      \sum_{j=1}^n \frac{x_j^n}{\prod_{\substack{k=1 \\ k \neq j}}^n (x_j - x_k)} = \sum_{j = 1}^n x_j
    $$
    
    which also holds for 
    
    $$
      \sum_{j=1}^{n+1} \frac{x_j^n}{\prod_{\substack{k=1 \\ k \neq j}}^{n+1} (x_j - x_k)} = \sum_{j = 1}^{n+1} x_j
    $$
    
    which completes the proof."
  %}
  
  The next theorem requires the definition of Vandermonde matrices.
  
  {%
    include definition.html
    title="Vandermonde Matrix"
    text="Let \( \textbf{x} \) be a vector of length \( n \). The associated Vandermonde matrix, \( V \), is a square \( n \)-by-\( n \) matrix with elements
    
    $$
      V_{j,k} = x_k^{j-1}
    $$
    
    As an example, for \( n = 3 \), the Vandermonde matrix is,
    
    $$
      V = \left[ \begin{array}{ccc}
        1 & 1 & 1 \\
        x_1 & x_2 & x_3 \\
        x_1^2 & x_2^2 & x_3^2
      \end{array} \right]
    $$"
  %}
  
  A Cauchy matrix can be decomposed in terms of Vandermonde matrices.
  
  {%
    include theorem.html
    title="Vandermonde Decomposition of Cauchy Matrices"
    text="Let \( \textbf{x} \) and \( \textbf{y} \) be \( n \) vectors and \( C \) the associated Cauchy matrix. Let \( p(z) \) be a polynomial with roots \( x_k \).
    
    $$
      p(z) = \prod_{k=1}^n (z-x_k)
    $$
    
    Also, define the related polynomials \( p_j(z) \) as 
    
    $$
      p_j(z) = \prod_{\substack{k=1 \\ k \neq j}}^n (z - x_k)
    $$
    
    Define the diagonal matrices \( P \) and \( Q \) as having elements,
    
    $$
      \begin{aligned}
        P_{j,k} = \left\{ \begin{array}{ll}
          p_j(x_j), & j = k\\
          0, & j \neq k
        \end{array} \right. && \text{and} && Q_{j,k} = \left\{ \begin{array}{ll}
          p(y_j), & j = k\\
          0, & j \neq k
        \end{array} \right.
      \end{aligned}
    $$
    
    Let \( X \) and \( Y \) be the Vandermonde matrices created from the vectors \( \textbf{x} \) and \( \textbf{y} \), respectively.
    Then the Cauchy matrix \( C \) can be decomposed into,
    
    $$
      C = -P X^{-1} Y Q^{-1}
    $$"
    proof="The proof begins by rewriting the statement to prove as,
    
    $$
      -CQ = P X^{-1} Y
    $$
    
    and then shows that the two matrices on either side of the equals sign have equal elements.
    Starting with the right-hand side, we begin solving,
    
    $$
      AX = P
    $$
    
    for the matrix \( A \).
    From matrix multiplication, the (i,j) elements of the matrix product \( AX \) is,
    
    $$
      (AX)_{i,j} = \sum_{k=1}^n A_{i,k} X_{k,j} = \sum_{k=1}^n A_{i,k} x_j^{k-1}
    $$
    
    The matrix \( P \) can also be written as having elements,
    
    $$
      P_{j,k} = p_j(x_k)
    $$
    
    as it is clear \( p_j(x_k) = 0 \) for \( j \neq k \).
    
    From the definition of monic polynomials, note that,
    
    $$
      p_j(z) = \sum_{k=0}^{n-1} a_{j,k} z^k = \sum_{k=1}^n a_{j,k-1} z^{k-1}
    $$
    
    Comparing the elements of \( AX \) and \( P \) we see they are both polynomials and the elements of \( A \) are,
    
    $$
      A_{j,k} = a_{j,k-1}
    $$
    
    the coefficients of the polynomials \( p_j(z) \), \(j = 1,2,\ldots,n\).
    
    Now, post-multiply the expression by \( Y \), that is, find the elements of the matrix product,
    
    $$
      AY = PX^{-1} Y
    $$
    
    By the form of \( A \) determined previously and the fact \( Y \) is a Vandermonde matrix created from \( \textbf{y} \), we have,
    
    $$
      (AY)_{i,j} = \sum_{k=1}^n A_{i,k} Y_{k,j} = \sum_{k=1}^n a_{i,k-1} y_j^{k-1} = p_i (y_j) = \frac{p(y_j)}{y_j - x_i}
    $$
    
    where the two equivalent definitions of \( p_j(z) \) are used.
    
    Now, turning to the left-hand side of the original matrix equation, the elements are determined using the definition of a matrix product,
    
    $$
      -(CQ)_{i,j} = - \sum_{k=1}^n C_{i,k} Q_{k,j} = -C_{i,j} Q_{j,j} = -\frac{p(y_j)}{x_i - y_j} = \frac{p(y_i)}{y_j - x_i}
    $$
    
    which is equal to the elements of \( P X^{-1} Y \) thus completing the proof.
    "
  %}
  
  Finally, the sum of elements of an inverse Cauchy matrix can be proven.
  
  {%
    include theorem.html
    title="Sum of Elements of the Inverse Cauchy Matrix"
    text="Let \( C \) be a Cauchy matrix with associated vectors \( \textbf{x} \) and \( \textbf{y} \). Then the sum of elements of its inverse is the sum of the differences of each pair of vector elements,
    
    $$
      \textbf{1}_n^T C^{-1} \textbf{1}_n = \sum_{j=1}^n (x_j - y_j)
    $$"
    proof="
    The proof first applies the Vandermonde decomposition of a Cauchy matrix, evaluates the two resulting vectors, and finally computes the resulting vector product.
    
    Let the matrices \( P,Q,X,Y \) be defined as in the previous theorem.
    Using the inverse of a matrix product identity, the inverse of a Cauchy matrix is,
    
    $$
      C^{-1} = - Q Y^{-1} X P^{-1}
    $$
    
    Applying this decomposition to the statement to prove, we can write the expression as a vector product,
    
    $$
      \textbf{1}^T C^{-1} \textbf{1} = - \textbf{1}_n^T Q Y^{-1} X P^{-1} \textbf{1} = - \textbf{v}^T \textbf{w}
    $$
    
    where the vectors,
    
    $$
      \begin{aligned}
        \textbf{v} = Y^{-T} Q \textbf{1}\\
        \textbf{w} = X P^{-1} \textbf{1}
      \end{aligned}
    $$
    
    and the superscript \( -T \) denotes the transpose of the inverse of a matrix.
    Each of these vectors is solved in turn.
    First, the vector \( \textbf{v} \) solves the linear equation,
    
    $$
      Y^T \textbf{v} = Q \textbf{1}
    $$
    
    where the \( i \)'th element on either side is,
    
    $$
      (Y^T \textbf{v})_i = \sum_{j=1}^n y_i^{j-1} v_j = (Q \textbf{1})_i = p(y_i)
    $$
    
    To determine the values of \( v_j \), first note the monic polynomial \( p(z) \) can be written,
    
    $$
      p(z) = \prod_{j=1}^n (z-x_j) = z^n + \sum_{j=0}^{n-1} a_j z^{j}
    $$
    
    and introduce the monic polynomial,
    
    $$
      q(z) = \prod_{j=1}^n (z-y_j) = z^n + \sum_{j=0}^{n-1} b_j z^{j}
    $$
    
    Note that clearly \( q(y_i) = 0 \) (evaluating the polynomial at a root) and solving for the highest order shows,
    
    $$
      y_i^n = -\sum_{j=0}^{n-1} b_j y_i^{j}
    $$
    
    Now, the original polynomial \( p(z) \) evaluated at a root of \( q(z) \) (like \( y_i \) ) can be expressed as a polynomial in summation form,
    
    $$
      p(y_i) = \sum_{j=0}^{n-1} (a_j - b_j) y_i^j
    $$
    
    Comparing forms for \( p(y_i) \), it is clear that 
    
    $$
      v_j = a_{j-1} - b_{j-1}
    $$
    
    Moving to the other vector, \( \textbf{w} \), evaluating the \( i \)'th element of either side is,
    
    $$
      w_i = (XP^{-1} \textbf{1} )_i = \sum_{j=1}^n X_{i,j} \frac{1}{p_j(x_j)} = \sum_{j=1}^n \frac{x_j^{i-1}}{\prod_{\substack{k=1 \\ k \neq j}}^n (x_j - x_k)}
    $$
    
    where the definition \( p_j(x_j) = \prod_{\substack{k=1 \\ k \neq j}}^n (x_j - x_k) \) is used.
    Applying the Summation of Powers over Product of Differences theorem, the values of \( w_i \) are,
    
    $$
      w_i = \left\{ \begin{array}{ll}
        0, & i = 1,2,\ldots,n-1\\
        1, & i = n
      \end{array} \right.
    $$
    
    Now, the vector product is,
    
    $$
      -\textbf{v}^T \textbf{w} = -\sum_{i=1}^n v_i w_i = -v_n = b_{n-1} - a_{n-1}
    $$
    
    Returning to definition of monic polynomials at the beginning of this section, it was shown the coefficient of the second highest power of a monic polynomial is the negative sum of the roots, so
    
    $$
      a_n = -\sum_{j=1}^n x_j \quad \text{and} \quad b_n = -\sum_{j=1}^n y_j
    $$
    
    and we can complete the proof,
    
    $$
      \textbf{1}^T C^{-1} \textbf{1} = - \textbf{v}^T \textbf{w} = \sum_{j=1}^n (x_j - y_j)
    $$
    "
  %}
  
  <hr size="5" noshade>
</details>

The matrix $$ W $$ that appears in the control energy expression has elements,

$$
  W_{j,k} = \frac{1}{a_j + a_k}
$$

which is a Cauchy matrix by defining $$ x_j = a_j $$ and $$ y_j = -a_j $$ so that the control energy is,

$$
  E(n) = 2 \sum_{j=1}^n a_j
$$

This analytic expression disagrees with the original figure! 

For instance, if the values $$ a_j $$ are drawn from a normal distribution with variance $$ \sigma^2 = 1 $$ and mean $$ \mu = 3 $$ (the choice used to generate the original figure) the expected control energy is

$$
  \mathbb{E}[E(n)] = 2 \mathbb{E} \left[ \sum_{j=1}^n a_j \right] = 2 \sum_{j=1}^n \mathbb{E} [a_j] = 2n\mu
$$

and the variance is $$ \sigma^2 = 4n $$.

{% include image.html max-width="500px" file="/images/cauchy-analytic.png" alt="/images/500x300.png" caption="Numerical vs. Analytic Energy Growth. The purple curve is the same as in the first figure while the blue line plots the mean and standard deviation " %}

So what happened? Why did the code fail to produce the correct result?

## Floating Point Precision

A detailed discussion of floating point arithmetic is beyond the scope of this post, only the effects related to numerical methods.
Some of this section is a little hand wave-y but to avoid getting lost in the specific IEEE 754 floating point standards, I think this is OK.

Let $$ x > 0 $$ and $$ y > x $$ be two real numbers stored as double precision numbers.
It is possible, in floating point arithmetc, that $$ x + y = x $$.

A rough outline in base-10 (as opposed to base-2 which is how the number is actually stored) is as follows.
A real number $$ x $$ can be represented as,

$$
  x = \pm d_1.d_2d_3d_4\ldots \times 10^n
$$

where $$ d_k $$ is a digit between 0 and 9, $$ k = 1,2,\ldots $$ and $$ n $$ is an integer.
The string of digits $$ d_1d_2d_3 \ldots $$ is called the __mantissa__ and $$ n $$ is called the __exponent__.
In floating point arithmetic, the digits $$ d_k $$ are truncated after some digit $$ k = p $$ and the exponent $$ n $$ is bounded.

If we add two numbers together, we can think of it as adding each corresponding digit after properly shifting the digits.

As an example, consider adding $$ x = 265.21 $$ and $$ y = 0.54 $$ with $$ p = 6 $$.

$$
  \begin{aligned}
    x &= 2.65210 \times 10^2\\
    y &= 5.40000 \times 10^{-1}
  \end{aligned}
$$

Adding these two numbers is done by matching the digits of appropriate power.

$$
  \begin{array}{r}
    265210\\
    000540\\
    +\\
    265750
  \end{array}
$$

Now, if instead of $$ y = 0.54 $$, what if $$ y = 0.0054 $$ so

$$
  y = 5.40000 \times 10^{-3}
$$

The sum is now,

$$
  \begin{array}{r}
    2652100\\
    0000054\\
    +\\
    2652154
  \end{array}
$$

but due to the fact $$ p = 6 $$, the last digit is truncated so we get the result $$ x + y = 2.65215 \times 10^2 $$ which is incorrect in infinite precision ($$ p = \infty $$) but is correct in our finite $$ p = 6 $$ precision.

If two finite precision numbers have different exponents $$ n $$ and $$ m $$, and the smaller number has nonzero final digits, then some rounding will occur.

In the most extreme case, if $$ |n-m| > p $$, then we get the situation $$ x + y = x $$, that is, the smaller of the two numbers is completely lost.

```c
  #include <stdlib.h>
  #include <stdio.h>
  
  int main(void) {
    double x = 1E0;
    double y = 1E17;
  
    double z = x + y;
  
    if (z == y) {
      printf("x + y = x\n");
    }
    return 0;
  }
```

In double precision, if the exponents of two numbers differ by more than $$ 15 $$, then the smaller of the two numbers will be lost.

In matrix calculations, to determine if the precision is adequate, take a norm of either side of the equation,

$$
  || A \textbf{x} || = || \textbf{b} ||
$$

and its formal solution,

$$
  || \textbf{x} || = || A^{-1} \textbf{b} ||
$$

If the ratio of the norms of $$ \textbf{x} $$ over $$ \textbf{b} $$ is either more than $$ 10^{15} $$ or less than $$ 10^{-15} $$ then the result should not be trusted as double precision is unlikely to be adequate.

This ratio can be approximated by the __condition number__ of the matrix $$ A $$,

$$
  \kappa (A) = || A || || A^{-1} ||
$$

If the condition number of $$ A $$ is outside the range $$ 10^{15} $$ to $$ 10^{-15} $$ then double precision is inadequate for solving linear systems involving $$ A $$.

Returning to the original problem, using double precision, the condition number is computed.

{%
  include image.html
  max-width="1000px" 
  file="/images/cauchy-cond.png" 
  alt="/images/500x300.png" 
  caption="Double Precision and Analytic Results compared with the Estimate of the Condition Numbers (computed using double precision).
  The shaded regions represent when the condition number is larger than \( 10^{15} \)."
%}

The condition number of $$ W $$ is estimated using double precision in the above plot and we see the deviation from the correct result begins as $$ \kappa (W) $$ grows larger than $$ 10^{15} $$.

## Proper Calculation

As it is clear that double precision cannot accurately compute the control energy $$ E(n) $$ for large enough values of $$ n $$, to properly compute $$ E(n) $$ extended precision is required.

MPFR is a free, open-source library capable of extended (or multiple) precision arithmetic.

I wrote a small code that uses libmpfr to compute the Cholesky factor of a symmetric positive definite matrix and then uses back-substitution to solve the linear equation $$ W \textbf{x} = \textbf{1} $$ with multiple precision.

Using this code, evaluating $$ E(n) $$ for 5000 realizations of matrices $$ W $$ using $$ a_j $$ drawn from the same distribution as was used to generate the result in the first figure and enough precision to accurately.

{%
  include image.html
  max-width="500px" 
  file="/images/cauchy-mpprec.png" 
  alt="/images/500x300.png" 
  caption="Control energy using extended precision versus double precision. The extended precision almost exactly matches the analytic result."
%}

Plotting the results, we see they nearly exactly match the analytic result as expected (with small discrepancies due to the random nature of the problem).

