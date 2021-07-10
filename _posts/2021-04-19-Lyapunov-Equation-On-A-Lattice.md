---
title: 'Lyapunov Equation on a 2D Lattice'
date: 2021-04-19
permalink: /posts/2021/04/control-of-1d-lattices/
tags:
  - lattices
  - minimum energy control
---

## Motivation

This problem arose during my graduate studies when I was investigating the optimal control of linear systems which could be described by lattices.
By a lattice, I mean a system which is **translationally invariant**, or in other words, every section of the system looks the exactly like every other section.

Also, I enjoyed the journey this problem took me on, a journey that involved many different types of mathematics I hadn't used in my research.
It is a bit of a long trip but I hope to highlight useful concepts and some personal struggles that every STEM graduate student probably experiences at least a few times.

## The Lyapunov Equation

The equation of interest can be shown visually on the square lattice.

Each point is described by a time-changing variable, denoted $$ W_{j,k}(t) $$, for each lattice point $$ (j,k) \in \mathbb{Z}^2 $$.
There is flow between each grid point with rate $$ s^+ $$ to the right and up and $$ s^- $$ to the left and down.
There is also absorption at each lattice point of rate $$ -2p $$.
Finally, there is a set of lattice points along the main diagonal, denoted $$ \mathcal{D} \subset \mathbb{Z} $$, that can be thought of as sources.

Putting together these components, the time evolution of the lattice point variables is described by the following linear differential equation,

$$
  \begin{aligned}
  \frac{d}{dt} W_{j,k}(t) &= -2p W_{j,k}(t) + s^+ W_{j-1,k}(t) + s^- W_{j+1,k}(t)\\
  &+ s^+ W_{j,k-1}(t) + s^- W_{j,k+1}(t) + \sum_{a \in \mathcal{D}} \delta_{j,a} \delta_{k,a}
  \end{aligned}
$$

where $$ \delta_{j,k} = 1 $$ is the Kronecker delta and $$ W_{j,k}(0) = 0 $$.

<video controls autoplay controls loop style="text-align: center">
  <source src="/images/1dgram-gram.mp4">
</video>

In the above video, the values of $$ W_{j,k}(t) $$ is simulated for $$ p = 3.0 $$, $$ s^+ = 1.5 $$, $$ s^- = 0.5 $$, and $$ \mathcal{D} = \{ 0 \} $$.
For $$ t = 0 $$ to $$ t = 0.02 $$, the video steps by $$ 0.001 $$ each frame and then for $$ t= 0.02 $$ to $$ t = 1 $$ the video steps by $$ 0.01 $$ each frame.

For the case $$ p > s^+ + s^- $$, we see that $$ W_{j,k}(t) $$ converges as $$ t \rightarrow \infty $$.

To solve for $$ W_{j,k}(t) $$, the following steps are taken:

  1. Use the Laplace transform to express the differential equations as algebraic equations.
  2. Use the discrete time Fourier transform (DTFT) to decouple the differential equations.
  3. Solve the individual equations.
  4. Apply the inverse DTFT.
  5. Apply the inverse Laplace transform.


### Laplace Transform

The first step when solving most linear differential equations is to apply the **Laplace transform**.
This is because the Laplace transform of derivatives of a function become algebraic expressions of the transformed function.

Define 

$$
  \tilde{W}_{j,k}(\epsilon) = \int_0^{\infty} W_{j,k}(t) e^{-\epsilon t} dt
$$

The derivative after transformation is,

$$
  \int_0^{\infty} \frac{d W_{j,k}(t)}{dt} e^{-\epsilon t} dt = \epsilon \tilde{W}_{j,k}(\epsilon) - W_{j,k}(0)
$$

The transformed lattice equation is now,

$$
  \begin{aligned}
  (\epsilon + 2p) \tilde{W}_{j,k}(\epsilon) &= s^+ \tilde{W}_{j+1,k}(\epsilon) + s^- \tilde{W}_{j-1,k}(\epsilon) + s^+ \tilde{W}_{j,k+1}(\epsilon)\\
  &+ s^- \tilde{W}_{j,k-1}(\epsilon) + \frac{1}{\epsilon} \sum_{a \in \mathcal{D}} \delta_{a,j} \delta_{a,k}
  \end{aligned}
$$

This is a system of coupled linear algebraic equations in $$ \tilde{W}_{j,k} (\epsilon) $$.

### Discrete Time Fourier Transform

A useful tool to try when solving equations on periodic or lattice-like structures is the Discrete Fourier Transform.

