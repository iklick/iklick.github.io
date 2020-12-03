---
title: 'Optimal Control Problem: Average Final State'
date: 2020-09-11
permalink: /posts/2020/09/optimal-control-framework/
tags:
  - optimal control
  - pontryagin
---

# Average Final State Minimization

$$
  \begin{aligned}
    \min && &J = \frac{\alpha}{M} \sum_{i=0}^{M-1} (x_i(t_f) - c_x)^2 + (y_i(t_f) - c_y)^2 + \frac{1}{2} \int_0^{t_f} \left( u_x^2(t) + u_y^2(t) \right) dt\\
    \text{s.t.} && &\dot{x}_i(t) = v_i(t), \quad x_i(0) = x_{i,0}\\
    && &\dot{v}_i(t) = u_x(t), \quad v_i(0) = v_{i,0}, \quad v_i(t_f) = 0\\
    && &\dot{y}_i(t) = w_i(t), \quad y_i(0) = y_{i,0}\\
    && &\dot{w}_i(t) = u_y(t), \quad w_i(0) = w_{i,0}, \quad w_i(t_f) = 0\\
    && &i = 0,1,\ldots,M-1\\
    && &\sum_{i=0}^{M-1} x_i(t_f) = \bar{x}\\
    && &\sum_{i=0}^{M-1} y_i(t_f) = \bar{y}
  \end{aligned}
$$
