---
title: 'Optimal Control Problem: Combining Control Constraints and Free Final Time'
date: 2020-09-16
permalink: /posts/2020/09/bang-bang-free-final-time/
tags:
  - optimal control
  - double integrator
  - free final time
  - bang bang
  - constraints
---

# Combining Bang-Bang Control with Free Final Time

$$
  \begin{aligned}
    \min && &J = (1-\alpha) t_f + \alpha \int_0^{t_f} \left( |u_x(t)| + |u_y(t)| \right) dt\\
    \text{s.t.} && &\dot{x}(t) = v(t), \quad x(0) = x_0, \quad x(t_f) = x_f\\
    && &\dot{v}(t) = u_x(t), \quad v(0) = v_0, \quad v(t_f) = v_f\\
    && &\dot{y}(t) = w(t), \quad y(0) = y_0, \quad y(t_f) = y_f\\
    && &\dot{w}(t) = u_y(t), \quad w(0) = w_0, \quad w(t_f) = w_f\\
    && &-1 \leq u_x(t) \leq 1\\
    && &-1 \leq u_y(y) \leq 1
  \end{aligned}
$$
