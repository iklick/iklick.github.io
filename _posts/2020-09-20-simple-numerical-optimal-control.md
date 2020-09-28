---
title: 'A Simple Numerical Optimal Control Scheme'
date: 2020-09-20
permalink: /posts/2020/09/-simple-numerical-optimal-control/
tags:
  - optimal control
  - numerical methods
  - nonlinear programming
  - optimization
---

## The Optimal Control Problem

The optimal control problem addressed in this post can most generally be defined as,

$$
  \begin{aligned}
    \min && &J = E(x(t_0), x(t_f), t_0, t_f) + \int_{t_0}^{t_f} F(x(t),u(t)) dt\\
    \text{s.t.} && &\dot{x}(t) = f(x(t),u(t))\\
    && &h^L \leq h(x(t),u(t)) \leq h^U\\
    && &e^L \leq e(x(t_0), x(t_f), t_0, t_f) \leq e^U
  \end{aligned}
$$

## Discretizing the Dynamics

There are many ways to discretize time in the differential equations that appear in the optimal control problem.
The simplest approach is using a central difference method (or the trapezoidal rule) which is what is descibed here.
Additional methods, such as at the roots of orthogonal polynomials, which have the benefit of faster convergence in relation to the number of grid points, will be discussed in future posts.

The initial step is to normalize the time interval to \$ [0,1] \$ which can be done with the following transformation,

$$
  \tau = \frac{t - t_0}{t_f-t_0} \quad \leftrightarrow \quad t = (t_f-t_0) \tau + t_0
$$

Using the chain rule, the dynamical equation can be rewritten in terms of the transformed independent variable \$ \tau \$,

$$
  \frac{dx}{d\tau} = \frac{dx}{dt} \frac{dt}{d\tau} = (t_f-t_0) f(x(\tau), u(\tau))
$$

Define \$ N+1 \$ points in time, denoted \$ \tau_k \$, \$ k = 0,1,\ldots,N \$, such that \$ \tau_0 = 0 \$ is the initial time and \$ \tau_f = 1 \$ is the final time.
Let the distance between grid points be denoted

$$
  d_k = \tau_{k+1} - \tau_k, \quad k = 0,1,\ldots,N-1
$$

Between each of these points, the ODE can be expressed as,

$$
  \frac{dx}{d\tau} = (t_f-t_0) f(x(\tau),u(\tau)), \quad \tau \in [\tau_k,\tau_{k+1}], \quad k = 0,1,\ldots,N-1
$$

where each subsequent initial condition is found from the end state at the previous interval.
Integrating this expression,

$$
  x(\tau_{k+1}) - x(\tau_k) = (t_f-t_0) \int_{\tau_k}^{\tau_{k+1}} f(x(\tau),u(\tau)) dt
$$

The trapezoidal rule approximates the integral on the RHS as,

$$
  \int_{\tau_k}^{\tau_{k+1}} f(x(\tau),u(\tau)) dt \approx \frac{d_k}{2} \left( f(x(\tau_{k+1}), u(\tau_{k+1})) + f(x(\tau_k),u(\tau_k)) \right)
$$

Let \$ x(\tau_k) = x_k \$, \$ u(\tau_k) = u_k \$, and \$ f(x_k,u_k) = f_k \$.
The defect constraints can now be composed using the trapezoidal rule,

$$
  \zeta_k = x_{k+1} - x_k - (t_f-t_0) \frac{d_k}{2} \left(f_{k+1} + f_k \right) = 0, \quad k = 0,1,\ldots,N-1
$$

These defect constraints are the approximate form of the dynamical equations that appeared in the original optimal control.
In the next section, we use the same discretization scheme to approximate the integral in the cost function.

## Approximation of the Integral

The cost function is similarly approximated using the trapezoidal rule

$$
  \begin{aligned}
    J &= E(x(t_0), x(t_f), t_0, t_f) + \int_{t_0}^{t_f} F(x(t),u(t)) dt\\
    &= E(x(t_0), x(t_f), t_0, t_f) + (t_f-t_0) \int_{0}^{1} F(x(\tau),u(\tau)) d\tau\\
    &\approx E(x(t_0), x(t_f), t_0, t_f) + (t_f-t_0) \sum_{k=0}^{N-1} \frac{d_k}{2} F(X(\tau_k), U(\tau_k))\\
    &= E(x_0, x_N, t_0, t_f) + (t_f-t_0) \frac{d_k}{2} (F_k + F_{k+1})
  \end{aligned}
