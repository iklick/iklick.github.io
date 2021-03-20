---
title: 'Ising Model on the Square Lattice: Transfer Matrix'
date: 2021-01-02
permalink: /posts/2021/01/ising-square/
tags:
  - statistical mechanics
  - ising model
---

## The Square Lattice

The energy of a configuration of the Ising model is a summation over edges of a graph.

To be general, assume all vertical edges have weight $$ J_v $$ and all horiztonal edges have weight $$ J_h $$.

{%
  include image.html
  file="/images/ising_graph0.png"
  max-width="300px"
  caption="The square lattice with vertical and horiztonal edges highlighted in blue and red."
%}

The energy of a configuration is,

$$
  E(\textbf{s}) = -J_v \sum_{(j,k) \in \mathcal{E}_v} s_j s_k - J_h \sum_{(j,k) \in \mathcal{E}_h} s_js_k
$$

where the first summation is over vertical edges (red ones in the figure) and the second summation is over horizontal edges (blue ones in the figure).

The nodes in the square lattice can also be __bisected__ into two sets, shown in black and white below, such that each black node is only connected to white nodes and vice versa.
This ensures each edge connects a black node with a white.

{%
  include image.html
  file="/images/ising_graph1.png"
  max-width="300px"
  caption="The same square lattice with nodes highlighted according to the bisection such that each white node is only connected to black nodes and vice versa."
%}

The main purpose of this post is to group the edges into sets so that the summation may be done in a way that looks like the transfer matrix identity.

### Rotate the Grid

The grid is rotated 45 degrees so the edges are now diagonal.
The original rows and columns are now diagonals.
The original diagonals are now rows and columns, each consisting of only black nodes or only white nodes.

{%
  include image.html
  file="/images/ising_graph2.png"
  max-width="300px"
  caption="The square lattice rotated"
%}

The nodes are labeled according to their new rows and their new columns so that node $$ s_j^k $$ is the spin located in the $$ j $$'th column and the $$ k $$'th row.

There are $$ m $$ rows and each row consists of $$ n $$ nodes so that in total there are $$ N = nm $$ nodes.
Also, assume that both $$ n $$ and $$ m $$ are even.
As we eventually will only be concerned about the free energy in the thermodynamic limit, this assumption will not affect the results.

The edges are split into sets corresponding to those which pass between adjacent rows of nodes, called strips of edges.

There are two types of strips, ones with black nodes below white nodes, called type $$ V $$, and ones with white nodes below black nodes, called type $$ W $$.

{%
  include image.html
  file="/images/ising_graph3.png"
  max-width="300px"
  caption="The square lattice rotated with edges grouped according to strips between rows of nodes."
%}

The summation over edges is now expressed two strips at a time, centered on a black row of nodes.
Call the row of black nodes $$ 2k $$ so that the white row below it is numbered $$ 2k-1 $$ and the white row above it is numbered $$ 2k+1 $$.

$$
  E(\textbf{s}) = \sum_{k=1}^{m/2} \left[ - \underbrace{\left( \sum_{j=1}^n s_{j+1}^{2k-1} s_j^{2k} + J_h \sum_{j=1}^n s_j^{2k-1} s_j^{2k} \right)}_{V \text{ strip}} - \underbrace{\left(J_v \sum_{j=1}^n s_j^{2k} s_j^{2k+1} + J_h \sum_{j=1}^n s_j^{2k} s_{j+1}^{2k+1} \right)}_{W \text{ strip}} \right]
$$

I recommend spending some time comparing this expression for the edge summation to the edges in the figure, especially the indices of the neighboring nodes in each summation.

### Square Lattice Partition Function

Before writing the partition function, make the following substitutions.

$$
  K = \frac{J_v}{k_B T} \quad \text{and} \quad L = \frac{J_h}{k_B T}
$$

The partition function for the square lattice in terms of the edge strips described above is,

$$
  \begin{aligned}
  Z_N &= \sum_{\textbf{s} \in \mathcal{S}} \exp \left[ \sum_{k=1}^{m/2} \left( K \sum_{j=1}^n s_j^{2k} s_{j+1}^{2k-1} + L \sum_{j=1}^n s_j^{2k} s_j^{2k-1} + K \sum_{j=1}^n s_j^{2k} s_j^{2k+1} + L \sum_{j=1}^n s_j^{2k} s_{j+1}^{2k+1} \right) \right]\\
  &= \sum_{\textbf{s} \in \mathcal{S}} \prod_{k=1}^{m/2} \underbrace{\exp \left[ \sum_{j=1}^n K s_j^{2k} s_{j+1}^{2k-1} + L s_j^{2k} s_j^{2k-1} \right]}_{V_{\textbf{s}^{2k-1},\textbf{s}^{2k}}} \underbrace{\exp \left[ \sum_{j=1}^n K s_j^{2k} s_j^{2k+1} + L s_j^{2k} s_{j+1}^{k+1} \right]}_{W_{\textbf{s}^{2k},\textbf{s}^{2k+1}}}
  \end{aligned}
