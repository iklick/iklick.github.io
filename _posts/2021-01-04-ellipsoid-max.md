---
title: 'Optimal Driver Node Selection (Part 1): Ellipsoid Volume Maximization'
date: 2021-01-04
permalink: /posts/2021/01/ellipsoid-max/
tags:
  - optimization
  - driver nodes
  - set functions
  - heuristics
---

## OUTLINE

This post presents the connection between the optimal driver node selection problem and the ellipsoid volume maximization problem.
Small problems are investigated which can be solved using an exhaustive search over all possible sets of driver nodes.

1. Solution of the Minimum Energy Optimal Control Problem and the Controllability Gramian
2. Connection to Ellipsoids and Showing its Volume is Submodular
3. Discussion of the Applicability of the Greedy Algorithm

## MINIMUM ENERGY OPTIMAL CONTROL

### PROBLEM DESCRIPTION

Let $$ \mathcal{G} = (\mathcal{V},\mathcal{E}) $$ be a graph with $$ |\mathcal{V}| = n $$ nodes and set of directed edges $$ \mathcal{E} \subset \mathcal{V} \times \mathcal{V} $$.
An edge $$ (i,j) \in \mathcal{E} $$ can be read as 'the edge from node $$ i $$ to node $$ j $$.
Let the set of incoming neighbors of node $$ i $$ be denoted $$ \mathcal{N}_i $$ defined to be,

\begin{equation}
  \mathcal{N}_i = \left\{ j \in \mathcal{V} | (j,i) \in \mathcal{E} \right\}
\end{equation}

In addition to the edges in the graph, there is a <i>loop</i> at each node, which can be thought of as an edge $$ (i,i) $$ (but they will be treated separately from the other edges).

Each edge is assigned a <i>weight</i> which can be thought of as a function $$ w : \mathcal{E} \mapsto \mathbb{R} $$.
For notational brevity, denote the weight $$ w(j,i) = w_{i,j} $$ (the switch in index will be made clear).
Similarly, the loop weights can be thought of as a function $$ p : \mathcal{V} \mapsto \mathbb{R} $$, or for brevity, $$ p(i) = p_i $$.

To each node, assign a time varying value (or state), $$ x_i(t) $$, $$ i = 1,2,\ldots,n $$.
The states are updated based on their neighbors.

\begin{equation}
  \frac{d}{dt} x_i(t) = p x_i(t) + \sum_{j \in \mathcal{N}_i} w_{i,j} x_j(t)
\end{equation}

### LINEAR SYSTEMS

This post focuses on systems that can be described by $$ n $$ linear differential equations over the interval $$ t \in [0,t_f] $$.

\begin{equation}
  \frac{d}{dt} \textbf{x}(t) = A \textbf{x}(t) + B \textbf{u}(t), \quad \textbf{x}(0) = \textbf{0}_n
  \label{eq:linear}
\end{equation}

<ol>
  <li> The state of the system \( \textbf{x} : [0,t_f] \mapsto \mathbb{R}^n \)  </li>
  <li> The state matrix \( A \in \mathbb{R}^{n \times n} \) describes the dependence of the states on each other </li>
  <li> The input matrix \( B \in \mathbb{R}^{n \times m} \) describes how the inputs affect each state </li>
  <li> The control input \( \textbf{u} : [0,t_f] \mapsto \mathbb{R}^m \) is designed to affect the states.</li>
</ol>

The goal is to find a control input $$ \textbf{u} $$ to drive the state of the system from the initial state $$ \textbf{x}(0) = \textbf{0}_n $$ to some desired final state $$ \textbf{x}(t_f) = \textbf{x}_f \in \mathbb{R}^n $$.

<br/>

{%
  include definition.html
  title="Controllability"
  text="A pair \((A,B)\) is said to be controllable if for all \( t_f > 0 \) and \( \textbf{x}_f \in \mathbb{R}^n \) there exists an input \( \textbf{u} : [0,t_f] \mapsto \mathbb{R}^m \) such that \( \textbf{x}(t_f) = \textbf{x}_f \) subject to the linear dynamics
  
  \begin{equation*}
    \frac{d}{dt} \textbf{x}(t) = A \textbf{x}(t) + B \textbf{u}(t), \quad \textbf{x}(0) = \textbf{0}_n
  \end{equation*}"
%}

