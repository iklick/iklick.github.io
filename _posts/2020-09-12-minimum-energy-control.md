---
title: 'Minimum Energy Control'
date: 2020-09-12
permalink: /posts/2020/09/minimum-energy-control/
tags:
  - optimal control
  - minimum energy control
  - linear systems
---

## Background

Linear (and time-invariant) dynamical systems are ubiquitous in the field of control because 

1. they are (at least approximately) representative of many real systems and
2. they are the most analytically tractable.

In the system, there are \$ n \$ states which completely define the system, \$ m \$ control inputs which we will design, and \$ p \$ outputs which are quantites that we care about in some sense.

The system of differential equations that describes the evolution of the states is,

$$
  \dot{x}(t) = A x(t) + B u(t), \quad x(t_0) = x_0, \quad t \geq t_0
$$

where \$ x(t) \in \mathbb{R}^n \$ is the value of the states and \$ u(t) \in \mathbb{R}^m \$ is the value of the control inputs at time \$ t \$.
At the initial time, designated \$ t_0 \$, the state vector is known completely, called the initial condition.
The state matrix \$ A \in \mathbb{R}^{n \times n} \$ describes the dependencies on the evolution of states and the input matrix \$ B \in \mathbb{R}^{n \times m} \$ describes the effect our external control inputs have on the system.

The output of the system is a set of linear combinations of the states,

$$
  y(t) = C x(t)
$$

where the matrix \$ C \in \mathbb{R}^{p \times n} \$ describes which linear combinations of the states constitute the outputs.
We are interested in deriving a control input that drives this linear system from the prescribed initial condition to some desired output, \$ y(t_f) = y_f \$, for a prescribed final time \$ t_f > t_0 \$.

The first question one must ask is whether there exists any control input \$ u : [t_0,t_f] \mapsto \mathbb{R}^m \$ to drive the system from an initial condition \$ x_0 \$ to the final output, \$ y_f \$.

> **Definition: Output Controllability**
>
> A linear system, \$ (A,B,C) \$, is _output controllable_ if there exists a control input which drives the linear system from any initial condition \$ x(t_0) = x_0 \$ to any final output \$ y(t_f) = y_f \$ for any time duration \$ [t_0,t_f] \$.

The sufficient and necessary conditions for \$ (A,B,C) \$ to be _output controllable_ will be investigated in the below derivation.

If the system \$ (A,B,C) \$ is output controllable, then there is an infinite amount of possible inputs to drive the system from the initial condition \$ x_0 \$ to the final output \$ y_f \$ which will also be demonstrated.

As the designer, we must select which of these inputs to choose based on any number of potential criteria:

1. Energy consumption (how much fuel is available?)
2. Saturation of the actuator (are there values \$ u^L \leq u(t) \leq u^U \$ that bound the input?)
3. Locality of the states (are there values \$ x^L \leq x(t) \leq x^U \$ that bound the states?)

**Optimal control** provides a framework for choosing among the infinite set of control inputs an input that can satisfy these constraints while minimizing some cost.

## Minimum Energy Control Problem

This post is a first application of the [general optimal control framework](/posts/2020/09/optimal-control-framework/) presented in the previous post.
The minimum energy control problem is special as it admits a simple closed-form solution (a rare occurence in the field of optimal control).

$$
  \begin{aligned}
    \min && &J = \frac{1}{2} \int_{t_0}^{t_f} u^T(t) R u(t) dt\\
    \text{s.t.} && &\dot{x}(t) = A x(t) + B u(t)\\
    && &x(t_0) = x_0, \quad C x(t_f) = y_f
  \end{aligned}
$$

### Determine Problem Dimensions

In the MEC problem, there are \$ n \$ states, \$ m \$ controls and \$ p + n \$ events. 
There are no path constraints.

### Constructing the Hamiltonian

The Hamiltonian of the MEC is,

$$
  H = \frac{1}{2} u^T R u^T + \lambda^T \left( A x + B u \right)
