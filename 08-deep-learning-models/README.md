# Deep Learning Models for EEG-based Affective Computing

## Overview

This chapter provides a comprehensive survey of deep learning architectures applied to EEG-based affective computing. It covers both **discriminative models** (classifying or regressing emotional states from EEG) and **generative models** (learning the underlying distribution of EEG signals for augmentation, denoising, and representation learning). We progress from foundational architectures through generative paradigms to trending frontiers.

### Chapter Structure

#### Part I: Discriminative Architectures (Sections 1–6)

1. **Multi-Layer Perceptrons (MLPs)** (Section 01): The foundation
   - Fully connected architectures for hand-crafted EEG features
   - Strong baseline with frequency-domain and statistical features

2. **Convolutional Neural Networks (CNNs)** (Section 02): Spatial-temporal patterns
   - Automatic feature extraction from raw EEG and spectrograms
   - Captures local channel and temporal dependencies

3. **Recurrent Neural Networks & LSTMs** (Section 03): Temporal dynamics
   - Sequential processing of EEG time series
   - Long-term memory for emotional state evolution

4. **Transformer Models** (Section 04): Parallel attention
   - Self-attention for global temporal patterns
   - Efficient parallel training, strong with large datasets

5. **Hybrid Architectures** (Section 05): Combining paradigms
   - CNN-LSTM, attention-enhanced, multi-task, ensemble methods
   - State-of-the-art practical performance

6. **Graph Neural Networks (GNNs)** (Section 06): Brain network structure
   - Channel connectivity modeling via anatomical/functional graphs
   - Highly interpretable, neuroscience-aligned

#### Part II: Generative Models (Sections 7–9)

7. **Variational Autoencoders (VAEs)** (Section 07): Probabilistic latent models
   - Structured latent space, disentanglement (β-VAE), conditional generation (CVAE)
   - Semi-supervised learning, anomaly detection, emotion interpolation

8. **Generative Adversarial Networks (GANs)** (Section 08): Adversarial generation
   - Sharp, realistic EEG generation (WGAN-GP, CGAN)
   - Data augmentation, cross-subject translation (CycleGAN), artifact removal

9. **Flow-based and Diffusion Models** (Section 09): Frontier generative models
   - Flow: exact likelihood, invertible mappings (RealNVP, Glow)
   - Diffusion: state-of-the-art generation and denoising (DDPM, DDIM, LDM)

#### Part III: Emerging Frontiers (Section 10)

10. **Trending Architectures** (Section 10): The cutting edge
    - KAN, Mamba/SSM, SNN, Foundation Models, Neural ODE, Hypernetworks
    - Emerging paradigms reshaping EEG deep learning

### Generative vs. Discriminative Approaches

| Aspect | Discriminative Models | Generative Models |
|---|---|---|
| **Objective** | $P(y|x)$ — predict label given EEG | $P(x)$ or $P(x,y)$ — model EEG distribution |
| **Output** | Emotion class or regression value | Generated EEG, latent representation, or label |
| **Data augmentation** | External methods needed | Built-in generation capability |
| **Interpretability** | Attention maps, feature importance | Latent space traversal, factor disentanglement |
| **Robustness to noise** | Requires clean training data | Can learn to denoise and reconstruct |
| **Training complexity** | Relatively simpler | Often more complex, can be unstable |

## Key Concepts

### Latent Variable Models

Generative models often assume observed EEG data $x$ is generated from unobserved latent variables $z$:

$$p(x) = \int p(x|z) p(z) dz$$

The latent $z$ captures essential factors — emotional state, subject identity, noise level — in a compressed form.

### The Generative Process

1. **Sample latent**: $z \sim p(z)$ (e.g., standard Gaussian)
2. **Generate observation**: $x \sim p_\theta(x|z)$ (decoder/generator network)
3. **Inference**: Given observed $x$, infer $q_\phi(z|x)$ (encoder network)

### Applications in Affective Computing

| Application | VAE | GAN | Flow/Diffusion |
|---|---|---|---|
| **Data augmentation** | ✓ Good | ✓✓ Excellent | ✓ Good |
| **Denoising / Artifact removal** | ✓✓ Excellent | ✓ Good | ✓✓ Excellent |
| **Cross-subject translation** | ✓ Good | ✓✓ Excellent | ✓ Moderate |
| **Latent representation learning** | ✓✓ Excellent | ✓ Moderate | ✓ Good |
| **Conditional generation** | ✓✓ Excellent | ✓✓ Excellent | ✓✓ Excellent |
| **Anomaly detection** | ✓ Good | ✓✓ Excellent | ✓✓ Excellent |
| **Missing channel imputation** | ✓✓ Excellent | ✓ Good | ✓ Good |

## Preprocessing for Generative Models

Unlike discriminative models, generative models require careful preprocessing to ensure generated outputs are realistic:

### 1. Signal Standardization

```python
# Per-channel z-score normalization (reversible)
for ch in range(n_channels):
    mean_ch = signal[:, ch].mean()
    std_ch = signal[:, ch].std()
    signal[:, ch] = (signal[:, ch] - mean_ch) / std_ch
    # Store mean_ch, std_ch for inverse transform
```

### 2. Spectrogram / Time-Frequency Representation

Generative models often work better on 2D representations:

```python
# Convert to spectrogram for CNN-based generators
from scipy import signal as scipy_signal

f, t, Sxx = scipy_signal.spectrogram(
    eeg_signal, fs=256, nperseg=256, noverlap=192
)
# Sxx shape: (n_channels, n_freq, n_time)
```

### 3. Segmentation

```python
# Fixed-length segments for stable training
segment_length = 2048  # ~8 seconds at 256 Hz
segments = sliding_window(eeg_signal, segment_length, stride=512)
# segments shape: (n_segments, n_channels, segment_length)
```

### 4. Normalization Considerations

- **VAE**: Often uses $[-1, 1]$ or standardized inputs for Gaussian likelihood
- **GAN**: Tanh output layer expects $[-1, 1]$; scale inputs accordingly
- **Diffusion**: Data scaled to $[-1, 1]$ for stable noise schedule

## Evaluating Generative Models for EEG

Standard metrics adapted for EEG:

| Metric | What It Measures | EEG-Specific Consideration |
|---|---|---|
| **FID** (Fréchet Inception Distance) | Distribution similarity | Needs EEG-specific feature extractor (not ImageNet) |
| **Reconstruction MSE** | Signal fidelity | Window-level or channel-level |
| **Classification accuracy on generated data** | Utility of augmented data | Train classifier on generated EEG, test on real |
| **Latent space smoothness** | Interpolation quality | Linear interpolation in $z$ should yield smooth EEG transitions |
| **Disentanglement metrics** (MIG, DCI) | Factor separation | Separate emotion from subject identity |
| **Spectral similarity** | Frequency content match | PSD correlation, band power ratios |

## Practical Guidelines

1. **Start with VAEs** for representation learning and reconstruction
2. **Use GANs** when data augmentation is the primary goal
3. **Adopt diffusion models** for denoising and high-quality generation
4. **Always validate** generated EEG with neuroscience domain experts
5. **Combine approaches**: VAE-GAN hybrids offer the best of both worlds
6. **Consider computational budget**: Diffusion models are the most expensive

---

Next: [Multi-Layer Perceptrons and Dense Networks](01-mlp-dense-networks.md)
