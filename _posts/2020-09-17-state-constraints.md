---
title: 'Optimal Control Problem: State Constraints'
date: 2020-09-17
permalink: /posts/2020/09/state-constraints/
tags:
  - optimal control
  - constraints
  - switching
---

# Constraints on the States

$$
  \begin{aligned}
    \min && &J = \frac{1}{2} \int_0^{t_f} \left( u_x^2(t) + u_y^2(t) \right) dt\\
    \text{s.t.} && &\dot{x}(t) = v(t), \quad x(0) = x_0, \quad x^L \leq x(t) \leq x^U, \quad x(t_f) = x_f\\
    && &\dot{v}(t) = u_x(t), \quad v(0) = v_0, \quad v(t_f) = v_f\\
    && &\dot{y}(t) = w(t), \quad y(0) = y_0, \quad y^L \leq y(t) \leq y^U, \quad y(t_f) = y_f\\
    && &\dot{w}(t) = u_y(t), \quad w(0) = w_0, \quad w(t_f) = w_f
  \end{aligned}
$$

