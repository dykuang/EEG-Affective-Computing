# Empirical Risk Minimization and Optimization

## Overview

Once a neural network architecture is specified, learning becomes an optimization problem. We choose model parameters so that predictions align well with observed training data. The dominant framework for this is **empirical risk minimization**.

For EEG-based affective computing, this perspective is essential because model behavior is shaped not only by architecture, but by the loss function, training distribution, and optimization dynamics.

## Population Risk and Empirical Risk

Suppose data pairs $(x, y)$ are drawn from an unknown distribution $\mathcal{D}$. Let $f_\theta(x)$ be a model parameterized by $\theta$, and let $\ell(f_\theta(x), y)$ be a loss.

The ideal objective is the **population risk**:

$$R(\theta) = \mathbb{E}_{(x,y) \sim \mathcal{D}}[\ell(f_\theta(x), y)].$$

But $\mathcal{D}$ is unknown. In practice we minimize the **empirical risk** over a dataset of size $n$:

$$\hat{R}_n(\theta) = \frac{1}{n} \sum_{i=1}^n \ell(f_\theta(x_i), y_i).$$

This is the basic learning principle behind most deep learning systems.

## Loss Functions

The choice of loss determines what errors matter.

### Classification Loss

For emotion classification with $K$ classes, the standard choice is cross-entropy:

$$\ell_{\text{CE}} = - \sum_{k=1}^{K} y_k \log \hat{p}_k,$$

where $\hat{p}_k$ is the predicted probability of class $k$.

### Regression Loss

For continuous valence or arousal prediction, a common choice is mean squared error:

$$\ell_{\text{MSE}} = \|y - \hat{y}\|^2.$$

### Contrastive or Representation Losses

In self-supervised or metric learning settings, objectives may encourage similar samples to have nearby embeddings and dissimilar samples to be separated.

## Why ERM Matters for EEG

In EEG-based affective computing, empirical risk minimization is complicated by:

- label noise from subjective emotion annotations,
- distribution shift across subjects and recording sessions,
- class imbalance,
- small sample regimes,
- noisy inputs with artifacts.

Minimizing empirical risk too aggressively can fit spurious correlations rather than emotion-relevant structure.

## Optimization as Parameter Search

Neural learning seeks

$$\theta^* = \arg\min_\theta \hat{R}_n(\theta).$$

For deep networks, this objective is high-dimensional and nonconvex. Closed-form solutions are unavailable, so we rely on iterative gradient-based optimization.

## Gradient Descent

The simplest update rule is gradient descent:

$$\theta_{t+1} = \theta_t - \eta \nabla_\theta \hat{R}_n(\theta_t),$$

where $\eta$ is the learning rate.

The gradient tells us how parameters should change locally to reduce loss. In large datasets, computing the full gradient is expensive.

## Stochastic Gradient Descent

Instead of using the full dataset, **stochastic gradient descent** uses mini-batches:

$$\theta_{t+1} = \theta_t - \eta \nabla_\theta \hat{R}_{\mathcal{B}}(\theta_t),$$

where $\mathcal{B}$ is a batch sampled from the training set.

This introduces noise into optimization, but that noise is often beneficial:

- it reduces computation per step,
- it helps escape sharp local regions,
- it may improve generalization.

## Common Optimizers

### SGD with Momentum

Momentum accumulates past gradients:

$$v_{t+1} = \mu v_t - \eta \nabla_\theta \hat{R}_{\mathcal{B}}(\theta_t),$$
$$\theta_{t+1} = \theta_t + v_{t+1}.$$

### Adam

Adam adapts learning rates per parameter using first and second moments of the gradient. It is widely used in EEG studies because it is easy to tune and works well in noisy settings.

### RMSProp and AdamW

These are variants that often improve training stability or regularization behavior.

## Optimization Landscape

Deep learning objectives are nonconvex. This means:

- many local minima and saddle points may exist,
- optimization depends on initialization,
- different runs may converge to different solutions,
- low training loss does not guarantee good generalization.

In practice, modern deep learning often succeeds despite nonconvexity because many solutions are good enough, but their generalization properties can differ substantially.

## Initialization and Conditioning

Optimization quality depends strongly on initialization. Poor initialization can lead to:

- exploding activations,
- vanishing activations,
- unstable gradients,
- slow convergence.

Common initialization schemes such as Xavier/Glorot and He initialization are designed to keep signal magnitudes stable through depth.

## EEG-Specific Optimization Issues

For EEG, optimization can be unusually fragile because:

- sample counts are often low relative to model size,
- mini-batches may mix heterogeneous subjects,
- emotional labels may be weak or inconsistent,
- artifacts can dominate gradients if not controlled.

Useful responses include:

- subject-aware batching,
- careful normalization,
- robust loss functions,
- balanced sampling,
- early stopping.

## Regularized Objective Functions

In practice, one often minimizes a regularized risk:

$$\hat{R}_n^{\text{reg}}(\theta) = \hat{R}_n(\theta) + \lambda \Omega(\theta),$$

where $\Omega(\theta)$ could be:

- L2 weight decay,
- L1 sparsity,
- smoothness penalties,
- domain-specific priors.

This links optimization directly to generalization.

## Summary

Empirical risk minimization frames deep learning as loss minimization on data. Optimization algorithms such as SGD and Adam make this feasible in practice, but they do not solve the deeper issues of stability, trainability, and generalization automatically. To understand how gradients are actually computed through layered models, we next turn to backpropagation.

---

Next: [Backpropagation and Training Dynamics](03-backpropagation-and-training-dynamics.md)
