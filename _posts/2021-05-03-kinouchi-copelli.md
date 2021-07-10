---
title: 'Excitable Networks: The Kinouchi-Copelli Model'
date: 2021-05-03
permalink: /posts/2021/05/kinouchi-copelli/
header:
  teaser: Teaser
excerpt: An example of a phase transition in a network model which can be analyzed using the mean-field equation of the Kinouchi-Copelli model.
tags:
  - network models
  - dynamic range
  - networks
  - dynamics
---

## OUTLINE

This post covers the results in 

> Kinouchi, Osame, and Mauro Copelli. "Optimal dynamical range of excitable networks at criticality." Nature physics 2.5 (2006): 348-351.

which presents a simple model on a network with a phase transition. The model proposed is an attempt to explain how communicating and excitable neurons can differentiate between inputs of different magnitudes.

{%
  include image.html
  file="/images/kc_sigma_1.00.gif"
  caption="Exciting a Network with an External Stimulus"
%}
## THE KINOUCHI-COPELLI MODEL

### General Description
The model begins with an undirected graph with $$ N $$ neurons (nodes). 
Let $$ \mathcal{N}_i $$ be the set of neighbors of node $$ i $$.
Let $$ \langle \kappa \rangle $$ be the average degree of the graph.
Each edge is assigned a weight uniformly drawn from the distribution $$ p_{i,j} \in [0,p_{\max}] $$.

The <i> strength </i> of a node is its <i> weighted degree </i>

$$
  \sigma_i = \sum_{j \in \mathcal{N}_i} p_{i,j}
$$

The average strength of a node is then the average weight times the average degree, $$ \langle \sigma \rangle = \frac{p_{\max}}{2} \langle \kappa \rangle $$.

With the graph defined, assign an integer value $$ s_i[k] \in [0,1,\ldots,n-1] $$ at each unit of time $$ k = 0,1,2,\ldots $$

<ul>
  <li> If \( s_i[k] = 0 \) then neuron \( i \) is <i> resting </i> </li> 
  <li> If \( s_i[k] = 1 \) then neuron \( i \) is <i> excited </i>. </li>
  <li> If \( s_i[k] = 2,3,\ldots,n-1 \) then neuron \( i \) is in a <i> refractory state </i>. </li>
</ul>

The neuron's states are updated according to the following rules:
<ol>
  <li> If neuron \( i \) is resting at time \( k \), \( s_i[k] = 0 \), then it may become excited at time \( k+1 \) either 
  <ol>
    <li> by an external stimulus with probability \( \lambda(r) = 1- e^{-r} \) for external stimulus rate \( r \geq 0 \) or </li>
    <li> by one of its excited neighbors \( j \in \mathcal{N}_i \) such that \( s_j[k] = 1 \) with probability \( p_{i,j} \). </li>
  </ol> 
  Otherwise it remains resting. </li>
  <li> If a neuron is excited or in a refractory state, then \( s_i[k+1] = (s_i[k]+1) \bmod n \) </li>
</ol>

In the following simulations:
<ul>
  <li> all graphs are Erdos-Renyi graphs </li>
  <li> an initial 10 percent of nodes are excited </li>
  <li> the average branching ratio is chosen, \( \langle \sigma \rangle \), and \( p_{\max} \) is determined from it. </li>
</ul>

The <i> instantaneous activity </i> is defined as the fraction of nodes that are excited at time $$ k $$,

\begin{equation}
  \rho[k] = \sum_{i=1}^N \delta (s_i[k],1)
\end{equation}

The average activity over $$ T $$ time steps is the average of the instaneous activities.

\begin{equation}
  F[T] = \frac{1}{T} \sum_{k=1}^T \rho[k]
\end{equation}

With the Kinouchi-Copelli model fully defined, let's start running simulations to examine the behavior.

The following GIFs are generated using a simulation with the following parameters:
<ul>
  <li> \( N = 200 \) nodes </li>
  <li> \( n = 5 \) states </li>
  <li> average degree \( \langle \kappa \rangle = 5 \) </li>
  <li> external stimulus rate \( r = 0.003 \) </li>
  <li> the simulations are run for \( T = 500 \) time steps. </li>
</ul>

Three average branching ratios are chosen to show the different regimes, $$ \langle \sigma \rangle = 0.5 $$, $$ \langle \sigma \rangle = 1 $$, and $$ \langle \sigma \rangle = 1.5 $$.

{%
  include image.html
  file="/images/kc_sigma_0.50.gif"
  caption="\( \langle \sigma \rangle = 0.5 \)"
%}

For $$ \langle \sigma \rangle < 1 $$, nodes excited by external stimulus may excite a few neighbors but we do not see any long cascading waves of excited nodes.

{%
  include image.html
  file="/images/kc_sigma_1.50.gif"
  caption="\( \langle \sigma \rangle = 1.5 \)"
%}

For $$ \langle \sigma \rangle > 1 $$, nodes excited by an external stimulus lead to long cascades of excitement and a significant fraction of the nodes are always excited.

{%
  include image.html
  file="/images/kc_sigma_1.00.gif"
  caption="\( \langle \sigma \rangle = 1.0 \)"
%}

At the intermediate value $$ \langle \sigma \rangle = 1 $$, some external stimuli excitements die out quickly while others can lead to long cascades.

This phenomena can be clearly seen when plotting $$ \rho[k] $$ for many different graphs using values of $$ \langle \sigma \rangle $$ close to one.
In the figure below, simulations were run using the following parameters:

