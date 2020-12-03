---
title: 'Minimum Energy Control of 1D Lattices'
date: 2020-09-12
permalink: /posts/2020/09/control-of-1d-lattices/
tags:
  - lattices
  - minimum energy control
---

## 1D Lattice

This post presents a general framework to address optimal control problems which I found very useful when determining the potential for deriving an analytic solution.
The notation is (mostly) what appears in I. Michael Ross's book "A Primer on Pontryagin's Principle in Optimal Control".
After presenting the components of the problem, we use Pontryagin's principle to derive a set of necessary conditions for an optimal solution.
In some special cases, Pontryagin's method is also sufficient and so there is a single global optimal solution.

In the formulation presented here, we assume the system can be expressed as \$ n_x \$ ordinary differential equations (ODEs).
Let the independent parameter, usually time \$ t \$, exist only in the interval \$ \mathcal{I} = [t_0,t_f] \$, where \$ t_0 \$ and \$ t_f \$ may be free variables.
There are also \$ n_u \$ control inputs which we, as the engineer, are free to design (up to some constraints which will be covered).
The vector of states, \$ x(t) : \mathcal{I} \mapsto \mathbb{R}^{n_x} \$, evolves according to \$ dx/dt = f(x(t), u(t), t) \$ where we assume that the functions \$ f : \mathbb{R}^{n_x} \times \mathbb{R}^{n_u}, \mathbb{R} \mapsto \mathbb{R}^{n_x} \$ are assumed to be at least once differentiable in each of its inputs.

Along with the set of ODEs, there may (in fact must) be some _boundary conditions_ at either or both ends of the interval.
In general, these can be expressed as inequalities \$ e^L \leq e(x(t_0), x(t_f), t_0, t_f) \leq e^U \$ where the function \$ e : \mathbb{R}^{n_x} \times \mathbb{R}^{n_x} \times \mathbb{R} \times \mathbb{R} \mapsto \mathbb{R}^{n_e} \$ is again assumed to be at least once differentiable in each of its inputs.

Beyond the boundary conditions, there may also be constraints on the state variables or control inputs themselves.
These _path constraints_ can be expressed as \$ h^L \leq h ( x(t), u(t), t) \leq h^U \leq h^U \$ where the function \$ h : \mathbb{R}^{n_x} \times \mathbb{R}^{n_u} \times \mathbb{R} \mapsto \mathbb{R}^{n_h} \$ is assumed to be at least once differentiable in each of its inputs.

Finally, the last component is some _cost_ assigned to any choice of the control input (and by association the result values of the states) which comes in two parts; a _boundary cost_ and a _running cost_.
The boundary cost is denoted \$ E(x(t_0), x(t_f), t_0, t_f)) \$ which assigns a cost to the values of the initial and final states and initial and final times.
The running cost is denoted \$ F(x(t), u(t), t) \$ which assigns a cost to each value of the state, the control input and time.
These two functions are again assumed to be at least once differentiable in each of their inputs.

Combining all of these elements into the standard optimal control problem which can be addressed using Pontryagin's principle leads to the following equation:

$$
\begin{aligned}
  \min && &J = E({x}(t_0), {x}(t_f), t_0, t_f) + \int_{t_0}^{t_f} F({x}(t), {u}(t), t)dt\\
  \text{s.t.} && &\dot{x}(t) = f(x(t), u(t), t)\\
  && &h^L \leq h(x(t), u(t), t) \leq h^U\\
  && &e^L \leq e(x(t_0), x(t_f), t_0, t_f) \leq e^U
\end{aligned}
$$

## The Necessary Conditions

To write the necessary conditions we introduce _multipliers_ for each of the *constraints* that appears;
1) For each state variable \$ x_j(t) \$ we introduce a co-state \$ \lambda_j(t) \$ for \$ j = 1,2,\ldots,n_x \$.
2) For each path constraint \$ h_j(x(t),u(t),t) \$, there is a co-path variable \$ \mu_j(t) \$ for \$ j = 1,2,\ldots,n_h \$.
3) For each boundary condition (sometimes referred to as an _event_) \$ e_j (x(t_0),x(t_f),t_0,t_f) \$, there is a co-event \$ \nu_j \$ for \$ j = 1,2,\ldots,n_e \$.


