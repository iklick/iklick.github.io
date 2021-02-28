---
layout: single
title:  "Case Study: Pendula Patterns"
date: 2021-02-01
permalink: /posts/2021/02/pendula-patterns/
header:
  teaser: "unsplash-gallery-image-2-th.jpg"
categories: 
  - dynamics
  - oscillators
tags:
  - pendulum
  - synchronization
  - oscillators
---

# Pendula Patterns

I was sent a video of some pendula swinging that seemed to make interesting patterns.

## Dynamics of a Pendulum

A pendula swings, starting from rest at an initial angle $$ \theta(0) = \theta_0 $$ according to the second order nonlinear dynamics,

$$
  \ddot{\theta}(t) + \frac{g}{\ell} \sin \theta(t) = 0, \quad \theta(0) = \theta_0, \quad \dot{\theta}(0) = 0
$$

The period of the pendulum is defined as the amount of time $$ T $$ such that,

$$
  \theta(t) = \theta(t+T) \quad \text{and} \quad \dot{\theta}(t) = \dot{\theta}(t+T)
$$

With a little work, the period of a pendulum can be shown to be,

$$
  T = 4 \sqrt{\frac{\ell}{g}} \int_0^{\pi / 2} \frac{dx}{\sqrt{1 - \sin^2 \frac{\theta_0}{2} \sin^2 x}} = 4 \sqrt{\frac{\ell}{g}} K \left( \sin \frac{\theta_0}{2} \right)
$$

where $$ K(k) $$ is the complete elliptic integral of the first kind.

<details>
  <summary>**How to Derive the Period of a Pendulum**</summary>
  To derive the period we first use an order reduction technique, and then use the method of separation.
  First, multiply the dynamical equation by the angular velocity.
  
  $$
    \dot{\theta}(t) \ddot{\theta}(t) + \frac{g}{\ell} \dot{\theta}(t) \sin \theta(t) = 0
  $$
  
  Extract a time derivative from both terms,
  
  $$
    \frac{d}{dt} \left[ \frac{1}{2} \dot{\theta}^2(t) - \frac{g}{\ell} \cos \theta(t) \right] = 0
  $$
  
  As the time derivative of this expression is zero, then what is being differentiated is constant.
  
  $$
    \frac{1}{2} \dot{\theta}^2(t) - \frac{g}{\ell} \cos \theta(t) = c_1
  $$
  
  To determine this integration constant, evaluate this expression at $$ t = 0 $$.
  
  $$
    \frac{1}{2} \dot{\theta}^2(0) - \frac{g}{\ell} \cos \theta (0) = - \frac{g}{\ell} \cos \theta_0 = c_1
  $$
  
  Applying this constant and rearranging to isolate the squared angular velocity yields,
  
  $$
    \dot{\theta}^2(t) = 2 \frac{g}{\ell} \left( \cos \theta(t) - \cos \theta_0 \right)
  $$
  
  Separating the variables yields the differential equation,
  
  $$
    dt = \frac{d\theta}{\sqrt{2 \frac{g}{\ell} (\cos \theta - \cos \theta_0)}}
  $$
  
  Now, pause and think about the pendulum's period.
  The period can be split into four parts,
  
  1. From \( \theta(t) = \theta_0 \) to \( \theta(t + T/4) = 0 \)
  2. to \( \theta(t + T/2) = -\theta_0 \)
  3. back to \( \theta(t + 3T/4) = 0 \)
  4. and finally back to \( \theta(t+T) = \theta_0 \).
  
  Thus, integrating over any of these intervals leads to one fourth of the period.
  To keep sign issues at bay, I choose the interval from step 3 to 4,
  
  $$
    \int_0^{T/4} dt = \frac{T}{4} = \int_0^{\theta_0} \frac{d\theta}{\sqrt{2 \frac{g}{\ell} (\cos \theta - \cos \theta_0)}} = \sqrt{\frac{\ell}{2g}} \int_0^{\theta_0} \frac{d\theta}{\sqrt{\cos \theta - \cos \theta_0}}
  $$
  
  To solve this integral, first use trigonometric identity,
  
  $$
    \cos \theta = 1 - 2 \sin^2 \frac{\theta}{2}
  $$
  
  so that the integral becomes,
  
  $$
    \begin{aligned}
    T &= 4 \sqrt{\frac{\ell}{2g}} \int_0^{\theta_0} \frac{d\theta}{\sqrt{2 \sin^2 \frac{\theta_0}{2} - 2 \sin^2 \frac{\theta}{2}}}\\
    &= \frac{2}{\sin \frac{\theta_0}{2}} \sqrt{\frac{\ell}{g}} \int_0^{\theta_0} \frac{d\theta}{\sqrt{1 - \frac{1}{\sin^2 \frac{\theta_0}{2}} \sin^2 \frac{\theta}{2}}}
    \end{aligned}
  $$
  
  Define $$ k = \sin \frac{\theta_0}{2} $$ and use the substitution $$ u = \frac{\theta}{2} $$ to put the integral in the form,
  
  $$
    T = \frac{2}{k} \sqrt{\frac{\ell}{g}} \int_0^{\frac{\theta_0}{2}} \frac{2du}{\sqrt{1 - \frac{1}{k^2} \sin^2 u}} = \frac{4}{k} \sqrt{\frac{\ell}{g}} F \left( \sin^{-1} k, \frac{1}{k} \right)
  $$
  
  where the first elliptic integral is defined as,
  
  $$
    F(\phi, r) = \int_0^{\phi} \frac{dx}{\sqrt{1-r^2 \sin^2 x}} = \int_0^{\sin \phi} \frac{dy}{\sqrt{1-y^2}\sqrt{1-r^2 y^2}}
  $$
  
  To further simplify the expression, use the identity relating the elliptic integral of the first kind to the complete elliptic integral of the first kind,
  
  $$
    \frac{1}{k} F \left( \sin^{-1} k, \frac{1}{k} \right) = K(k)
  $$
  
  which completes the derivation of the period of a simple pendulum,
  
  $$
    T = 4 \sqrt{\frac{\ell}{g}} K \left( \sin \frac{\theta_0}{2} \right)
  $$
