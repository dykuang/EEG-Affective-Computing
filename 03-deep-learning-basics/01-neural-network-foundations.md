# Neural Network Foundations

## Overview

Deep learning begins with a simple question: how can we approximate a complex mapping from input to output? In EEG-based affective computing, the mapping of interest is often from a multichannel brain signal to an emotional state such as valence, arousal, or dominance. Neural networks provide a flexible function class for learning such mappings from data.

This section introduces the core structural ideas behind neural networks: linear models, nonlinear activations, hidden layers, representation learning, and the universal approximation theorem.

## From Linear Models to Neural Networks

A linear predictor maps input $x \in \mathbb{R}^d$ to output $\hat{y}$ as

$$\hat{y} = w^T x + b.$$

This is easy to optimize and interpret, but limited. Many EEG-emotion relationships are nonlinear:

- frontal asymmetry may interact with spectral power,
- emotional signatures may depend on combinations of channels and bands,
- temporal or cross-subject effects may bend decision boundaries in complex ways.

A neural network overcomes this by composing multiple transformations.

## The Basic Neuron

A neuron computes

$$z = w^T x + b, \qquad a = \sigma(z),$$

where $\sigma$ is a nonlinear activation function.

Common activations include:

- **Sigmoid**: $\sigma(z) = \frac{1}{1+e^{-z}}$
- **Tanh**: $\sigma(z) = \tanh(z)$
- **ReLU**: $\sigma(z) = \max(0, z)$
- **GELU**: a smooth activation widely used in modern architectures

Without nonlinearity, stacking many layers still collapses to a single linear map. Nonlinear activation is therefore the key step that turns a layered linear system into a genuinely expressive model.

## Hidden Layers and Representation Learning

A feedforward neural network applies a sequence of transformations:

$$h^{(1)} = \sigma(W^{(1)}x + b^{(1)}),$$
$$h^{(2)} = \sigma(W^{(2)}h^{(1)} + b^{(2)}),$$
$$\hat{y} = f_{\text{out}}(W^{(L)}h^{(L-1)} + b^{(L)}).$$

The intermediate vectors $h^{(1)}, h^{(2)}, \dots$ are **hidden representations**. Deep learning works in part because these hidden layers can transform raw or lightly processed data into more useful internal features.

For EEG, these hidden representations may progressively encode:

- local signal structure,
- band-specific information,
- inter-channel interactions,
- subject-invariant emotional cues.

## Depth, Width, and Capacity

Two basic architectural axes are:

- **Width**: number of units in a layer
- **Depth**: number of layers

A wider network can represent many functions by expanding the number of hidden units. A deeper network can represent hierarchical structure by composing simpler functions into more complex ones.

In EEG applications, width and depth must be balanced against:

- limited data volume,
- noise sensitivity,
- computational constraints,
- risk of overfitting.

## Universal Approximation Theorem

One foundational theoretical result is the **universal approximation theorem**. Informally, it states that a sufficiently wide neural network with a nonlinear activation can approximate any continuous function on a compact domain arbitrarily well.

A common form of the result says that for a continuous function $f$ on a compact set and for suitable nonlinear activation $\sigma$, there exists a one-hidden-layer network

$$g(x) = \sum_{i=1}^{m} a_i \sigma(w_i^T x + b_i)$$

such that

$$\sup_x |f(x) - g(x)| < \epsilon$$

for any $\epsilon > 0$, provided $m$ is large enough.

## What the Theorem Does Mean

The theorem supports several important intuitions:

- neural networks are not inherently too rigid for complex EEG tasks,
- shallow networks are theoretically expressive enough in principle,
- nonlinearity is central to expressive power.

## What the Theorem Does Not Mean

The theorem is often misinterpreted. It does **not** guarantee:

- that a network can be learned efficiently from finite data,
- that optimization will find the desired approximation,
- that a shallow network is the best practical choice,
- that generalization will be good,
- that arbitrarily wide models are data efficient.

This distinction matters in EEG. Even if a network can represent the target mapping, the training data may be too noisy or too limited to identify that mapping reliably.

## Why Deep Representations Help in Practice

Although a shallow network may be a universal approximator, deep networks are often preferred because they can express some structured functions much more efficiently. Repeated composition allows features to be built hierarchically.

For EEG, one can think of depth as enabling stages such as:

1. signal-level filtering,
2. local temporal pattern formation,
3. cross-channel interaction encoding,
4. emotion-relevant abstraction.

## Function Approximation View of EEG Learning

In EEG-based affective computing, the target function may take different forms:

- **Classification**: $f(x) \to$ discrete emotion label
- **Regression**: $f(x) \to$ valence/arousal score
- **Representation learning**: $f(x) \to z$ where $z$ is a compact latent code

The neural network perspective unifies these tasks: all require learning a mapping that balances expressiveness with learnability.

## Practical Implications for EEG

When designing neural models for EEG, keep in mind:

- expressive models are useful because EEG-emotion relationships are nonlinear,
- deeper models are not automatically better if data is scarce,
- hidden layers should be interpreted as learned feature spaces,
- the universal approximation theorem justifies neural modeling, but not careless model scaling.

## Summary

Neural networks extend linear models by stacking nonlinear transformations and learning internal representations. Their expressive power explains why they are useful for EEG-based affective computing, but expressive power alone is not enough. The next step is to understand how these models are actually fitted to data through empirical risk minimization and optimization.

---

Next: [Empirical Risk Minimization and Optimization](02-empirical-risk-minimization-and-optimization.md)
