---
title: 'Optimal Driver Node Selection (Part 1): Submodular Function Maximization'
date: 2021-01-01
permalink: /posts/2021/01/driver-node-1/
tags:
  - optimization
  - driver nodes
  - set functions
  - heuristics
---

## OUTLINE

This post provides a (nearly) self contained derivation of the well known approximation guarantee of the greedy heuristic when maximizing an increasing submodular set function.
The results in this post will be used in the following post in this series for the optimal driver node selection problem introduced previously.

1. Definition of Submodular Increasing Set Functions
2. Submodular Optimization as a Linear Program
3. Approximation Guarantee of the Greedy Heuristic

## DEFINITIONS

### POWER SET AND UPPER SETS

{%
  include definition.html
  title="Power Set"
  text="Let \( \mathcal{V} \) be a set. Its power set is \( 2^{\mathcal{V}} \) is the set of all subsets of \( \mathcal{V} \).
  \begin{equation*}
    2^{\mathcal{V}} = \left\{ \mathcal{D} | \mathcal{D} \subseteq \mathcal{V} \right\}
  \end{equation*}"
%} 

<br/>

A power set can be represented as a <i>Hasse diagram</i>, which is a graph with nodes corresponding to each subset $$ \mathcal{D} \subseteq \mathcal{V} $$ and edges between nodes if the sets are different by a single element, that is, there is an edge between $$ \mathcal{D} $$ and $$ \mathcal{D}' $$ if $$ \mathcal{D}' = \mathcal{D} \cup \{ j \} $$ for all $$ j \notin \mathcal{D} $$.

{%
  include image.html
  file="/images/dns_hasse_5.png"
  caption="Hasse Diagram for \( n = 5 \)."
%}

<br/>

{%
  include definition.html
  title="Upper Set"
  text="Let \( \mathcal{V} \) be a set.
  Then the upper set of \( \mathcal{D} \subseteq \mathcal{V} \) is 
  
  \begin{equation*}
    \mathcal{U}(\mathcal{D}) = \left\{ \mathcal{D}' \subseteq \mathcal{V} | \mathcal{D} \subseteq \mathcal{D}' \right\}
  \end{equation*}"
%}

<br/>

An upper set $$ \mathcal{U}(\mathcal{D}) \subseteq 2^{\mathcal{V}} $$ and specifically $$ \mathcal{U}(\emptyset) = 2^{\mathcal{V}} $$.

The upper set can be highlighted in the Hasse diagram, for instance, $$ \mathcal{U}(\{0,2\}) $$ is shown below.

{%
  include image.html
  file="/images/dns_hasse_5_colored.png"
  caption="The upper set for \( \mathcal{D} = \{0,2\} \) when \( n = 5 \)."
%}

The optimization problem addressed in this post looks to maximize a set function $$ h : \mathcal{U}(\mathcal{D}_C) \mapsto \mathbb{R} $$ for some initial set $$ \mathcal{D}_C $$ of cardinality $$ m_0 < m $$.

\begin{equation}
  \begin{array}{cl}
    \max\limits_{\mathcal{D} \in \mathcal{U}(\mathcal{D}_C)} & h(\mathcal{D})\newline
    \text{s.t.} & |\mathcal{D}| = m
  \end{array}
  \label{eq:opt}
\end{equation}

Before addressing the effectiveness of the greedy algorithm though, some restrictions are placed on the set function $$ h $$.

### SUBMODULAR INCREASING SET FUNCTION

In the following definitions, we assume that $$ \mathcal{D}_C = \emptyset $$ for simplicity but extensions to the  case $$ \mathcal{D}_C $$ is nonempty are straightforward.

<br/>

{%
  include definition.html
  title="Set Function"
  text="Let \( \mathcal{V} \) be a set. A set function \( h : 2^{\mathcal{V}} \mapsto \mathbb{R} \) maps each subset of \( \mathcal{V} \) and maps it to a real number."
%}

<br/>

