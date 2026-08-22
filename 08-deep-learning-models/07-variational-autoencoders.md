# Variational Autoencoders (VAEs) for EEG

## Overview

Variational Autoencoders learn a probabilistic latent representation of EEG data. Unlike standard autoencoders, VAEs impose structure on the latent space, encouraging it to follow a prior distribution (typically Gaussian). This structured latent space enables controlled generation, interpolation, and factor disentanglement — all highly valuable for EEG-based affective computing.

![Conditional variational autoencoder for EEG. The diagram should show an EEG segment entering an encoder that outputs latent mean and variance, a reparameterization sampling step, a structured latent space optionally conditioned on emotion labels, and a decoder that reconstructs or generates multichannel EEG. Show reconstruction loss and KL-divergence regularization.](figures/vae-eeg-architecture.png)

**Figure 8.7: Conditional variational autoencoder for EEG.** A VAE encodes EEG into a probabilistic latent distribution, samples a structured code, and decodes it for reconstruction or condition-controlled generation.

## Theoretical Foundations

### Standard Autoencoder vs. VAE

**Standard Autoencoder**:

$$\text{Encoder: } z = f_\phi(x)$$
$$\text{Decoder: } \hat{x} = g_\theta(z)$$
$$\mathcal{L}_{\text{AE}} = \|x - \hat{x}\|^2$$

**Variational Autoencoder**:

$$\text{Encoder: } q_\phi(z|x) = \mathcal{N}(\mu_\phi(x), \sigma_\phi^2(x))$$
$$\text{Decoder: } p_\theta(x|z) = \mathcal{N}(g_\theta(z), \sigma^2 I)$$

$$\mathcal{L}_{\text{VAE}} = \mathbb{E}_{q_\phi(z|x)}[\log p_\theta(x|z)] - D_{\text{KL}}(q_\phi(z|x) \| p(z))$$

where $p(z) = \mathcal{N}(0, I)$ is the prior.

### The Reparameterization Trick

To enable backpropagation through the sampling step:

$$z = \mu_\phi(x) + \sigma_\phi(x) \odot \epsilon, \quad \epsilon \sim \mathcal{N}(0, I)$$

### Why VAEs for EEG?

**Advantages**:
- **Structured latent space**: Smooth transitions between emotional states
- **Probabilistic**: Captures uncertainty in EEG measurements
- **Disentanglement potential**: Separate emotion, subject identity, noise
- **Generative**: Synthesize new EEG samples for data augmentation
- **Reconstruction**: Natural denoising capability
- **Semi-supervised**: Can leverage unlabeled EEG data

**Limitations**:
- Generated samples may be blurry (averaging effect of Gaussian likelihood)
- Posterior collapse: decoder ignores $z$, making latent uninformative
- KL annealing often needed for stable training

## Adaptation to EEG Affective Computing

### β-VAE for Disentanglement

Standard VAE balances reconstruction and KL regularization. β-VAE adds a weight to enhance disentanglement:

$$\mathcal{L}_{\beta\text{-VAE}} = \mathbb{E}_{q_\phi(z|x)}[\log p_\theta(x|z)] - \beta \cdot D_{\text{KL}}(q_\phi(z|x) \| p(z))$$

With $\beta > 1$, the model is forced to use the latent space more efficiently, potentially separating:
- $z_1$: Emotion valence
- $z_2$: Emotion arousal
- $z_3$: Subject identity
- $z_4$: Noise level

### Conditional VAE (CVAE) for Controlled Generation

Add emotion labels as conditioning:

$$\mathcal{L}_{\text{CVAE}} = \mathbb{E}_{q_\phi(z|x,y)}[\log p_\theta(x|z,y)] - D_{\text{KL}}(q_\phi(z|x,y) \| p(z|y))$$

This enables:
- Generate EEG given a target emotion label
- Control emotional content of generated samples
- Data augmentation for under-represented emotions

### EEG-Specific Architecture

```
Input: EEG segment (n_channels, n_samples)
Example: (14, 2048)
   ↓
[Encoder]
  Conv1D(64, 5) → ReLU → MaxPool(2)
  Conv1D(128, 5) → ReLU → MaxPool(2)
  Conv1D(256, 5) → ReLU → MaxPool(2)
  Flatten → Dense(512) → ReLU
   ↓
μ = Dense(latent_dim)     log(σ²) = Dense(latent_dim)
   ↓                           ↓
   z = μ + σ ⊙ ε,  ε ~ N(0,I)
   ↓
[Decoder]
  Dense(512) → ReLU → Reshape
  Conv1DTranspose(256, 5) → ReLU
  Conv1DTranspose(128, 5) → ReLU
  Conv1DTranspose(64, 5) → ReLU
  Conv1DTranspose(n_channels, 5) → Tanh
   ↓
Output: Reconstructed EEG (n_channels, n_samples)
```

