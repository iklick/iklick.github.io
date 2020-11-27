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
Modifications are made which will make the general problem more amenable to numerical solution.
After presenting the components of the problem, Pontryagin's principle is used to derive a set of necessary conditions for an optimal solution.
In some special cases, Pontryagin's method is also sufficient and so there is a single global optimal solution.

In the formulation presented here, we assume the system can be expressed as \$ n_x \$ ordinary differential equations (ODEs).
Let the independent parameter, usually time \$ t \$, exist only in the interval \$ \mathcal{I} = [t_0,t_f] \$, where \$ t_0 \$ and \$ t_f \$ may be free variables.
There are also \$ n_u \$ control inputs which we, as the engineers and scientists, are free to design (up to some constraints which will be covered).
The vector of states, \$ x(t) : \mathcal{I} \mapsto \mathbb{R}^{n_x} \$, evolves according to \$ dx/dt = f(x(t), u(t)) \$ where we assume that the functions \$ f : \mathbb{R}^{n_x} \times \mathbb{R}^{n_u} \mapsto \mathbb{R}^{n_x} \$ are assumed to be at least once differentiable in each of its inputs.

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
    \min\limits_{u} && &H(\lambda, x, u) = F(x,u) + \lambda^T f(x,u)\\
    \text{s.t.} && &h^L \leq h(x,u) \leq h^U\\
    && &x^L \leq x \leq x^U\\
    && &u^L \leq u \leq u^U
  \end{aligned}
$$

The Lagrangian of the Hamiltonian minimization problem is used to derive the necessary conditions.

$$
  \bar{H} = F(x,u) + \lambda^T f(x,u) + \mu^T h(x,u) + \mu_x^T x + \mu_u^T u
$$

The stationarity condition provides the condition for the optimal control input,

$$
  \frac{\partial \bar{H}}{\partial u} = \frac{\partial F}{\partial u} + \left( \frac{\partial f}{\partial u} \right)^T \lambda + \left( \frac{\partial h}{\partial u} \right)^T \mu + \mu_u = 0
$$

The complementarity conditions for the general constraints  \$ h \$ and the box constraints \$ x \$ and \$ u \$ all satisfy the forms,

$$
  \mu_j(t) \left\{ \begin{array}{ccl}
    \leq 0 & \text{if} & h_j(x(t),u(t)) = h_j^L\\
    = 0 & \text{if} & h_j^L < h_j(x(t),u(t)) < h_j^U\\
    \geq 0 & \text{if} & h_j(x(t),u(t)) = h_j^U\\
    \text{unrestricted} & \text{if} & h_j^L = h_j^U
  \end{array} \right.
$$

with similar conditions for \$ mu_{x,j}(t) \$ for \$ j = 1,2,\ldots,n_x \$ and \$ \mu_{u,j}(t) \$ for \$ j = 1,2,\ldots,n_u \$.

The coupled state and co-state dynamical system evolves according to the following sytem of differential equations,

$$
  \begin{aligned}
    \frac{dx}{dt} &= \frac{\partial \bar{H}}{\partial \lambda} = f(x(t),u(t))\\
    \frac{d\lambda}{dt} &= -\frac{\partial \bar{H}}{\partial x} = -\frac{\partial F}{\partial x} - \left( \frac{\partial f}{\partial x} \right)^T \lambda(t) - \left( \frac{\partial h}{\partial x} \right)^T \mu(t) - \mu_x(t)
  \end{aligned}
$$

## The End-Point Minimization Problem

$$
  \begin{aligned}
    \min && &E(x(t_0), x(t_f), t_0, t_f)\\
    \text{s.t.} && &e^L \leq e(x(t_0),x(t_f),t_0,t_f) \leq e^U\\
    && &x_0^L \leq x(t_0) \leq x_0^U, \quad t_0^L \leq t_0 \leq t_0^U\\
    && &x_f^L \leq x(t_f) \leq x_f^U, \quad t_f^L \leq t_f \leq t_f^U
  \end{aligned}
$$

The Lagrangian of the end-point minimization problem is,

$$
  \bar{E} = E(x(t_0), x(t_f), t_0, t_f) + \nu^T e(x(t_0),x(t_f),t_0,t_f) + \eta_0^T x(t_0) + \eta_f^T x(t_f) + \xi_0 t_0 + \xi_f t_f
$$

The end-point Lagrangian provides the conditions for the boundary values of the co-states,

$$
  \lambda (t_0) = - \frac{\partial \bar{E}}{\partial x(t_0)}, \quad \lambda(t_f) = \frac{\partial \bar{E}}{\partial x(t_f)}
$$

and the conditions for the start and stop times are,

$$
  H[@t_0] = \frac{\partial \bar{E}}{\partial t_0}, \quad H[@t_f] = - \frac{\partial \bar{E}}{\partial t_f}
$$

## Outline of Analytic Examination

1. Count the number of state variables, control variables, non-box path and end-point constraints called \$ n_x \$, \$ n_u \$, \$ n_h \$, and \$ n_e \$, respectively. 
2. Write down the functions \$ E \$, \$ F \$, \$ f \$, \$ h \$, and \$ e \$ and their gradients/Jacobians with respect to the states and controls.
3. Use the stationarity condition to either (i) express the control input in terms of the states and co-states or (ii) express the switching structure.
4. First try solving the state / co-state dynamical equations without the constraints and check if the solution violates any of the constraints. If it does not, then the optimal solution has been found! If it does, then 

