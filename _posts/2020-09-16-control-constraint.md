---
title: 'Optimal Control Problem: Control Constraint'
date: 2020-09-16
permalink: /posts/2020/09/control-constraint/
tags:
  - optimal control
  - double integrator
  - constraints
---

# Control Constraints

$$
  \begin{aligned}
    \min && &J = \frac{1}{2} \int_0^{t_f} \left( u_x^2(t) + u_y^2(t) \right) dt\\
    \text{s.t.} && &\dot{x}(t) = v(t), \quad x(0) = x_0, \quad x(t_f) = x_f\\
    && &\dot{v}(t) = u_x(t), \quad v(0) = v_0, \quad v(t_f) = v_f\\
    && &\dot{y}(t) = w(t), \quad y(0) = y_0, \quad y(t_f) = y_f\\
    && &\dot{w}(t) = u_y(t), \quad w(0) = w_0, \quad w(t_f) = w_f\\
    && &-1 \leq u_x(t) \leq 1\\
    && &-1 \leq u_y(y) \leq 1
  \end{aligned}
$$

# Solution

$$
  \bar{H} = \frac{1}{2} u_x^2 + \frac{1}{2} u_y^2 + \lambda_x v + \lambda_v u_x + \lambda_y w + \lambda_w u_y + \mu_x u_x + \mu_y u_y
$$

### Stationarity Condition

$$
  \frac{\partial \bar{H}}{\partial u_x} = u_x + \lambda_v + \mu_x = 0 \quad \rightarrow \quad u_x(t) = -\lambda_v(t) - \mu_x(t)
$$

### Transversality Condition

$$
  \mu_x(t) \left\{ \begin{array}{lll}
    \leq 0 & \text{if} & u_x(t) = -1\\
    = 0 & \text{if} & -1 < u_x(t) < 1\\
    \geq 0 & \text{if} & u_x(t) = 1
  \end{array} \right.
$$

### Co-State Evolution

$$
  \dot{\lambda}_x(t) = -\frac{\partial \bar{H}}{\partial x} = 0 \quad \rightarrow \quad \lambda_x(t) = \lambda_x(0)
$$

$$
  \dot{\lambda}_v(t) = -\frac{\partial \bar{H}}{\partial v} = -\lambda_x(t) \quad \rightarrow \quad \lambda_v(t) = \lambda_v(0) - \lambda_x(0) t
$$

### Optimal Control Switching

$$
  u_x(t) = \lambda_x(0) t - \lambda_v(0) - \mu_x(t)
$$

Note that as \$ \lambda_v(t) \$ is a monotonic function, we can determine the switching structure to be one of the following,

1. -1 -> mid -> 1
2. mid -> 1
3. -1 -> mid
4. 1 -> mid -> -1
5. 1 -> mid
6. mid -> -1

### Existence of Control Input

When there are constraints applied to the control input, we must explore the _reachable set_.

The x-direction position and velocity attainable can be determined by applying the minimum and maximum control inputs for the whole duration \$ [0,t_f] \$.

$$
  \begin{aligned}
    v^{\min}(t) &= v_0 + \int_0^{t} u^{\min} dt = v_0 + u^{\min} t\\
    x^{\min} &= x_0 + \int_0^{t} v^{\min}(t) dt = x_0 + v_0 t + \frac{1}{2} u^{\min} t^2
  \end{aligned}
$$

$$
  \begin{aligned}
    v^{\max}(t) &= v_0 + \int_0^t u^{\max} dt = v_0 + u^{\max} t\\
    x^{\max}(t) &= x_0 + \int_0^t v^{\max}(t) dt = x_0 + v_0 t + \frac{1}{2} u^{\max} t^2
  \end{aligned}
$$

For this problem, the reachable values of the 


