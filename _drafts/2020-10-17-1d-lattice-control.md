---
title: 'Minimum Energy Control of 1D Lattices'
date: 2020-10-17
permalink: /posts/2020/10/control-of-1d-lattices/
tags:
  - lattices
  - minimum energy control
---

## 1D Lattice


$$
  \dot{W}_{j,k} = -2p W_{j,k}(t) + \nabla_j W_{j,k}(t) + \nabla_k W_{j,k}(t) + \sum_{v_a \in \mathcal{D}} \delta_{j,a} \delta_{k,a}, \quad W_{j,k}(0) = 0
$$

where 

$$
  \nabla_j W_{j,k}(t) = \sum_{\ell \in \mathcal{N}} s_{\ell} W_{j+\ell,k}(t)
$$


### Discrete Fourier Transform

The DFT transforms a sequence of values \$ y_k \$ to another set \$ y_{\hat{k}} \$

$$
  y_{\hat{k}} = \sum_{k=0}^{n-1} y_k e^{- \frac{i2\pi}{n} k \hat{k}}
$$

and the inverse 

$$
  y_k = \sum_{\hat{k} = 0}^{n-1} y_{\hat{k}} e^{ \frac{i2\pi}{n} k \hat{k}}
$$

TODO: Prove this transformation and its inverse 

The usefulness of the DFT for this problem comes from its ability to remove spatial separations.

$$
  \sum_{j=0}^{n-1} e^{- \frac{i2\pi}{n} j \hat{j}} y_{j+\ell} = \sum_{j=0}^{n-1} e^{ \frac{i2\pi}{n} \ell \hat{j}} e^{-\frac{i2\pi}{n} (j+\ell) \hat{j}} y_{j+\ell} = e^{ \frac{i2\pi}{n} \ell \hat{j}} \sum_{j' = 0}^{n-1} e^{- \frac{i2\pi}{n} j' \hat{j}} y_{j'}
$$

### Fourier Transform of the Lyapunov Equation

First, take the Laplace transform of the Gramian equation to make it an algebraic equation in frequency,

$$
  \epsilon V_{j,k} = -2p V_{j,k}(\epsilon) + \nabla_j V_{j,k}(\epsilon) + \nabla_{k} V_{j,k}(\epsilon) + \frac{1}{\epsilon} \sum_{v_a \in \mathcal{D}} \delta_{j,a} \delta_{k,a}
$$

The transformed values on the lattice are defined as,

$$
  V_{\hat{j},\hat{k}}(\epsilon) = \sum_{j=0}^{n-1} \sum_{k=0}^{n-1} e^{- \frac{i2\pi}{n} j \hat{j}} e^{- \frac{i2\pi}{n} k \hat{k}} V_{j,k}(\epsilon)
$$

The transformed lattice equation 

$$
  \sum_{j=0}^{n-1} e^{- \frac{i2\pi}{n} j \hat{j}} \nabla_j V_{j,k}(\epsilon) = \hat{\nabla}_{\hat{j}} V_{\hat{j}, k}(\epsilon)
$$

$$
  \hat{\nabla}_{\hat{j}} = \sum_{\ell \in \mathcal{N}} s_{\ell} e^{ \frac{i2\pi}{n} \ell \hat{j}} 
$$
