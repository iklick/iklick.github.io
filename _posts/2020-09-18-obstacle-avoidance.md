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

$$
  \bar{H} = \frac{1}{2} u_x^2 + \frac{1}{2} u_y^2 + \lambda_x v + \lambda_v u_x + \lambda_y w + \lambda_w u_y + \mu_h \left[ (x(t) - c_x)^2 + (y(t)-c_y)^2 \right]
$$

$$
  \frac{\partial \bar{H}}{\partial u_x} = u_x + \lambda_v = 0 \quad \rightarrow \quad u_x(t) = -\lambda_v(t)
$$

$$
  \frac{d}{dt} \lambda_x(t) = -\frac{\partial \bar{H}}{\partial x} = -2 \mu_h (x - c_x)
$$

In this problem, we assume that the obstacle constraint is at least active at a single instant in time.
The question is whether the constraint is active for an instant or for a finite time interval.
I will assume the former is the correct solution without further detailed discussion, but the latter can be shown to be incorrect by contradiction.

Define the instance that the obstacle constraint is active as \$ t_1 \in [0,t_f] \$.
The co-obstacle multiplier is then,

$$
  \mu_h(t) \left\{ \begin{array}{lll}
    = 0 & \text{if} & (x(t)-c_x)^2 + (y(t)-c_y)^2 > r^2\\
    \leq 0 & \text{if} & (x(t)-c_x)^2 + (y(t)-c_y)^2 = r^2
  \end{array} \right.
$$

so we can write,

$$
  \mu_h(t) = A \delta(t_1)
$$

a delta-function activated at time \$ t_1 \$.
At the moment of activation, define the position offsets as,

$$
  \begin{aligned}
    x_1 &= (x(t_1) - c_x)\\
    y_1 &= (y(t_1) - c_y)
  \end{aligned}
$$

The co-position can now be integrated forward in time,

$$
  \lambda_x(t) = \left\{ \begin{array}{ll}
    \lambda_x(0) & t < t_1\\
    \lambda_x(0) - 2 A x_1 & t > t_1
  \end{array} \right.
$$

which is a step function.
With the co-position, the co-velocity can now be integrated as well,

$$
  \frac{d}{dt} \lambda_v(t) = -\frac{\partial \bar{H}}{\partial v} = -\lambda_x \quad \rightarrow \quad \lambda_v(t) = \left\{ \begin{array}{ll}
    \lambda_v(0) - \lambda_x(0) t & t < t_1\\
    \lambda_v(0) - \lambda_x(0) t + 2A x_1 (t-t_1) & t > t_1
  \end{array} \right.
$$

The optimal control is now known from the stationarity condition above,

$$
  u_x(t) = -\lambda_v(t) = \left\{ \begin{array}{ll}
    \lambda_x(0) t - \lambda_v(0) & t < t_1\\
    \lambda_x(0) t - \lambda_v(0) - 2Ax_1 (t-t_1) & t > t_1
  \end{array} \right.
$$

With the optimal control input, the velocity can be determined by integrating the optimal control,

$$
  v(t) = v(0) + \int_0^t u_x(\tau) d\tau = \left\{ \begin{array}{ll}
    v_0 + \frac{1}{2} \lambda_x(0) t^2 - \lambda_v(0) t & t < t_1\\
    v_0 + \frac{1}{2} \lambda_x(0) t^2 - \lambda_v(0) t - A x_1 (t-t_1)^2 & t > t_1
  \end{array} \right.
$$

With the optimal time-evolution of the velocity, the optimal time-evolution of the position can also be found by integrating forward the velocity,

$$
  x(t) = x(0) + \int_0^t v(\tau) d\tau) = \left\{ \begin{array}{ll}
    x_0 + v_0 t + \frac{1}{6} \lambda_x(0) t^3 - \frac{1}{2} \lambda_v(0) t^2 & t < t_1\\
    x_0 + v_0 t + \frac{1}{6} \lambda_x(0) t^3 - \frac{1}{2} \lambda_v(0) t^2 - \frac{1}{3} x_1 (t-t_1)^3 & t > t_1
  \end{array} \right.
$$

The same series of steps can be performed to arrive at the optimal solution in the y-direction,

$$
  \begin{aligned}
    \lambda_y(t) &= \left\{ \begin{array}{ll}
      \lambda_y(0) & t < t_1\\
      \lambda_y(0) - 2A y_1 & t > t_1
    \end{array} \right.\\
    \lambda_w(t) &= \left\{ \begin{array}{ll}
      \lambda_w(0) - \lambda_y(0) t & t < t_1\\
      \lambda_w(0) - \lambda_y(0) t + 2 A y_1 (t-t_1) & t > t_1
    \end{array} \right.\\
    u_y(t) &= \left\{ \begin{array}{ll}
      \lambda_y(0) t - \lambda_w(0) & t < t_1\\
      \lambda_y(0) t - \lambda_w(0) + 2 A y_1 (t-t_1) & t > t_1
    \end{array} \right.\\
    w(t) &= \left\{ \begin{array}{ll}
      w_0 + \frac{1}{2} \lambda_y(0) t^2 - \lambda_w(0) t & t < t_1\\
      w_0 + \frac{1}{2} \lambda_y(0) t^2 - \lambda_w(0) t + A y_1 (t-t_1)^2 & t > t_1
    \end{array} \right.\\
    y(t) &= \left\{ \begin{array}{ll}
      y_0 + w_0 t + \frac{1}{6} \lambda_y(0) t^3 - \frac{1}{2} \lambda_w(0) t^2 & t < t_1\\
      y_0 + w_0 t + \frac{1}{6} \lambda_y(0) t^3 - \frac{1}{2} \lambda_w(0) t^2 + \frac{1}{3} A y_1 (t-t_1)^3 & t > t_1
    \end{array} \right.
  \end{aligned}
$$ 

## Static Unknowns

The remaining unknowns are \$ \lambda_x(0) \$, \$ \lambda_v(0) \$, \$ \lambda_y(0) \$, \$ \lambda_w(0) \$, \$ A \$, and \$ t_1 \$.
To determine them, we set the final state constraints,

$$
  \begin{aligned}
    x_f &= x_0 + v_0 t_f + \frac{1}{6} \lambda_x(0) t_f^3 - \frac{1}{2} \lambda_v(0) t_f^2 - \frac{1}{3} A x_1 (t_f-t_1)^3\\
    y_f &= y_0 + w_0 t_f + \frac{1}{6} \lambda_y(0) t_f^3 - \frac{1}{2} \lambda_w(0) t_f^2 - \frac{1}{3} A y_1 (t_f-t_1)^3\\
    v_f &= v_0 + \frac{1}{2} \lambda_x(0) t_f^2 - \lambda_v(0) t_f - A x_1 (t_f-t_1)^2\\
    w_f &= w_0 + \frac{1}{2} \lambda_y(0) t_f^2 - \lambda_w(0) t_f - A y_1 (t_f-t_1)^2\\
    r^2 &= x_1^2 + y_1^2\\
    0 &= x_1 v(t_1) + y_1 w(t_1)
  \end{aligned}
$$

The last two equations come from the definition of the time \$ t_1 \$ and the fact that if \$ x_1^2 + y_1^2 = r^2 \$ then the velocity orthogonal to the obstacle must be zero (i.e. the velocity can not be moving the particle into or out of the obstacle's exclusion zone).

At the time of contact, we have the following definitions,

$$
  \begin{aligned}
    x_1 &= x_0 + v_0 t_1 + \frac{1}{6} \lambda_x(0) t_1^3 - \frac{1}{2} \lambda_v(0) t_1^2 - c_x\\
    y_1 &= y_0 + w_0 t_1 + \frac{1}{6} \lambda_y(0) t_1^3 - \frac{1}{2} \lambda_w(0) t_1^2 - c_y\\
    v(t_1) &= v_0 + \frac{1}{2} \lambda_x(0) t_1^2 - \lambda_v(0) t_1\\
    w(t_1) &= w_0 + \frac{1}{2} \lambda_y(0) t_1^2 - \lambda_w(0) t_1
  \end{aligned}
$$

Applying the states at the contact point of the obstacle's exclusion zone provides the full system of equations to solve for the static unknowns.

$$
  \begin{aligned}
    x_f - x_0 - v_0 t_f &= \frac{1}{6} \lambda_x(0) t_f^3 - \frac{1}{2} \lambda_v(0) t_f^2 - \frac{1}{3} A \left( x_0 + v_0 t_1 + \frac{1}{6} \lambda_x(0) t_1^3 - \frac{1}{2} \lambda_v(0) t_1^2 - c_x \right) (t_f-t_1)^3\\
    y_f - y_0 - w_0 t_f &= \frac{1}{6} \lambda_y(0) t_f^3 - \frac{1}{2} \lambda_w(0) t_f^2 - \frac{1}{3} A \left( y_0 + w_0 t_1 + \frac{1}{6} \lambda_y(0) t_1^3 - \frac{1}{2} \lambda_w(0) t_1^2 - c_y \right) (t_f-t_1)^3\\
    v_f - v_0 &= \frac{1}{2} \lambda_x(0) t_f^2 - \lambda_v(0) t_f - A \left( x_0 + v_0 t_1 + \frac{1}{6} \lambda_x(0) t_1^3 - \frac{1}{2} \lambda_v(0) t_1^2 - c_x \right) (t_f - t_1)^2\\
    w_f - w_0 &= \frac{1}{2} \lambda_y(0) t_f^2 - \lambda_w(0) t_f - A \left( y_0 + w_0 t_1 + \frac{1}{6} \lambda_y(0) t_1^3 - \frac{1}{2} \lambda_w(0) t_1^2 - c_y \right) (t_f - t_1)^2\\
    r^2 &= \left( x_0 + v_0 t_1 + \frac{1}{6} \lambda_x(0) t_1^3 - \frac{1}{2} \lambda_v(0) t_1^2 - c_x \right)^2 + \left( y_0 + w_0 t_1 + \frac{1}{6} \lambda_y(0) t_1^3 - \frac{1}{2} \lambda_w(0) t_1^2 - c_y \right)^2\\
    0 &= \left( x_0 + v_0 t_1 + \frac{1}{6} \lambda_x(0) t_1^3 - \frac{1}{2} \lambda_v(0) t_1^2 - c_x \right) \left( v_0 + \frac{1}{2} \lambda_x(0) t_1^2 - \lambda_v(0) t_1 \right) + \left( y_0 + w_0 t_1 + \frac{1}{6} \lambda_y(0) t_1^3 - \frac{1}{2} \lambda_w(0) t_1^2 - c_y \right) \left( w_0 + \frac{1}{2} \lambda_y(0) t_1^2 - \lambda_w(0) t_1 \right)
  \end{aligned}
$$

## Optimal Cost in Terms of Static Unknown

The optimal cost can now be expressed in terms of the static unknowns,

$$
  \begin{aligned}
    E_x &= \int_0^{t_f} u_x(t) dt = \int_0^{t_1} u_x(t) dt + \int_{t_1}^{t_f} u_x(t) dt\\
    &= \int_0^{t_1} \left( \lambda_x(0) t - \lambda_v(0) \right)^2 dt + \int_{t_1}^{t_f} \left( \lambda_x(0) t - \lambda_v(0) - 2 A x_1 (t-t_1) \right)^2 dt\\
    &= \int_0^{t_f} \left( \lambda_x^2(0) t^2 - 2 \lambda_x(0) \lambda_v(0) t + \lambda_v^2(0) \right) dt + \int_{t_1}^{t_f} \left( -4 \lambda_x(0) A x_1 t (t-t_1) + 4 \lambda_v(0) A x_1 (t-t_1) + 4 A^2 x_1^2 (t-t_1)^2 \right) dt\\
    &= \frac{1}{3} \lambda_x^2(0) t_f^3 - \lambda_x(0) \lambda_v(0) t_f^2 + \lambda_v^2(0) t_f 
  \end{aligned}
$$