### Suitable Input Features

VAEs work best with structured inputs:

**Option 1: Raw EEG Segments**
```python
# Shape: (batch, n_channels, n_samples)
# (32, 14, 2048) — standardized to [-1, 1]

# Advantages: Full information, reconstruction is end-to-end
# Disadvantages: Higher computational cost, longer training
```

**Option 2: Spectrogram Representation**
```python
# Shape: (batch, n_channels, n_freq, n_time)
# (32, 14, 129, 32) — STFT with 256-point window

# Advantages: Frequency structure explicit, easier for CNN
# Disadvantages: Information loss from STFT
```

**Option 3: Pre-extracted Features**
```python
# Shape: (batch, n_features)
# (32, 128) — PSD, statistics, asymmetry features

# Advantages: Compact, fast training
# Disadvantages: Cannot reconstruct raw EEG, limited generative use
```

### Preprocessing Pipeline

```python
import numpy as np
from scipy import signal as scipy_signal

# 1. Load and filter EEG
eeg = load_eeg()  # (n_channels, n_total_samples)
sos = scipy_signal.butter(4, [0.5, 40], btype='band', fs=256, output='sos')
eeg_filtered = scipy_signal.sosfilt(sos, eeg, axis=1)

# 2. Segment into windows
window_size = 2048  # 8 seconds at 256 Hz
stride = 1024       # 50% overlap
segments = []
for start in range(0, eeg_filtered.shape[1] - window_size, stride):
    segments.append(eeg_filtered[:, start:start+window_size])
segments = np.array(segments)  # (n_segments, n_channels, window_size)

# 3. Normalize per-channel to [-1, 1]
for ch in range(segments.shape[1]):
    ch_max = np.abs(segments[:, ch, :]).max()
    segments[:, ch, :] = segments[:, ch, :] / (ch_max + 1e-8)

# 4. Reshape for Conv1D: (batch, time, channels)
segments = segments.transpose(0, 2, 1)  # (n_segments, 2048, 14)
```

## Implementation

### VAE Model

```python
import tensorflow as tf
from tensorflow.keras import layers, Model

class EEG_VAE(Model):
    def __init__(self, latent_dim=32):
        super().__init__()
        self.latent_dim = latent_dim
        
        # Encoder
        self.encoder = tf.keras.Sequential([
            layers.Input(shape=(2048, 14)),
            layers.Conv1D(64, 5, strides=2, padding='same'),
            layers.BatchNormalization(),
            layers.ReLU(),
            layers.Conv1D(128, 5, strides=2, padding='same'),
            layers.BatchNormalization(),
            layers.ReLU(),
            layers.Conv1D(256, 5, strides=2, padding='same'),
            layers.BatchNormalization(),
            layers.ReLU(),
            layers.Flatten(),
            layers.Dense(512),
            layers.ReLU(),
        ])
        
        # Latent space parameters
        self.mu = layers.Dense(latent_dim)
        self.log_var = layers.Dense(latent_dim)
        
        # Decoder
        self.decoder = tf.keras.Sequential([
            layers.Input(shape=(latent_dim,)),
            layers.Dense(256 * 256),
            layers.ReLU(),
            layers.Reshape((256, 256)),
            layers.Conv1DTranspose(128, 5, strides=2, padding='same'),
            layers.BatchNormalization(),
            layers.ReLU(),
            layers.Conv1DTranspose(64, 5, strides=2, padding='same'),
            layers.BatchNormalization(),
            layers.ReLU(),
            layers.Conv1DTranspose(14, 5, strides=2, padding='same'),
            layers.Activation('tanh'),
        ])
    
    def encode(self, x):
        h = self.encoder(x)
        return self.mu(h), self.log_var(h)
    
    def reparameterize(self, mu, log_var):
        epsilon = tf.random.normal(tf.shape(mu))
        return mu + tf.exp(0.5 * log_var) * epsilon
    
    def decode(self, z):
        return self.decoder(z)
    
    def call(self, x, training=False):
        mu, log_var = self.encode(x)
        z = self.reparameterize(mu, log_var)
        x_recon = self.decode(z)
        
        if training:
            # Compute VAE loss
            recon_loss = tf.reduce_mean(
                tf.reduce_sum(tf.square(x - x_recon), axis=[1, 2])
            )
            kl_loss = -0.5 * tf.reduce_mean(
                tf.reduce_sum(1 + log_var - tf.square(mu) - tf.exp(log_var), axis=1)
            )
            self.add_loss(recon_loss + kl_loss)
        
        return x_recon
    
    def generate(self, n_samples=1):
        """Generate new EEG samples from prior"""
        z = tf.random.normal((n_samples, self.latent_dim))
        return self.decode(z)
    
    def interpolate(self, x1, x2, n_steps=10):
        """Interpolate between two EEG segments in latent space"""
        mu1, _ = self.encode(x1)
        mu2, _ = self.encode(x2)
        
        interpolations = []
        for alpha in np.linspace(0, 1, n_steps):
            z = (1 - alpha) * mu1 + alpha * mu2
            x_gen = self.decode(z)
            interpolations.append(x_gen)
        
        return tf.stack(interpolations)
```

