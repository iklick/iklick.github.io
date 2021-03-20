---
title: 'A General Framework for Optimal Control'
date: 2020-09-11
permalink: /posts/2020/09/optimal-control-framework/
tags:
  - optimal control
  - pontryagin
---

# Numerical Optimal Control

I spent a good amount of my graduate school career studying optimal control problems. Anyone who has will have noticed that most optimal control problems cannot be solved analytically and so has probably turned to numerical methods.

While some of the codes to numerically solve optimal control problems are quite easy to use, they can be quite opaque about what's going on under the hood (as most of them are licensed software).

This can lead to frustration by users when they see poor convergence or inaccurate results or unreasonably long computation time.

The purpose of this post is to be an introduction to the main concepts of numerical optimal control for graduate students including

1. stating the optimal control problem,
2. discretizing time,
3. approximating the derivatives,
4. building the nonlinear programming problem (NLP),
5. solving the NLP, 
6. and judging the solution.

## The Standard Optimal Control Problem

When can optimal control be applied?

If you are working with a system that can be described by a set of smooth ordinary differential and algebraic equations which can be affected by an external input.

For instance, consider a robotic arm described by the position of the gripper in spherical coordinates, $$(\rho(t),\theta(t),\phi(t))$$.
The length of the arm, $$ \rho(t) $$, can be controlled through an actuator, the swivel angle in the horizontal plane, $$ \theta(t) $$, can also be controlled with a servo as well as the angle from the horizontal, $$ \phi(t) $$.

The equations of motion are,

$$
  \begin{aligned}
    L \ddot{\rho}(t) &= u_{\rho}(t)\\
    I_{\theta} \ddot{\theta}(t) &= u_{\theta}(t)\\
    I_{\phi} \ddot{\phi}(t) &= u_{\phi}(t)
  \end{aligned}
$$

$$
  \begin{aligned}
    \min && &J = E(x(t_0), x(t_f), t_0, t_f) + \int_0^{t_f} F(x(t),u(t),t) dt\\
    \text{s.t.} && &\frac{dx}{dt} = f(x(t),u(t),t)\\
    && &x^L \leq x(t) \leq x^U, \quad u^L \leq u(t) \leq u^U\\
    && &h^L \leq h(x(t),u(t),t) \leq h^U\\
    && &x_0^L \leq x(t_0) \leq x_0^U, \quad x_f^L \leq x(t_f) \leq x_f^U\\
    && &t_0^L \leq t_0 \leq t_0^U, \quad t_f^L \leq t_f \leq t_f^U
  \end{aligned}
$$

Making sure that you can write your problem in terms of functions $$ E,F,f,h $$ and bounds $$ x^L, x^U, u^L, u^U, h^L, h^U, x_0^L, x_0^U, x_f^L, x_f^U, t_0^L, t_0^U, t_f^L, t_f^U $$ can help you decide if optimal control is the proper tool to solve it.

Note that the optimal control problem written above is not exhaustive in its form.
Additional non-box constraints can be introduced at the end-points,

$$
  e^L \leq e(x(t_0),x(t_f),t_0,t_f) \leq e^U
$$

representing the case you may want to move a certain distance from the origin for example.
Integral constraints may be useful,

$$
  g^L \leq \int_0^{t_f} g(x(t),u(t),t) dt \leq g^U
$$

representing cases where there is a finite resource that is allowed to be used.
The problem may require more than one phase, that is, for time $$ t \in [t_0,t_1] $$ one set of equations applies while for $$ t \in [t_1,t_f] $$ another set applies with some functions,

$$
  \ell^L \leq \ell (x^{(1)}(t_1), x^{(2)}(t_1), t_1) \leq \ell^U 
$$

describing how the states between the two phases should relate to each other.

## Discretization of Time

The first step to any numerical solution is to decide how to descretize the continuous variables.
In the type of optimal control problem addressed here, the only continuous variable is time.
Note though that the end-points of the time interval of interest are variables and so time is rescaled to a standard interval.
Let the scaled time be denoted 

$$
  \tau \in [\tau_0,\tau_f]
$$

The standard time interval is discretized using any one of a number of discretization techniques, which can be generically written as,

$$
  \{ \tau_0, \tau_1, \ldots, \tau_{N-1}, \tau_N \}