<br/>

If the linear system in Eq. \eqref{eq:linear} is controllable, then any control input $$ \textbf{u} : [0,t_f] \mapsto \mathbb{R}^m $$ that drives the system to $$ \textbf{x}(t_f) = \textbf{x}_f $$ is not unique.
This non-uniqueness is quite easy to demonstrate.

The formal solution of the linear system in Eq. \eqref{eq:linear} is,

\begin{equation\*}
  \textbf{x}(t) = \int_0^t e^{A(t-\tau)} B \textbf{u}(\tau) d\tau
\end{equation\*}

For a function $$ R : [0,t_f] \mapsto \mathbb{R}^{m \times n} $$, define the control input to be,

\begin{equation\*}
  \textbf{u}(t) = R(t) \left[ \int_0^{t_f} e^{A(t_f-\tau)} B R(\tau) d\tau \right]^{-1} \textbf{x}_f
\end{equation\*}

with the requirement that the matrix,

\begin{equation\*}
  \int_0^{t_f} e^{A(t_f-\tau)} B R(\tau) d\tau
\end{equation\*}

is invertible.

<br/>

{%
  include definition.html
  title="Control Energy"
  text="The control energy of a control input is its \(\mathcal{L}_2\) norm,
  
  \begin{equation}
    E(\textbf{u}) = \int_0^{t_f} || \textbf{u}(t) ||_2^2 dt
    \label{eq:energy_def}
  \end{equation}"
%}

<br/>

The minimum energy control input provides a lower bound for the control energy for all other control inputs that satisfy the final state condition.

$$
  \begin{aligned}
    \min && &E = \int_0^{t_f} || \textbf{u}(t)||_2^2 dt\\
    \text{s.t.} && &\frac{d}{dt} \textbf{x}(t) = A \textbf{x}(t) + B \textbf{u}(t)\\
    && &\textbf{x}(0) = \textbf{0}_n, \quad \textbf{x}(t_f) = \textbf{x}_f
  \end{aligned}
$$

The solution of this optimal control input is well-known,

\begin{equation}
  \textbf{u}^*(t) = B^T e^{A^T (t_f-t)} W^{-1}(t_f) \textbf{x}_f
  \label{eq:ustar}
\end{equation}

where the matrix $$ W(t) $$ is the <i>controllability Gramian</i>.

<br/>

{%
  include definition.html
  title="Controllability Gramian"
  text="The controllability Gramian \( W : [0,t_f] \mapsto \mathbb{R}^{n \times n} \) is a matrix function that is the solution to the differential Lyapunov equation,
  
  \begin{equation*}
    \frac{d}{dt} W(t) = A W(t) + W(t) A^T + BB^T, \quad W(0) = O_{n,n}
  \end{equation*}
  
  and its formal solution is,
  
  \begin{equation}
    W(t) = \int_0^t e^{A\tau} BB^T e^{A^T \tau} d\tau
  \end{equation}"
%}

<br/>

For future reference, positive definite (PD) and positive semi-definite (PSD) matrices are formally defined.

<br/>

{%
  include definition.html
  title="Positive Definite (PD) and Positive Semi-Definite (PSD) Matrices"
  text="A symmetric square \( n \)-by-\( n \) matrix \( W \) is positive semi-definite (PSD) if,
  
  \begin{equation*}
    \textbf{x}^T W \textbf{x} \geq 0, \quad \forall \textbf{x} \in \mathbb{R}^n
  \end{equation*}
  
  A symmetric square \( n \)-by-\( n \) matrix \( W \) is positive definite (PD) is,
  
  \begin{equation*}
    \textbf{x}^T W \textbf{x} > 0, \quad \forall \textbf{x} \in \mathbb{R}^n \backslash \{ \textbf{0}_n \}
  \end{equation*}
  
  If a matrix \( W \) is PD then it is also PSD.
  "
%}

