---
title: 'Optimal Control Problem: 2D Double Integrator'
date: 2020-09-14-2d-double-integrator
permalink: /posts/2020/09/2d-double-integrator/
tags:
  - optimal control
  - double integrator
---

# The 2D Double Integrator Problem

## Motivation

Double integrators represent the most simple mechanical system. They consist of a _position_ state and a _velocity_ state where the time derivative of the position is the velocity and the time derivative of the velocity is the acceleration which we can control with an actuator that applies a force.

$$
  \begin{aligned}
    \dot{x}(t) &= v(t)\\
    \dot{v}(t) &= u(t)
  \end{aligned}
$$

The name _double integrator_ comes from the fact that the time evolution of the position twice integrates the forcing \$ u(t) \$.

$$
  u(t) = \int_0^{t} \int_0^{\tau} u(s) ds d\tau
$$

In the following problem (and many subsequent optimal control posts which demonstrate the effect of various cost terms and constraints) we examine a system composed of two double integrators which exists in the (x,y) plane.
The additional complications over a single double integrator will be justified when we include more complex constraints that cannot exist in one dimension.

## Problem Statement

The optimal control problem consists of a cost function that minimizes the *control energy*.

$$
  E = \int_0^{t_f} u^2(t) dt
$$

If we take the units of the input \$ u(t) \$ to be those of acceleration, then for the cost to strictly have units of energy, there should be factor in front with units of mass times time.

The optimal control problem drives the 2D double integrator system from an initial condition to a final condition in a prescribed window of time, \$ [0,t_f] \$.

A factor of 1/2 is included in the cost function to remove other factors that would appear otherwise in the derivation.

$$
  \begin{aligned}
    \min && &J = \frac{1}{2} \int_0^{t_f} \left( u_x^2(t) + u_y^2(t) \right) dt\\
    \text{s.t.} && &\dot{x}(t) = v(t), \quad x(0) = x_0, \quad x(t_f) = x_f\\
    && &\dot{v}(t) = u_x(t), \quad v(0) = v_0, \quad v(t_f) = v_f\\
    && &\dot{y}(t) = w(t), \quad y(0) = y_0, \quad y(t_f) = y_f\\
    && &\dot{w}(t) = u_y(t), \quad w(0) = w_0, \quad w(t_f) = w_f
  \end{aligned}
$$

As for every optimal control problem, the first step is to write the Lagrangian of the Hamiltonian.
As this problem has no explicit path constraints (this will be discussed further) the Lagrangian of the Hamiltonian is just the Hamiltonian.

$$
  \bar{H} = \frac{1}{2} u_x^2 + \frac{1}{2} u_y^2 + \lambda_x v + \lambda_v u_x + \lambda_y w + \lambda_w u_y
$$

In the following series of steps, the optimal control, velocity, and position in the x-direction, \$ u_x(t), v(t), x(t) \$, are derived and it is easy to show that the optimal control, velocity, and position in the y-direction can be derived using the same steps.

The first step is to use the stationarity condition to derive either the control input or the switching structure.

$$
  \frac{\partial \bar{H}}{\partial u_x} = u_x + \lambda_v = 0 \quad \rightarrow \quad u_x(t) = -\lambda_v(t)
$$

With the optimal input shown to depend only on the co-states, we begin by solving for the co-position which is shown to be constant.

$$
  \dot{\lambda}_x(t) = -\frac{\partial \bar{H}}{\partial x} = 0 \quad \rightarrow \quad \lambda_x(t) = \lambda_x(0)
$$

With the co-position, we can solve for the co-velocity which is shown to be linear.

$$
  \dot{\lambda}_v(t) = -\frac{\partial \bar{H}}{\partial v} = -\lambda_x(t) \quad \rightarrow \quad \lambda_v(t) = \lambda_v(0) - \int_0^t \lambda_x(\tau) d\tau = \lambda_v(0) - \lambda_x(0) t
$$

With the co-velocity known, the optimal control input is now known to be a linear function with unknown slope, \$ \lambda_x(0) \$, and unknown intersection, \$ \lambda_v(0) \$.

$$
  u_x(t) = -\lambda_v(t) = \lambda_x(0) t - \lambda_v(0)
$$