$$

where $$ \tau_N = \tau_f $$ at then end-points.

To map between the standard interval and the original interval, we assume a linear function,

$$
  \tau = at + b
$$

where the slope and interval can be found by matching the end-points,

$$
  \begin{aligned}
    \tau_0 &= a t_0 + b\\
    \tau_f &= a t_f + b
  \end{aligned}
$$

Solving the linear system,

$$
  \left[ \begin{array}{cc}
    t_0 & 1 \\ t_f & 1
  \end{array} \right] \left[ \begin{array}{c}
    a \\ b
  \end{array} \right] = \left[ \begin{array}{c}
    \tau_0 \\ \tau_f
  \end{array} \right]
$$

which yields the function,

$$
  \tau = \frac{\tau_f - \tau_0}{t_f - t_0} (t - t_0) + \tau_0
$$

and its inverse

$$
  t = \frac{t_f-t_0}{\tau_f - \tau_0} (\tau - \tau_0) + t_0
$$

The integrals that appear in the optimal control problem are adjusted by substituting $$ t(\tau) $$,

$$
  \int_{t_0}^{t_f} F(x(t),u(t),t) dt = \frac{t_f-t_0}{\tau_f - \tau_0} \int_{\tau_0}^{\tau_f} F(x(t(\tau)), u(t(\tau)), t(\tau)) d\tau
$$

The integral can now be approximated with the discretization described above,

$$
  \int_{t_0}^{t_f} F(x(t),u(t),t) dt \approx \frac{t_f-t_0}{\tau_f-\tau_0} \sum_{k=0}^N w_k F(X_k, U_k, t_k)
$$

where the weights $$ w_k $$ are derived from whichever quadrature rule used.
The simplest choice is the trapezoidal rule,

$$
  \begin{aligned}
  \int_{\tau_0}^{\tau_f} F(x(\tau),u(\tau),t(\tau)) d\tau &= \sum_{k=0}^{N-1} \int_{\tau_k}^{\tau_{k+1}} F(x(\tau),u(\tau),t(\tau)) d\tau\\
   &\approx \sum_{k=0}^{N-1} \frac{\tau_{k+1} - \tau_k}{2} \left( F(x(\tau_k), u(\tau_k), t(\tau_k)) + F(x(\tau_{k+1}), u(\tau_{k+1}), t(\tau_{k+1})) \right)\\
  &= \sum_{k=0}^N w_k F(x(t(\tau_k)), u(t(\tau_k)), t(\tau_k))
  \end{aligned}
$$

where the weights are,

