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

$$
  \begin{aligned}
    \min && &J = E(x(t_0), x(t_f), t_0, t_f) + \int_{t_0}^{t_f} F(x(t),u(t), t) dt\\
    \text{s.t.} && &\dot{x}(t) = f(x(t),u(t),t)\\
    && &h^L \leq h(x(t),u(t),t) \leq h^U\\
    && &e^L \leq e(x(t_0), x(t_f), t_0, t_f) \leq e^U
  \end{aligned}
$$

## Discretizing the Dynamics

There are innumerable ways to discretize the differential equations that appear in the optimal control problem.
The simplest approach is using a central difference method (or the trapezoidal rule).

The initial step is to normalize the time interval to \$ [0,1] \$ which can be done with the following transformation,

$$
  \tau = \frac{t - t_0}{t_f-t_0} 
$$

Using the chain rule, the dynamical equation can be rewritten in terms of the transformed independent variable \$ \tau \$,

$$
  \frac{dx}{d\tau} = \frac{dx}{dt} \frac{dt}{d\tau} = (t_f-t_0) f(x(\tau), u(\tau), \tau(t_f-t_0)+t_0)
$$

Define \$ N+1 \$ points in time, denoted \$ \tau_k \$, \$ k = 0,1,\ldots,N \$, such that \$ \tau_0 = 0 \$ is the initial time and \$ \tau_f = 1 \$ is the final time.

Between each of these points, the ODE can be expressed as,

$$
  \frac{dx}{d\tau} = (t_f-t_0) f(x(\tau),u(\tau), (t_f-t_0)\tau+t_0), \quad \tau \in [\tau_k,\tau_{k+1}], \quad k = 0,1,\ldots,N-1
$$

where each subsequent initial condition is found from the end state at the previous interval.
Integrating this expression,

$$
  x(\tau_{k+1}) - x(\tau_k) = (t_f-t_0) \int_{\tau_k}^{\tau_{k+1}} f(x(\tau),u(\tau),(t_f-t_0)\tau+t_0) dt
$$

The trapezoidal rule approximates the integral on the RHS as,

$$
  \int_{\tau_k}^{\tau_{k+1}} f(x(\tau),u(\tau),(t_f-t_0)\tau+t_0) dt \approx \frac{\tau_{k+1}-\tau_k}{2} \left( f(x(\tau_{k+1}), u(\tau_{k+1}), (t_f-t_0)\tau_{k+1}+t_0) + f(x(\tau_k),u(\tau_k),(t_f-t_0)\tau_k+t_0) \right)
$$

Let \$ x(\tau_k) = x_k \$, \$ u(\tau_k) = u_k \$, and \$ f(x_k,u_k,(t_f-t_0)t_k+t_0) = f_k \$.
The defect constraints can now be composed using the trapezoidal rule,

$$
  \zeta_k = x_{k+1} - x_k - (t_f-t_0) \frac{t_{k+1}-t_k}{2} \left(f_{k+1} + f_k \right) = 0, \quad k = 0,1,\ldots,N-1
$$

These defect constraints are the approximate form of the dynamical equations that appeared in the original optimal control.
In the next section, we use the same discretization scheme to approximate the integral in the cost function.

## Approximation of the Integral

The cost function is similarly approximated using the trapezoidal rule

$$
  \begin{aligned}
    J &= E(x(t_0), x(t_f), t_0, t_f) + \int_{t_0}^{t_f} F(x(t),u(t),t) dt\\
    &= E(x(t_0), x(t_f), t_0, t_f) + (t_f-t_0) \int_{\tau_0}^{\tau_f} F(x(\tau),u(\tau),(t_f-t_0)\tau+t_0) d\tau\\
    &\approx E(x(t_0), x(t_f), t_0, t_f) + (t_f-t_0) \sum_{k=0}^{N-1} \frac{\tau_{k+1}-\tau_k}{2} F(X(\tau_k), U(\tau_k), (t_f-t_0)\tau_k+t_0)\\
    &= E(X_0, X_N, t_0, t_f) + (t_f-t_0) \frac{\tau_{k+1}-\tau_k}{2} (F_k + F_{k+1})
  \end{aligned}
$$

With the approximations of the dynamics and cost function completely defined, we can define the complete transcribed optimal control problem in terms of the variables \$ X_k \$ and \$ U_k \$ for \$ k = 0,1,\ldots,N \$ and \$ t_0 \$ and \$ t_f \$.

## Nonlinear Programming

A generic nonlinear programming problem can be written,

$$
  \begin{aligned}
    \min && &g(z)\\
    \text{s.t.} && &c^L \leq c(z) \leq c^U
  \end{aligned}
$$

where \$ z \in \mathbb{R}^M \$ is the number of variables to optimize, \$ g : \mathbb{R}^M \mapsto \mathbb{R} \$ is the cost function and \$ c : \mathbb{R}^M \mapsto \mathbb{R}^P \$ is the set of constraints that lie between \$ c^L, c^U \in \mathbb{R}^p \$.

### IPOPT

The library we will use to solve the NLP that arises from transcribing is called Interior Point OPTimization or IPOPT.
To solve the NLP, we need to provide the following functions to IPOPT,

