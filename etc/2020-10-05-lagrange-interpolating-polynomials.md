---
title: 'Optimal Control Case Study: 3D Double Integrator Energy-Time Tradeoff'
date: 2020-10-05
permalink: /posts/2020/10/lagrange-interpolating-polynomials/
tags:
  - optimal control
  - numerical methods
  - nonlinear programming
  - optimization
---

## The Optimal Control Problem

This problem again uses double integrators as a simplistic actuated mechanical system to alleviate some of the complexity of the analytic solution.
The system now consists of 3 double integrators, representing motion in 3-dimensional space, which can be independently actuated in any direction.
We look for a control that drives the system from an initial position and velocity to a final position and velocity.
The cost consists of a weighted sum of the elapsed time, \$ t_f \$, and the control energy.

$$
  \begin{aligned}
    \min && &J = \alpha t_f + \frac{1}{2} \int_0^{t_f} \left( a_x^2(t) + a_y^2(t) + a_z^2(t) \right) dtdt\\
    \text{s.t.} && &\dot{x}(t) = v(t), \quad x(0) = x_0, \quad x(t_f) = x_f\\
    && &\dot{y}(t) = w(t), \quad y(0) = y_0, \quad y(t_f) = y_f\\
    && &\dot{z}(t) = u(t), \quad z(0) = z_0, \quad z(t_f) = z_f\\
    && &\dot{v}(t) = a_x(t), \quad v(0) = v_0, \quad v(t_f) = v_f\\
    && &\dot{w}(t) = a_y(t), \quad w(0) = w_0, \quad w(t_f) = w_f\\
    && &\dot{u}(t) = a_z(t), \quad u(0) = u_0, \quad u(t_f) = u_f
  \end{aligned}
$$

The position of the system is denoted \$ (x(t), y(t), z(t)) \$ and its velocity is denoted \$ (v(t), w(t), u(t)) \$.
The control inputs \$, a_x(t), a_y(t), a_z(t) \$ represent the acceleration of the system.
The parameter \$ \alpha \$ allows us to tune the relative importance of elapsed time with respect to the control energy when deriving the optimal control input.

## Deriving the Solution

The Hamiltonian of the optimal control problem is,

$$
  H = \frac{1}{2} a_x^2 + \frac{1}{2} a_y^2 + \frac{1}{2} a_z^2 + \lambda_x v + \lambda_y w + \lambda_z u + \lambda_v a_x + \lambda_w a_y + \lambda_y a_z 
$$

### Stationarity Conditions

$$
  \begin{aligned}
    \frac{\partial H}{\partial a_x} = a_x + \lambda_v = 0 \quad \rightarrow \quad a_x(t) = -\lambda_v(t)\\
    \frac{\partial H}{\partial a_y} = a_y + \lambda_w = 0 \quad \rightarrow \quad a_y(t) = -\lambda_w(t)\\
    \frac{\partial H}{\partial a_z} = a_z + \lambda_u = 0 \quad \rightarrow \quad a_z(t) = -\lambda_u(t)
  \end{aligned}
$$

### Co-State Evolution

Co-position equations

$$
  \begin{aligned}
    \dot{\lambda}_x(t) = -\frac{\partial H}{\partial x} = 0 \quad \rightarrow \quad \lambda_x(t) = \lambda_x(0)\\
    \dot{\lambda}_y(t) = -\frac{\partial H}{\partial y} = 0 \quad \rightarrow \quad \lambda_y(t) = \lambda_y(0)\\
    \dot{\lambda}_z(t) = -\frac{\partial H}{\partial z} = 0 \quad \rightarrow \quad \lambda_z(t) = \lambda_z(0)
  \end{aligned}
$$

Co-velocity equations

$$
  \begin{aligned}
    \dot{\lambda}_v(t) = -\frac{\partial H}{\partial v} = -\lambda_x(t) \quad \rightarrow \quad \lambda_v(t) = \lambda_v(0) - \lambda_x(0) t\\
    \dot{\lambda}_w(t) = -\frac{\partial H}{\partial w} = -\lambda_y(t) \quad \rightarrow \quad \lambda_w(t) = \lambda_w(0) - \lambda_y(0) t\\
    \dot{\lambda}_u(t) = -\frac{\partial H}{\partial u} = -\lambda_z(t) \quad \rightarrow \quad \lambda_u(t) = \lambda_u(0) - \lambda_z(0) t
  \end{aligned}
$$

With the co-velocity equations, the optimal controls are the following linear curves,

$$
  \begin{aligned}
    a_x(t) = \lambda_x(0) t - \lambda_v(0)\\
    a_y(t) = \lambda_y(0) t - \lambda_w(0)\\
    a_z(t) = \lambda_z(0) t - \lambda_u(0)
  \end{aligned}
$$

### Optimal State Evolution