$$

and the end-point Lagrangian is,

$$
  \bar{E} = \nu^T \left[ \begin{array}{cc} x(t_0) - x_0 \\ C x(t_f) - y_f \end{array} \right] = \nu_0^T \left( x(t_0) - x_0 \right) + \nu_f^T \left( C x(t_f) - y_f \right)
$$

### Stationarity Condition

The first step is to determine the optimal control input from the stationarity condition,

$$
  0 = \frac{\partial H}{\partial u} = R u^*(t) + B^T \lambda^* \quad \rightarrow \quad u^*(t) = -R^{-1} B^T \lambda^*(t)
$$

From the stationarity condition, the optimal input is a linear combination of the co-states which guides the next step.

### Solving for the Co-States

From the Hamiltonian, the evolution of the costates can be determined,

$$
  \dot{\lambda}(t) = - \frac{\partial H}{\partial x} = -A^T \lambda (t), \quad \lambda(t_f) = \frac{\partial \bar{E}}{\partial x(t_f)} = C^T \nu_f
$$

The solution of this linear system of equations is,

$$
  \lambda(t) = e^{-A(t-t_f)} C^T \nu_f = e^{A^T(t_f-t)} C^T \nu_f
$$

This expression can be plugged into the optimal input to cast the unknown time-varying input into a set of \$ p \$ unknown variables, \$ \nu_f \$.

$$
  u^*(t) = -R^{-1} B^T e^{A^T(t_f-t)} C^T \nu_f
$$

### Optimal State Trajectory

With the optimal input, the optimal state trajectory can be determined,

$$
  x^*(t) = e^{A(t-t_0)} x_0 + \int_{t_0}^t e^{A (t-\tau)} B u^*(\tau) d\tau = e^{A(t-t_0)} x_0 - \int_{t_0}^t e^{(t-\tau)} BR^{-1}B^T e^{A^T(t_f-\tau)} C^T \nu_f
$$

which can be re-expressed in a more succinct form as,

$$
  x^*(t) = e^{A(t-t_0)} x_0 - W(t-t_0) e^{A^T(t_f-t)} C^T \nu_f
$$

where the matrix \$ W(t) \$ is the _controllability Gramian_,

$$
  W(t) = \int_0^t e^{A \tau} BR^{-1} B^T e^{A^T \tau} d\tau
$$

The above expression for \$ W(t) \$ is the formal solution of the _differential Lyapunov equation_,

$$
  \dot{W}(t) = A^T W(t) + W(t) A + BR^{-1}B^T
$$

Some more details about the controllability Gramian and its properties are contained in [ADD POST HERE].

Applying the final output condition yields the equation for the unknowns,

$$
  y_f = C e^{A(t_f-t_0)} x_0 - C W(t_f-t_0) C^T \nu_f \quad \rightarrow \quad \nu_f = \left(C W(t_f-t_0) C^T \right)^{-1} \left( C e^{A(t_f-t_0)} x_0 - y_f \right)
$$

With the unknowns determined, the optimal control input is now fully defined in terms of known quantities,

$$
  u^*(t) = R^{-1} B^T e^{A^T(t_f-t)} C^T \left( C W(t_f-t_0) C^T \right)^{-1} \left( y_f - C e^{A(t_f-t_0)} x_0 \right)
$$

as is the optimal state trajectory

$$
  x^*(t) = e^{A(t-t_0)} x_0 + W(t-t_0) e^{A^T(t_f-t)} C^T \left( C W(t_f-t_0) C^T \right)^{-1} \left( y_f - C e^{A(t_f-t_0)} x_0 \right)
$$

### Optimal Cost

Returning to the original cost,

$$
  J = \frac{1}{2} \int_{t_0}^{t_f} u^T(t) u(t) dt = \frac{1}{2} \beta^T \left( C W(t_f-t_0) C^T \right)^{-1} C \int_{t_0}^{t_f} 
$$
