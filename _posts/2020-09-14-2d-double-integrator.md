---
title: 'Optimal Control Problem: 2D Double Integrator'
date: 2020-09-14-2d-double-integrator
permalink: /posts/2020/09/2d-double-integrator/
tags:
  - optimal control
  - double integrator
---

# The 2D Double Integrator Problem

Double integrators represent the most simple mechanical system. They consist of a _position_ state and a _velocity_ state where the time derivative of the position is the velocity and the time derivative of the velocity is the acceleration which we can control with an actuator that applies a force.

In the following problem (and many subsequent optimal control posts which demonstrate the effect of various cost terms and constraints) we examine a system composed of two double integrators which exists in the (x,y) plane.
The additional complications over a single double integrator will be useful when we include more complex constraints that cannot exist in one dimension.
Also, for the first few simple optimal control problems, the optimal solution will be shown to be _independent_ with respect to each direction, that is, the optimal control in the x-direction does not affect the optimal contrl in the y-direction and vice versa.

$$
  \begin{aligned}
    \min && &J = \frac{1}{2} \int_0^{t_f} \left( u_x^2(t) + u_y^2(t) \right) dt\\
    \text{s.t.} && &\dot{x}(t) = v(t), \quad x(0) = x_0, \quad x(t_f) = x_f\\
    && &\dot{v}(t) = u_x(t), \quad v(0) = v_0, \quad v(t_f) = v_f\\
    && &\dot{y}(t) = w(t), \quad y(0) = y_0, \quad y(t_f) = y_f\\
    && &\dot{w}(t) = u_y(t), \quad w(0) = w_0, \quad w(t_f) = w_f
  \end{aligned}
$$

$$
  \bar{H} = \frac{1}{2} u_x^2 + \frac{1}{2} u_y^2 + \lambda_x v + \lambda_v u_x + \lambda_y w + \lambda_w u_y
$$

$$
  \frac{\partial \bar{H}}{\partial u_x} = u_x + \lambda_v = 0 \quad \rightarrow \quad u_x(t) = -\lambda_v(t)
$$

$$
  \dot{\lambda}_x(t) = -\frac{\partial \bar{H}}{\partial x} = 0 \quad \rightarrow \quad \lambda_x(t) = \lambda_x(0)
$$

$$
  \dot{\lambda}_v(t) = -\frac{\partial \bar{H}}{\partial v} = -\lambda_x(t) \quad \rightarrow \quad \lambda_v(t) = \lambda_v(0) - \int_0^t \lambda_x(\tau) d\tau = \lambda_v(0) - \lambda_x(0) t
$$

$$
  u_x(t) = -\lambda_v(t) = \lambda_x(0) t - \lambda_v(0)
$$

$$
  v(t) = v(0) + \int_0^t u_x(\tau) d\tau = v_0 + \frac{1}{2} \lambda_x(0) t^2 - \lambda_v(0) t
$$

$$
  x(t) = x(0) + \int_0^t v(\tau) d\tau = x_0 + v_0 t + \frac{1}{6} \lambda_x(0) t^3 - \frac{1}{2} \lambda_v(0) t^2
$$

$$
  \begin{aligned}
    \lambda_y(t) &= \lambda_y(0)\\
    \lambda_w(t) &= \lambda_w(0) - \lambda_y(0) t\\
    u_y(t) &= \lambda_y(0) t - \lambda_w(0)\\
    w(t) &= w_0 + \frac{1}{2} \lambda_y(0) t^2 - \lambda_w(0) t\\
    y(t) &= y_0 + w_0 t + \frac{1}{6} \lambda_y(0) t^3 - \frac{1}{2} \lambda_w(0) t^2
  \end{aligned}
$$

Applying the final constraints leads to the following system of equations,

$$
  \begin{aligned}
    x_f - x_0 - v_0 t_f &= \frac{t_f^3}{6} \lambda_x(0) - \frac{t_f^2}{2} \lambda_v(0)\\
    v_f - v_0 &= \frac{t_f^2}{2} \lambda_x(0) - t_f \lambda_v(0)
  \end{aligned}
$$

$$
  \left[ \begin{array}{cc}
    \frac{t_f^3}{6} & -\frac{t_f^2}{2} \\ \frac{t_f^2}{2} & t_f
  \end{array} \right] \left[ \begin{array}{c}
    \lambda_x(0) \\ \lambda_v(0)
  \end{array} \right] = \left[ \begin{array}{c}
    x_f - x_0 - v_0 t_f\\ v_f - v_0
  \end{array} \right]
$$

The optimal control energy,

$$
  \begin{aligned}
    E_x &= \frac{1}{2} \int_0^{t_f} u_x^2(t) dt\\
    &= \frac{1}{2} \int_0^{t_f} \left( \lambda_x(0) t - \lambda_v(0) \right)^2 dt\\
    &= \frac{1}{2} \int_0^{t_f} \lambda_x^2(0) t^2 dt - \int_0^{t_f} \lambda_x(0) \lambda_v(0) t + \frac{1}{2} \lambda_v^2(0) dt\\
    &= \frac{1}{6} \lambda_x^2(0) t_f^3 - \frac{1}{2} \lambda_x(0) \lambda_v(0) t_f^2 + \frac{1}{2} \lambda_v^2(0) t_f 
  \end{aligned}
$$