<br/>



<br/>

{%
  include theorem.html
  title="Positive Definiteness of the Controllability Gramian"
  text="The controllability Gramian \( W(t) \) is PSD. The controllability Gramian is PD if and only if the pair \( (A,B) \) is controllable."
  proof="For the first statement, simply use the formal solution of the controllability Gramian and any vector \( \textbf{v} \in \mathbb{R}^n \).
  
  \begin{equation*}
    \textbf{v}^T W(t) \textbf{v} = \int_0^t \textbf{v}^T e^{A t} B B^T e^{A^T t} \textbf{v} dt = \int_0^t || B^T e^{A^T t} \textbf{v}||_2^2 dt \geq 0
  \end{equation*}
  
  For the second statement the proof is broken into parts:
  <ol>
    <li>The case 'if \( W(t_f) \) is PD then the pair \( (A,B) \) is controllable'.
  Assume \( W(t) \) is positive definite, then it is invertible, and we can define the control input 
  \begin{equation*} 
    \textbf{u}(t) = B^T e^{A^T (t_f-t)} W^{-1}(t_f) \textbf{x}_f 
  \end{equation*}
  for any \( t_f > 0 \) and \( \textbf{x}_f \in \mathbb{R}^n \) so the linear system is controllable.</li>
  <li> The case 'if the pair \( (A,B) \) is controllable then \( W(t_f) \) is positive definite'.
  Assume \( W(t_f) \) is positive semi-definite but not positive definite.
  Then there exists a vector \( \textbf{v} \neq \textbf{0}_n \) such that \( \textbf{v}^T W(t_f) \textbf{v} = 0 \).
  
  \begin{equation*}
    \textbf{v}^T W(t_f) \textbf{v} = \int_0^{t_f} \textbf{v}^T e^{A \tau} BB^T e^{A^T \tau} \textbf{v} d\tau = \int_0^{t_f} || B^T e^{A^T \tau} \textbf{v} ||_2^2 d\tau = 0
  \end{equation*}
  
  This can only be true if \( B^T e^{A^T \tau} \textbf{v} = \textbf{0}_m \) for all \( \tau \in [0,t_f] \). 
  
  If the pair \( (A,B) \) is controllable, then there exists a control input \( \textbf{u} : [0,t_f] \mapsto \mathbb{R}^m \) such that,
  
  \begin{equation*}
    \textbf{v} = \int_0^{t_f} e^{A (t-\tau)} B \textbf{u}(\tau) d\tau
  \end{equation*}
  
  Pre-multiply this equation by \( \textbf{v}^T \) 
  
  \begin{equation*}
    \textbf{v}^T \textbf{v} = \int_0^{t_f} \textbf{v}^T e^{A(t-\tau)} B \textbf{u}(\tau) d\tau = \int_0^{t_f} \textbf{0}_m^T \textbf{u}(\tau) d\tau = 0
  \end{equation*}
  
  This is a contradiction so \( W(t_f) \) must be positive definite.</li>
  </ol>"
%}

<br/>

Returning to the minimum energy control problem, plugging Eq. \eqref{eq:ustar} into Eq. \eqref{eq:energy_def} the minimum energy is,

\begin{equation}
  E^{\min} = \int_0^{t_f} || \textbf{u}^*(t) ||_2^2 dt = \textbf{x}_f^T W^{-1}(t_f) \textbf{x}_f
  \label{eq:energy}
\end{equation}

Now note that we may hold the control energy $$ E^{\min} $$ and time $$ t_f > 0 $$ constant and investigate all final states $$ \textbf{x}_f \in \mathbb{R}^n $$ that requires the same amount of control energy to reach.

The set of all final states that can be reached with $$ E $$ units of energy (or less) in $$ t_f $$ units of time is then equal to the,

\begin{equation}
  \mathcal{E}(t_f,E) = \left\\{ \textbf{x}_f \in \mathbb{R}^n | \textbf{x}_f^T W^{-1}(t_f) \textbf{x}_f \leq E \right\\}
  \label{eq:ellipsoid}
\end{equation}