$$

With the approximations of the dynamics and cost function completely defined, we can define the complete transcribed optimal control problem in terms of the variables \$ x_k \$ and \$ u_k \$ for \$ k = 0,1,\ldots,N \$ as well as \$ t_0 \$ and \$ t_f \$.

## Nonlinear Programming

A generic nonlinear programming problem (NLP) can be written,

$$
  \begin{aligned}
    \min && &g(z)\\
    \text{s.t.} && &c^L \leq c(z) \leq c^U
  \end{aligned}
$$

where \$ z \in \mathbb{R}^M \$ is the number of variables to optimize, \$ g : \mathbb{R}^M \mapsto \mathbb{R} \$ is the cost function and \$ c : \mathbb{R}^M \mapsto \mathbb{R}^P \$ is the set of constraints that lie between \$ c^L, c^U \in \mathbb{R}^p \$.

The main assumptions we must ensure are true is that the cost function \$ g \$ and the constraint functions \$ c \$ are differentiable with respect to all variables \$ z \$ at least twice.

This assumption removes the possibility of jump discontinuities in the cost or constraints.

Much research has gone into effective, general purpose solvers for NLPs.
There are a number of commercial solvers available but in this post I will use IPOPT, an open-source NLP solver available through coin-or.

### IPOPT

The library we will use to solve the NLP that arises from transcribing is called Interior Point OPTimization or IPOPT.
To solve the NLP, we need to provide the following functions to IPOPT,

1. The cost function \$ g : \mathbb{R}^M \mapsto \mathbb{R} \$
2. The constraint function \$ c : \mathbb{R}^M \mapsto \mathbb{R}^P \$
3. The gradient of the cost function \$ \frac{d g}{dz} : \mathbb{R}^M \mapsto \mathbb{R}^M \$
4. The Jacobian of the constraints \$ \frac{dc}{dz} : \mathbb{R}^M \mapsto \mathbb{R}^{P \times M} \$ which must be provided in triplet form

The Hessian of the cost and each of the constraints are also required but I will use an approximation available through IPOPT rather than explicitly defining them.
I may include a way for explicit inclusion of Hessian information in the future.

The specializations of these routines for the transcribed optimal control problem will be discussed in the next sections.

## The Discretized Optimal Control Problem