### Conditional VAE for Emotion-Conditioned Generation

```python
class EEG_CVAE(Model):
    def __init__(self, latent_dim=32, n_emotions=3):
        super().__init__()
        self.latent_dim = latent_dim
        
        # Encoder (takes EEG + label as input)
        self.encoder = tf.keras.Sequential([
            layers.Input(shape=(2048, 14 + n_emotions)),  # + one-hot labels
            layers.Conv1D(64, 5, strides=2, padding='same'),
            layers.ReLU(),
            layers.Conv1D(128, 5, strides=2, padding='same'),
            layers.ReLU(),
            layers.Flatten(),
            layers.Dense(256),
            layers.ReLU(),
        ])
        
        self.mu = layers.Dense(latent_dim)
        self.log_var = layers.Dense(latent_dim)
        
        # Decoder (takes latent z + label)
        self.decoder = tf.keras.Sequential([
            layers.Input(shape=(latent_dim + n_emotions,)),
            layers.Dense(256 * 256),
            layers.ReLU(),
            layers.Reshape((256, 256)),
            layers.Conv1DTranspose(128, 5, strides=2, padding='same'),
            layers.ReLU(),
            layers.Conv1DTranspose(64, 5, strides=2, padding='same'),
            layers.ReLU(),
            layers.Conv1DTranspose(14, 5, strides=2, padding='same'),
            layers.Activation('tanh'),
        ])
    
    def call(self, x, y, training=False):
        # Concatenate EEG with emotion label
        y_tiled = tf.tile(y[:, tf.newaxis, :], [1, x.shape[1], 1])
        x_cond = tf.concat([x, y_tiled], axis=-1)
        
        # Encode
        h = self.encoder(x_cond)
        mu, log_var = self.mu(h), self.log_var(h)
        
        # Sample
        epsilon = tf.random.normal(tf.shape(mu))
        z = mu + tf.exp(0.5 * log_var) * epsilon
        
        # Decode with conditioning
        z_cond = tf.concat([z, y], axis=-1)
        x_recon = self.decoder(z_cond)
        
        if training:
            recon_loss = tf.reduce_mean(tf.reduce_sum(tf.square(x - x_recon), axis=[1,2]))
            kl_loss = -0.5 * tf.reduce_mean(tf.reduce_sum(
                1 + log_var - tf.square(mu) - tf.exp(log_var), axis=1
            ))
            self.add_loss(recon_loss + kl_loss)
        
        return x_recon
    
    def generate_emotion(self, emotion_label, n_samples=1):
        """Generate EEG for a specific emotion"""
        z = tf.random.normal((n_samples, self.latent_dim))
        y = tf.one_hot([emotion_label] * n_samples, depth=3)
        z_cond = tf.concat([z, y], axis=-1)
        return self.decoder(z_cond)
```

## Applications in EEG Affective Computing

### 1. Data Augmentation

Generate synthetic EEG samples for under-represented emotions:

```python
# Train CVAE on labeled data
cvae = EEG_CVAE()

# Generate synthetic samples for minority class
synthetic_eeg = cvae.generate_emotion(emotion_label='sad', n_samples=500)

# Mix with real data for classifier training
augmented_X = np.concatenate([real_X, synthetic_eeg])
augmented_y = np.concatenate([real_y, ['sad'] * 500])
```

### 2. Emotion Space Interpolation

Explore the continuous nature of emotion:

```python
# Encode EEG from happy and sad trials
z_happy = vae.encode(happy_eeg)
z_sad = vae.encode(sad_eeg)

# Interpolate in latent space
interp_eeg = vae.interpolate(happy_eeg, sad_eeg, n_steps=10)
# Each interpolated EEG should show smooth transition happy → sad
```

### 3. Disentangled Representation Learning

Separate emotion from subject identity using β-VAE:

```python
# Train β-VAE with β > 1
beta_vae = train_beta_vae(eeg_data, beta=4.0)

# After training, some latent dimensions should capture:
# - z[0:4]: Emotion-related factors
# - z[4:8]: Subject identity
# - z[8:16]: Noise and session effects

# Manipulate only emotion dimensions to change emotional content
# while preserving subject identity
```

### 4. Semi-Supervised Emotion Recognition

Leverage unlabeled EEG with VAE pre-training:

