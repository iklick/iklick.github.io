---
layout: archive
title: "Optimal Control"
permalink: /optimalcontrol/
author_profile: true
---

This page is used as a reference for important topics regarding optimal control which I may reference in the individual posts about particular problems.

The problem is presented in a modified form compared to textbooks on optimal control.
The changes done are to make the presentation more amenable to numerical solving in future posts.

The optimal control problem addressed here consists of \$ n_x \$ time-varying states on the interval \$ \mathcal{I} = [t_0,t_f] \$ denoted \$ x : \mathcal{I} \mapsto \mathbb{R}^n \$.
In addition to the states, there are \$ n_u \$ external control inputs which we may design to perform a particular task related to the states \$ u : \mathcal{I} \mapsto \mathbb{R}^{n_u} \$.
The states and controls are bounded by _box constraints_

$$
  x^L \leq x(\cdot ) \leq x^U, \quad u^L \leq u(\cdot ) \leq u^U
$$

where the name comes from the fact that in two dimensions the constraints form a box.
Along with the box constraints on the states and controls, we assume there are also box constraints on the initial and final times,

$$
  t_0^L \leq t_0 \leq t_0^U, \quad t_f^L \leq t_f \leq t_f^U
$$

Note that if the times are prescribed one can set \$ t_0^L = t_0^U \$ and/or \$ t_f^L = t_f^U \$.

Finally, often the initial condition is prescribed and possibly the final condition as well, leading to box constraints applied at these points as well,

$$
  x_0^L \leq x(t_0) \leq x_0^U, \quad x_f^L \leq x(t_f) \leq x_f^U
$$

The states evolve according to the system of controlled ordinary differential equations,

$$
  \frac{d x}{dt} = f(x(t),u(t)), \quad t \in \mathcal{I}
$$

where the function \$ f : \mathbb{R}^{n_x} \times \mathbb{R}^{n_u} \times \mathbb{R} \mapsto \mathbb{R}^{n_x} \$ is assumed to be at least once differentiable.
In addition to the dynamics, an additional \$ n_h \$ constraints may be imposed on the state and control variables.
These constraints are assumed to be different from the box constraints already imposed by default.

$$
  h^L \leq h(x(t),u(t)) \leq h^U
$$

These algebraic constraints are also called _path constraints_ as they restrict the behavior of the states and controls from the initial time to the final time.

Finally, there may be constraints placed on the end point-points as well,

$$
  e^L \leq e(x(t_0),x(t_f),t_0,t_f) \leq e^U
$$ 

which should not be simple box constraints as described above as these are handled separately.

If there exists multiple control signals \$ u : \mathcal{I} \mapsto \mathbb{R}^m \$ that satisfy the above constraints according to the dynamics then we select the (or one of the) control input which minimizes the cost,

$$
  J = E(x(t_0), x(t_f), t_0, t_f) + \int_{t_0}^{t_f} F(x(t),u(t)) dt
$$

### Pontryagin Maximum Principle

The Hamiltonian minimization problem

$$
  \begin{aligned}
    \min && &H = F(x(t_0),x(t_f),t_0,t_f) + \lambda^T(t) f(x(t),u(t))\\
    \text{s.t.} && &h^L \leq h(x(t),u(t)) \leq h^U\\
    && &x^L \leq x \leq x^U\\
    && &u^L \leq u \leq u^U
  \end{aligned}
$$

The Lagrangian of the Hamiltonian is,

$$
  \bar{H} = F + \lambda^T f + \mu^T h + \xi_x^T x + \xi_u^T u
$$

The stationarity condition provides an identity for either the time evolution of the optimal control input or the switching structure of the optimal control input.

$$
  \frac{\partial \bar{H}}{\partial u} = \frac{\partial F}{\partial u} + \left( \frac{\partial f}{\partial u} \right)^T \lambda + \left( \frac{\partial h}{\partial u} \right)^T \mu + \xi_u = 0
$$

The evolution of the co-states is also determined from the Lagrangian of the Hamiltonian,

$$
  \frac{d\lambda}{dt} = - \frac{\partial \bar{H}}{\partial x} = - \frac{\partial F}{\partial x} - \left( \frac{\partial f}{\partial x} \right)^T \lambda - \left( \frac{\partial h}{\partial x} \right) \mu
$$

and finally the states evolve according to,

$$
  \frac{dx}{dt} = f
$$

The co-path and co-box variables satisfy,

$$
  \mu_k \left\{ \begin{array}{lll}
    \leq 0 & \text{if} & h_k = h_k^L\\
    = 0 & \text{if} & h_k^L < h_k < h_k^U\\
    \geq 0 & \text{if} & h_k = h_k^U\\
    \text{unrestricted} & \text{if} & h_k^L = h_k^U
  \end{array} \right.
$$

with similar requirements for \$ \xi_x \$ and \$ \xi_u \$.

The end-point optimization problem is,

$$
  \begin{aligned}
    \min && &E\\
    \text{s.t.} && &e\\
    && &x_0^L \leq x(t_0) \leq x_0^U\\
    && &x_f^L \leq x(t_f) \leq x_f^U\\
    && &t_0^L \leq t_0 \leq t_0^U\\
    && &t_f^L \leq t_f \leq t_f^U
  \end{aligned}
$$

so that the end-point Lagrangian can be written,

$$
  \bar{E} = E + \nu^T e + \zeta_0^T x(t_0) + \zeta_f^T x(t_f) + \eta_0 t_0 + \eta_f t_f
$$

The initial and final co-states can be determined from derivatives of the end-point Lagrangian.

$$
  \lambda (t_0) = - \frac{\partial \bar{E}}{\partial x(t_0)} = - \frac{\partial E}{\partial x(t_0)} - \left( \frac{\partial e}{\partial x(t_0)} \right)^T \nu + \zeta_0
$$

$$
  \lambda (t_f) = \frac{\partial \bar{E}}{\partial x(t_f)} = \frac{\partial E}{\partial x(t_f)} + \left( \frac{\partial e}{\partial x(t_f)} \right)^T \nu + \zeta_f
$$

The initial and final times can be found from the Hamiltonian value conditions,

$$
  H[@t_0] = \frac{\partial \bar{E}{\partial t_0} = \frac{\partial E}{\partial t_0} + \nu^T \frac{\partial e}{\partial t_0} + \eta_0
$$

$$
  H[@t_f] = -\frac{\partial \bar{E}}{\partial t_f} = -\frac{\partial E}{\partial t_f} - \nu^T \frac{\partial e}{\partial t_f} - \eta_f
$$

### Multiple Independent Parameters

Above we focused on optimal control problems over a single independent variable, usually time.
Alternatively, an optimal control problem may be over two or more independent variables, such as both time and space, or multiple spatial dimensions.

Integral constraints play an important role when there is some finite amount of resource such as fuel or momentum.

$$
  h^L \leq \int_{t_0}^{t_f} h(x(t),u(t),t) dt \leq h^U
$$
