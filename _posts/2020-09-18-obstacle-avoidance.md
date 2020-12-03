---
title: 'Optimal Control Problem: Double Integrator Obstacle Avoidance'
date: 2020-09-18
permalink: /posts/2020/09/obstacle-avoidance/
tags:
  - optimal control
  - constraints
  - double integrator
---

# Obstacle Avoidance for Double Integrators

$$
  \begin{aligned}
    \min && &J = \frac{1}{2} \int_0^{t_f} \left( u_x^2(t) + u_y^2(t) \right) dt\\
    \text{s.t.} && &\dot{x}(t) = v(t), \quad x(0) = x_0, \quad x(t_f) = x_f\\
    && &\dot{v}(t) = u_x(t), \quad v(0) = v_0, \quad v(t_f) = v_f\\
    && &\dot{y}(t) = w(t), \quad y(0) = y_0, \quad y(t_f) = y_f\\
    && &\dot{w}(t) = u_y(t), \quad w(0) = w_0, \quad w(t_f) = w_f\\
    && &(x(t) - c_x)^2 + (y(t) - c_y)^2 \geq r^2
  \end{aligned}
$$