The set $$ \mathcal{E} (t_f,E) $$ is an $$ n $$-dimensional ellipsoid.

{%
  include definition.html
  title="Ellipsoid"
  text="Let \( W \) be a \(n \)-by-\( n\) symmetric PD matrix.
  The set
  
  \begin{equation*}
    \mathcal{E} = \left\{ \textbf{x} \in \mathbb{R}^n | \textbf{x}^T W^{-1} \textbf{x} \leq E \right\}
  \end{equation*}
  
  is an ellipsoid with principle axes aligned along each eigenvector \( \textbf{v}_k \) of \( W \), \( k = 1,2,\ldots,n \) and semi-axis lengths \( \sqrt{E \mu_k} \) where \( \mu_k \) is an eigenvalue of \( W \)."
%}

<br/>

The definition used here is equivalent to the more familiar definition in three dimensions,

\begin{equation\*}
  \frac{x^2}{a_1} + \frac{y^2}{a_2} + \frac{z^2}{a_3} \leq 1
\end{equation\*}

Create the digaonal matrix $$ W = \left[ \begin{array}{ccc} a_1 & 0 & 0 \\ 0 & a_2 & 0 \\ 0 & 0 & a_3 \end{array} \right] $$ and the vector $$ \textbf{x} = \left[ \begin{array}{c} x \\y \\ z \end{array} \right] $$, then the expression above can be written as,

\begin{equation\*}
  \textbf{x}^T W^{-1} \textbf{x} \leq 1
\end{equation\*}

<br/>