<ul>
  <li> \( N = 100,000 \) nodes </li>
  <li> \( n = 10 \) states </li>
  <li> average degree \( \langle \kappa \rangle = 10 \) </li>
  <li> there are no external stimuli, \( r = 0 \) </li>
  <li> initially \( 10 \% \) of nodes are excited </li>
</ul>

With a lack of external stimuli, it is possible for the network to reach a quiescent state (no excited nodes).

{%
  include image.html
  file="/images/kc_rho.png"
  caption="Instantaneous activity of the Kinouchi-Copelli model without external stimuli for sub-critical  \( \langle \sigma \rangle = 0.95 \), super-critical \( \langle \sigma \rangle = 1.05 \), and critical \( \langle \sigma \rangle = 1 \) average branching ratio. For each value of \( \langle \sigma \rangle \), the model is run on 50 different networks."
%}

The black curves are when $$ \langle \sigma \rangle = 0.95 $$ and the network reaches a quiescent curve quickly while the blue curves are when $$ \langle \sigma \rangle = 1.05 $$ and the networks remain perpetually excited.
At the critical value, the excitement dies down eventually but over a large range of times.

## DYNAMIC RANGE

From the GIFs shown previously, while the instantaneous activity is stochastic, the average activity $$ F[T] $$ seems to approach a constant value.
Computing this value in the presence of different external stimuli $$ r $$ for different values of average branching ratio $$ \langle \sigma \rangle $$ shows two different regimes.

## MEAN FIELD MODEL

A mean field model, in its most simple form, replaces local quantities (node degree, node strength) in a model with their averages.
In the KC model, the nodes' degrees, $$ \kappa_i $$, is replaced with the average degree $$ \langle \kappa \rangle $$, and the local edge weights are replaced by their average, $$ \langle p \rangle = \frac{p_{\max}}{2} = \frac{\langle \sigma \rangle}{\langle \kappa \rangle} $$.

The following probabilities will play a role in the mean field equation.

<ul>
  <li> The fraction of excited nodes at time \( k \) is denoted \( \rho[k] \). </li>
  <li> The fraction of nodes in the resting phase at time \( k \) is all of the nodes minus those which were excited in the previous \( n-1 \) time steps.
  
  \begin{equation}
    P_k(0) = 1 - \sum_{\ell = k-n+1}^{k} \rho[\ell]
  \end{equation}
  
  </li>
  <li> The fraction of nodes that will be excited in the next time step is one minus the probability that none of a nodes' \( \langle \kappa \rangle \) excite the node with probability \( \frac{\langle \sigma \rangle}{\langle \kappa \rangle} \).
  
  \begin{equation}
    p[k] = 1 - \left( 1 - \frac{\langle \sigma \rangle}{\langle \kappa \rangle} \rho[k] \right)^{\langle \kappa \rangle}
  \end{equation}
  </li>
  <li> The probability a resting node is excited by the extermal stimulus is \( \lambda (r) \). </li>
</ul>

With the above probabilities, combining them yield the mean field model's update equation.

\begin{equation}
  \begin{aligned}
  \rho[k+1] &= P_k(0) \lambda(r) + P_k(0) (1-\lambda(r)) p[k]\newline
  &= \left( 1 - \sum_{\ell=k-n+1}^{k} \rho[\ell] \right) \left( \lambda(r) + (1-\lambda(r)) \left( 1 - \left( 1 - \frac{\langle \sigma \rangle}{\langle \kappa \rangle} \rho[k] \right)^{\langle \kappa \rangle} \right) \right)\newline
  &= \left( 1 - \sum_{\ell=k-n+1}^{k} \rho[\ell] \right) \left( 1 - (1-\lambda(r)) \left( 1 - \frac{\langle \sigma \rangle}{\langle \kappa \rangle} \rho[k] \right)^{\langle \kappa \rangle} \right)
  \end{aligned}
\end{equation}

In the first line, the first term is the probability a resting node is excited by the external stimulus while the second term is the probability the external stimulus does not excite the node but a neighbor does.

In the mean field hypothesis we assume that after enough time goes by, the instaneous activity in the average approaches a constant,

$$

F[T] = \frac{1}{T} \sum_{k=1}^T \rho[k] \rightarrow F

$$ 

which yields the algebraic expression,

\begin{equation}
  F = (1 - (n-1) F) \left( 1 - (1 - \lambda(r) ) \left(1 -  \frac{\langle \sigma \rangle}{\langle \kappa \rangle} F \right)^{\langle \kappa \rangle} \right)
  \label{eq:equilibrium_density}
\end{equation}

If we can solve Eq. \eqref{eq:equilibrium_density} for $$ F $$, we would know the equilibrium density.

### BISECTION METHOD

The bisection method is a particulary effective method to solve the problem,

\begin{equation}
  f(x) = 0, \quad x \in [a,b]
\end{equation}

when $$ f(x) $$ is <i> monotonic </i> on the domain $$ x \in [a,b] $$.

To determine if we can use the bisection method to solve Eq. \eqref{eq:equilibrium_density} one must

<ol>
  <li> determine appropriate bounds \( a \) and \( b \) </li>
  <li> evaluate \( f(a) \) and \( f(b) \) and show they have opposite signs (or possible one may be zero) </li>
  <li> show that \( \frac{df}{dx} \) is either strictly positive or negative on the interior \( (a,b) \) of the interval. </li>
</ol>

Once the above have been proven

