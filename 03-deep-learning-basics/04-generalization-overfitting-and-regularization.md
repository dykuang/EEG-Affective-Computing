# Generalization, Overfitting, and Regularization

## Overview

A deep model can achieve very low training loss and still fail badly on new data. This is the central problem of **generalization**. In EEG-based affective computing, generalization is particularly difficult because datasets are often small, label quality is imperfect, and subject-to-subject variability is large.

This section introduces overfitting, generalization, bias-variance tradeoffs, and practical regularization strategies.

## Training Error vs. Test Error

- **Training error** measures performance on the data used for fitting.
- **Test error** measures performance on unseen data.

A model **overfits** when training error becomes very low while test error remains high or worsens.

This typically means the model has learned dataset-specific quirks rather than robust emotion-related structure.

## Generalization Gap

The **generalization gap** is the difference between expected performance on new data and observed performance on training data.

Conceptually,

$$\text{generalization gap} = R(\theta) - \hat{R}_n(\theta).$$

A small gap is desirable, but in EEG it can be hard to obtain because:

- the training sample may not represent the full population,
- subjects differ strongly,
- recording setups and stimulus protocols vary,
- emotional labels are noisy and subjective.

## Bias, Variance, and Capacity

A useful conceptual lens is the **bias-variance tradeoff**.

- **High bias**: the model is too simple and underfits.
- **High variance**: the model is too sensitive to the training sample and overfits.

Model capacity increases with depth, width, and architectural flexibility. More capacity helps fit complex EEG patterns, but also raises the risk of memorization.

## Why Overfitting Is Common in EEG

EEG-based affective computing often combines:

- high-dimensional inputs,
- relatively small subject pools,
- many correlated channels,
- noisy annotations,
- strong inter-subject distribution shift.

This is exactly the kind of regime in which overfitting becomes severe. A model may learn:

- subject identity instead of emotion,
- session-specific noise,
- artifact patterns,
- dataset collection biases.

## Underfitting vs. Overfitting

### Underfitting

A model underfits when both training and test performance are poor. Causes include:

- insufficient capacity,
- poor feature representation,
- optimization failure,
- inappropriate loss design.

### Overfitting

A model overfits when training performance is strong but test performance is weak. Causes include:

- too much model capacity relative to data,
- weak regularization,
- leakage across train/test partitions,
- spurious subject or session cues.

## Regularization Strategies

### Weight Decay

L2 regularization penalizes large weights:

$$\mathcal{L}_{\text{reg}} = \mathcal{L} + \lambda \|\theta\|_2^2.$$

This encourages smoother, less extreme solutions.

### Dropout

Dropout randomly suppresses units during training. It reduces co-adaptation and often improves robustness in EEG classifiers with limited data.

### Early Stopping

Training is stopped when validation performance stops improving. This is one of the most practical defenses against overfitting in EEG.

### Data Augmentation

For EEG, augmentation may include:

- temporal jittering,
- additive noise,
- frequency perturbation,
- masking channels or time spans,
- mixup or manifold mixup.

### Normalization and Standardization

Proper input scaling reduces training instability and can improve generalization indirectly.

### Architectural Control

Sometimes the best regularizer is simply a smaller model.

## Validation Protocols Matter

In EEG, bad evaluation design can create the illusion of generalization.

Important protocol choices include:

- subject-dependent vs. subject-independent evaluation,
- leave-one-subject-out validation,
- session-aware splits,
- leakage prevention during normalization and preprocessing.

A model that generalizes within a subject may fail completely across subjects.

## The Role of Inductive Bias

Architectures generalize partly because they encode assumptions:

- CNNs assume locality,
- RNNs assume sequence structure,
- GNNs assume graph structure,
- Transformers assume flexible contextual interaction.

Good inductive bias can reduce sample complexity. For EEG, a well-matched architecture often generalizes better than a larger but less structured model.

## Label Noise and Weak Supervision

Emotion labels are often noisy because they depend on:

- self-report variability,
- coarse annotation scales,
- delayed or averaged responses,
- cultural and personal interpretation.

Thus, part of the apparent overfitting problem may actually be fitting label noise. This motivates robust losses, uncertainty-aware modeling, and semi/self-supervised methods.

## Practical EEG Guidelines

To reduce overfitting in EEG affective computing:

1. prefer subject-aware validation,
2. start from simpler models before scaling up,
3. use early stopping and weight decay routinely,
4. monitor train/validation divergence,
5. test whether the model is learning emotion or subject identity,
6. use augmentation and unlabeled data where possible.

## Summary

Generalization is the real goal of learning, not low training loss alone. Overfitting is especially dangerous in EEG-based affective computing because data are scarce and heterogeneous. Regularization, validation design, and inductive bias all play central roles in making deep models useful beyond the training set. The next step is to examine the major learning paradigms that determine what supervision signal is available in the first place.

---

Next: [Learning Paradigms](05-learning-paradigms.md)