$$

There is a matrix hiding in this form of the partition function.
Note that the expression,

$$
  \exp \left[ \sum_{j=1}^n K s_j^{2k} s_{j+1}^{2k-1} + L s_j^{2k} s_j ^{2k-1} \right] = \prod_{j=1}^n \exp \left[ K s_j^{2k} s_{j+1}^{2k-1} + L s_j^{2k} s_j^{2k-1} \right]
$$

is a pair-wise function of the spins in adjacent rows, $$ \textbf{s}^{2k-1} $$ and $$ \textbf{s}^{2k} $$.
Define the following $$ 2^n $$-by-$$ 2^n $$ matrix with the elements above,

$$
  V_{\textbf{s},\textbf{s}'} = \prod_{j=1}^n \exp \left[ K s_j' s_{j+1} + L s_j' s_j \right]
$$

Similarly, the edges in the $$ W $$ strip form the entries of another $$ 2^n $$-by-$$ 2^n $$ matrix as well,

$$
  W_{\textbf{s},\textbf{s}'} = \prod_{j=1}^n \exp \left[ K s_j s_j' + L s_j s_{j+1}' \right]
$$

The partition function can now be written in terms of the entries of these two matrices.

$$
  Z_N = \sum_{\textbf{s} \in \mathcal{S}} \prod_{k=1}^{m/2} V_{\textbf{s}^{2k-1}, \textbf{s}^{2k}} W_{\textbf{s}^{2k},\textbf{s}^{2k+1}}
$$

This is in the proper form to apply the transfer matrix identity.
Unlike in the one dimensional case where term was a single matrix, this time there are $$ m/2 $$ copies of the $$ 2^n $$-by-$$ 2^n $$ matrix product $$ VW $$.
Applying the transfer matrix identity lets us write the the partition function as the following matrix trace.

$$
  Z_N = \text{Tr} \left( VW \right)^{m/2} = \sum_{j=1}^{2^n} \Lambda^m_j
$$

where $$ \Lambda^2_j $$ is the $$ j $$'th eigenvalue of the matrix product $$ VW $$.

What remains of this series of posts is determining the eigenvalues of $$ VW $$.
This process is done in two steps.
First, a functional form of the eigenvalues is determined by proving a set of matrices commutes with each other.

## Deriving Properties of V and W

Before continuing, some properties about $$ V $$ an $$ W $$ are derived for use later.

{%
  include definition.html
  title="Negation Operator"
  text="Define the matrix \( R : \mathcal{S}_n \times \mathcal{S}_n \mapsto \{0,1\} \).
  
  $$
    R_{\textbf{s},\textbf{s}'} = \prod_{j=1}^n \delta (s_j, -s_j')
  $$
  
  In words, \( R_{\textbf{s},\textbf{s}'} = 1 \) only if \( s_j = -s_j' \) for \( j = 1,2,\ldots,n \) and zero otherwise.
  "
%}

{%
  include theorem.html
  title="V Commutes with R"
  text="Let \( V \) be defined as above and let \( R \) be the negation operator.
  Then
  
  $$
    VR = RV
  $$"
  proof="The proof is constructive using the definition of matrix multiplication on both sides of the equation.
  
  $$
    \begin{aligned}
    (VR)_{\textbf{s},\textbf{s}'} &= \sum_{\textbf{s}'' \in \mathcal{S}_n} V_{\textbf{s},\textbf{s}''} R_{\textbf{s}'',\textbf{s}'}\\
    &= \sum_{\textbf{s}'' \in \mathcal{S_n}} \left(\prod_{j=1}^n \exp \left[ K s_j'' s_{j+1} + L s_j'' s_j \right] \right) \left( \prod_{j=1}^n \delta(s_j'',-s_j') \right)
    \end{aligned}
  $$
  From the definition of \( R \), the only nonzero term in the summation over \( \textbf{s}'' \in \mathcal{S}_n \) is for \( \textbf{s}'' = -\textbf{s}' \).
  
  $$
    (VR)_{\textbf{s},\textbf{s}'} = \prod_{j=1}^n \exp \left[ -Ks_j' s_{j+1} - L s_j' s_j \right]
  $$
  
  Considering an entry of the product in the other order,
  
  $$
    \begin{aligned}
      (RV)_{\textbf{s},\textbf{s}'} &= \sum_{\textbf{s}'' \in \mathcal{S}_n} R_{\textbf{s},\textbf{s}''} V_{\textbf{s}'',\textbf{s}'}\\
      &= \sum_{\textbf{s}'' \in \mathcal{S}_n} \left( \prod_{j=1}^n \delta(s_j,-s_j'') \right) \left( \prod_{j=1}^n \exp \left[ K s_j'' s_{j+1}' + L s_j'' s_j' \right] \right)\\
      &= \prod_{j=1}^n \exp \left[ -Ks_j s_{j+1}' - L s_j s_j' \right]
    \end{aligned}
  $$
  
  which completes the proof.
  "
%}

{%
  include definition.html
  title="Cycling Operator"
  text="Define the matrix \( C : \mathcal{S}_n \times \mathcal{S}_n \mapsto \{0,1\} \) to have entries,
  
  $$
    C_{\textbf{s},\textbf{s}'} = \prod_{j=1}^n \delta (s_j,s_{j+1}')
  $$
  
  In words, \( C_{\textbf{s},\textbf{s}'} = 1 \) if \( \textbf{s}' \) is \( \textbf{s} \) shifted by one.
  For instance, if \( n = 4 \), then if
  
  $$
    \textbf{s} = \left[ \begin{array}{c}
      -1 \\ +1 \\ -1 \\ -1
    \end{array} \right] \quad \textbf{s}' = \left[ \begin{array}{c}
      -1 \\ -1 \\ +1 \\ -1
    \end{array} \right]
  $$
  then \( C_{\textbf{s},\textbf{s}'} = 1\)."
%}

{%
  include theorem.html
  title="V Commutes with C"
  text="Let \( V \) be defined as above and let \( C \) be the cycling operator. Then
  
  $$
    VC = CV
  $$"
  proof="The proof examines each side of the equation entry by entry.
  
  $$
    \begin{aligned}
      (VC)_{\textbf{s},\textbf{s}'} &= \sum_{\textbf{s}'' \in \mathcal{S}_n} V_{\textbf{s},\textbf{s}''} C_{\textbf{s}'',\textbf{s}'}\\
      &= \sum_{\textbf{s}'' \in \mathcal{S}_n} \left( \prod_{j=1}^n \exp \left[ K s_j'' s_{j+1} + L s_{j}'' s_j \right]\right) \left( \prod_{j=1}^n \delta (s''_j,s'_{j+1}) \right)
    \end{aligned}
  $$
  
  The only nonzero term in the summation is when \( \textbf{s}'' \) is equal to \( \textbf{s}' \) cycled by one.
  
  $$
    (VC)_{\textbf{s},\textbf{s}'} = \prod_{j=1}^n \exp \left[ K s'_{j+1} s_{j+1} + L s_{j+1}' s_j \right]
  $$
  
  Examining the same entry of the product in the other order,
  
  $$
    \begin{aligned}
      (CV)_{\textbf{s},\textbf{s}'} &= \sum_{\textbf{s}'' \in \mathcal{S}_n} \left( \prod_{j=1}^n \delta (s_j,s_{j+1}'')\right) \left( \prod_{j=1}^n \exp \left[ K s_{j+1}'' s_j' + L s_j' s_j'' \right] \right)\\
      &= \prod_{j=1}^n \exp \left[ K s_j s_j' + L s_j' s_{j-1} \right]\\
      &= \prod_{j=1}^n \exp \left[ K s_{j+1} s_{j+1}' + L s_{j+1}' s_j \right]
    \end{aligned}
  $$"
%}

{%
  include theorem.html
  title="Relation Between W and V"
  text="Let \( W \) and \( V \) be defined as above and \( C \) be the cycling operator.
  Then 
  
  $$
    W = VC
  $$"
  proof="From the proof to show \( V \) and \( C \) commute, entries of the product \( VC \) are,
  $$
    \begin{aligned}
    (VC)_{\textbf{s},\textbf{s}'} &= \prod_{j=1} \exp \left[ K s_{j+1} s_{j+1}' + L S_j s_{j+1}' \right]\\
    &= \left( \prod_{j=1}^n \exp \left[ K s_{j+1} s_{j+1}' \right] \right) \left( \prod_{j=1}^n \exp \left[ L s_{j} s_{j+1}' \right] \right)\\
    &= \left( \prod_{j=1}^n \exp \left[ K s_j s_j' \right] \right) \left( \prod_{j=1}^n \exp \left[ L s_j s_{j+1}' \right] \right)\\
    &= \prod_{j=1}^n \exp \left[ K s_j s_j' + L s_j s_{j+1}' \right]
  $$
  
  which is equal to the entries of the matrix \( W \) defined above."
%}

It is straightforward now to show that $$ V $$ and $$ W $$ commute.

$$
  VW = VVC = VCV = WV
$$

Then it also follows that $$ V $$ and $$ VW $$ commute as well.
Now that we have shown $$ V $$ commutes with $$ VW $$ and $$ C $$, the eigenvalues of interest, $$ \lambda^2 $$, can be written in terms of the eigenvalues of $$ V $$ and $$ C $$.

$$
  VW \textbf{x} = \Lambda^2 \textbf{x} = VVC \textbf{x} = v^2 c \textbf{x}
$$

which implies that the square root of the eigenvalue is,

$$
  \Lambda = v \sqrt{c}
$$

This form of the eigenvalues of interest will be used in the next post of this series where a functional form of the eigenvalues is developed.