$$
  w_k = \left\{ \begin{array}{ll}
    \frac{\tau_1 - \tau_0}{2} & k = 0\\
    \frac{\tau_{k+1} - \tau_{k-1}}{2} & k = 1,2,\ldots,N-1\\
    \frac{\tau_N - \tau_{N-1}}{2} & k = N
  \end{array} \right.
$$

We also use the chain rule to derive the derivatives with respect to the standard time interval,

$$
  \frac{dx}{d\tau} = \frac{dx}{dt} \frac{dt}{d\tau} = \frac{t_f-t_0}{\tau_f - \tau_0} f(x(t(\tau)), u(t(\tau)), t(\tau))
$$

## Approximating the Derivatives

Much research continues to go into how best to approximate the derivatives for numerical optimal control.
The most important choice is whether to use a global collocation or a local collocation method.
Only local collocation methods are addressed here, but I will probably talk about global collocation in a future post as evidence shows it can be more accurate for a given number of grid points.
A local collocation method looks to approximate the derivatives between adjacent grid points,

$$
  \int_{\tau_k}^{\tau_{k+1}} \frac{dx}{d\tau} d\tau = X_{k+1} - X_k = \frac{t_f-t_0}{\tau_f - \tau_0} \int_{\tau_k}^{\tau_{k+1}} f(x(t(\tau)), u(t(\tau)), t(\tau)) d\tau
$$

The simplest choice is the trapezoidal rule again so,

$$
  \int_{\tau_k}^{\tau_{k+1}} f(x(t(\tau)), u(t(\tau)), t(\tau)) d\tau \approx \frac{\tau_{k+1} - \tau_k}{2} (f(X_k,U_k,t_k) + F(X_{k+1}, U_{k+1}, t_{k+1}) )
$$

The defect constraints represent the error of the approximation,

$$
  \zeta_k = d_k X_{k+1} - d_k X_k - \frac{t_f-t_0}{\tau_f - \tau_0} \left( f_k + f_{k+1} \right) 
$$

where $$ d_k = \frac{2}{\tau_{k+1} - \tau_k} $$.

## Building the Nonlinear Programming Problem

With the integral approximations made using the trapezoidal rule, the NLP can be constructed with the discretized variables $$ X_k $$ and $$ U_k $$, and the end-points $$ t_0 $$ and $$ t_f $$.

$$
  \begin{aligned}
    \min && &\hat{J} = E(X_0, X_N, t_0, t_f) + \frac{t_f-t_0}{\tau_f - \tau_0} \sum_{k=0}^N w_k F(X_k,U_k,t_k)\\
    \text{s.t.} && &d_k X_{k+1} - d_k X_k - \frac{t_f-t_0}{\tau_f - \tau_0} \left( f(X_k,U_k,t_k) + f(X_{k+1}, U_{k+1}, t_{k+1}) \right) = 0_{n_x}\\
    && &h^L \leq h(X_k, U_k, t_k) \leq h^U\\
    && &x^L \leq X_k \leq x^U, \quad u^L \leq U_l \leq u^U\\
    && &x_0^L \leq x(t_0) \leq x_0^U, \quad x_f^L \leq x(t_f) \leq x_f^U\\
    && &t_0^L \leq t_0 \leq t_0^U, \quad t_f^L \leq t_f \leq t_f^U
  \end{aligned}
$$

Collect the unknowns in the vector $$ z $$

$$
  z = \left[ \begin{array}{c}
    X_0 \\ X_1 \\ \vdots \\ X_N \\ U_0 \\ U_1 \\ \vdots \\ U_N \\ t_0 \\ t_f
  \end{array} \right] \in \mathbb{R}^{(N+1)(n_x+n_u)+2}
$$

## Solving the NLP

To solve the NLP that has been constructed, I use IPOPT, a free and open source NLP solver distributed by coin-OR. 
It is by far the most advanced free NLP solver available and is actively being developed.

The NLP solver requires first and second order derivatives of the cost function and constraint functions though.
There are three general methods for providing the NLP solver with derivatives.

1. You can compute the gradient of the cost function, the Jacobian of the constraints, and the Hessian of the Lagrangian by hand and then write code to provide the exact derivatives. While the code will execute quickly and the NLP algorithm will not be hampered with inaccuracies, this choice is prone to human error and makes updating cost functions and constraints time consuming.
2. You can use numerical differentiation techniques to produce approximate derivatives. This method also tends to execute quickly but may cause issues with convergence due to the innaccuracies introduced.
3. Automatic differentiation is a marriage of the two. It is quite complex and beyond the scope of this post but I am definitely going to discuss it in the future. It is a numerical technique to compute derivatives exactly but it is the slowest of the three.

For my implementation, the user must provide the gradients of the functions $$ E $$ and $$ F $$ and the Jacobians of $$ f $$ and $$ h $$ but nothing else, as the Hessians are computed using a numerical technique.

While the burden of computing gradients and Jacobians can be heavy, exploring the structure of the gradients and Jacobians can be useful while analyzing the optimal control problem.

The gradient of the discretized cost function is,

$$
  \frac{d\hat{J}}{d z} = \left[ \begin{array}{c} 
    \frac{\partial \hat{J}}{\partial X_0} \\ \frac{\partial \hat{J}}{\partial X_1} \\ \vdots \\ \frac{\partial \hat{J}}{\partial X_N} \\ \frac{\partial \hat{J}}{\partial U_0} \\ \vdots \\ \frac{\partial \hat{J}}{\partial U_N} \\ \frac{\partial \hat{J}}{\partial t_0} \\ \frac{\partial \hat{J}}{\partial t_f}
  \end{array} \right] =  \left[ \begin{array}{c}
    \frac{\partial E}{\partial x(t_0)} + \frac{t_f-t_0}{\tau_f - \tau_0} w_0 \left. \frac{\partial F}{\partial x} \right|_{k = 0}\\
    \frac{t_f - t_0}{\tau_f - \tau_0} w_1 \left. \frac{\partial F}{\partial x} \right|_{k=1}\\
    \vdots \\
    \frac{\partial E}{\partial x(t_f)} + \frac{t_f - t_0}{\tau_f - \tau_0} w_N \left. \frac{\partial F}{\partial x} \right|_{k=N}\\
    \frac{t_f - t_0}{\tau_f - \tau_0} w_0 \left. \frac{\partial F}{\partial u} \right|_{k=0} \\
    \vdots \\
    \frac{t_f - t_0}{\tau_f - \tau_0} w_N \left. \frac{\partial F}{\partial u} \right|_{k=N}\\
    \frac{\partial \hat{J}}{\partial t_0} \\
    \frac{\partial \hat{J}}{\partial t_f}
  \end{array} \right]
$$

where the derivative with respect to the initial time is,

$$
  \frac{\partial \hat{J}}{\partial t_0} = \frac{\partial E}{\partial t_0} -\frac{1}{\tau_f - \tau_0} \sum_{k=0}^N w_k F (X_k, U_k, t_k) + \frac{t_f - t_0}{\tau_f - \tau_0} \sum_{k=0}^N w_k \left. \frac{\partial F}{\partial t_0} \right|_k
$$

and the final time is,

$$
  \frac{\partial \hat{J}}{\partial t_f} = \frac{\partial E}{\partial t_f} + \frac{1}{\tau_f - \tau_0} \sum_{k=0}^N w_k F (X_k, U_k, t_k) + \frac{t_f - t_0}{\tau_f - \tau_0} \sum_{k=0}^N w_k \left. \frac{\partial F}{\partial t_f} \right|_k
$$

The temporal derivatives that appear are derived using the chain rule,

$$
  \left. \frac{\partial F}{\partial t_0} \right|_k = \left. \frac{\partial F}{\partial t}\right|_k \left. \frac{\partial t}{\partial t_0} \right|_k \quad \text{and} \quad \left. \frac{\partial F}{\partial t_f} \right|_k = \left. \frac{\partial F}{\partial t} \right|_k \left. \frac{\partial t}{\partial t_f} \right|_k
$$

where the derivatives of time with respect to end-points are,

$$
  \left. \frac{\partial t}{\partial t_0} \right|_k = - \frac{\tau_k - \tau_0}{\tau_f - \tau_0} + 1 \quad \text{and} \quad \left. \frac{\partial t}{\partial t_f} \right|_k = \frac{\tau_k - \tau_0}{\tau_f - \tau_0}
$$

With the gradient computed, we see the user needs to provide,

$$
  \frac{\partial E}{\partial x(t_0)}, \quad \frac{\partial E}{\partial x(t_f)}, \quad \frac{\partial E}{\partial t_0}, \quad \frac{\partial E}{\partial t_f}, \quad \frac{\partial F}{\partial x}, \quad \frac{\partial F}{\partial u}, \quad \frac{\partial F}{\partial t}
$$

The Jacobian of the $$ k $$'th defect constraint consists of blocks corresponding to $$ X_k $$, $$ U_k $$, $$ t_0 $$, and $$ t_f $$.

$$
  \frac{\partial \zeta_k}{\partial z} = \left[ \begin{array}{cccccccc}
    \frac{\partial \zeta_k}{\partial X_0} & \frac{\partial \zeta_k}{\partial X_1} & \cdots & \frac{\partial \zeta_k}{\partial X_N} & \frac{\partial \zeta_k}{\partial U_0} & \cdots & \frac{\partial \zeta_k}{\partial U_N} & \frac{\partial \zeta_k}{\partial t_0} & \frac{\partial \zeta_k}{\partial t_f}
  \end{array} \right]
$$

The blocks with respect to the states are,

$$
  \frac{\partial \zeta_k}{\partial X_j} = \left\{ \begin{array}{ll}
    -d_k I_{n_x} - \frac{t_f - t_0}{\tau_f - \tau_0} \left. \frac{\partial f}{\partial x} \right|_k, & j = k\\
    d_k I_{n_x} - \frac{t_f - t_0}{\tau_f - \tau_0} \left. \frac{\partial f}{\partial x} \right|_{k+1}, & j = k+1\\
    O_{n_x}, & \text{otherwise}
  \end{array} \right.
$$

and similarly the blocks with respect to the controls are,

$$
  \frac{\partial \zeta_k}{\partial X_j} = \left\{ \begin{array}{ll}
    - \frac{t_f - t_0}{\tau_f - \tau_0} \left. \frac{\partial f}{\partial u} \right|_k, & j = k\\
    - \frac{t_f - t_0}{\tau_f - \tau_0} \left. \frac{\partial f}{\partial u} \right|_{k+1}, & j = k+1\\
    O_{n_x,n_u}, & \text{otherwise}
  \end{array} \right.
$$

The block corresponding to the initial time is,

$$
  \frac{\partial \zeta_k}{\partial t_0} = \frac{1}{\tau_f - \tau_0} \left( \left. \frac{\partial f}{\partial t} \right|_k  \left. \frac{\partial t}{\partial t_0} \right|_k + \left. \frac{\partial f}{\partial t} \right|_{k+1} \left. \frac{\partial t}{\partial t_0} \right|_{k+1} \right)
$$

and for the final time,

$$
  \frac{\partial \zeta_k}{\partial t_f} = - \frac{1}{\tau_f - \tau_0} \left( \left. \frac{\partial f}{\partial t} \right|_k \left. \frac{\partial t}{\partial t_0} \right|_k + \left. \frac{\partial f}{\partial t \right|_{k+1} \left. \frac{\partial t}{\partial t_0} \right|_{k+1} \right)
$$

With the Jacobian written out, we see the user must provide routines to return $$ \frac{\partial f}{\partial x}, \frac{\partial f}{\partial u}, \frac{\partial f}{\partial t} $$ and the corresponding derivatives for $$ h $$.

## Providing an Initial Guess

While solving an optimization problem, the NLP solver takes the current candidate solution $$ z^{(j)} $$ and updates it to $$ z^{(j+1)} $$ such that it reduces some combination of the cost function and the constraint violations.
This means that for it to start, the user must supply some initial guess, denoted $$ z^{(0)} $$.
Choosing a good initial guess is imperative for the solver as the smaller the distance between $$ z^{(0)} $$ to a local minimum $$ z^* $$ the less time the solver will require.

So how should one choose an initial guess? There are a number of ways to do this depending on the type of problem.
Often, a good method is to select a control input $$ u (t) $$ that satisfies the box constraints and integrate the dynamics forward in time to $$ t_f $$ chosen to satisfy its box constraints as well.
If the final state after integration violates the final state bounds, $$ x_f^L $$ and $$ x_F^U $$ the user could either accept this initial guess and let the solver deal with it or use a shooting method to try and improve the initial guess to satisfy the final guess.

The RKF45 algorithm can be used to integrate the states forward in time from the initial condition given the control input guess.

## Collecting the Data

To numerically solve the optimal control problem requires the following data:

1. The problem sizes, $$ N $$, $$ n_x $$, $$ n_u $$, $$ n_h $$
2. The $$ 6n_x + 2n_u + 4 + 2n_h $$ problem bounds, $$ x^L, x^U, u^L, u^U, x_0^L, x_0^U, x_f^L, x_f^U, t_0^L, t_0^U, t_f^L, t_f^U, h^L, h^U $$
3. The functions $$ E $$, $$ F $$, $$ f $$, $$ h $$, $$ \frac{\partial E}{\partial x(t_0)} $$, $$\frac{\partial E}{\partial x(t_f)} $$, $$ \frac{\partial E}{\partial t_0} $$, $$ \frac{\partial E}{\partial t_f} $$, $$ \frac{\partial F}{\partial x} $$, $$ \frac{\partial F}{\partial u} $$, $$ \frac{\partial F}{\partial t} $$, $$ \frac{\partial f}{\partial x} $$, $$ \frac{\partial f}{\partial u} $$, $$ \frac{\partial f}{\partial t} $$, $$ \frac{\partial h}{\partial x} $$, $$ \frac{\partial h}{\partial u} $$, $$ \frac{\partial h}{\partial y} $$.
4. The initial guess got the control input, initial condition, and end-point times.

## Analyzing the Solution