The optimal time-evolution if the velocity can now be determined by integrating the optimal control input from the initial velocity.

$$
  v(t) = v(0) + \int_0^t u_x(\tau) d\tau = v_0 + \frac{1}{2} \lambda_x(0) t^2 - \lambda_v(0) t
$$

Similarly, the optimal time-evolution of the position is determined by integrating the optimal time-evolution of the velocity.

$$
  x(t) = x(0) + \int_0^t v(\tau) d\tau = x_0 + v_0 t + \frac{1}{6} \lambda_x(0) t^3 - \frac{1}{2} \lambda_v(0) t^2
$$

Following the same steps, the optimal time-evolution of the states, co-states, and controls in the y-direction can be found.

$$
  \begin{aligned}
    \lambda_y(t) &= \lambda_y(0)\\
    \lambda_w(t) &= \lambda_w(0) - \lambda_y(0) t\\
    u_y(t) &= \lambda_y(0) t - \lambda_w(0)\\
    w(t) &= w_0 + \frac{1}{2} \lambda_y(0) t^2 - \lambda_w(0) t\\
    y(t) &= y_0 + w_0 t + \frac{1}{6} \lambda_y(0) t^3 - \frac{1}{2} \lambda_w(0) t^2
  \end{aligned}
$$

Evaluating the optimal time-evolution of the position and velocity at the final time, \$ t_f \$, and setting them equal to the final conditions in the problem statement, yields the following system of linear equations.

$$
  \begin{aligned}
    x_f - x_0 - v_0 t_f &= \frac{t_f^3}{6} \lambda_x(0) - \frac{t_f^2}{2} \lambda_v(0)\\
    v_f - v_0 &= \frac{t_f^2}{2} \lambda_x(0) - t_f \lambda_v(0)
  \end{aligned}
$$

Rearranging these equations into the familiar \$ A x = b \$ form yields the following matrix equation.

$$
  \left[ \begin{array}{cc}
    \frac{t_f^3}{6} & -\frac{t_f^2}{2} \\ \frac{t_f^2}{2} & t_f
  \end{array} \right] \left[ \begin{array}{c}
    \lambda_x(0) \\ \lambda_v(0)
  \end{array} \right] = \left[ \begin{array}{c}
    x_f - x_0 - v_0 t_f\\ v_f - v_0
  \end{array} \right]
$$

As this is a 2-by-2 matrix, we can compute its inverse explicitly with its determinant,

$$
  \det (A) = -\frac{1}{6} t_f^4 + \frac{1}{4} t_f^4 = \frac{1}{12} t_f^4
$$

so that,

$$
  A^{-1} = \frac{12}{t_f^4} \left[ \begin{array}{cc}
    -t_f & \frac{t_f^2}{2} \\ -\frac{t_f^2}{2} & \frac{t_f^3}{6}
  \end{array} \right] = \left[ \begin{array}{cc}
    - \frac{12}{t_f^3} & \frac{6}{t_f^2} \\ -\frac{6}{t_f^2} & \frac{2}{t_f}
  \end{array} \right]
$$

The two unknowns can now be expressed explicitly in terms of the problem data,

$$
  \begin{aligned}
    \lambda_x(0) &= \frac{6}{t_f^2} (v_f - v_0) - \frac{12}{t_f^3} (x_f - x_0 - v_0t_f) = \frac{6}{t_f^2} \left[ b_x - \frac{2}{t_f} a_x \right]\\
    \lambda_v(0) &= \frac{2}{t_f} (v_f - v_0) - \frac{6}{t_f^2} (x_f - x_0 - v_0 t_f) = \frac{2}{t_f} \left[ b_x - \frac{3}{t_f} a_x \right]
  \end{aligned}
$$

Similarly, the unknowns for the optimal solution in the y-direction are,

$$
  \begin{aligned}
    \lambda_y(0) &= \frac{6}{t_f^2} (w_f - w_0) - \frac{12}{t_f^3} (y_f - y_0 - w_0t_f) = \frac{6}{t_f^2} \left[ b_y - \frac{2}{t_f} a_y \right]\\
    \lambda_w(0) &= \frac{2}{t_f} (w_f - w_0) - \frac{6}{t_f^2} (y_f - y_0 - w_0 t_f) = \frac{2}{t_f} \left[ b_y - \frac{3}{t_f} a_y \right]
  \end{aligned}