In this problem, as the indices are over all integers, I decided to use the Discrete Time Fourier Transform (DTFT) over two variables defined as,

$$
  \hat{\tilde{W}}(x,y;\epsilon) = \sum_{j,k \in \mathbb{Z}} e^{-ijx} e^{-iky} \tilde{W}_{j,k}(\epsilon)
$$

where the summation is over all pairs of integers.
The usefulness of the DTFT comes from the fact we can shift the summation indices.
Consider the single indexed variable $$ A_j $$ defined for all $$ j \in \mathbb{Z} $$ and its DTFT $$ \hat{A}(x) = \sum_{j \in \mathbb{Z}} e^{-ijx} A_j $$.

$$
  \sum_{j \in \mathbb{Z}} e^{-i j x} A_{j + \ell} = e^{ i \ell x} \sum_{j \in \mathbb{Z}} e^{-i (j+\ell) x} A_{j+\ell} = e^{i \ell x} \sum_{j \in \mathbb{Z}} e^{ -i j x} A_j = e^{i \ell x} \hat{A}(x)
$$

Using this identity, the Laplace transformed equation becomes,

$$
  \begin{aligned}
  (\epsilon + 2p) \hat{\tilde{W}}(x,y;\epsilon) &= \left( s^+ e^{ix} + s^- e^{-ix} + s^+ e^{iy} + s^- e^{-iy} \right) \hat{\tilde{W}}(x,y;\epsilon)\\
  &+ \frac{1}{\epsilon} \sum_{a \in \mathcal{D}} e^{-i a x} e^{-i a y}
  \end{aligned}
$$

Define the variables $$ s_1 $$ and $$ s_2 $$ by the relations,

$$
  \begin{aligned}
    s^+ = s_1 + s_2 && \text{and} && s^- = s_1 - s_2
  \end{aligned}
$$

Using the trignometric identities 

$$
  \begin{aligned}
    2i \sin \theta = e^{i\theta} - e^{-i \theta} && \text{and} && 2 \cos \theta = e^{i\theta} + e^{i \theta}
  \end{aligned}
$$

and collecting the terms that multiply the transformed lattice variables,

$$
  \left( \epsilon + 2p - 2s_1 \cos x - 2 i s_2 \sin x - 2 s_1 \cos y - 2i s_2 \sin y \right) \hat{\tilde{W}}(x,y;\epsilon) = \frac{1}{\epsilon} \sum_{a \in \mathcal{D}} e^{-i a x} e^{-i a y}
$$

Finally, we can rearrange to isolate the lattice variables of interest,

$$
  \hat{\tilde{W}}(x,y;\epsilon) = \sum_{a \in \mathcal{D}} \frac{1}{\epsilon} \frac{e^{-iax} e^{-iay}}{\epsilon + 2p - 2s_1 \cos x - 2i s_2 \sin x - 2 s_1 \cos y - 2 i s_2 \sin y}
$$

All that remains is to apply the inverses of the transformations taken!

### Inverse Discrete Time Fourier Transform

The inverse of the DTFT is found using the integral,

$$
  \tilde{W}_{j,k}(\epsilon) = \frac{1}{4 \pi^2} \int_{-\pi}^{\pi} \int_{-\pi}^{\pi} e^{i j x} e^{i k y} \hat{\tilde{W}}(x,y;\epsilon) dx dy
$$

Applying this inverse transform to the previous expression yields,

$$
  \tilde{W}_{j,k}(\epsilon) = \sum_{a \in \mathcal{D}} \frac{1}{4 \epsilon \pi^2} \int_{-\pi}^{\pi} \int_{-\pi}^{\pi} \frac{e^{i (j-a) x} e^{i (k-a) y}}{\epsilon + 2p - 2s_1 \cos x - 2i s_2 \sin x - 2 s_1 \cos y - 2i s_2 \sin y} dx dy
$$

### Inverse Laplace Transform

The inverse Laplace transform is found using the identity,

$$
  \mathcal{L}^{-1} \left\{ \frac{1}{\epsilon (\epsilon + b)} \right\} = \int_0^t e^{-b \tau} dt
$$

This yields the expression,

