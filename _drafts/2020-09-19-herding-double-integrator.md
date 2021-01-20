---
title: 'Optimal Control Problem: Herding Double Integrators'
date: 2020-09-19
permalink: /posts/2020/09/herding-double-integrator/
tags:
  - optimal control
  - double integrator
  - constraints
  - final constraints
---

# Herding Double Integrators

$$
  \begin{aligned}
    \min && &J = \frac{1}{2} \int_0^{t_f} \left( u_x^2(t) + u_y^2(t) \right) dt\\
    \text{s.t.} && &\dot{x}_i(t) = v_i(t), \quad x_i(0) = x_{i,0}, \quad x_i(t_f) \geq x_f\\
    && &\dot{v}_i(t) = u_x(t), \quad v_i(0) = v_{i,0}\\
    && &\dot{y}_i(t) = w_i(t), \quad y_i(0) = y_{i,0}, \quad y_i(t_f) \geq y_f\\
    && &\dot{w}_i(t) = u_y(t), \quad w_i(0) = w_{i,0}\\
    && &i = 0,1,\ldots, M-1
  \end{aligned}
$$
