# Features and Representations for EEG-based Affective Computing

## Overview

Feature extraction is the bridge between raw EEG signals and machine learning models. In EEG-based affective computing, the choice of features fundamentally determines what kind of information the model can access, how much prior domain knowledge is encoded, and how the system balances between interpretability and end-to-end learning. This chapter surveys the most important families of EEG features and representations, from classical hand-crafted measures to learned representations emerging from deep architectures.

The feature landscape can be organized along several axes: the domain in which features are computed (time, frequency, time-frequency, spatial, or relational), the level of abstraction (from raw statistics to deep embeddings), and the modality (EEG alone vs. multimodal fusion). This chapter covers each of these dimensions progressively.

### Chapter Structure

1. **Time-Domain Features** (Section 01): The simplest and most interpretable features
   - Statistical moments, Hjorth parameters, event-related potentials, and waveform morphology

2. **Frequency-Domain Features** (Section 02): Spectral representations
   - Power spectral density, band power ratios, spectral asymmetry, and peak frequency

3. **Time-Frequency Features** (Section 03): Joint temporal and spectral representations
   - Short-time Fourier transform, wavelet transforms, and Hilbert-Huang spectrum

4. **Differential Entropy** (Section 04): Information-theoretic spectral features
   - DE as a principled EEG feature, Gaussian approximation, and DE-based emotion recognition

5. **Relation-Based Features** (Section 05): Connectivity and interaction representations
   - Functional connectivity, phase synchronization, graph-theoretic measures, and spatial patterns

6. **Multimodal Features** (Section 06): Features from complementary modalities
   - Peripheral physiology, eye tracking, facial expressions, and behavioral signals

7. **Nonlinear and Complexity Features** (Section 07): Capturing nonlinear brain dynamics
   - Sample entropy, approximate entropy, fractal dimension, HFD, and Lempel-Ziv complexity

8. **Feature Engineering and Selection** (Section 08): From raw features to model-ready representations
   - Dimensionality reduction, feature selection, feature fusion, and domain adaptation

### Why Features Matter for Affective EEG

Feature engineering for affective EEG is especially challenging because emotional states are diffuse, slowly varying, and individually expressed. Unlike motor imagery or epileptic spike detection, where characteristic patterns are relatively well localized in time, frequency, and space, emotion-related EEG features tend to be broadband, spatially distributed, and subject-dependent. This means that:

- **No single feature family dominates**: The best systems typically combine features from multiple domains.
- **Individual differences are large**: Feature distributions often shift substantially across subjects.
- **Temporal dynamics matter**: How features evolve over time can be as informative as their instantaneous values.
- **Complementary modalities help**: EEG alone may be insufficient; peripheral and behavioral signals provide synergistic information.

> Figure suggestion: Place a feature taxonomy diagram here showing the hierarchy from raw EEG → time/frequency/time-frequency/spatial/relational features → feature fusion → model input.

### Hand-Crafted vs. Learned Representations

A central tension in this chapter is between hand-crafted features and learned representations. Hand-crafted features encode explicit domain knowledge, are highly interpretable, and work well with small datasets. Learned representations (from autoencoders, contrastive learning, or end-to-end deep networks) can discover novel patterns but require more data and may be less interpretable. The most successful approaches often combine both: using hand-crafted features as input to deep feature extractors, or using learned representations as a complement to classical features.