```python
# Phase 1: Pre-train VAE on all EEG (labeled + unlabeled)
vae.fit(all_eeg_data, epochs=100)

# Phase 2: Use encoder as feature extractor for classifier
classifier = tf.keras.Sequential([
    vae.encoder,           # Pre-trained encoder (frozen)
    layers.Dense(128),     # New classification head
    layers.Dropout(0.3),
    layers.Dense(3, activation='softmax')
])

classifier.fit(labeled_eeg, labels, epochs=50)
```

### 5. Anomaly Detection

Detect unusual EEG patterns (artifacts, seizures, etc.):

```python
# Train VAE on normal EEG
vae.fit(normal_eeg, epochs=100)

# For new EEG segment:
x_recon = vae(x_new)
recon_error = np.mean((x_new - x_recon) ** 2, axis=(1,2))

# Threshold: flag segments with high reconstruction error
anomaly_threshold = np.percentile(train_recon_errors, 95)
anomalies = recon_error > anomaly_threshold
```

## VAE Variants for EEG

### VQ-VAE (Vector Quantized VAE)

Discrete latent representations — natural for categorical emotion states:

```python
# Instead of continuous z, use a discrete codebook
# z_continuous → nearest codebook vector → z_discrete
# Useful for clustering EEG patterns into discrete emotional categories
```

### Hierarchical VAE

Multiple levels of latent variables:

```
z_high: global factors (subject, session, overall mood)
z_mid: segment-level factors (emotional state)
z_low: sample-level factors (instantaneous fluctuations)
```

### Temporal VAE

Model EEG sequences with recurrent encoder/decoder:

```python
# Encoder: LSTM processes EEG sequence → μ, σ
# Decoder: LSTM generates sequence from z
# Captures temporal dynamics in latent space
```

## Comparison with Other Generative Models

| Aspect | VAE | GAN | Diffusion |
|---|---|---|---|
| **Training stability** | Stable | Can be unstable | Stable |
| **Latent space structure** | Excellent (Gaussian) | None (implicit) | None (implicit) |
| **Sample quality** | Moderate (blurry) | High (sharp) | Highest |
| **Likelihood computation** | Approximate (ELBO) | None | Exact |
| **Inference (encoding)** | Fast | Requires inversion | Requires inversion |
| **Mode coverage** | Good (covers all modes) | Poor (mode collapse) | Good |
| **Disentanglement** | Excellent (β-VAE) | Limited | Limited |

## Best Practices

1. **Use KL annealing**: Gradually increase KL weight from 0 to 1
   ```python
   kl_weight = min(1.0, epoch / warmup_epochs)
   ```

2. **Monitor posterior collapse**: If KL → 0, try:
   - Reducing decoder capacity
   - Using free bits ($\max(\lambda, \text{KL})$) to prevent KL from going too low
   - KL annealing with cyclical schedule

3. **Choose latent dimension wisely**:
   - Too small: insufficient capacity
   - Too large: unused dimensions
   - For EEG: 16-64 dimensions typically work well

4. **Validate generated EEG**:
   - Check PSD of generated vs. real EEG
   - Ensure frequency bands are preserved
   - Verify with domain experts

5. **Architecture for EEG**:
   - Prefer 1D convolutions for raw signals
   - Use 2D convolutions for spectrograms
   - Add channel-wise normalization

## Summary

VAEs provide a principled probabilistic framework for EEG representation learning:

**Key Strengths for EEG**:
- Structured latent space enables emotion interpolation and manipulation
- Disentanglement potential separates emotion from confounding factors
- Natural denoising through reconstruction
- Semi-supervised learning leverages unlabeled EEG
- Stable training compared to GANs

**When to Use VAEs**:
- Need interpretable latent representations of emotional state
- Want to explore continuous emotion space
- Data augmentation with controlled generation
- Semi-supervised learning scenarios
- Anomaly/artifact detection

**When to Prefer Alternatives**:
- Highest sample quality needed → GAN or Diffusion
- Exact likelihood needed → Flow-based models
- Pure data augmentation without interpretation → GAN

---

**Next**: [Generative Adversarial Networks](08-generative-adversarial-networks.md)

## References

- Kingma, D. P., and Welling, M. (2014). Auto-encoding variational Bayes. In *ICLR*.
- Rezende, D. J., Mohamed, S., and Wierstra, D. (2014). Stochastic backpropagation and approximate inference in deep generative models. In *ICML*.
- Sohn, K., Lee, H., and Yan, X. (2015). Learning structured output representation using deep conditional generative models. In *NeurIPS*.
- Higgins, I., Matthey, L., Pal, A., et al. (2017). beta-VAE: Learning basic visual concepts with a constrained variational framework. In *ICLR*.
- van den Oord, A., Vinyals, O., and Kavukcuoglu, K. (2017). Neural discrete representation learning. In *NeurIPS*.
- Chung, J., Kastner, K., Dinh, L., Goel, K., Courville, A., and Bengio, Y. (2015). A recurrent latent variable model for sequential data. In *NeurIPS*.