Define the global variable array \$ Z \in \mathbb{R}^{(N+1)(n+m)+2 \$ as having elements,

$$
  z = \left[ \begin{array}{c}
    x_0 \\ x_1 \\ \vdots \\ x_N \\ u_0 \\ u_1 \\ \vdots \\ u_N \\ t_0 \\ t_f
  \end{array} \right]
$$

The trascribed optimal control problem which is now a nonlinear programming problem is,

$$
  \begin{aligned}
    \min && &\hat{J} = E(x_0,x_N,t_0,t_f) + (t_f-t_0) \left[ \frac{d_0}{2} F_0 + \sum_{k=1}^{N-1} \frac{d_{k-1}+d_k}{2} F_k + \frac{d_{N-1}}{2} F_N \right]\\
    \text{s.t.} && &x_{k+1} - x_k - (t_f-t_0) \frac{d_k}{2} \left(f_{k+1} + f_k \right), \quad k = 0,1,\ldots,N-1\\
    && &h^L \leq h(x_k, u_k) \leq h^U, \quad k = 0,\ldots,N\\
    && &e^L \leq e(x_0, x_N, t_0, t_f) \leq e^U
  \end{aligned}
$$

From the previous function, the four functions we need to provide are specialized.

1. The cost function requires computing the end-point cost \$ E \$ and the running cost \$ F \$.
2. The constraint function is split into three components,  
$$
  c(z) = \left[ \begin{array}{c}
    x_1 - x_0 - (t_f-t_0) \frac{d_0}{2} \left(f_{1} + f_0 \right)\\
    x_2 - x_1 - (t_f-t_0) \frac{d_1}{2} \left(f_2 + f_1 \right)\\
    \vdots\\
    x_N - x_{N-1} - (t_f-t_0) \frac{d_{N-1}}{2} \left(f_N + f_{N-1} \right)\\
    h_0\\
    h_1\\
    \vdots\\
    h_N\\
    e
  \end{array} \right]
$$  
which yields \$ Nn_x + (N+1)n_h + n_e \$ constraints in total.
The lower and upper bounds of the constraints are,  
$$
  c^L = \left[ \begin{array}{c}
    0_{Nn_x} \\ h^L \\ h^L \\ \vdots \\ h^L \\ e^L
  \end{array} \right], \quad c^U = \left[ \begin{array}{c}
    0_{Nn_x} \\ h^U \\ h^U \\ \vdots \\ h^U \\ e^U
  \end{array} \right]
$$  
where \$ 0_n \$ is a vector of zeros of length \$ n \$ and the vertical dots represent \$ (N+1) \$ copies of \$ h^L \$ and \$ h^U \$.
3. The gradient of the cost function can be written component wise now,  
$$
  \frac{d \hat{J}}{d z} = \left[ \begin{array}{c}
    \frac{\partial E}{\partial x_0} + (t_f-t_0) \frac{d_0}{2} \left. \frac{\partial F}{\partial x} \right|_{@ \tau_0}\\
    (t_f-t_0) \frac{d_1 + d_0}{2} \left. \frac{\partial F}{\partial x} \right|_{@ \tau_1}\\
    \vdots\\
    \frac{\partial E}{\partial x_f} + (t_f-t_0) \frac{d_{N-1}}{2} \left. \frac{\partial F}{\partial x} \right|_{@ \tau_N}\\
    (t_f-t_0) \frac{d_0}{2} \left.\frac{\partial F}{\partial u}\right|_{@ \tau_0}\\
    (t_f-t_0) \frac{d_1+d_0}{2} \left. \frac{\partial F}{\partial u}\right|_{@ \tau_1}\\
    \vdots\\
    (t_f-t_0) \frac{d_{N-1}}{2} \left. \frac{\partial F}{\partial u} \right|_{@ \tau_N}\\
    \frac{\partial E}{\partial t_0} - \left[ \frac{d_0}{2} F_0 + \sum_{k=1}^{N-1} \frac{d_k+d_{k-1}}{2} F_k + \frac{d_{N-1}}{2} F_N\right]\\
    \frac{\partial E}{\partial t_f} + \left[ \frac{d_0}{2} F_0 + \sum_{k=1}^{N-1} \frac{d_k+d_{k-1}}{2} F_k + \frac{d_{N-1}}{2} F_N\right]
  \end{array} \right]
$$  
The gradient of the cost functions requires the gradient of the end-point cost in terms of all four of its inputs and the running cost in terms of all three of its inputs.
4. The Jacobian of the constraints consists of a block structure.
Writing it generically would take too much room, and so instead, let us set \$ N = 3 \$ so that the pattern should be clear.
Let \$ f^x_k = \left. \frac{\partial f}{\partial x} \right|_{@ \tau_k} \$ and \$ f^u_k \$ and \$ f^t_k \$ have similar meanings, as well as \$ h^x_k, h^u_k, h^t_k \$.  
$$
  \frac{dc}{dz} = \left[ \begin{array}{cccccccccc}
    -I_{n_x} - (t_f-t_0) \frac{h_0}{2} f^x_0 & I_{n_x} - (t_f-t_0) \frac{h_0}{2} f^x_1 & O_{n_x} & O_{n_x} & -(t_f-t_0) \frac{h_0}{2} f^u_0 & -(t_f-t_0) \frac{h_0}{2} f^u_1 & O_{n_x,n_u} & O_{n_x,n_u} & \frac{h_0}{2} (f_1 + f_0) & -\frac{h_0}{2} (f_1 + f_0)\\
    O_{n_x} & -I_{n_x} - (t_f-t_0) \frac{h_1}{2} f^x_1 & I_{n_x} - (t_f-t_0) \frac{h_1}{2} f^x_2 & O_{n_x} & O_{n_x,n_u} & -(t_f-t_0) f^u_1 & -(t_f-t_0) f^u_2 & O_{n_x,n_u} & \frac{h_1}{2} (f_2 + f_1) & -\frac{h_1}{2} (f_2 + f_1)\\
    O_{n_x} & O_{n_x} & -I_{n_x} - (t_f-t_0) \frac{h_2}{2} f^x_2 & I_{n_x} - (t_f-t_0) \frac{h_2}{2} f^x_3 & O_{n_x,n_u} & O_{n_x,n_u} & -(t_f-t_0) f^u_2 & -(t_f-t_0) f^u_3 & \frac{h_2}{2} (f_3 + f_2) & -\frac{h_2}{2} (f_3 + f_2)\\
    h^x_0 & O_{n_h,n_x} & O_{n_h,n_x} & O_{n_h,n_x} & h^u_0 & O_{n_h,n_u} & O_{n_h,n_u} & O_{n_h,n_u} & O_{n_h,1} & O_{n_h,1}\\
    O_{n_h,n_x} & h^x_1 & O_{n_h,n_x} & O_{n_h,n_x} & O_{n_h,n_u} & h^u_1 & O_{n_h,n_u} & O_{n_h,n_u} & O_{n_h,1} & O_{n_h,1}\\
    O_{n_h,n_x} & O_{n_h,n_x} & h^x_3 & O_{n_h,n_x} & O_{n_h,n_u} & O_{n_h,n_u} & h^u_2 & O_{n_h,n_u} & O_{n_h,1} & O_{n_h,1}\\
    O_{n_h,n_x} & O_{n_h,n_x} & O_{n_h,n_x} & h^x_3 & O_{n_h,n_u} & O_{n_h,n_u} & O_{n_h,n_u} & h^u_3 & O_{n_h,1} & O_{n_h,1}\\
    e^{x_0} & O_{n_e,n_X} & O_{n_e,n_x} & e^{x_f} & O_{n_e,n_u} & O_{n_e,n_u} & O_{n_e,n_u} & O_{n_e,n_u} & e^{t_0} & e^{t_f}
  \end{array} \right]
$$  
This matrix must be constructed in triplet format so each Jacobian block is constructed in triplet format as well.
One issue that may be addressed in a future post is the poor ordering of the constraints and variables.
Ideally, one would want this Jacobian to have a _narrow band_, that is, all non-zeros lie within some interval around the diagonal.

## EXAMPLES

The following small examples are used to test the implementation to ensure it was coded correctly.

### The Simplest Example (Scalar Minimum Energy)

The scalar minimum energy problem has a defined initial time and state and final time and state, a single state \$ n_x = 1 \$, a single control input \$ n_u = 1 \$, and no non-box constraints so \$ n_h = n_e = 0 \$.

The problem can be expressed as,

$$
  \begin{aligned}
    \min && &J = \frac{1}{2} \int_{t_0}^{t_f} u^2(t) dt\\
    \text{s.t.} && &\dot{x}(t) = a x(t) + b u(t)\\
    && &x(t_0) = x_0, \quad x(t_f) = x_f
  \end{aligned}
$$

The Hamiltonian for this problem is,

$$
  H = \frac{1}{2} u^2 + a \lambda x + b \lambda u
$$

The stationarity condition is,

$$
  \frac{\partial H}{\partial u} = u + b \lambda = 0 \quad \Rightarrow \quad u(t) = -b \lambda(t)
$$

The evolution of the co-state can now be written,

$$
  \dot{\lambda}(t) = -\frac{\partial H}{\partial x} = -a \lambda(t) \quad \Rightarrow \quad \lambda(t) = e^{-a(t-t_f)} \lambda(t_f)
$$

With the optimal input now known up to an unknown constant, we can write the optimal state trajectory,

$$
  \begin{aligned}
    x(t) &= e^{a (t-t_0)} x_0 - b^2 \int_{t_0}^t e^{a(t-\tau)} e^{a(t_f-\tau)} d\tau \lambda(t_f)\\
    &= e^{a(t-t_0)} x_0 - b^2 \int_{t_0}^t e^{a(t_f+t-2\tau)} d\tau \lambda(t_f)\\
    &= e^{a(t-t_0)} x_0 - \frac{b^2}{2a} \left[ e^{a(t_f+t-2t_0)} - e^{a(t_f-t)} \right] \lambda(t_f)
  \end{aligned}
$$

To determine the final co-state value, apply the constraint \$ x(t_f) = x_f \$ by setting \$ t = t_f \$ in the optimal state trajectory,

$$
  x_f = e^{a(t_f-t_0)} x_0 + \frac{b^2}{2a} \left[1 - e^{2a (t_f-t_0)} \right] \lambda(t_f)
$$

which, after rearranging, yields the final value of the co-state,

$$
  \lambda(t_f) = \frac{2a}{b^2} \left[ \frac{x_f - e^{a(t_f-t_0)} x_0}{1 - e^{2a(t_f-t_0)}} \right]
$$

With this value, we have the optimal state trajectory and optimal control input written above.

