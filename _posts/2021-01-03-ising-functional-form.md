---
title: 'Ising Model on the Square Lattice: Eigenvalue Function'
date: 2021-01-03
permalink: /posts/2021/01/ising-functional-form/
tags:
  - statistical mechanics
  - ising model
---

# Square Lattice Ising Model: Part 3

From the previous post, we found the partition function in the thermodynamic limit is proportional to the largest eigenvalue of a matrix, $$ VW $$.
The elements of these two $$ 2^n $$-by-$$ 2^n $$ matrices are,

$$
  \begin{aligned}
    V_{\textbf{s},\textbf{s}'} &= \prod_{j=1}^n \exp \left[ K s_{j+1} s_j' + L s_j s_j' \right]\\
    W_{\textbf{s},\textbf{s}'} &= \prod_{j=1}^n \exp \left[ K s_j s_j' + Ls_j s_{j+1}' \right]
  \end{aligned}
$$ 

Also shown were that $$ V $$ and $$ W $$ commute, as well as both matrices commute with the negation operator $$ R $$ and the cycling operator $$ C $$.

In this post, the dependence of $$ V $$ and $$ W $$ on $$ K $$ and $$ L $$ are made explicit, $$ V(K,L) $$ and $$ W(K,L) $$.

Two additional properties are proved.

1. Find the condition on $$ K $$, $$ L $$, $$ K' $$, and $$ L' $$ such that $$ V(K,L) W(K',L') = V(K',L') W(K,L).
2. Find the condition on $$ K $$, $$ L $$, $$ K' $$, and $$ L' $$ such that $$ V(K,L) W(K',L') = \alpha I + \beta R $$

The first condition will be used to show that $$ V (K,L) $$ and $$ V(K',L') $$ commute given a condition on $$ K $$, $$ L $$, $$ K' $$, and $$ L' $$ and the second condition is used to get a function relation on the eigenvalues.

## Elements of the Matrix Product V(K,L)W(K',L')

$$
  \begin{aligned}
    \left(V(K,L) W(K',L') \right)_{\textbf{s},\textbf{s}'} &= \sum_{\textbf{s}'' \in \mathcal{S}_n} \left( \prod_{j=1}^n \exp \left[ K s_{j+1} s_j'' + L s_j s_j'' \right] \right) \left( \prod_{j=1}^n \exp \left[ K' s_j' s_j'' + L' s_j'' s_{j+1}' \right] \right)\\
    &= \sum_{\textbf{s}'' \in \mathcal{S}_n} \prod_{j=1}^n \exp \left[ s_j'' \left( K s_{j+1} + L s_j + K' s_j' + L' s_{j+1}' \right) \right]
  \end{aligned}
$$

{%
  include theorem.html
  title="Sum of Products to Product of Sums"
  text="Let \( m \) and \( n \) be positive integers. 
  Let \( a_{i} \), \( i = 1,\ldots,m \) 
  
  $$
    \sum_{i_1=1}^m \sum_{i_2 = 1}^m \ldots \sum_{i_n=1}^m \prod_{j=1}^n a_{i_j} b_j = \prod_{j=1}^n \sum_{i_j = 1}^m a_{i_j} b_j
  $$"
  proof=""
%}

Using the above theorem, the elements of the matrix product $$ V(K,L) W(K',L') $$ can be written as,

$$
  \left( V(K,L) W(K',L') \right)_{\textbf{s},\textbf{s}'} = \prod_{j=1}^n \sum_{s_j'' \pm 1} \prod_{j=1}^n \sum_{s_j'' \pm 1} \exp \left[ s_j'' \left( L s_j + K s_{j+1} + K' s_j' + L' s_{j+1}' \right) \right]
$$

Also, the other product has elements,

$$
  \left( V(K',L') W(K,L) \right)_{\textbf{s},\textbf{s}'} = \prod_{j=1}^n \sum_{s_j'' \pm 1} \exp \left[ s_j'' \left( L' s_j + K' s_{j+1} + K s_j' + L s_{j+1}' \right) \right]
$$

To show $$ V(K,L) W(K',L') = V(K',L') W(K,L) $$ we must show each entry is equal.
For $$ \left( V(K,L) W(K',L') \right)_{\textbf{s},\textbf{s}'} = \left( V(K',L') W(K,L) \right)_{\textbf{s},\textbf{s}'} $$ for all $$ \textbf{s},\textbf{s}' \in \mathcal{S}_n $$, it suffices to find the condition to make each term in the products equal.

$$
   \sum_{s_j'' \pm 1} \exp \left[ s_j'' \left( L s_j + K s_{j+1} + K' s_j' + L' s_{j+1}' \right) \right] = \sum_{s_j'' \pm 1} \exp \left[ s_j'' \left( L' s_j + K' s_{j+1} + K s_j' + L s_{j+1}' \right) \right], \quad j = 1,2,\ldots,n, \quad \forall \textbf{s},\textbf{s}' \in \mathcal{S}_n
$$

To determine the conditions on $$ K $$, $$ L $$, $$ K' $$, and $$ L' $$ to make this expression true, a graphical approach is taken called the star-triangle identity.

### Star Triangle Relation

{%
  include theorem.html
  title="Star-Triangle Relation"
  text="A sufficient condition on $$ K_1, K_2, K_3 $$ and  $$ L_1,L_2,L_3 $$ to make the statement,
  
  $$
    \sum_{s_{\ell} \pm 1} \exp \left[ L_1 s_i s_{\ell} + L_2 s_j s_{\ell} + L_3 s_k s_{\ell} \right] = R \exp \left[ K_1 s_j s_k + K_2 s_i s_k + K_3 s_i s_j \right]
  $$
  
  true is for
  
  $$
    \sinh 2K_1 \sinh 2L_1 = \sinh 2K_2 \sinh 2L_2 = \sinh 2K_3 \sinh 2L_3 = \frac{1}{k}
  $$
  
  where $$ k $$ is a determined from only $$ L_1, L_2, L_3 $$ or $$ K_1, K_2, K_3 $$.
  The coefficient $$ R $$ is 
  $$
    R^2 = 2k\sinh (2L_1) \sinh (2L_2) \sinh (2L_3)
  $$"
  proof="First, note that
  
  $$
    \sum_{s_{\ell}''} \exp \left[ L_1 s_i s_{\ell} + L_2 s_j s_{\ell} + L_3 s_k s_{\ell} \right] = 2 \cosh (L_1 s_i + L_2 s_j + L_3 s_k )
  $$
  
  There are four unique equations that can arise from particular configurations of $$ s_i,s_j,s_k $$.
  
  $$
    \begin{aligned}
      [s_i,s_j,s_k] = [+1,+1,+1]: && 2 \cosh (L_1+L_2+L_3) &= R e^{K_1 + K_2 + K_3}\\
      [s_i,s_j,s_k] = [-1,+1,+1]: && 2 \cosh (-L_1 + L_2 + L_3) &= R e^{K_1 - K_2 - K_3}\\
      [s_i,s_j,s_k] = [+1,-1,+1]: && 2 \cosh (L_1 - L_2 + L_3) &= R e^{-K_1 + K_2 - K_3}\\
      [s_i,s_j,s_l] = [+1,+1,-1]: && 2 \cosh (L_1 + L_2 - L_3) &= E e^{-K_1 - K_2 + K_3}
    \end{aligned}
  $$
  
  Make the following definitions
  
  $$
    \begin{aligned}
      c &= \cosh (L_1 + L_2 + L_3) = \frac{R}{2} e^{K_1+K_2+K_3}\\
      c_1 &= \cosh (-L_1 + L_2 + L_3) = \frac{R}{2} e^{K_1 - K_2 - K_3}\\
      c_2 &= \cosh (L_1 - L_2 + L_3) = \frac{R}{2} e^{-K_1 + K_2 - K_3}\\
      c_3 &= \cosh (L_1 + L_2 - L_3) = \frac{R}{2} e^{-K_1 - K_2 + K_3}
    \end{aligned}
  $$
  
  To find expressions for each $$ K_i $$ in terms of $$ L_1,L_2,L_3 $$ in turn, it is staightforward to show,
  
  $$
    \frac{cc_i}{c_jc_k} = e^{4K_i}
  $$
  
  where $$ i,j,k $$ is some permutation of $$ 1,2,3 $$.
  To find an equality, the following expression is evaluated in terms of both $$ K_i $$ and $$ L_i $$.
  
  $$
    \sqrt{\frac{cc_i}{c_jc_k}} - \sqrt{\frac{c_jc_k}{cc_i}}
  $$
  
  For $$ K_i $$ this becomes simply,
  
  $$
    \sqrt{\frac{cc_i}{c_jc_k}} - \sqrt{\frac{c_jc_k}{cc_i}} = \sqrt{e^{4K_i}} - \sqrt{e^{-4K_i}} = 2 \sinh 2K_i
  $$
  
  To determine this expression in terms of $$ L_i $$,
  
  $$
    \begin{aligned}
    \sqrt{\frac{cc_i}{c_jc_k}} - \sqrt{\frac{c_jc_k}{cc_i}} &= \frac{cc_i - c_jc_k}{\sqrt{cc_ic_jc_k}}\\
    &= \frac{\cosh(L_i + L_j + L_k) \cosh (L_j + L_k - L_i) - \cosh(L_i+L_j - L_k) \cosh (L_i + L_k - L_j)}{\sqrt{cc_ic_jc_k}}\\
    &= \frac{1}{2} \frac{\cosh(2L_j + 2L_k) - \cosh (2L_j - 2L_k)}{\sqrt{cc_ic_jc_k}}\\
    &= \frac{\sinh (2L_j) \sinh (2L_k)}{\sqrt{cc_1c_2c_3}}
    \end{aligned}
  $$
  
  Multiply both sides by $$ \sinh (2L_i) $$ to finish the equality.
  
  $$
    \sinh (2K_i) \sinh (2L_i) = \frac{\sinh (2L_i) \sinh (2L_j) \sin (2L_k)}{2 \sqrt{cc_ic_jc_k}}
  $$
  
  Notice the right hand side is invariant under any permutation of $$ i,j,k $$ so it is a constant.
  
  $$
    \frac{\sinh (2L_i) \sinh (2L_j) \sin (2L_k)}{2 \sqrt{cc_ic_jc_k}} = \frac{1}{k}
  $$
  
  This implies the invariant quantity of interest.
  
  $$
    \sinh (2K_1) \sinh (2L_1) = \sinh (2K_2) \sinh (2L_2) = \sinh (2K_3) \sinh (2L_3) = \frac{1}{k}
  $$
  
  Also, the coefficient \( R \) can be determined in a number of different ways, but as one example,
  
  $$
    cc_1c_2c_3 = R^4 \quad \rightarrow \quad \frac{1}{k} = \frac{\sinh (2L_1) \sinh 2(L_2) \sinh (2L_3)}{2 \sqrt{R^4/16}}
  $$
  
  which, after rearrangement, achieves the expression in the theorem statement."
  
%}

This theorem must be used carefully. Simply finding values $$ K_1,K_2,K_3 $$ and $$ L_1,L_2,L_3 $$ that make

$$
  \sinh (2K_1) \sinh (2L_1) = \sinh (2L_2) \sinh (2K_2) = \sinh (2L_3) \sinh (2K_3)
$$

true is not enough because each of them must equal a value $$ \frac{1}{k} $$ that is dependent on $$ L_1,L_2,L_3 $$.
This is important in the following discussion.

### Conditions for Generalized Commutation Relation

Define the matrix products to be products of the following functions,

$$
  \begin{aligned}
  \left( V(K,L) W(K',L') \right)_{\textbf{s},\textbf{s}'} &= \prod_{j=1}^n f(s_j,s_{j+1},s_j',s_{j+1}')\\
  \left( V(K',L') W(K,L) \right)_{\textbf{s},\textbf{s}'} &= \prod_{j=1}^n f'(s_j,s_{j+1},s_j',s_{j+1}')
  \end{aligned}
$$

where

$$
  \begin{aligned}
  f(s_j,s_{j+1},s_j',s_{j+1}') &= \sum_{s_j'' = \pm 1} \exp \left[ s_j'' \left( L s_j + K s_{j+1} + K' s_j' + L' s_{j+1}' \right) \right]\\
  f(s_j,s_{j+1},s_j',s_{j+1}') &= \sum_{s_j'' = \pm 1} \exp \left[ s_j'' \left( L' s_j + K' s_{j+1} + K s_j' + L s_{j+1}' \right) \right]
  \end{aligned}
$$

To introduce an extra degree of freedom, note that the matrix elements do not change by substituting, $$ f $$ with $$ e^{M s_j s_j'} f e^{-M s_{j+1} s_{j+1}'} $$ as each adjacent term in the product will cancel.

The term by term equality with this extra degree of freedom is $$ e^{Ms_js'_j} f e^{-Ms_{j+1} s_{j+1}'} = f' $$ or written out,

$$
  e^{M s_j s_j'} f(s_j,s_{j+1},s_j',s_{j+1}') = e^{M s_{j+1} s_{j+1}'} f'(s_j,s_{j+1},s_j',s_{j+1}')
$$

Either side of this equation can be drawn in diagram form.
Noting that both diagrams have $$ (M,K',L) $$ triangles, they are converted to $$ (L_1,L_2,L_3) $$ stars.
Making these two new diagrams equal suggest assigning $$ L_1 = K $$ and $$ L_2 = L' $$.
Using the star triangle relation then requires that

$$
  \sinh 2K \sinh 2L = \sinh 2K' \sinh 2L' = \frac{1}{k}
$$

Note though that we cannot arbitrarily choose $$ M $$ and $$ L_3 $$ to make $$ \sinh 2M \sinh 2L_3 = \frac{1}{k} $$ because $$ L_3 $$ must first be selected to make

$$
  \frac{\sinh 2K \sinh 2L' \sinh 2L_3}{2 \sqrt{cc_1c_2_c_3}} = \frac{1}{k}
$$

where $$ L_3 $$ also appears in the product $$ cc_1c_2c_3 $$.
Thankfully the specific value of $$ L_3 $$ is not needed, but if one is interested, a method like bisection can be used to find $$ L_3 $$ numerically.

To summarize, a sufficient condition for $$ V(K,L) W(K',L') = V(K',L') W(K,L) $$ is for,

$$
  \sinh 2K \sinh 2L = \sinh 2K' \sinh 2L'
$$

An immediate consequence of this result, using the commutation relations from the previous post, for all values that satisfy this condition on $$ K,L,K',L' $$, we get that,

$$
  V W' = V' W \quad \rightarrow \quad VV' C = V'V C \quad \rightarrow \quad VV' V'V
$$

where the fact $$ C $$ is nonsingular must be used.
This implies that given a pair $$ K,L $$ and an eigenvector for a simple eigenvalue of $$ V $$, all other pairs $$ K',L' $$ that satisfy the condition must also generate a matrix $$ V' $$ with the same eigenvector.
As there are an infinite number of such matrices, the eigenvector $$ \textbf{x} $$ must be a function of $$ k $$ and not any particular pairs $$ K, L $$.

## Finding Nonzeros of the Matrix Product

As a reminder, the entries of the matrix product $$ V W' $$ are,

$$
  \left( V W' \right)_{\textbf{s},\textbf{s}'} = \prod_{j=1}^n f(s_j,s_{j+1},s_j',s_{j+1}') = 2 \prod_{j=1}^n \cosh (Ls_j + Ks_{j+1} + K' s_j' + L' s_{j+1}')
 $$
 
Given a pair $$ K, L $$, can we select $$ K',L' $$ such that many of the entries of this matrix product are zero.

One idea is to make the product diagonal, that is,

$$
  \left( V W' \right)_{\textbf{s},\textbf{s}'} = a_{\textbf{s}} \prod_{j=1}^n \delta (s_j,s_j')
$$

This implies that we need to design $$ K' $$ and $$ L' $$ to make a term in the product zero any time $$ s_j \neq s_j' $$ or $$ s_{j+1} \neq s_{j+1}' $$.

This situation consists of the following cases (up to a swap of signs).

$$
  \begin{aligned}
    [+1,+1,+1,-1] && [+1,+1,-1,+1]\\
    [+1,+1,-1,-1] && [+1,-1,+1,+1]\\
    [+1,-1,-1,-1] && [+1,-1,-1,+1]
  \end{aligned}
$$

These configurations lead to the following expressions which must all be made zero simultaneously.

$$
  \begin{aligned}
    \cosh (L+K+K'-L') &= 0 &&
    \cosh (L+K-K'+L) &= 0\\
    \cosh (L+K-K'-L') &= 0 &&
    \cosh (L-K+K'+L') &= 0\\
    \cosh (L-K-K'-L') &= 0 &&
    \cosh (L-K-K'+L') &= 0
  \end{aligned}
$$

The zeroes of $$ \cosh (z) $$ occur when

$$
  z = i \frac{\pi (2n+1)}{2}, \quad \forall n \in \mathbb{Z}
$$

Making all of these expressions simultaneouly zero requires satisfying six linear equations with only two degrees of freedom.

Rather than making the product $$ V W' $$ diagonal, a more practical choice is to design $$ K' $$ and $$ L' $$ when $$ s_j \neq s_j' $$ and $$ s_{j+1} == s'_{j+1} $$.

The specific cases that must be set to zero (up to a swap of sign) are,

$$
  \begin{aligned}
    [+1,+1,-1,+1] && [+1,-1,-1,-1]
  \end{aligned}
$$

which corresponds to the following system of equations,

$$
  \begin{aligned}
    \cosh (L+K-K'+L') = 0 && \cosh (L-K-K'-L') = 0
  \end{aligned}
$$

This corresponds to the following system of equations

$$

\begin{aligned}
  \left[ \begin{array}{cc}
    -1 & 1 \\ -1 & -1
  \end{array} \right] \left[ \begin{array}{c}
    K' \\ L'
  \end{array} \right] = \left[ \begin{array}{c}
    i \frac{\pi(2n_1-1)}{2} - L - K\\
    i \frac{\pi(2n_2-1)}{2} -L + K
  \end{array} \right]
\end{aligned}

$$

where $$ n_1,n_2 $$ can be any integers.
Noting that $$ L' $$ and $$ K $$ have opposite signs on both sides of the equation so a valid choice is $$ L' = -K $$.
Then we must find a value of $$ K' $$ to make the following two equations hold.

$$
  \begin{aligned}
    -K' &= i \frac{\pi (2n_1-1)}{2} - L\\
    -K' &= i \frac{\pi (2n_2-1)}{2} - L
  \end{aligned}
$$

Clearly to make these simultaneously true, $$ n_1 = n_2 $$ and for a simplicity, choose $$ n_1 = n_2 = -1 $$.

$$
  K' = \frac{i \pi}{2} + L, \quad L' = K
$$

This choice must be checked to verify that it satisfies the previous expression for the generalized commutation relation.

$$
  \begin{aligned}
  \sinh 2K' \sinh 2L' &= \sinh \left( i \pi \right + 2L \right) \sinh (-2K)\\
  &= -\left( \sinh \left( i\pi \right) \cosh (2L) + \cosh \left( i\pi \right) \sinh (2L) \right) \sinh (2K)\\
  &= \sinh (2L) \sinh (2K)
  \end{aligned}
$$

### Nonzero Elements of VW'

The elements of the product $$ V W' $$ are equal to zero if $$ s_j \neq s_j' $$ and $$ s_{j+1} = s_{j+1}' $$.
This implies that when $$ s_j = s_j' $$ and $$ s_{j+1} = s_{j+1}' $$ or when $$ s_j \neq s_j' $$ and $$ s_{j+1} \neq s_{j+1}' $$, the corresponding entries are not equal to zero.

These entries are categorized into \textit{like} cases ( $$ s_j = s_j' $$ and $$ s_{j+1} = s_{j+1}' $$) and \textit{unlike} ($$ s_j \neq s_j' $$ and $$ s_{j+1} \neq s_{j+1}' $$) cases.

For the \textit{like} case,

$$
  \begin{aligned}
  f_{like} &= 2 \cosh (L s_j + K s_{j+1} + K' s_j' + L' s_{j+1}')\\
  &= 2 \cosh \left( L s_j + K s_{j+1} + \left( \frac{i\pi}{2} + L \right) s_j - K s_{j+1} \right)\\
  &= 2 \cosh \left( \left( 2L + \frac{i\pi}{2} \right) s_j \right)\\
  &= 2 \cosh 2L \cosh \frac{i\pi}{2} + 2 \sinh 2 L \sinh \frac{i\pi}{2}\\
  &= 2i \sinh 2L
  \end{aligned}
$$

while for the \textit{unlike} case,

$$
  \begin{aligned}
    f_{unlike} &= 2\cosh (L s_j + K s_{j+1} + K' s_j' + L' s_{j+1}')\\
    &= 2 \cosh \left( L s_j + K s_{j+1} - \left(L + \frac{i\pi}{2} \right) s_j + K s_{j+1} \right)\\
    &= 2 \cosh \left( 2 K s_{j+1} - \frac{i\pi}{2} s_j \right)\\
    &= 2\cosh (2K s_{j+1} ) \cosh \left( \frac{i\pi}{2} s_j \right) - \sinh (2K s_{j+1} ) \sinh \left( \frac{i\pi}{2} s_j \right)\\
    &= -2is_js_{j+1} \sinh 2K
  \end{aligned}
$$

Elements of the matrix product can now be written in terms of $$ f_{like} $$ and $$ f_{unlike} $$.

$$
  \begin{aligned}
  \left(V(K,L) W(K',L') \right)_{\textbf{s},\textbf{s}'} &= \prod_{j=1}^n \left( f_{\like} \delta(s_j,s_j') \delta(s_{j+1},s_{j+1}') + f_{unlike} \delta(s_j,-s_j') \delta(s_{j+1},-s_{j+1}') \right)\\
  &= f_{\like} \prod_{j=1}^n \delta (s_j,s_j') + f_{unlike} \prod_{j=1}^n \delta(s_j,-s_j')\\
  &= (2i\sinh 2L)^n I_{\textbf{s},\textbf{s}'} + (-2i \sinh 2K)^n R_{\textbf{s},\textbf{s}'}
  \end{aligned}
$$

where $$ I $$ is the $$ 2^n $$-by-$$ 2^n $$ identity matrix and $$ R $$ is the negation operation discussed in the first post in this series.

The inversion identity is now complete.

$$
  V(K,L) W\left(L + \frac{i\pi}{2}, -K \right) = (2i \sinh 2L)^n I + (-2i\sinh 2K)^n R
$$

From the previous analysis, we had found $$ W = VC $$ so,

$$
  V(K,L) V \left( L + \frac{i\pi}{2}, -K \right) C = (2i\sinh 2L)^n I + (-2i \sinh 2L)^n R
$$

We also have shown that $$ V $$ commutes with $$ V' $$, $$ C $$, $$ R $$, and of course the identity matrix.
By Perron-Frobenius, we know that $$ V $$ has a unique largest eigenvalue so there exists a vector $$ \textbf{x} $$ that is a common eigenvector for all of these matrices.
Let $$ v(K,L) $$, $$ c $$, and $$ r $$ be the eigenvalues of $$ V(K,L) $$, $$ C $$, and $$ R $$, respectively.

$$
  v(K,L) v\left( L + \frac{i\pi}{2}, -K \right) c \textbf{x} = (2i\sinh 2L)^n \textbf{x} + (-2i\sinh (2K))^n r \textbf{x}
$$

As the vector appears in all terms in the equation, only the coefficients need be considered.

$$
  v(K,L) v \left( L+\frac{i\pi}{2}, -K \right) c = (2i\sinh 2L)^n + (-2i\sinh 2K))^n r
$$

From the end of the previous post in this series, the expression for the square root of the eigenvalues of $$ V(K,L) W(K,L) $$ is

$$
  \Lambda (K,L) = v(K,L) \sqrt{c}
$$

which can be plugged into the scalar equation

$$
  \Lambda (K,L) \Lambda \left( L + \frac{i\pi}{2}, -K \right) = (2i\sinh 2L)^n + (-2i \sinh 2K))^n r
$$

This is the functional form of $$ \Lambda (K,L) $$ that is address in the remaining posts in this series.
Before finishing this post though, the fact $$ K $$ and $$ L $$ are not independent is addressed by introducing another variable to connect them.

## Finding a Relation Between K and L

Before finishing this post, a discussion about $$ \Lambda (K,L) $$ is presented (and about eigenvalues in general).

Remember from the first part of this post that $$ K $$ and $$ L $$ cannot be varied freely, but rather once the initial pair is chosen, all other pairs $$ K' $$ and $$ L' $$ must satisfy

$$
  \sinh 2K \sinh 2L = \sinh 2K' \sinh 2L' = \frac{1}{k}
$$

From the discussion about eigenvalues in the first post of this series, it was shown that an eigenvalue $$ \lambda $$ of a matrix $$ A $$ with eigenvector $$ \textbf{x} $$ is a linear sum of the entries of the matrix,

$$ 
  \lambda = \sum_{j=1}^n a_{i,j} \frac{x_j}{x_i}
$$

where the coefficients of each entry $$ a_{i,j} $$ depends on the eigenvector.

Thus, the eigenvalues of $$ V(K,L) $$ are linear sums of its entries weighted by coefficients that do not depend on $$ K $$ or $$ L $$.

$$ 
  v(K,L) = \sum_{\textbf{s}' \in \mathcal{S}_n} \frac{x_{\textbf{s}'}}{x_{\textbf{s}}} V_{\textbf{s},\textbf{s}'}(K,L)
$$

Before determining a useful variable to connect $$ K $$ and $$ L $$, the entries of the matrix $$ V(K,L) $$ are examined one last time.
As a reminder, the matrix $$ V(K,L) $$ has entries,

$$
  \left( V(K,L) \right)_{\textbf{s},\textbf{s}'} = \exp \left[ \sum_{j=1}^n \left( K s_{j+1} s_j' + L s_j s_j' \right) \right]
$$

Let $$ d_1 $$ be the number of sign changes between $$ s_{j+1} $$ and $$ s_j' $$ and let $$ d_2 $$ be the number of sign changes between $$ s_j $$ and $$ s_j' $$.

Then the entries of $$ V $$ can be expressed in terms of sign changes.

$$
  \left( V(K,L) \right)_{\textbf{s},\textbf{s}'} = \exp \left[ (n-2d_1) K + (n-2d_2) L \right]
$$

The number of sign changes are multiplied by two because each sign change reduces the number of same sign terms by two.
As an example, if every pair of spins switches sign, then $$ d_2 = n $$ so the coefficient in front of $$ L $$ should be $$ -n $$.

Remember that $$ n $$ is assumed even and assign $$ 2d_1' = |n-2d_1| $$ and $$ 2d_2' = |n-2d_2| $$ so 

$$
  \left( V(K,L) \right)_{\textbf{s},\textbf{s}'} = \exp \left[ \pm 2d_1' K \pm 2d_2' L \right]
$$

With this form of the elements of the matrix $$ V $$, it will be useful to find an expression for $$ e^{2K} $$ and $$ e^{2L} $$ that connects $$ K $$ and $$ L $$.
An obvious first choice is

$$
  \sinh 2K = x \quad \sinh 2L = \frac{1}{xk}
$$

so obviously for any choice of $$ x $$, $$ \sinh 2K \sinh 2L = \frac{1}{k} $$.

To find the expressions for $$ e^{2K} = f_K(x) $$ and $$ e^{2L} = f_L(x) $$, the definition of $$ \sinh $$ is used.
First, for $$ f_K(x) $$,

$$
  \sinh (2K) = \frac{1}{2} \left( f_K(x) - \frac{1}{f_K(x)} \right) = x
$$

Multiply through by $$ 2f_K (x) $$ to get a quadratic expression for $$ f_K(x) $$

$$
  f_K^2(x) - 2 x f_K(x) - 1 = 0
$$

which implies the expression for $$ f_K(x) $$ is,

$$
  f_K(x) = \frac{1}{2} \left( 2 x \pm \sqrt{4 x^2 + 4} \right) = x \pm \sqrt{x^2+1}
$$

Note that for $$ K $$ real, $$ e^{2K} > 0 $$ so only the sum term need be considered.

$$
  e^{2K} = x \pm \sqrt{1+x^2}
$$

Finding $$ f_L(x) $$ follows a similar line,

$$
  f_L^2(x) - \frac{2}{xk} f_L(x) - 1 = 0
$$

which implies the expression for $$ f_L(x) $$ is,

$$
  f_L(x) = \frac{1}{2} \left( \frac{2}{xk} \pm \sqrt{\frac{4}{x^2k^2} + 4} \right) = \frac{1}{xk} \left( 1 \pm \sqrt{x^2 k^2 + 1} \right)
$$

Again, only the summation version need be considered to ensure for real $$ L $$ $$ f_L(x) > 0 $$.

$$
  \begin{aligned}
  e^{2K} &= x + \sqrt{1+x^2}\\
  e^{2L} &= \frac{1}{kx} \left( 1 + \sqrt{1+k^2 x^2} \right)
  \end{aligned}
$$

It would be helpful in the analysis to find a family of functions for some variable $$ u $$ such that

$$
  g_K^2(u) = 1+x^2 \quad \text{and} \quad g_L^2(u) = 1 + k^2x^2 
$$

In the next post, Jacobi elliptic functions will be shown to be such a family of functions.