1. The cost function \$ g : \mathbb{R}^M \mapsto \mathbb{R} \$
2. The constraint function \$ c : \mathbb{R}^M \mapsto \mathbb{R}^P \$
3. The gradient of the cost function \$ \frac{d g}{dz} : \mathbb{R}^M \mapsto \mathbb{R}^M \$
4. The Jacobian of the constraints \$ \frac{dc}{dz} : \mathbb{R}^M \mapsto \mathbb{R}^{P \times M} \$ which must be provided in triplet form

The specializations of these routines for the transcribed optimal control problem will be discussed in the next sections.

## The Discretized Optimal Control Problem

Define the global variable array \$ Z \in \mathbb{R}^{(N+1)(n+m)+2 \$ as having elements,

$$
  Z = \left[ \begin{array}{c}
    X_0 \\ X_1 \\ \vdots \\ X_N \\ U_0 \\ U_1 \\ \vdots \\ U_N \\ t_0 \\ t_f
  \end{array} \right]
$$

The trascribed optimal control problem which is now a nonlinear programming problem is,

$$
  \begin{aligned}
    \min && &\hat{J} = E(X_0,X_f,t_0,t_f) + (t_f-t_0) \left[ \frac{h_0}{2} F_0 + \sum_{k=1}^{N-1} \frac{h_{k-1}+h_k}{2} F_k + \frac{h_{N-1}}{2} F_N \right]\\
    \text{s.t.} && &X_{k+1} - X_k - (t_f-t_0) \frac{h_k}{2} \left(f_{k+1} + f_k \right), \quad k = 0,1,\ldots,N-1\\
    && &h^L \leq h(X_k, U_k, (t_f-t_0) \tau_k+t_0) \leq h^U, \quad k = 0,\ldots,N\\
    && &e^L \leq e(X_0, X_N, t_0, t_f) \leq e^U
  \end{aligned}
$$

From the previous function, the four functions we need to provide are specialized.

1. The cost function requires computing the end-point cost \$ E \$ and the running cost \$ F \$.
2. The constraint function is split into three components,  
$$
  c(Z) = \left[ \begin{array}{c}
    X_1 - X_0 - (t_f-t_0) \frac{h_0}{2} \left(f_{1} + f_0 \right)\\
    X_2 - X_1 - (t_f-t_0) \frac{h_1}{2} \left(f_2 + f_1 \right)\\
    \vdots\\
    X_N - X_{N-1} - (t_f-t_0) \frac{h_{N-1}}{2} \left(f_N + f_{N-1} \right)\\
    h_0\\
    h_1\\
    \vdots\\
    h_N\\
    e
  \end{array} \right]
$$  
which yields \$ Nn_x + (N+1)n_h + n_e \$ constraints in total.
3. The gradient of the cost function can be written component wise now,  
$$
  \frac{d \hat{J}}{d Z} = \left[ \begin{array}{c}
    \frac{\partial E}{\partial x_0} + (t_f-t_0) \frac{d_0}{2} \left. \frac{\partial F}{\partial X} \right|_{@ \tau_0}\\
    (t_f-t_0) \frac{d_1 + d_0}{2} \left. \frac{\partial F}{\partial X} \right|_{@ \tau_1}\\
    \vdots\\
    \frac{\partial E}{\partial x_f} + (t_f-t_0) \frac{d_{N-1}}{2} \left. \frac{\partial F}{\partial X} \right|_{@ \tau_N}\\
    (t_f-t_0) \frac{d_0}{2} \left.\frac{\partial F}{\partial U}\right|_{@ \tau_0}\\
    (t_f-t_0) \frac{d_1+d_0}{2} \left. \frac{\partial F}{\partial U}\right|_{@ \tau_1}\\
    \vdots\\
    (t_f-t_0) \frac{d_{N-1}}{2} \left. \frac{\partial F}{\partial U} \right|_{@ \tau_N}\\
    \frac{\partial E}{\partial t_0} - \left[ \frac{d_0}{2} \left. \frac{\partial F}{\partial t} \right|_{@ \tau_0} + \sum_{k=1}^{N-1} \frac{d_k - d_{k-1}}{2} \left. \frac{\partial F}{\partial t} \right|_{@ \tau_k} + \left. \frac{d_{N-1}}{2} \frac{\partial F}{\partial t} \right|_{@ \tau_N} \right]\\
    \frac{\partial E}{\partial t_f} + \left[ \frac{d_0}{2} \left. \frac{\partial F}{\partial t} \right|_{@ \tau_0} + \sum_{k=1}^{N-1} \frac{d_k - d_{k-1}}{2} \left. \frac{\partial F}{\partial t} \right|_{@ \tau_k} + \left. \frac{d_{N-1}}{2} \frac{\partial F}{\partial t} \right|_{@ \tau_N} \right]
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
    O_{n_x} & O_{n_x} & -I_{n_x} - (t_f-t_0) \frac{h_2}{2} f^x_2 & I_{n_x} - (t_f-t_0) \frac{h_2}{2} f^x_3 & O_{n_x,n_u} & O_{n_x,n_u} & -(t_f-t_0) f^u_2 & -(t_f-t_0) f^u_3 & \frac{h_2}{2} (f_3 + f_2) & -\frac{h_2}{2} (f_3 + f_2)
  \end{array} \right]
$$