{%
  include definition.html
  title="Increasing Set Function"
  text="A set function \( h:2^{\mathcal{V}} \mapsto\mathbb{R} \) is said to be increasing if,
  
  \begin{equation}
    h(\mathcal{D}_1) \leq h(\mathcal{D}_2), \quad \forall \mathcal{D}_1 \subseteq \mathcal{D}_2 \subseteq \mathcal{V}
    \label{eq:increasing}
  \end{equation}"
%}

<br/>

{%
  include definition.html
  title="Submodular Set Function"
  text="A set function \( h : 2^{\mathcal{V}} \mapsto \mathbb{R} \) is submodular if,
  \begin{equation}
    h(\mathcal{D}) + h(\mathcal{D}') \geq h(\mathcal{D} \cup \mathcal{D}') + h(\mathcal{D} \cap \mathcal{D}'), \quad \forall \mathcal{D},\mathcal{D}' \subseteq \mathcal{V}
    \label{eq:submodular}
  \end{equation}"
%}

<br/>

A useful quantity is the incremental cost of including one additional element to the set.

\begin{equation}
  h_j (\mathcal{D}) = h(\mathcal{D} \cup \{ j \}) - h(\mathcal{D})
  \label{eq:incremental}
\end{equation}

If $$ h $$ is an increasing set function, then using Eq. \eqref{eq:increasing} we have,

\begin{equation\*}
  h_j (\mathcal{D}) \geq 0, \quad \forall j \in \mathcal{V}
\end{equation\*}

{%
  include lemma.html
  title="Equivalent Definitions of Submodularity"
  text="Let \( h : 2^{\mathcal{V}} \mapsto \mathbb{R} \) be a set function. Then the following statements are all equivalent.
  
  <ol>
    <li> \( h(\mathcal{D}) + h (\mathcal{D}') \geq h ( \mathcal{D} \cup \mathcal{D}') + h (\mathcal{D} \cap \mathcal{D}'), \quad \forall \mathcal{D},\mathcal{D}' \subseteq \mathcal{V}\) </li>
    <li> \( h_j(\mathcal{D}_1) \geq h_j(\mathcal{D}_2), \quad \forall \mathcal{D}_1 \subseteq \mathcal{D}_2 \subseteq \mathcal{V}, \quad \forall j \in \mathcal{V} \backslash \mathcal{D}_2 \)</li>
    <li> \( h(\mathcal{D} \leq h(\mathcal{D}') + \sum_{j \in \mathcal{D} \backslash \mathcal{D}'} h_j(\mathcal{D}') - \sum_{j \in \mathcal{D}' \backslash \mathcal{D}} h_j (\mathcal{D} \cup \mathcal{D}' \backslash \{ j \}) \)</li>
  </ol>
  
  One needs only to prove one of these statements is true for the function \( h \) to prove it is submodular."
  proof="The proof is somewhat extensive and not very englightening, but for the interested reader it can be found in Proposition 2.1 in Nemhauser."
%}


### GREEDY HEURISTIC

I will now refine the optimization problem in Eq. \eqref{eq:opt} using the above definitions.

\begin{equation}
  \begin{array}{cl}
    \min\limits_{\mathcal{D} \in \mathcal{U}(\mathcal{D}_C)} & h(\mathcal{D})\newline
    \text{s.t.} & |\mathcal{D}| = m\newline
    & h \text{ is submodular and increasing}
  \end{array}
  \label{eq:opt2}
\end{equation}

The set $$ \mathcal{D}_{C} $$, $$ \vert \mathcal{D}_C \vert = m_0 $$, is some initial set, possible the empty set $$ \emptyset $$.

A simple heuristic that can provide approximate solutions is the greedy algorithm.

{%
  include definition.html
  title="Greedy Heuristic"
  text="Given a set \( \mathcal{V} \), an initial set \( \mathcal{D}_{C} \) of \( m_0 \) elements, a desired set size \( m > m_0 \), and a set function \( h : 2^{\mathcal{V}} \mapsto \mathbb{R} \), then the greedy algorithm takes the following steps:
  
  <ol> 
  <li>Initialize \( \mathcal{D} \gets \mathcal{D}_{C} \), \( k \gets m_0 \) </li>
  <li> Compute \( j_k \gets \text{arg}\max\limits_{j \in \mathcal{V} \backslash \mathcal{D}} h_j(\mathcal{D}) \) </li>
  <li> Update \( \mathcal{D} \gets \mathcal{D} \cup \{ j_k \} \) and \( k \gets k+1 \) </li>
  <li> If \( k = m \) return the set \( \mathcal{D} \). Otherwise go back to 2 and repeat. </li>
  </ol>" 
%}

<br/>

If we denote $$ \mathcal{D}^*_C $$ as a true optimal solution with $$ \mathcal{D}_C \subset \mathcal{D}_C^* $$ and the set $$ \mathcal{D}^G_C $$ as the set returned by the greedy algorithm starting from $$ \mathcal{D}_C $$, then the greedy algorithm's solution satisfies the bound,

\begin{equation}
  \frac{h(\mathcal{D}^\*\_C) - h(\mathcal{D}^G\_C)}{h(\mathcal{D}^\*\_C) - h(\mathcal{D}\_{C})} \leq \left( \frac{m-m_0-1}{m-m_0} \right)^{m-m_0}
  \label{eq:bound}
\end{equation}

To prove this bound, the following steps are taken

<ol>
  <li> At each step of the greedy heuristic, the cost of the set created is shown to satisfy a linear inequality. </li>
  <li> The linear inequalities are collected into a linear program which admits an exact solution. </li>
  <li> Using the exact solution of the linear program, the bound shown in Eq. \eqref{eq:bound} can be derived.</li>
</ol>

## SUBMODULAR MAXIMIZATION AS A LINEAR PROGRAM

### BOUNDING THE OPTIMAL COST

The greedy algorithm creates a set $$ \mathcal{D}_C^k $$ at each step $$ k = 0,1,\ldots,m-m_0-1 $$ such that $$ \mathcal{D}_C^0 = \mathcal{D}_C $$ by adding whichever element $$ j \in \mathcal{V} \backslash \mathcal{D}_C^{k} $$ maximizes

\begin{equation\*}
 h\_k = \max\limits_{j \in \mathcal{V} \backslash \mathcal{D}\_C^{k}} h_j (\mathcal{D}\_C^{k})
\end{equation\*}

Denote $$ j_k $$ as the $$ k $$'th node selected.

The solution returned by the greedy heuristic can be expressed as the telescoping sum using the quantities $$ h_k $$, $$ k = 1,2,\ldots,m-m_0 $$.

$$
  \begin{aligned}
  h(\mathcal{D}_C^k) &= h(\mathcal{D}_{C}) + \sum_{i=0}^{k-1} \left(h(\mathcal{D}_C^{i-1} \cup \{ j_i \}) - h(\mathcal{D}_C^{i-1}) \right)\\
  &= h(\mathcal{D}_{C}) + \sum_{i=0}^{k-1} h_i, \quad k = 0,1,\ldots,m-m_0-1
  \end{aligned}
$$

{%
  include lemma.html
  title="Upper Bounds of the Optimal Cost"
  text="Let \( \mathcal{D}^*_C \) be an optimal set and \( \mathcal{D}^k_C \) be the \( k \)'th set returned by the greedy heuristic.
  
  \begin{equation}
    h(\mathcal{D}^*_C) \leq h(\mathcal{D}_{C}) + \sum_{i=1}^{k-1} h_i + (m-m_0) h_k, \quad k = 0,1,\ldots,m-m_0-1
  \end{equation}"
  proof="Using Identity (3) for Submodularity for the \( k \)'th subset constructed by the greedy algorithm,
  
  \begin{equation*}
    h(\mathcal{D}^*_C) \leq h(\mathcal{D}_C^k) + \sum_{ j \in \mathcal{D}^*_C \backslash \mathcal{D}_C^k} h_j (\mathcal{D}_C^k) - \sum_{j \in \mathcal{D}_C^k \backslash \mathcal{D}^*_C} h_j (\mathcal{D}^*_C \cup \mathcal{D}_C^k \backslash \{j\}), \quad k = 0, \ldots,m-m_0-1
  \end{equation*}
  
  Using the following inequalities,
  
  \begin{equation*}
    \begin{aligned}
      h_j(\mathcal{D}) \geq 0\\
      |\mathcal{D}^*_C \backslash \mathcal{D}_C^k| \leq (m-m_0)\\
      h_j(\mathcal{D}_C^k) \leq h_k\\
      h(\mathcal{D}_C^k) = h(\mathcal{D}_{C}) + \sum_{i=0}^{k-1} h_i
    \end{aligned}
  \end{equation*}
  
  Using these inequalities, we can upper bound the optimal solution as
  
  \begin{equation*}
    h(\mathcal{D}^*_C) \leq h(\mathcal{D}_{C}) + \sum_{i=0}^{k-1} h_i + (m-m_0) h_k, \quad k=0, 1, \ldots,m-m_0-1
  \end{equation*}
  "
%}

<br/>

This system of inequalities in Lemma UPPER BOUNDS can be expressed in matrix form,

\begin{equation}
  T(1,m-m_0) \textbf{h} \geq (h(\mathcal{D}^*_C) - h(\mathcal{D}_{C})) \textbf{1}
  \label{eq:lincon}
\end{equation}

where $$ \textbf{1} $$ is the vector of all ones, $$ \textbf{h} $$ stacks the variables $$ h_{m_0}, h_{m_0+1}, \ldots, h_{m-m_0} $$, and the matrix $$ T(a,b) $$ is the triangular matrix,

\begin{equation}
  T(a,b) = \left[ \begin{array}{cccc}
    b & 0 & 0 & \cdots & 0\\
    a & b & 0 & \cdots & 0\\
    a & a & b & \cdots & 0\\
    \vdots & \vdots & \vdots & \ddots & \vdots\\
    a & a & a & \cdots & b
  \end{array} \right]
  \label{eq:triangular}
\end{equation}

This linear system of equations will be used in a linear program to derive an upper bound of $$ h(\mathcal{D}^*) $$.

### PRELIMINARIES

{%
  include lemma.html
  title="Solution of a Triangular System of Equations"
  text="Define the triangular matrix \( T(a,b) \) as shown above of dimension \( n \), then the solution of the system of equations,
  
  \begin{equation*}
    T(a,b) \textbf{x} = \textbf{1}
  \end{equation*}
  
  is
   
  \begin{equation*}
    x_k = \frac{(b-a)^{k-1}}{b^k}, \quad k = 1,2,\ldots,n
  \end{equation*}"
  proof="The proof is by induction. The base case for \( k = 1 \) is easily shown to be \( x_1 = \frac{1}{b} \).
  The inductive hypothesis states that
  
  \begin{equation*}
    x_k = \frac{(b-a)^{k-1}}{b^k}
  \end{equation*}
  
  The inductive step investigates the value of \( x_{k+1} \) 
  
  \begin{equation*}
    \begin{aligned}
      x_{k+1} &= \frac{1}{b} \left(1 - a\sum_{i=1}^k x_i \right)\\
      &= \frac{1}{b} \left( 1 - a \sum_{i=1}^k \frac{(b-a)^{i-1}}{b^i} \right)\\
      &= \frac{1}{b} \left( 1 - \frac{a}{b-a} \sum_{i=1}^k \left( \frac{b-a}{b} \right)^i \right)\\
      &= \frac{1}{b} \left( 1 - \frac{a}{b-a} \left( \frac{ \left( \frac{b-a}{b} \right) - \left( \frac{b-a}{b} \right)^{k+1}}{1 - \frac{b-a}{b} } \right) \right)\\
      &= \frac{1}{b} \left( 1 - \left( \frac{a}{b-a} \right) \left( \frac{b-a}{b} \right) \left( \frac{1 - \left( \frac{b-a}{b} \right)^k}{ \frac{a}{b} } \right) \right)\\
      &= \frac{1}{b} \left( 1- \left(1 - \left( \frac{b-a}{b} \right)^k \right) \right)\\
      &= \frac{(b-a)^k}{b^{k+1}}
    \end{aligned}
  \end{equation*}
  
  which is the form expected."
%}

<br/>

{%
  include lemma.html
  title="Asymmetric Duality of Linear Programs"
  text="The primal linear program
  
  \begin{equation*}
    \begin{aligned}
      \min && &P = \textbf{c}^T \textbf{x}\\
      \text{s.t.} && &A \textbf{x} \geq \textbf{b}
    \end{aligned}
  \end{equation*}
  
  has a dual linear program
  
  \begin{equation*}
    \begin{aligned}
      \max && &Q = \textbf{b}^T \textbf{y}\\
      \text{s.t.} && &A^T \textbf{y} = \textbf{c}\\
      && &\textbf{y} \geq \textbf{0}
    \end{aligned}
  \end{equation*}
  
  where the optimal objective value of the primal \( P^* \) is equal to the optimal objective of the dual \( Q^* \)."
  proof="The proof of this can be found in any introductory text on optimization and is outside the scope of this post."
%}

<br/>

{%
  include lemma.html
  title="The Solution of a Linear Program"
  text="Consider the following linear program for \( (m-m_0) \) variables \( x_i \), \(i = 1,2,\ldots,m-m_0\),
  
  \begin{equation*}
    \begin{aligned}
    \min && &P = \textbf{1}^T \textbf{x}\\
    \text{s.t.} && &T(1,m-m_0) \textbf{x} \geq \textbf{1}
    \end{aligned}
  \end{equation*}
  
  It's optimal objective value is,
  
  \begin{equation}
    P^* = 1 - \left( \frac{m-m_0-1}{m-m_0} \right)^{m-m_0} 
    \label{eq:linprog_sol}
  \end{equation}"
  proof="The dual of this linear program is,
  
  \begin{equation*}
    \begin{aligned}
      \max && &Q = \textbf{1}^T \textbf{y}\\
      \text{s.t.} && &T^T(1,m-m_0) \textbf{y} = \textbf{1}\\
      && &\textbf{y} \geq \textbf{0}
    \end{aligned}
  \end{equation*}
  
  The system of equations yields a unique solution as \( T(1,m-m_0) \) is a square non-singular matrix.
  Using the lemma for the triangular system of equations, and using the fact we transpose the solution, we have, the optimal values,
  
  \begin{equation*}
    y_i^* = \frac{(m-m_0-1)^{m-m_0-i}}{(m-m_0)^{m-m_0-i+1}}
  \end{equation*}
  
  Plugging these optimal dual variables into the objective function, and for notation's sake define \( z = m-m_0 \)
  
  \begin{equation*}
    \begin{aligned}
      Q^* &= \sum_{i=1}^{z} y_i\\
      &= \sum_{i=1}^{z} \frac{(z-1)^{z-i}}{z^{z-i+1}}\\
      &= \frac{1}{z} \left( \frac{z-1}{z} \right)^z \sum_{i=1}^z \left( \frac{z}{z-1} \right)^i\\
      &= \frac{1}{z} \left( \frac{z-1}{z} \right)^z \left[ \frac{\left( \frac{z}{z-1} \right) - \left( \frac{z}{z-1}\right)^{z+1}}{1 - \frac{z}{z-1}} \right]\\
      &= \frac{1}{z} \left( \frac{z-1}{z} \right)^{z-1} \left[ \frac{1 - \left( \frac{z}{z-1} \right)^z}{- \frac{1}{z-1} } \right]\\
      &= \left( \frac{z-1}{z} \right)^z \left( \left( \frac{z}{z-1} \right)^z - 1 \right)\\
      &= 1 - \left( \frac{z-1}{z} \right)^z
    \end{aligned}
  \end{equation*}
  
  Now from the linear program duality, we know
  
  \begin{equation*}
    P^* = Q^* = 1 - \left( \frac{m-m_0-1}{m-m_0} \right)^{m-m_0}
  \end{equation*}"
%}

<br/>

The linear program described above will be used in the next section to come up with the bound for the greedy solution.

## APPROXIMATION GUARANTEE OF THE GREEDY HEURISTIC

{%
  include theorem.html
  title="Approximation Guarantee of the Greedy Heuristic Applied to Submodular Set Function Maximization"
  text="Let \( h : \mathcal{U}(\mathcal{D}_C) \mapsto \mathbb{R} \) be a submodular increasing set function.
  Then using the greedy heuristic to find an approximate solution, \( \mathcal{D}^G_C \), to,
  
  \begin{equation*}
    \begin{aligned}
      \min\limits_{\mathcal{D} \in \mathcal{U}(\mathcal{D}_C)} && &h(\mathcal{D})\\
      \text{s.t.} && &|\mathcal{D}| = m
    \end{aligned}
  \end{equation*}
  
  where \( |\mathcal{D}_C| = m_0 \) and \( m > m_0 \) and \( \mathcal{D}^*_C \) is an optimal solution satisfies the inequality,
  
  \begin{equation}
    \frac{h(\mathcal{D}^*_C) - h(\mathcal{D}^G_C)}{h(\mathcal{D}^*_C) - h(\mathcal{D}_C)} \leq \left( \frac{m-m_0-1}{m-m_0} \right)^{m-m_0}
  \end{equation}"
  proof="Consider the linear program described above with the variable substitution,
  
  \begin{equation}
    x_i = \frac{h_{i}}{h(\mathcal{D}^*_C) - h(\mathcal{D}_C)}
    \label{eq:sub}
  \end{equation}
  
  By Lemma NUMBER HERE the system of inequalities the greedy heuristic satisfies appears as the constraints in the linear program described in Lemma NUMBER HERE.
  
  \begin{equation*}
    \begin{aligned}
      \min && &P = \textbf{1}^T \textbf{x}\\
      \text{s.t.} && &T(1,m-m_0) \textbf{x} \geq \textbf{1}
    \end{aligned}
  \end{equation*}
  
  The objective is lower bounded by \( P^* \),
  
  \begin{equation*}
  \begin{aligned}
    P &= \sum_{i=1}^{m-m_0} x_i\\
    &= \frac{ \sum_{i=m_0}^{m-1} h_i}{h(\mathcal{D}^*_C) - h(\mathcal{D}_C)}\\
    &= \frac{h(\mathcal{D}^G_C) - h(\mathcal{D}_C)}{h(\mathcal{D}^*_C) - h(\mathcal{D}_C)}\\
    &\geq P^*\\
    &= 1 - \left( \frac{m-m_0-1}{m-m_0} \right)^{m-m_0}
  \end{aligned}
  \end{equation*}
  
  Multiplying through by negative one and adding one to both sides,
  
  \begin{equation*}
    \frac{h(\mathcal{D}^*) - h(\mathcal{D}^G)}{h(\mathcal{D}^*) - h(\mathcal{D}_C)} \leq \left( \frac{m-m_0-1}{m-m_0} \right)^{m-m_0}
  \end{equation*}"
%}

<br/>

Rearranging this inequality gives us a lower bound and an upper bound for the objective associated with the greedy heuristic.

\begin{equation}
  h(\mathcal{D}_C^\*) \geq h(\mathcal{D}_C^G) \geq h(\mathcal{D}_C^\*) -\left( \frac{m-m_0-1}{m-m_0} \right)^{m-m_0} \left( h(\mathcal{D}^\*_C) - h(\mathcal{D}_C) \right)
  \label{eq:bound2}
\end{equation}

At first glance, the result in Eq. \eqref{eq:bound2} appears like quite a useful result.
We know that if we use the greedy heuristic, the approximate solution returned will be no worse than the lower bound in Eq. \eqref{eq:bound2}.
On the other hand though, if $$ h $$ is a rapidly increasing function so $$ h(\mathcal{D}_C^*) - h(\mathcal{D}_C) \gg 0 $$ and the lower bound decreases below 

\begin{equation\*}
  \begin{array}{cl}
  \min\limits_{\mathcal{D} \in \mathcal{U}(\mathcal{D}_C)} & h(\mathcal{D})\newline
  \text{s.t.} & |\mathcal{D}| = m
\end{equation\*}

so the lower bound does not exclude any solution.
