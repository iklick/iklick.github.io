---
title: 'Lyapunov Equation on a 2D Lattice'
date: 2021-01-27
permalink: /posts/2020/10/control-of-1d-lattices/
tags:
  - lattices
  - minimum energy control
---

## Motivation

This problem arose during my graduate studies when I was investigating the optimal control of linear systems which could be described by lattices.
By a lattice, I mean a system which is **translationally invariant**, or in other words, every section of the system looks the exactly like every other section.

Also, I enjoyed the journey this problem took me on, a journey that involved many different types of mathematics I hadn't used in my research.
It is a bit of a long journey but I hope to highlight useful concepts and some personal struggles that every STEM graduate student probably experiences at least a few times.

## The Lyapunov Equation

The equation of interest can be shown visually on the square lattice.

Each point is described by a time-changing variable, denoted $$ W_{j,k}(t) $$, for each lattice point $$ (j,k) \in \mathbb{Z}^2 $$.
There is flow between each grid point with rate $$ s^+ $$ to the right and up and $ s^- $$ to the left and down.
There is also absorption at each lattice point of rate $$ -2p $$.
Finally, there is a set of lattice points along the main diagonal, denoted $$ \mathcal{D} \subset \mathbb{Z} $$, that can be thought of as sources.

Putting together these components, the time evolution of the lattice point variables is described by the following linear differential equation,

$$
  \frac{d}{dt} W_{j,k}(t) = -2p W_{j,k}(t) + s^+ W_{j-1,k}(t) + s^- W_{j+1,k}(t) + s^+ W_{j,k-1}(t) + s^- W_{j,k+1}(t) + \sum_{a \in \mathcal{D}} \delta_{j,a} \delta_{k,a}
$$

where $$ \delta_{j,k} = 1 $$ if and only if $$ j = k $$.

### Laplace Transform

The first step to solving most linear differential equations is to apply the **Laplace transform**.

Define 

$$
  \tilde{W}_{j,k}(\epsilon) = \int_0^{\infty} W_{j,k}(t) e^{-\epsilon t} dt
$$

The transformed lattice equation is now,

$$
  (\epsilon + 2p) \tilde{W}_{j,k}(t) = s^+ \tilde{W}_{j+1,k}(\epsilon) + s^- \tilde{W}_{j-1,k}(\epsilon) + s^+ \tilde{W}_{j,k+1}(\epsilon) + s^- \tilde{W}_{j,k-1}(\epsilon) + \frac{1}{\epsilon} \sum_{a \in \mathcal{D}} \delta_{a,j} \delta_{a,k}
$$

This is a system of linear algeraic equations in $$ \tilde{W}_{j,k} (\epsilon) $$ rather then the linear differential equations before.

### Discrete Time Fourier Transform

A useful tool to try when solving equations on periodic or lattice-like structures is the Discrete Fourier Transform.

In this problem, as the indices over all integers, I decided to use the Discrete Time Fourier Transform (DTFT) over two variables defined as,

$$
  \hat{\tilde{W}}(x,y;\epsilon) = \sum_{j,k \in \mathbb{Z}} e^{-ijx} e^{-iky} \tilde{W}_{j,k}(\epsilon)
$$

The usefulness of the DTFT comes from the fact we can shift the summation indices.
Consider the single indexed variable $$ A_j $$ defined for all $$ j \in \mathbb{Z} $$ and its DTFT $$ \hat{A}(x) = \sum_{j \in \mathbb{Z}} e^{-ijx} A_j $$.

$$
  \sum_{j \in \mathbb{Z}} e^{-i j x} A_{j + \ell} = e^{ i \ell x} \sum_{j \in \mathbb{Z}} e^{-i (j+\ell) x} A_{j+\ell} = e^{i \ell x} \sum_{j \in \mathbb{Z}} e^{ -i j x} A_j = e^{i \ell x} \hat{A}(x)
$$

Using this identity, the Laplace transformed equation becomes,

$$
  (\epsilon + 2p) \hat{\tilde{W}}(x,y;\epsilon) = \left( s^+ e^{ix} + s^- e^{-ix} + s^+ e^{iy} + s^- e^{-iy} \right) \hat{\tilde{W}}(x,y;\epsilon) + \frac{1}{\epsilon} \sum_{a \in \mathcal{D}} e^{-i a x} e^{-i a y}
$$

Define the variables $$ s_1 $$ and $$ s_2 $$ by the relations,

$$
  s^+ = s_1 + s_2 && s^- = s_1 - s_2
$$

Using the trignometric identities 

$$
  \begin{aligned}
    2i \sin \theta = e^{i\theta} - e^{-i \theta} && 2 \cos \theta = e^{i\theta} + e^{i \theta}
  \end{aligned}
$$

and collecting the terms that multiply the transformed lattice variables,

$$
  \left( \epsilon + 2p - 2s_1 \cos x - 2 i s_2 \sin x - 2 s_1 \cos y - 2i s_2 \sin y \right) \hat{\tilde{W}}(x,y;\epsilon) = \frac{1}{\epsilon} \sum_{a \in \mathcal{D}} e^{-i a x} e^{-i a y}
$$

### Inverse Discrete Time Fourier Transform

### Inverse Laplace Transform


