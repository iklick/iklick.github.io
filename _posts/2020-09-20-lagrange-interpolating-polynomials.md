---
title: 'Lagrange Interpolating Polynomials'
date: 2020-10-05
permalink: /posts/2020/10/lagrange-interpolating-polynomials/
tags:
  - optimal control
  - numerical methods
  - nonlinear programming
  - optimization
---

## Previous Discretization Method

In a previous post, the integrals that appear in an optimal control problem were approximated using a quadrature.
While this leads to a very sparse structure in the NLP, the solution provided consists only of values at discrete points in time with no obvious choice for how to inteprolate between the points.

An alternative method discussed here is to pre-define the interpolation for the states and controls and then use the approximate forms to develop approximations.
