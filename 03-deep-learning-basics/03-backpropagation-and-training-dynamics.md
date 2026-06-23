# Backpropagation and Training Dynamics

## Overview

Optimization requires gradients, and backpropagation is the mechanism that computes them efficiently for multilayer models. Without backpropagation, training modern deep networks would be computationally infeasible.

This section explains backpropagation as repeated application of the chain rule through a computation graph, then connects it to practical training issues such as vanishing gradients, exploding gradients, normalization, and stable optimization for EEG data.

## Forward Computation and Computational Graphs

A neural network can be viewed as a directed graph of operations. For a simple two-layer network,

$$h = \sigma(W_1 x + b_1),$$
$$\hat{y} = W_2 h + b_2,$$
$$\mathcal{L} = \ell(\hat{y}, y).$$

The **forward pass** computes intermediate values and the final loss.

## The Chain Rule

Backpropagation relies on the chain rule. If

$$\mathcal{L} = \mathcal{L}(\hat{y}(h(W))),$$

then

$$\frac{\partial \mathcal{L}}{\partial W} = \frac{\partial \mathcal{L}}{\partial \hat{y}} \cdot \frac{\partial \hat{y}}{\partial h} \cdot \frac{\partial h}{\partial W}.$$

This allows gradients to be decomposed into local derivatives and propagated backward through the graph.

## Backpropagation in a Layered Network

For a layer

$$z^{(l)} = W^{(l)} h^{(l-1)} + b^{(l)}, \qquad h^{(l)} = \sigma(z^{(l)}),$$

the backward pass computes an error signal

$$\delta^{(l)} = \frac{\partial \mathcal{L}}{\partial z^{(l)}}.$$

Then the parameter gradients are

$$\frac{\partial \mathcal{L}}{\partial W^{(l)}} = \delta^{(l)} (h^{(l-1)})^T,$$
$$\frac{\partial \mathcal{L}}{\partial b^{(l)}} = \delta^{(l)}.$$

The error recursion is

$$\delta^{(l)} = \left((W^{(l+1)})^T \delta^{(l+1)}\right) \odot \sigma'(z^{(l)}).$$

This recursive structure makes backpropagation efficient: every layer reuses downstream information instead of recomputing gradients from scratch.

## Why Backpropagation Scales

Naive differentiation would be extremely expensive for large networks. Backpropagation avoids repeated work by caching forward activations and propagating derivatives once. This makes deep learning practical even with millions of parameters.

## Gradient Flow Problems

### Vanishing Gradients

If repeated derivatives are small, gradients shrink rapidly as they move backward through depth or time. This makes early layers learn very slowly.

This is especially common with:

- sigmoid activations,
- tanh in saturated regimes,
- deep recurrent models.

### Exploding Gradients

If repeated derivatives are large, gradients can grow uncontrollably. This causes unstable parameter updates and numerical problems.

Both issues matter in EEG models, particularly when:

- using deep temporal architectures,
- training on long sequences,
- working with poorly normalized signals.

## Practical Responses to Gradient Problems

### Activation Choice

ReLU-like activations reduce saturation and often help gradient flow.

### Initialization

Good initialization keeps the scale of activations and gradients under control at the start of training.

### Normalization

Batch normalization, layer normalization, and related techniques help stabilize intermediate representations and training dynamics.

### Residual Connections

Skip connections make it easier for gradients to propagate through deep networks.

### Gradient Clipping

For recurrent or unstable models, gradient clipping limits the update magnitude:

$$g \leftarrow g \cdot \min\left(1, \frac{c}{\|g\|}\right).$$

## Training Dynamics Beyond the Formula

Backpropagation provides gradients, but training behavior also depends on:

- optimizer choice,
- batch size,
- learning rate schedule,
- normalization scheme,
- regularization,
- data ordering.

Thus, trainability is not just a property of the architecture; it emerges from the interaction of model, objective, and optimization procedure.

## Backpropagation in EEG Context

EEG data presents several challenges for gradient-based learning:

- signals can have large amplitude variability across channels,
- artifacts may dominate gradients if preprocessing is weak,
- labels may be noisy, giving misleading gradient signals,
- subject heterogeneity may produce conflicting update directions.

Useful mitigation strategies include:

- channel-wise normalization,
- robust preprocessing before model training,
- balanced batching across subjects or classes,
- loss reweighting for imbalanced labels,
- shorter warm-up phases for learning rate schedules.

## Automatic Differentiation

Modern frameworks such as PyTorch and TensorFlow implement automatic differentiation. Conceptually, however, they are still applying backpropagation through a computation graph. Understanding the underlying mechanism remains important because many model pathologies are gradient pathologies.

## Summary

Backpropagation is the engine of deep learning optimization. It turns layered nonlinear computation into efficient gradient updates, but also exposes networks to trainability problems such as vanishing and exploding gradients. These issues become especially important in EEG applications because signals are noisy, labels are limited, and temporal depth can be substantial. The next question is whether fitting the training data actually leads to good performance on unseen data.

---

Next: [Generalization, Overfitting, and Regularization](04-generalization-overfitting-and-regularization.md)
