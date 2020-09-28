---
title: 'A General Framework for Optimal Control'
date: 2020-09-11
permalink: /posts/2020/09/optimal-control-framework/
tags:
  - optimal control
  - pontryagin
---

## The Optimal Control Problem

This post presents a general framework to address optimal control problems which I found very useful when determining the potential for deriving an analytic solution.
The notation is chosen to be similar to what appears in I. Michael Ross's book "A Primer on Pontryagin's Principle in Optimal Control".
After presenting the components of the problem, Pontryagin's principle is used to derive a set of necessary conditions for an optimal solution.
In some special cases, Pontryagin's method is also sufficient and so there is a single global optimal solution.

In the formulation presented here, we assume the system can be expressed as \$ n_x \$ ordinary differential equations (ODEs).
Let the independent parameter, usually time \$ t \$, exist only in the interval \$ \mathcal{I} = [t_0,t_f] \$, where \$ t_0 \$ and \$ t_f \$ may be free variables.
There are also \$ n_u \$ control inputs which we, as the engineers, are free to design (up to some constraints which will be covered).
The vector of states, \$ x(t) : \mathcal{I} \mapsto \mathbb{R}^{n_x} \$, evolves according to \$ dx/dt = f(x(t), u(t), t) \$ where we assume that the functions \$ f : \mathbb{R}^{n_x} \times \mathbb{R}^{n_u}, \mathbb{R} \mapsto \mathbb{R}^{n_x} \$ are assumed to be at least once differentiable in each of its inputs.

Without loss of generality, there is also a lower and upper bound assigned to each of the states and controls denoted \$ x_i^L \leq x_i(t) \leq x_i^U \$ and \$ u_i^L \leq u_i(t) \leq u_i(t) \$.
These _box constraints_ are treated separately from any other constraints that contain either a nonlinearity or more than one state and/or control.

Along with the set of ODEs, there may be some _boundary conditions_ at either or both ends of the interval.
Again, there are box constraints which can be written as \$ x_{0,i}^L \leq x(t_0) \leq x_{0,i}^U \$ and \$ x_{f,i}^L \leq x(t_f) \leq x_{f,i}^U \$ which are treated separately.
All other boundary constraints can be expressed as inequalities \$ e^L \leq e(x(t_0), x(t_f), t_0, t_f) \leq e^U \$ where the function \$ e : \mathbb{R}^{n_x} \times \mathbb{R}^{n_x} \times \mathbb{R} \times \mathbb{R} \mapsto \mathbb{R}^{n_e} \$ is again assumed to be at least once differentiable in each of its inputs.

Beyond the boundary conditions, there may also be constraints on the state variables or control inputs themselves.
These _path constraints_ can be expressed as \$ h^L \leq h ( x(t), u(t), t) \leq h^U \leq h^U \$ where the function \$ h : \mathbb{R}^{n_x} \times \mathbb{R}^{n_u} \times \mathbb{R} \mapsto \mathbb{R}^{n_h} \$ is assumed to be at least once differentiable in each of its inputs.

Finally, the last component is some _cost_ assigned to any choice of the control input (and by association the result values of the states) which comes in two parts; a _boundary cost_ and a _running cost_.
The boundary cost is denoted \$ E(x(t_0), x(t_f), t_0, t_f)) \$ which assigns a cost to the values of the initial and final states and initial and final times.
The running cost is denoted \$ F(x(t), u(t), t) \$ which assigns a cost to each value of the state, the control input and time.
These two functions are again assumed to be at least once differentiable in each of their inputs.

Combining all of these elements into the standard optimal control problem which can be addressed using Pontryagin's principle leads to the following equation:

$$
\begin{aligned}
  \min && &J = E({x}(t_0), {x}(t_f), t_0, t_f) + \int_{t_0}^{t_f} F({x}(t), {u}(t))dt\\
  \text{s.t.} && &\dot{x}(t) = f(x(t), u(t))\\
  && &h^L \leq h(x(t), u(t)) \leq h^U\\
  && &x^L \leq x(t) \leq x^U\\
  && &u^L \leq u(t) \leq u^U\\
  && &e^L \leq e(x(t_0), x(t_f), t_0, t_f) \leq e^U\\
  && &x_0^L \leq x(t_0) \leq x_0^U\\
  && &x_f^L \leq x(t_f) \leq x_f^U
\end{aligned}
$$

## The Necessary Conditions

To write the necessary conditions we introduce _multipliers_ for each of the *constraints* that appears;
1) For each state variable \$ x_j(t) \$ we introduce a co-state \$ \lambda_j(t) \$ for \$ j = 1,2,\ldots,n_x \$.
2) For each path constraint \$ h_j(x(t),u(t),t) \$, there is a co-path variable \$ \mu_j(t) \$ for \$ j = 1,2,\ldots,n_h \$.
3) For each boundary condition (sometimes referred to as an _event_) \$ e_j (x(t_0),x(t_f),t_0,t_f) \$, there is a co-event \$ \nu_j \$ for \$ j = 1,2,\ldots,n_e \$.

## The Hamiltonian Minimization Problem

$$
  \begin{aligned}
    \min\limits_{u} && &H(\lambda, x, u)\\
    \text{s.t.} && &h^L \leq h(x,u) \leq h^U\\
    && &x^L \leq x \leq x^U\\
    && &u^L \leq u \leq u^U
  \end{aligned}
$$

