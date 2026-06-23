# Deep Learning Basics for EEG-based Affective Computing

## Overview

This chapter introduces the conceptual foundations that readers need before studying specific neural architectures. The focus here is not yet on CNNs, Transformers, or Graph Neural Networks, but on the core ideas that make deep learning work: function approximation, empirical risk minimization, gradient-based learning, backpropagation, learning paradigms, and the tension between overfitting and generalization.

For EEG-based affective computing, these basics are especially important because EEG datasets are often small, noisy, high-dimensional, and highly variable across subjects and sessions. A strong grasp of learning fundamentals helps explain why some models succeed, why others fail, and how training choices affect robustness and interpretability.

## Chapter Structure

The material is organized in a logical progression from representation to optimization to learning settings:

1. **Neural Network Foundations**
   - From linear models to multilayer neural networks
   - Hidden representations and nonlinear activation functions
   - Universal approximation theorem and what it does, and does not, imply

2. **Empirical Risk Minimization and Optimization**
   - Learning as minimizing a task loss on data
   - Population risk vs. empirical risk
   - Gradient descent, stochastic optimization, and optimization landscapes

3. **Backpropagation and Training Dynamics**
   - Chain rule through layered computation graphs
   - Parameter updates and error signal propagation
   - Vanishing and exploding gradients, initialization, and normalization

4. **Generalization, Overfitting, and Regularization**
   - Why low training loss is not enough
   - Bias-variance tradeoff, model capacity, and sample complexity
   - Regularization strategies commonly used for EEG

5. **Learning Paradigms**
   - Supervised learning
   - Unsupervised learning
   - Semi-supervised learning
   - Self-supervised learning
   - Why different paradigms matter for EEG affective computing

## Why These Basics Matter for EEG

EEG-based affective computing combines several difficult properties:

- **High noise levels** from eye blinks, muscle activity, and environmental interference
- **Limited labels** because emotional annotation is expensive and often subjective
- **Inter-subject variability** because neural signatures of emotion differ across individuals
- **Temporal dependence** because emotional states evolve over time
- **Representation ambiguity** because useful information may appear in time, frequency, or connectivity domains

These properties make deep learning fundamentals more than background theory. They directly affect:

- which objectives are sensible,
- which optimization strategies are stable,
- how likely a model is to overfit,
- and whether unlabeled EEG can be exploited effectively.

## Reading Strategy

A practical reading order is:

1. Start with **Neural Network Foundations** to understand expressiveness.
2. Continue to **Empirical Risk Minimization and Optimization** to see how models are fitted.
3. Read **Backpropagation and Training Dynamics** to understand why deep models can be hard to train.
4. Read **Generalization, Overfitting, and Regularization** to understand model selection and robustness.
5. Finish with **Learning Paradigms** to frame later chapters on representation learning and modern training strategies.

## Relation to Later Chapters

This chapter provides the vocabulary and principles used throughout the rest of the book:

- Chapter 07 uses these ideas to discuss engineered and learned representations.
- Chapter 08 builds on them when introducing concrete deep learning model families.
- Chapter 09 relies on them for loss design, validation protocols, and evaluation.

---

Next: [Neural Network Foundations](01-neural-network-foundations.md)