</details>

The most important part in the following design problem though is that, holding the initial angle constant, the period of a pendulum is proportional to the square root of its length,

$$
  T = C(\theta_0) \sqrt{\ell}
$$

With this knowledge, let's put together a set of $$ n $$ pendula of lengths $$ \ell_j $$, $$ j = 1,2,\ldots,n $$, linearly spaced apart with lengths,

$$
  \ell_j = \frac{j-1}{n-1} (\ell_{\max} - \ell_{\min}) + \ell_{\min}
$$

so pendulum $$ 1 $$ has length $$ \ell_{\min} $$ and pendulum $$ n $$ has length $$ \ell_{\max} $$.

Watching this system oscillate for a while though, it seems to be void of the interesting patterns.

## What makes a Pattern Interesting?

This is a difficult question to ask as interesting-ness is somewhat subjective.
Nonetheless I propose the interesting quality is that periodically the pendulum bobs seem to partition themselves.

{%
  include image.html
  file="/images/penpat_two.jpg"
  max-width="500px"
  caption="Six frames spaced a second apart showing the interval of time when the pendula seem to partition themselves into two sets."
%}

For instance, in the above series of frames, the pendula, after some time passes, split into two sets.
Let the longest pendulum be labeled $$ j = 1 $$ (the yellow one in the foreground) and say it has completed $$ R $$ periods.
Then the second longest pendulum, (the blue one on the left side), has completed $$ R + \frac{1}{2} $$ periods.
The third one (red on the right) has completed $$ R + 1 $$ periods.
This continues so that pendulum $$ j $$, at this point, completes $$ R + \frac{j-1}{2} $$ periods.

{%
  include image.html
  file="/images/penpat_three.jpg"
  max-width="500px"
  caption="Same as before except the pendula partition into three sets."
%}

In this series of frames, the pendula have completed thirds of a period, so as before, if pendulum $$ j $$ has completed $$ R $$ periods, then pendulum $$ j + 1$ has completed $$ R + \frac{1}{3} $$ and pendulum $$ j + 2 $$ has completed $$ R + \frac{2}{3} $$ periods and in general for the $$ j $$'th pendulum, the number of periods completed is $$ R + \frac{j-1}{3} $$.

{%
  include image.html
  file="/images/penpat_four.jpg"
  max-width="500px"
  caption="The pendula partition into four sets."
%}

This can happen for four sets as well, when pendulum $$ j $$ has completed $$ \frac{R} + \frac{j}{4} $$ periods.


For example, at around 18 seconds, the pendula split into two sets, and at other moments the pendula split into three and four sets.

## Design of an Interesting Pendula System

For the case that at some time the pendula split into two sets, it means that after pendulum $$ j $$ swings $$ R $$ times, pendulum $$ j + 1 $$ swings $$ R + \frac{1}{2} $$ times.

$$
  R T_j = \left(R + \frac{1}{2} \right) T_{j+1} \quad \rightarrow \quad R \sqrt{\ell_j} = \left( R + \frac{1}{2} \right) \sqrt{\ell_{j+1}}
$$

After some experimenting of parameterized versions for $$ \ell_j $$, I found the following form especially easy to use.

$$
  \ell_j = \left( \frac{a}{a+bj} \right)^2, \quad a,b > 0
$$

The two positive parameters $$ a $$ and $$ b $$ are to be designed for interesting patterns.

$$
  R \frac{a}{a+bj} = \left( R + \frac{1}{2} \right) \frac{a}{a+bj + b}
$$

Multiplying both sides by $$ (a+bj)(a+bj+b) $$ and canceling common terms that appear on both sides yields,

$$
  R = \frac{1}{2} \frac{a}{b} \frac{j}{2}
$$
