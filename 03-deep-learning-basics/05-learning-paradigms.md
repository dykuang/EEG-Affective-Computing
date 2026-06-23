# Learning Paradigms

## Overview

Not all deep learning uses labels in the same way. The training paradigm determines what supervision signal is available and strongly affects data efficiency, robustness, and transferability.

This section introduces four paradigms that are central to modern EEG-based affective computing:

- supervised learning,
- unsupervised learning,
- semi-supervised learning,
- self-supervised learning.

These paradigms should not be seen as mutually exclusive. Many strong EEG systems combine them.

## Supervised Learning

### Definition

In supervised learning, the model learns from labeled pairs $(x, y)$, where $x$ is the EEG input and $y$ is the desired output.

Typical tasks include:

- emotion classification,
- valence/arousal regression,
- subject-specific emotion recognition.

The learning objective usually minimizes a loss such as cross-entropy or mean squared error.

### Strengths

- direct alignment with the target task,
- straightforward evaluation,
- strong performance when labels are abundant and reliable.

### Weaknesses in EEG

- emotion labels are expensive and noisy,
- labeled datasets are usually small,
- models may overfit subject-specific cues.

## Unsupervised Learning

### Definition

In unsupervised learning, the model receives inputs $x$ without external labels and tries to discover useful structure.

Typical objectives include:

- clustering,
- reconstruction,
- density modeling,
- dimensionality reduction.

### EEG-Relevant Examples

- autoencoders that reconstruct EEG,
- clustering latent EEG representations,
- learning compact signal manifolds,
- anomaly detection via reconstruction or likelihood.

### Why It Matters

Unsupervised learning is valuable when labels are sparse but raw EEG is plentiful. It can uncover structure that later helps downstream supervised tasks.

## Semi-Supervised Learning

### Definition

Semi-supervised learning uses both labeled and unlabeled data.

If labeled EEG is limited but unlabeled sessions are abundant, semi-supervised learning tries to benefit from both.

### Common Strategies

- consistency regularization,
- pseudo-labeling,
- entropy minimization,
- generative latent variable models,
- graph-based label propagation.

### EEG Use Cases

This is highly relevant in affective computing because:

- collecting raw EEG is easier than obtaining reliable emotion annotations,
- unlabeled trials may cover more subjects and contexts,
- regularization from unlabeled data can reduce overfitting.

## Self-Supervised Learning

### Definition

Self-supervised learning creates supervisory signals from the data itself. The task is not the final emotion prediction task, but a pretext objective that encourages useful representation learning.

### Common Self-Supervised Objectives

- contrastive learning,
- masked signal reconstruction,
- temporal order prediction,
- multi-view consistency,
- predictive coding.

### Example for EEG

A model may be trained to:

- distinguish whether two EEG segments come from the same trial,
- reconstruct masked channels or time intervals,
- align time and frequency views of the same signal.

Later, the learned encoder is fine-tuned for emotion recognition.

## Why Self-Supervision Is Important for EEG

EEG is a strong candidate for self-supervised learning because:

- raw recordings are often much more available than emotion labels,
- pretraining can learn subject-robust signal structure,
- it can reduce dependence on expensive annotation,
- it supports transfer across datasets and tasks.

## Comparison of Paradigms

| Paradigm | Uses Labels? | Main Benefit | Main Limitation |
|---|---|---|---|
| **Supervised** | Yes | Direct task optimization | Label scarcity and noise |
| **Unsupervised** | No | Learns general structure | Task alignment may be weak |
| **Semi-supervised** | Partly | Better data efficiency | Method design can be complex |
| **Self-supervised** | Derived from data | Strong reusable representations | Pretext-target mismatch risk |

## A Logical View for EEG Affective Computing

A useful mental model is:

1. **Unsupervised/self-supervised learning** helps learn a signal representation.
2. **Semi-supervised learning** helps inject limited labels efficiently.
3. **Supervised learning** aligns the representation with the final emotion task.

This layered strategy is increasingly common in modern EEG pipelines.

## Practical Recommendations

- Use **supervised learning** when you have reliable labels and clear task definitions.
- Use **unsupervised learning** for exploratory structure discovery or anomaly modeling.
- Use **semi-supervised learning** when labeled EEG is scarce but unlabeled recordings are available.
- Use **self-supervised learning** when large raw EEG corpora can support pretraining.

## Summary

Learning paradigms define where the supervision signal comes from. This choice is especially important in EEG-based affective computing because labels are limited, noisy, and expensive, while raw signals are relatively easier to collect. Modern systems increasingly combine supervised, unsupervised, semi-supervised, and self-supervised strategies rather than relying on just one.