$$
  \begin{aligned}
  W_{j,k}(t) &= \sum_{a \in \mathcal{D}} \frac{1}{4\pi^2} \int_0^t \int_{-\pi}^{\pi} \int_{-\pi}^{\pi} e^{i(j-a)x} e^{i(k-a)y} e^{-2p\tau} e^{2s_1 \tau \cos x} e^{2i s_2 \tau \sin x} e^{2s_1 \tau \cos y} e^{2i s_2 \tau \sin y} dx dy d\tau\\
  &= \sum_{a \in \mathcal{D}} \int_0^t e^{-2p\tau} \left[ \frac{1}{2\pi} \int_{-\pi}^{\pi} e^{2 s_1 \tau \cos x} e^{i((j-a)x + 2s_2 \tau \sin x)} dx \right] \left[ \frac{1}{2\pi} \int_{-\pi}^{\pi} e^{2s_1 \tau \cos y} e^{i((k-a)y + 2s_2 \tau \sin y} dy \right] d\tau\\
  &= \sum_{a \in \mathcal{D}} \int_0^t e^{-2p\tau} A_{j-a}(\tau) A_{k-a}(\tau) d\tau
  \end{aligned}
$$

where the expression,

$$
  \begin{aligned}
    A_m(\tau) &= \frac{1}{2\pi} \int_{-\pi}^{\pi} e^{2s_1 \tau \cos x} e^{i(m x + 2s_2 \tau \cos x)} dx\\
    &= \frac{1}{2\pi} \int_{-\pi}^{\pi} e^{2s_1 \tau \cos x} \left(\cos (mx + 2 s_2 \tau \sin x) + i \sin (mx + 2 s_2 \sin x) \right) dx\\
    &= \mathcal{R}(A_m(\tau)) + i \mathcal{I}(A_m(\tau))
  \end{aligned}
$$

The imaginary part of $$ A_m(\tau) $$ is equal to zero which can be shown by showing the integrand is an odd function and noting the integral is symmetric about $$ x = 0 $$.

$$
  A_m(\tau) = \frac{1}{2\pi} \int_{-\pi}^{\pi} e^{2s_1 \tau \cos x} \cos \left(mx + 2s_2 \tau \sin x \right) dx
$$

Note here that the integrand is an **even function** so the symmetric integral can be reduced to just half of the interval,

$$
  A_m(\tau) = \frac{1}{\pi} \int_0^{\pi} e^{2s_1 \tau \cos x} \cos \left( mx + 2s_2 \tau \sin x \right) dx
$$

At this point, we could either numerically integrate this expression, or look to express it in terms of **special functions**.

### Difficult Integrals

Anyone who has taken college level math courses in the last 10 years would look at this difficult integral and plug it into WolframAlpha.
Unfortunately, WolframAlpha fails at this integral as it (probably) requires specialized techniques to solve.

Instead, we turn to the mid century technique of cracking open an enormous tome of integral tables, in this case, Gradshteyn and Ryzhik.

The technique to use integral tables well is to:
1. Try and classify your own integral in terms of the types of functions that appear.
2. Search for an integral that has the same structure just perhaps with extra parameters or different integral bounds.
3. Use substitutions to rewrite your integral to match the integral in the table and write the solution in terms of your substitutions.

For this integral
1. The **definite** integral contains **exponentials of trigonometric** and **trigonometric** functions. 
2. That classification suggests looking in section 3.93 where we find identity 3.937.2
    $$
      \begin{aligned}
      &\int_0^{2\pi} \exp \left( p \cos x + q \sin x \right) \cos \left( a \cos x + b \sin x - nx \right) dx\\
      &= \pi \left[(b-p)^2 + (a+q)^2\right]^{-\frac{n}{2}} \left\{ (A+iB)^{\frac{n}{2}} I_n\left( \sqrt{C-iD} \right) + (A-iB)^{\frac{n}{2}} I_n \left( \sqrt{C + iD} \right) \right\}
      \end{aligned}
    $$
    
    with the caveats
    
    $$
      \begin{aligned}
        (b-p)^2 + (a+q)^2 > 0 && \text{and} && n \in \mathbb{N}
      \end{aligned}
    $$
    
    and the definitions
    
    $$
      \begin{aligned}
        A &= p^2 - q^2 + a^2 - b^2\\
        B &= 2(pq+ab)\\
        C &= p^2 + q^2 - a^2 - b^2\\
        D &= 2(ap+bq)
      \end{aligned}
    $$
   
3. Comparing the integrals, we first adjust the bounds of integration. To do this, we note that the integrand is symmetric about $$ x = \pi $$ because

    $$ 
      \begin{aligned}
      \cos (\pi - y) = \cos (\pi + y) && \text{and} && \sin (\pi - y) = -\sin (\pi + y)
      \end{aligned}
    $$
    
    so the inegral can be rewritten
    
    $$
      \begin{aligned}
      A_m(\tau) &= \frac{1}{2\pi} \int_0^{2\pi} e^{2 s_1 \tau \cos x} \cos \left( mx + 2s_2 \tau \sin x \right) dx\\
      &= \frac{1}{2\pi} \int_0^{2\pi} e^{2s_1 \tau \cos x} \cos \left( -2s_2 \tau \sin x - mx \right) dx
      \end{aligned}
    $$
    
    Comparing forms, the variables are,
    
    $$
      \begin{aligned}
        p &= 2s_1 \tau && q = 0\\
        a &= 0 && b = -2s_2 \tau
      \end{aligned}
    $$
    
    so the result variables are,
    
    $$
      \begin{aligned}
        A &= (2s_1 \tau)^2 - (2s_2 \tau)^2 = 4 \tau^2 (s_1^2 - s_2^2)\\
        &= 4 \tau^2 \left( \left( \frac{s_+ + s_-}{2} \right)^2 - \left( \frac{s_+ - s_-}{2} \right)^2 \right)\\
        &= 4 s_+ s_- \tau^2 
      \end{aligned}
    $$
    
    As $$ q = a = 0 $$ we have $$ B = D = 0 $$ and $$ C = A $$.
    Additionally, I will simplify,
    
    $$
      (b-p)^2 = 4 \tau^2 (s_2 - s_1)^2 = 4 \tau^2 s_-^2
    $$
    
    Plugging in these results, the integral can be written,
    
    $$
      \begin{aligned}
        A_m(\tau) &= \frac{1}{2\pi} \int_0^{2\pi} e^{2s_1 \tau \cos x} \cos \left(-2s_2 \tau \sin x - m x \right) dx\\
        &= \frac{1}{2} \left[ 4 \tau^2 s_-^2 \right]^{-m/2} \left\{ 2 (4 s_+ s_- \tau^2)^{m/2} I_m \left(4 \sqrt{s_+ s_-} \tau \right) \right\}\\
        &= \left( \frac{1}{(2 s_- \tau)^m} \right) (2 \sqrt{s_+ s_-} \tau)^m I_m (4 \sqrt{s_+ s_-} \tau)\\
        &= \left( \frac{s_+}{s_-} \right)^{\frac{m}{2}} I_m\left(2 \sqrt{s_+ s_-} \tau \right)
      \end{aligned}
    $$
    
With this result, we can construct the solution to the original system of differential equations.

$$
  W_{j,k}(t) = \sum_{a \in \mathcal{D}}  \left( \frac{s_+}{s_-} \right)^{\frac{j+k-2a}{2}}  \int_0^t e^{-2p\tau} I_{j-a}(2 \sqrt{s_+ s_-} \tau) I_{k-a}(2 \sqrt{s_+ s_-} \tau) d\tau
$$

Thus, the original infinite system of coupled differential equations on the two dimensional lattice has been solved exactly in terms of an integral.

Before going any further in analyzing the properties of this function, it is verified that it is correct by differentiating it with respect to time.

## Verifying a Solution

When attempting to solve a difficult equation that required many steps, the obvious question is whether our proposed solution is correct.
This can be done easily enough by substituting our solution into the original equation.

First, a substitution is used to rewrite the solution with $$ \tau = t - \sigma $$

$$
  W_{j,k}(t) = \sum_{a \in \mathcal{D}} \left( \frac{s_+}{s_-} \right)^{\frac{j+k-2a}{2}} \int_0^t e^{-2p(t-\sigma)} I_{j-a} (2 \sqrt{s_+ s_-} (t-\sigma)) I_{k-a} (2 \sqrt{s_+ s_-} (t-\sigma)) d\sigma
$$

$$
  \begin{aligned}
    \frac{d}{dt} W_{j,k}(t) &= \sum_{a \in \mathcal{D}} \left( \frac{s_+}{s_-} \right)^{\frac{j+k-2a}{2}} \frac{d}{dt} \int_0^t e^{-2p(t-\sigma)} I_{j-a} (2\sqrt{s_+ s_-} (t-\sigma)) I_{k-a} (2\sqrt{s_+ s_-} (t-\sigma)) d\sigma\\
    &= \sum_{a \in \mathcal{D}} \left( \frac{s_+}{s_-} \right)^{\frac{j+k-2a}{2}} \left[I_{j-a}(0) I_{k-a}(0) + \int_0^t \frac{d}{dt} e^{-2p(t-\sigma)} I_{j-a}(2 \sqrt{s_+ s_-} (t-\sigma)) I_{k-a} (2 \sqrt{s_+ s_-} (t-\sigma) d\sigma \right]\\
    &= \sum_{a \in \mathcal{D}} \left( \frac{s_+}{s_-} \right)^{\frac{j+k-2a}{2}} \left[ \delta_{j,a} \delta_{k,a} + \int_0^t \left(-2p e^{-2p(t-\sigma)} I_{j-a}(2 \sqrt{s_+ s_-} (t-\sigma)) I_{k-a}(2\sqrt{s_+ s_-}(t-\sigma)) \right. \right.\\
     &+ \left. \left. \frac{2 \sqrt{s_+ s_-}}{2} e^{-2p(t-\sigma)} \left( I_{j-a-1}(2 \sqrt{s_+ s_-} (t-\sigma)) + I_{j-a+1}(2\sqrt{s_+ s_-}(t-\sigma)) \right) I_{k-a} (2 \sqrt{s_+ s_-} t) \right. \right.\\
     &+ \left. \left. \frac{2 \sqrt{s_+ s_-}}{2} e^{-2p(t-\sigma)} I_{j-a}(2 \sqrt{s_+ s_-} (t-\sigma)) \left( I_{k-a-1}(2\sqrt{s_+ s_-} (t-\sigma)) I_{k-a+1}(2\sqrt{s_+ s_-}(t-\sigma)) \right) \right) d\sigma \right]\\
     &= \sum_{a \in \mathcal{D}} \delta_{j,a} \delta_{k,a} - 2p W_{j,k}(t)\\
     &+ \sum_{a \in \mathcal{D}} s_+ \left( \frac{s_+}{s_-} \right)^{\frac{(j-1)+k-2a}{2}} \int_0^t e^{-2p(t-\sigma)} I_{j-1-a}(2 \sqrt{s_+ s_-} (t-\sigma)) I_{k-a}(2\sqrt{s_+ s_-} (t-\sigma)) d\sigma 
  \end{aligned}
$$

## Steady State Solution

For values of $$ p > s^+ + s^- $$, $$ W_{j,k}(t) $$ converges to $$ \bar{W}_{j,k} $$, some constant value. 
To find this value, we take the limit $$ t \rightarrow \infty $$ so we try to evaluate,

$$
  \bar{W}_{j,k} = \sum_{a \in \mathcal{D}} \left( \frac{s_+}{s_-} \right)^{\frac{j+k-2a}{2}} \int_0^{\infty} e^{-2p\tau} I_{|j-a|}(2 \sqrt{s_+ s_-} \tau) I_{|k-a|}(2 \sqrt{s_+ s_-} \tau) d\tau
$$

Wolfram-Alpha cannot solve this integral either so again I turn to the integral tables.

The closest that I can find is identity 6.612.3 which states,

$$
  \int_0^{\infty} e^{-\alpha x} J_{\nu}(\beta x) J_{\nu}(\gamma x) dx = \frac{1}{\pi \sqrt{\gamma \beta}} Q_{\nu-\frac{1}{2}} \left( \frac{\alpha^2 + \beta^2 + \gamma^2}{2 \beta \gamma} \right)
$$

with the caveats $$ \mathcal{R}(\alpha \pm i \beta \pm i \gamma) > 0 $$, $$ \gamma > 0 $$, and $$ \mathcal{R}(\nu) > - \frac{1}{2} $$.

In our integral, we additionally have $$ \beta = \gamma $$ and $$ \alpha = 2p $$ which allows us to simplify the identity,

$$
  \int_0^{\infty} e^{-2p x} J_{\nu}^2 (\beta x) dx = \frac{1}{\pi \beta} Q_{\nu - \frac{1}{2}} \left( \frac{ 2 p^2 + \beta^2}{\beta^2} \right)
$$

Note that this identity can only be applied to the __diagonal__ elements, that is, when $$ j = k $$.

To rewrite the integral of interest like the one in the table, we first must use the connection formula between Bessel functions.

$$
  I_{\nu}(z) = e^{-i \nu \pi / 2} J_{\nu}(i z)
$$

This allows us to rewrite the steady state values in terms of Bessel functions,

$$
  \bar{W}_{j,k} = \sum_{a \in \mathcal{D}} \left( \frac{s_+}{s_-} \right)^{\frac{j+k-2a}{2}} e^{-i (|j-a| + |k-a|) \pi/2} \int_0^{\infty} e^{-2p\tau} J_{|j-a|} (2i \sqrt{s_+ s_-} \tau) J_{|k-a|} (2i \sqrt{s_+ s_-} \tau) d\tau
$$

Diagonal elements can be simplified to,

$$
  \bar{W}_{j,j} = \sum_{a \in \mathcal{D}} \left( \frac{s_+}{s_-} \right)^{j-a} e^{-i |j-a| \pi} \int_0^{\infty} e^{-2p\tau} J_{|j-a|}^2(2i \sqrt{s_+ s_-} \tau) d\tau
$$

Comparing forms, we have the following definitions $$ \beta = 2i \sqrt{s_+ s_-} $$.

Let us consider now the case $$ \mathcal{D} = \{ 0 \} $$.
To differentiate this case, we rewrite,

$$
  \begin{aligned}
    \bar{V}_{j,j} &= \left( \frac{s_+}{s_-} \right)^j e^{-i |j| \pi} \int_0^{\infty} e^{-2p\tau} J_{|j|}^2(2 i \sqrt{s_+ s_-} \tau) d\tau\\
    &= \left( \frac{s_+}{s_-} \right)^j e^{-i |j| \pi} \frac{1}{2i \pi \sqrt{s_+ s_-}} Q_{|j|-\frac{1}{2}} \left( \frac{2p^2 - 4 s_+ s_-}{-4 s_+ s_-} \right)
  \end{aligned}
$$

We have replaced the integral with a Legendre function of the second kind.
The main benefit of this substitution is there is a recurrence relation that describes the growth or decay of the Legendre function of the second kind.
From Abramowitz and Stegun 8.5.3 we have

$$
  (\nu - \mu + 1) Q_{\nu+1}^{\mu}(z) = (2\nu + 1) z Q_{\nu}^{\mu}(z) - (\nu+\mu) Q_{\nu-1}^{\mu}(z)
$$

For our problem, we have $$ \mu = 0 $$ and we map $$ \nu + 1 = \|j\|-\frac{1}{2} $$.

$$
  \frac{2|j|-1}{2} Q_{|j| - \frac{1}{2}}(z) = (2|j| - 2) z Q_{|j|-\frac{3}{2}}(z) - \frac{2|j|-3}{2} Q_{|j| - \frac{5}{2}}(z)
$$

To help with the following analysis, we relate the three terms that will appear in this recurrence, for $$ j \geq 2 $$,

$$
  \begin{aligned}
    \bar{V}_{j,j} &= \left( \frac{s_+}{s_-} \right)^j e^{-i j \pi} \frac{1}{2i \pi \sqrt{s_+ s_-}} Q_{j - \frac{1}{2}} \left( \frac{2p^2 - 4s_+ s_-}{-4s_+ s_-} \right)\\
    \bar{V}_{j-1,j-1} &= \left( \frac{s_+}{s_-} \right)^{j-1} e^{-i (j-1) \pi} \frac{1}{2i \pi \sqrt{s_+ s_-}} Q_{j - \frac{3}{2}} \left( \frac{2p^2 - 4s_+ s_-}{-4s_+ s_-} \right)\\
    \bar{V}_{j-2,j-2} &= \left( \frac{s_+}{s_-} \right)^{j-2} e^{-i (j-2) \pi} \frac{1}{2i \pi \sqrt{s_+ s_-}} Q_{j - \frac{5}{2}} \left( \frac{2p^2 - 4s_+ s_-}{-4s_+ s_-} \right)
  \end{aligned}
$$

Letting $$ z = 1 - \frac{p^2}{2 s_+ s_-} $$ we have,

$$
  \begin{aligned}
    \frac{2j-1}{2} \bar{V}_{j,j} &= \frac{e^{-ij\pi}}{2i\pi \sqrt{s_+ s_-}} \left( \frac{s_+}{s_-} \right)^j \frac{2j-1}{2} Q_{j-\frac{1}{2}} (z)\\
    &= \frac{e^{-ij\pi}}{2i \pi \sqrt{s_+ s_-}} \left( \frac{s_+}{s_-} \right)^j (2j-2) z Q_{j-\frac{3}{2}}(z) - \frac{e^{-ij\pi}}{2i \pi \sqrt{s_+ s_-}} \left( \frac{s_+}{s_-} \right)^j \frac{2j-3}{2} Q_{j - \frac{5}{2}}(z)
  \end{aligned}
$$
