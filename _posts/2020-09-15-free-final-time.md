---
title: 'Optimal Control Problem: Balance between Time and Energy'
date: 2020-09-15
permalink: /posts/2020/09/free-final-time/
tags:
  - optimal control
  - free final time
  - double integrator
---

# Leaving the Final Time Variable Free


$$
  \begin{aligned}
    \min && &J = \alpha t_f + \frac{1}{2} \int_0^{t_f} \left( u_x^2(\tau) + u_y^2(\tau) \right) d\tau\\
    \text{s.t.} && &\dot{x}(t) = v(t), \quad x(0) = x_0, \quad x(t_f) = x_f\\
    && &\dot{v}(t) = u_x(t), \quad v(0) = v_0, \quad v(t_f) = v_f\\
    && &\dot{y}(t) = w(t), \quad y(0) = y_0, \quad y(t_f) = y_f\\
    && &\dot{w}(t) = u_y(t), \quad w(0) = w_0, \quad w(t_f) = w_f
  \end{aligned}
$$



Thee co-states, states, and optimal control all have the same forms as in the previous post with the caveat that the final time is unknown.

$$
  H[@t_f] = \frac{1}{2} \u_x^2(t_f) + \frac{1}{2} \u_y^2(t_f) + \lambda_x(t_f) v(t_f) + \lambda_v(t_f) u_x(t_f) + \lambda_y(t_f) w(t_f) + \lambda_w(t_f) u_y(t_f) = -\alpha
$$

$$
  -\frac{1}{2} \left(\lambda_x(0) t_f - \lambda_v(0) \right)^2 - \frac{1}{2} \left( \lambda_y(0) t_f - \lambda_w(0) \right)^2 + \lambda_x(0) \left(v_0 + \frac{1}{2} \lambda_x(0) t_f^2 - \lambda_v(0) t_f \right) + \lambda_y(0) \left( w_0 + \frac{1}{2} \lambda_y(0) t_f^2 - \lambda_w(0) t_f) \right) = -\alpha
$$