Optimal velocity evolution can now be found,

$$
  \begin{aligned}
    v(t) &= v(0) + \int_0^t a_x(\tau) d\tau = v(0) - \lambda_v(0) t + \frac{1}{2} \lambda_x(0) t^2\\
    w(t) &= w(0) + \int_0^t a_y(\tau) d\tau = w(0) - \lambda_w(0) t + \frac{1}{2} \lambda_y(0) t^2\\
    u(t) &= u(0) + \int_0^t a_z(\tau) d\tau = u(0) - \lambda_u(0) t + \frac{1}{2} \lambda_z(0) t^2
  \end{aligned}
$$

The optimal position trajectory can now be found by integrating the velocities,

$$
  \begin{aligned}
    x(t) &= x(0) + \int_0^t v(\tau) d\tau = x(0) + v(0) t - \frac{1}{2} \lambda_v(0) t^2 + \frac{1}{6} \lambda_x(0) t^3\\
    y(t) &= y(0) + \int_0^t w(\tau) d\tau = y(0) + w(0) t - \frac{1}{2} \lambda_w(0) t^2 + \frac{1}{6} \lambda_y(0) t^3\\
    z(t) &= z(0) + \int_0^t u(\tau) d\tau = z(0) + u(0) t - \frac{1}{2} \lambda_u(0) t^2 + \frac{1}{6} \lambda_z(0) t^3
  \end{aligned}
$$

### Static Optimization Problem

The original optimal control problem has been reduced to 7 unknowns, namely,

$$
  \lambda_x(0), \quad \lambda_y(0), \quad \lambda_z(0), \quad \lambda_v(0), \quad \lambda_w(0), \quad \lambda_u(0), \quad t_f
$$

This is a substantial improvement over the original problem which consisted of three unknown continuous functions, the controls \$ a_x(t), a_y(t), a_z(t) \$.

The three final position constraints and three final velocity constraints provide the first six algebraic equations.

$$
  \begin{aligned}
    v_f - v_0 &= \frac{1}{2} \lambda_x(0) t_f^2 - \lambda_v(0) t_f\\
    w_f - w_0 &= \frac{1}{2} \lambda_y(0) t_f^2 - \lambda_w(0) t_f\\
    u_f - u_0 &= \frac{1}{2} \lambda_z(0) t_f^2 - \lambda_u(0) t_f\\
    x_f - x_0 &= v_0 t_f + \frac{1}{6} \lambda_x(0) t_f^3 - \frac{1}{2} \lambda_v(0) t_f^2\\
    y_f - y_0 &= w_0 t_f + \frac{1}{6} \lambda_y(0) t_f^3 - \frac{1}{2} \lambda_w(0) t_f^2\\
    z_f - z_0 &= u_0 t_f + \frac{1}{6} \lambda_z(0) t_f^3 - \frac{1}{2} \lambda_u(0) t_f^2
  \end{aligned}
$$

The seventh constraint arises from the final value condition of the Hamiltonian,

$$
  \begin{aligned}
  H[@t_f] + \frac{\partial \bar{E}}{\partial t_f} &= \frac{1}{2} \lambda_v^2(t_f) + \frac{1}{2} \lambda_w^2(t_f) + \frac{1}{2} \lambda_u^2(t_f)\\
  &+ \lambda_x(0) v_f + \lambda_y(0) w_f + \lambda_z(0) u_f - \lambda_v^2(t_f) - \lambda_w^2(t_f) - \lambda_u^2(t_f) + \alpha\\
  &= \lambda_x(0) v_f + \lambda_y(0) w_f + \lambda_z(0) u_f - \frac{1}{2} (\lambda_x(0) t_f - \lambda_v(0))^2\\
  &- \frac{1}{2} (\lambda_y(0) t_f - \lambda_w(0))^2 - \frac{1}{2} (\lambda_z(0) t_f - \lambda_u(0))^2 + \alpha\\
  &= 0
  \end{aligned}
$$

The original cost function is also expressed in terms of the seven unknowns,

$$
  \begin{aligned}
  J &= \alpha t_f + \frac{1}{2} \int_0^{t_f} \left[ (\lambda_x(0) t - \lambda_v(0))^2 + (\lambda_y(0) t - \lambda_w(0))^2 + (\lambda_z(0) t - \lambda_u(0))^2 \right] dt\\
  &= \alpha t_f + \frac{1}{6} (\lambda_x^2(0) + \lambda_y^2(0) + \lambda_z^2(0)) t_f^3 - \frac{1}{2} (\lambda_x(0) \lambda_v(0) + \lambda_y(0) \lambda_w(0) + \lambda_z(0) \lambda_w(0)) t_f^2\\
  &+ \frac{1}{2} (\lambda_v^2(0) + \lambda_w^2(0) + \lambda_u^2(0)) t_f
  \end{aligned}
$$