{%
  include lemma.html
  title="Volume of an Ellipsoid"
  text="The volume of an ellipsoid \( \mathcal{E} \) defined by the symmetric positive definite matrix \( W \) and characteristic size \( E \) is
  
  \begin{equation}
    \text{Vol}(\mathcal{E}) = \text{Vol}(\mathcal{B}_n) \sqrt{E^n \det W}
    \label{eq:volume}
  \end{equation}
  
  where \( \mathcal{B}_n \) is the unit \(n\)-ball."
  proof="The proof shows that a simple linear transformation maps the \( n \)-dimensional ellipsoid to the \( n \)-dimensional unit sphere.
  
  Consider the \( n \)-dimensional integral over some region \( \mathcal{R} \).
  
  \begin{equation*}
    \int_{\mathcal{R}} f(\textbf{x}) d\textbf{x}
  \end{equation*}
  
  If we instead want to integrate using a linearly transformed coordinate system, \( \textbf{z} = J \textbf{x} \), define the transformed region \( \mathcal{R}' = \{ J \textbf{x} | \textbf{x} \in \mathcal{R}\} \) then,
  
  \begin{equation*}
    \int_{\mathcal{R}'} f(\textbf{x}) d\textbf{x} = \int_{\mathcal{R}'} f(J^{-1} \textbf{z}) \det (J) d\textbf{z}
  \end{equation*}
  
  Let \( V \) be the orthogonal matrix of eigenvectors and \( \mathcal{M} \) be the diagonal matrix of positive eigenvalues of \( W \).
  
  Then define the transformation \( \textbf{z} = \frac{1}{\sqrt{E}} \mathcal{M}^{-1/2} V^T \textbf{x} \) so that,
  
  $$
    \begin{aligned}
    \frac{1}{E} \textbf{x}^T W^{-1} \textbf{x} &= \frac{1}{E} \left( \sqrt{E} \textbf{z}^T \mathcal{M}^{1/2} V^T \right) W^{-1} \left( \sqrt{E} V \mathcal{M}^{1/2} \textbf{x} \right)\\
    &= \textbf{z}^T \mathcal{M}^{1/2} \mathcal{M}^{-1} \mathcal{M}^{1/2} \textbf{z}\\
    &= \textbf{z}^T \textbf{z} \leq 1
    \end{aligned}
  $$
  
  which is simply the unit \( n \)-ball.
  The volume of the \( n \)-dimensional ellipsoid can then be written in terms of the volume of the \( n \)-ball.
  
  \begin{equation*}
    \int_{\frac{1}{E} \textbf{x}^T W^{-1} \textbf{x} \leq 1} d\textbf{x} = \int_{\textbf{z}^T \textbf{z} \leq 1} \det \left( \sqrt{E} V \mathcal{M}^{1/2} \right) d\textbf{z} = \text{Vol}(\mathcal{B}_n) E^{n/2} \det (V) \det (\mathcal{M}^{1/2})
  \end{equation*}
  
  As \( V \) is an orthogonal matrix, its determinant is \( \pm 1 \), but as this is a volume, we can take \( \det (V) = 1 \).
  The determinant of \( \mathcal{M}^{1/2} \) is equal to the determinant of \( W^{1/2} \).
  Thus,
  
  \begin{equation*}
    \text{Vol}(\mathcal{E}) = \text{Vol}(\mathcal{B}_n) E^{n/2} \sqrt{\det (W)}
  \end{equation*}
  
  which completes the proof."
%}

<br/>

The volume of the ellipsoid $$ \mathcal{E}(t_f,E) $$ can be thought of as the amount of possible final states reachable for a system given $$ t_f $$ units of time and $$ E $$ units of energy holding $$ A $$ and $$ B $$ constant.

<br/>

{%
  include image.html
  file="/images/dns_comp_3d.png"
  caption="A small example of the control ellipsoid. (A) A system diagram where \( n = 3 \) and \( m = 2 \). The state matrix \( A \) and input matrix \( B \) are shown below the diagram. (B) The control ellipsoid for \( E = 1 \) with the principal axes labeled \(v_1,v_2,v_3\) corresponding to the eigenvectors of \( W \) and the axes corresponding to the system state are labeled \( x,y,z\). The colored lines are the minimum energy state trajectory for six different choices of final state \( \textbf{x}_f \)."
%}

<br/>

In the next section, I instead hold $$ t_f $$ and $$ E $$ constant and let $$ B $$ vary.

## Driver Node Selection Problem

Let $$ \mathcal{V} = \{1,2\ldots,n\} $$ be the set of the first $$ n $$ integers.
Define $$ \textbf{e}_k $$ to be the $$ k $$'th elementary vector, that is, a vector of all zeros except in the $$ k $$'th location which is equal to one.

If $$ n = 4$$, the four elementary vectors are,

$$
  \textbf{e}_1 = \left[ \begin{array}{c} 1 \\ 0 \\ 0 \\ 0 \end{array} \right], \quad
  \textbf{e}_2 = \left[ \begin{array}{c} 0 \\ 1 \\ 0 \\ 0 \end{array} \right], \quad
  \textbf{e}_3 = \left[ \begin{array}{c} 0 \\ 0 \\ 1 \\ 0 \end{array} \right], \quad
  \textbf{e}_4 = \left[ \begin{array}{c} 0 \\ 0 \\ 0 \\ 1 \end{array} \right]
$$

Let $$ \mathcal{D} \subseteq \mathcal{V} $$ and construct the matrix $$ B(\mathcal{D}) $$ as having columns corresponding to each element in $$ \mathcal{D} $$.

If $$ n = 4 $$ two possible matrices $$ B $$ are

$$
  B(\{1,3\}) = \left[ \begin{array}{cc}
    1 & 0 \\ 0 & 0 \\ 0 & 1 \\ 0 & 0
  \end{array} \right], \quad B(\{1,2,4\}) = \left[ \begin{array}{ccc}
    1 & 0 & 0\\
    0 & 1 & 0\\
    0 & 0 & 0\\
    0 & 0 & 1
  \end{array} \right]
$$

With this definition of the matrix $$ B(\mathcal{D}) $$, the controllability Gramian can be decomposed into components corresponding to each element $$ j \in \mathcal{D} $$.

{%
  include lemma.html
  title="Controllability Gramian as a Sum"
  text="Let \( W_{\{k\}}(t) \) be the controllability Gramian corresponding to the case \( B = \textbf{e}_k \),
  
  \begin{equation*}
    \frac{d}{dt} W_{\{k\}}(t) = A W_{\{k\}}(t) + W_{\{k \}}(t) A^T + \textbf{e}_k \textbf{e}_k^T, \quad W_{\{k\}}(0) = O_{n,n}
  \end{equation*}
  
  Then the controllability Gramian for the matrix \( B(\mathcal{D}) \) can be written as the summation,
  
  \begin{equation}
    W_{\mathcal{D}}(t) = \sum_{k \in \mathcal{D}} W_{\{k\}}(t)
    \label{eq:Wsum}
  \end{equation}"
  proof="By the definition of \( B(\mathcal{D}) \), the product,
  
  \begin{equation*}
    B^T(\mathcal{D}) B(\mathcal{D}) = \sum_{j \in \mathcal{D}} \sum_{k \in \mathcal{D}} \textbf{e}_j \textbf{e}_k^T = \sum_{k \in \mathcal{D}} \textbf{e}_k \textbf{e}_k^T
  \end{equation*}
  
  which uses the fact \( \textbf{e}_j \textbf{e}_k^T = O_{n,n} \) for \( j \neq k \).
  Then using the formal definition of the controllability Gramian,
  
  $$
    \begin{aligned}
      W_{\mathcal{D}}(t) &= \int_0^t e^{A \tau} B(\mathcal{D}) B^T(\mathcal{D}) e^{A^T \tau} d\tau\\
      &= \int_0^t e^{A\tau} \left[ \sum_{k \in \mathcal{D}} \textbf{e}_k \textbf{e}_k^T \right] e^{A^T \tau} d\tau\\
      &= \sum_{k \in \mathcal{D}} \int_0^t e^{A \tau} \textbf{e}_k \textbf{e}_k^T e^{A^T \tau} d\tau\\
      &= \sum_{k \in \mathcal{D}} W_{\{k\}}(t)
    \end{aligned}
  $$
  
  which completes the proof."
%}

<br/>

I will now fix the time $$ t_f $$ and allowed energy $$ E $$ so that the ellipsoid can be thought solely as a function of $$ \mathcal{D} $$.

<br/>

\begin{equation}
    \max\limits_{\mathcal{D} \subseteq \mathcal{V}} \quad \text{Vol}(\mathcal{E}(\mathcal{D}))
    \label{eq:unconstrained}
\end{equation}

Before exploring this optimization problem further, some properties of PD and PSD matrices are discussed.

### Properties of PD and PSD Matrices

{%
  include lemma.html
  title="Sum of PSD Matrices"
  text="If two \( n \)-by-\( n \) matrices \( W_1 \) and \( W_2 \) are both PSD then their sum is also PSD.
  If two \( n \)-by-\( n \) matrices \( W_1 \) and \( W_2 \) are both PD then their sum is also PD."
  proof="For \( W_1 \) and \( W_2 \) PSD, 
  
  \begin{equation*}
    \textbf{x}^T (W_1 + W_2) \textbf{x} = \textbf{x}^T W_1 \textbf{x} + \textbf{x}^T W_2 \textbf{x} \geq 0, \quad \forall \textbf{x} \in \mathbb{R}^n
  \end{equation*}
  
  For \( W_1 \) and \( W_2 \) PD,
  
  \begin{equation*}
    \textbf{x}^T (W_1 + W_2) \textbf{x} = \textbf{x}^T W_1 \textbf{x} + \textbf{x}^T W_2 \textbf{x} > 0, \quad \forall \textbf{x} \in \mathbb{R}^n \backslash \{ \textbf{0}_n \}
  \end{equation*}"
%}

<br/>

{%
  include lemma.html
  title="Multi-Dimensional Gaussian Integral"
  text="Let \( W \) be an \( n \)-by-\( n \) symmetric PD matrix.
  The \( n \)-dimensional Gaussian integral is,
  
  \begin{equation}
    \int_{\mathbb{R}^n} \exp \left[ -\textbf{x}^T W \textbf{x} \right] d\textbf{x} = \sqrt{\frac{\pi^n}{\det W}}
    \label{eq:mgauss}
  \end{equation}"
  proof="Define the eigenvalue decomposition of \( W \) as
  
  $$
    W =  V \mathcal{M} V^T
  $$
  
  ensuring that \( \det (V) = 1\) and use this replacement in the integral \( \textbf{x} = V^T \textbf{z} \),
  
  $$
    \begin{aligned}
    \int_{\mathbb{R}^n} \exp \left[ - \textbf{x}^T V \mathcal{M} V^T \textbf{x} \right] d\textbf{x} &= \int_{\mathbb{R}^n} \exp \left[ -\textbf{z}^T \mathcal{M} \textbf{z} \right] \det (V) d\textbf{z}\\
    &= \int_{\mathbb{R}^n} \exp \left[ - \sum_{k=1}^n \mu_k z_k^2 \right] d\textbf{z}\\
    &= \int_{\mathbb{R}^n} \prod_{k=1}^n \exp \left[-\mu_k z_k^2 \right] d\textbf{z}\\
    &= \prod_{k=1}^n \left[ \int_{-\infty}^{\infty} e^{-mu_k z_k^2} dz_k \right]
    \end{aligned}
  $$
  
  which is the product of \( n \) Gaussian integrals.
  The Gaussian integral is well known to be,
  
  \begin{equation*}
    \int_{-\infty}^{\infty} e^{-ax^2} dx = \sqrt{\frac{\pi}{a}}
  \end{equation*}
  
  so the product is,
  
  \begin{equation*}
    \int_{\mathbb{R}^n} \exp \left[ -\textbf{x}^T W \textbf{x} \right] d\textbf{x} = \prod_{k=1}^n \sqrt{\frac{\pi}{\mu_k}} = \sqrt{\frac{\pi^n}{\prod_{k=1}^n \mu_k}} = \sqrt{\frac{\pi^n}{\det W}}
  \end{equation*}"
%}

<br/>

{%
  include lemma.html
  title="Determinant Inequality"
  text="If \( W_1 \) and \( W_2 \) are two \( n \)-by-\( n \) symmetric PSD matrices, then
  
  \begin{equation}
    \det (W_1 + W_2) \geq \det (W_1)
    \label{eq:det_order}
  \end{equation}"
  proof="For the first case, assume \( W_1 \) is singular, then \( \det (W_1) = 0 \).
  By Lemma SUM OF PSD IS PSD, \( W_1 + W_2 \) is also PSD, \( \det (W_1 + W_2) \geq 0 \).
  Clearly then \( \det (W_1 + W_2) \geq \det (W_1) = 0 \).
  For the case \( W_1 \) is PD, pick an arbitrary vector \( \textbf{x} \in \mathbb{R}^n \),
  
  \begin{equation*}
    \textbf{x}^T (W_1 + W_2) \textbf{x} = \textbf{x}^T W_1 \textbf{x} + \textbf{x}^T W_2 \textbf{x} \geq \textbf{x}^T W_1 \textbf{x}
  \end{equation*}
  
  Multiply through by negative one and exponentiate both sides.
  
  \begin{equation*}
    \exp \left[ -\textbf{x}^T (W_1 + W_2) \textbf{x} \right] \leq \exp \left[ -\textbf{x}^T W_1 \textbf{x} \right]
  \end{equation*}
  
  Integrate both sides over \( \mathbb{R}^n \)
  
  \begin{equation*}
    \int_{\mathbb{R}^n} \left[ -\textbf{x}^T (W_1 + W_2) \textbf{x} \right] d\textbf{x} \leq \int_{\mathbb{R}^n} \left[ -\textbf{x}^T W_1 \textbf{x} \right] d\textbf{x}
  \end{equation*}
  
  From the multi-dimensional Gaussian integral Lemma, 
  
  \begin{equation*}
    \sqrt{\frac{\pi^n}{\det (W_1+W_2)}} \leq \sqrt{\frac{\pi^n}{\det (W_1)}}
  \end{equation*}
  
  Squaring both sides, taking the reciprocal, and multiplying through by \( \pi^n \) gets the desired inequality.
  
  \begin{equation*}
    \det (W_1 + W_2) \geq \det (W_1)
  \end{equation*}"
%}

<br/>

A consequence of Lemma ORDERING OF DETERMINANT is that the unconstrained ellipsoid maximization problem in Eq. \eqref{eq:unconstrained} has a trivial solution, $$ \mathcal{D}^* = \mathcal{V} $$.

To make this problem more realistic though, we assume that there is some cost associated with adding additional inputs so we constrain the size of $$ \mathcal{D}$$.

\begin{equation}
  \begin{array}{cl}
    \max\limits_{\mathcal{D} \subseteq \mathcal{V}} & \det W_{\mathcal{D}}\newline
    \text{s.t.} & |\mathcal{D}| = m
  \end{array}
\end{equation}

One option to find the optimal solution is to compute $$ W_{\mathcal{D}} $$ for all sets $$ \mathcal{D} $$ of cardinality $$ m $$ and select the one that has maximum determinant.
There are $$ \binom{n}{m} $$ possible sets $$ \mathcal{D} $$, which grows factorially with $$ n $$.
This means performing a brute force search for even moderate values of $$ n $$ is computationally prohibitive.

{%
  include image.html
  file="/images/dns_hist_10.png"
  caption="Example of a brute force solution of the optimal driver node selection. The graph is shown on the left where each edge is assumed to have weight 1 and loops equal to -19. The highlighted nodes are the set \( \mathcal{D} \) that maximizes \( \det W_{\mathcal{D}} \) when \( |\mathcal{D}| = 10 \).
  On the right, a logarithmic histogram of \( \det W_{\mathcal{D}} \) for all sets of cardinality equal to ten."
%}

## A Greedy Algorithm

As a brute force search is infeasible for problems when $$ \binom{n}{m} $$ is too large (a few million starts to tax my laptop) an alternative approach must be taken.

By Lemma ORDERING DETERMINANT, we know that if $$ \mathcal{D}_1 $$ makes $$ (A,B(\mathcal{D}_1)) $$ controllable, then any set $$ \mathcal{D}_2 \supseteq \mathcal{D}_1 $$ also makes $$ (A,B(\mathcal{D}_2)) $$ controllable as well.

Additive greedy algorithms are a popular approach to set function optimization as their implementation is very simple.

{%
  include definition.html
  title="Additive Greedy Algorithm"
  text="Given an initial set \( \mathcal{D}_0 \subset \mathcal{V} \), a function to maximize \( h : 2^{\mathcal{V}} \mapsto \mathbb{R} \), and a desired cardinality \( m > |\mathcal{D}_0| \), then the greedy algorithm takes the following steps,
  <ol>
    <li> Initialize \( \mathcal{D} \gets \mathcal{D}_0 \) </li>
    <li> While \( |\mathcal{D}| < m \) 
    <ol>
      <li> Find \( j^* = \text{arg} \max\limits_{j \in \mathcal{V} \backslash \mathcal{D}} h(\mathcal{D} \cup \{j \}) \) </li>
      <li> Update the set \( \mathcal{D} \gets \mathcal{D} \cup \{j^*\} \)</li>
    </ol></li>
    <li> Return \( \mathcal{D} \) </li>
  </ol>  "
%}

<br/>

This is a heuristic as there is no guarantee that the solution returned by the additive greedy algorithm, $$ \mathcal{D}^G $$, will be the optimal solution, or even a 'good' solution.

In the following post in this series, for a special type of set function called submodular set functions, I will derive a bound on the solution returned by the greedy algorithm of the form,

\begin{equation*}
  h(\mathcal{D}^*) - f(m-m_0) (h(\mathcal{D}^*) - h(\mathcal{D}_C)) \leq h(\mathcal{D}^G) \leq h(\mathcal{D}^*)
\end{equation*}

where $$ |\mathcal{D}_C| = m_0 $$ and $$ \mathcal{D}^* $$ is the optimal set returned by the set function maximization problem,

\begin{equation*}
  \begin{array}{cl}
    \min\limits_{\mathcal{D}_C \subseteq \mathcal{D} \subseteq \mathcal{V}} & h(\mathcal{D})\newline
    \text{s.t.} & |\mathcal{D}| = m
  \end{array}
\end{equation*}