$$

The optimal control energy,

$$
  \begin{aligned}
    E_x &= \int_0^{t_f} u_x^2(t) dt\\
    &= \int_0^{t_f} \left( \lambda_x(0) t - \lambda_v(0) \right)^2 dt\\
    &=  \int_0^{t_f} \lambda_x^2(0) t^2 dt - 2\int_0^{t_f} \lambda_x(0) \lambda_v(0) t + \lambda_v^2(0) dt\\
    &= \frac{1}{3} \lambda_x^2(0) t_f^3 - \lambda_x(0) \lambda_v(0) t_f^2 +  \lambda_v^2(0) t_f\\
    &= \frac{12}{t_f} \left[ b_x - \frac{2}{t_f} a_x \right]^2 - \frac{12}{t_f} \left[ b_x - \frac{2}{t_f} a_x \right] \left[ b_x - \frac{3}{t_f} a_x \right] + \frac{4}{t_f} \left[ b_x - \frac{3}{t_f} a_x \right]^2\\
    &= \frac{4}{t_f} \left( \frac{3}{2t_f} a_x - b_x \right)^2 + \frac{3}{t_f^3} a_x^2
  \end{aligned}
$$

## Numerical Solution using OptCon

To solve this optimal control problem numerically using OptCon, we first define the cost, dynamic, and constraint functions.
For this problem, there is only a running cost and no non-box constraints.

The running cost is

$$
  F = \frac{1}{2} u_x^2 + u_y^2
$$

which is implemented with the function,

```c
int running_cost (double *x, double *u, double *F, UserDataPtr prob_data) {
  *F = 0.5 * u[0] * u[0] + 0.5 * u[1] * u[1];
  return 0;
}
```

The gradient of the cost function is also required,

$$
  \begin{aligned}
    \frac{\partial F}{\partial x} = \left[ \begin{array}{c}
      0 \\ 0 \\ 0 \\ 0
    \end{array} \right] && \frac{\partial F}{\partial u} = \left[ \begin{array}{c}
      u_x \\ u_y
    \end{array} \right]
  \end{aligned}
$$

which is implemented with the function,


```c
int grad_running_cost (double *x, double *u, double *dFdx, double *dFdu, UserDataPtr prob_data) {

  dFdx[0] = 0.0;
  dFdx[1] = 0.0;
  dFdx[2] = 0.0;
  dFdx[3] = 0.0;
  
  dFdu[0] = u[0];
  dFdu[1] = u[1];
  
  return 0;
}
```

The dynamics can be written as the vector function,

$$
  f = \left[ \begin{array}{c}
    v \\ u_x \\ w \\ u_y
  \end{array} \right]
$$

which is implemented with the function,

```c
int dynamics (double *x, double *u, double *f, UserDataPtr prob_data) {
  
  f[0] = x[1]; // dx/dt = v
  f[1] = u[0]; // dv/dt = ux
  
  f[2] = x[3]; // dy/dt = w
  f[3] = u[1]; // dw/dt = uy
  
  return 0;
}
```

```c
int jac_dynamics (double *x, double *u, int *irowx, int *jcolx, double *dfdx, int *irowu, int *jcolu, double *dfdu, UserDataPtr prob_data) {
  
  if (dfdx == NULL) {
    irowx[0] = 0; jcolx[0] = 0;
    irowx[1] = 0; jcolx[1] = 1;
    irowx[2] = 1; jcolx[2] = 1;
    irowx[3] = 2; jcolx[3] = 2;
    irowx[4] = 2; jcolx[4] = 3;
    irowx[5] = 3; jcolx[5] = 3;
  }
  else {
    dfdx[0] = 0.0;
    dfdx[1] = 1.0;
    dfdx[2] = 0.0;
    dfdx[3] = 0.0;
    dfdx[4] = 1.0;
    dfdx[5] = 0.0;
  }
  
  if (dfdu == NULL) {
    irowu[0] = 1; jcolu[0] = 0;
    irowi[1] = 3; jcolu[1] = 1;
  }
  else {
    dfdu[0] = 1.0;
    dfdu[1] = 1.0;
  }
  
  return 0;
}
```
