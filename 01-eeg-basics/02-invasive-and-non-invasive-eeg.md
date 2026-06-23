# Invasive and Non-Invasive EEG

## Overview

Not all electrophysiological recordings called “EEG-like” are obtained in the same way. A central distinction is between **invasive** and **non-invasive** recordings. For affective computing, most practical systems use non-invasive scalp EEG, but understanding the full spectrum of recording paradigms helps clarify tradeoffs in spatial accuracy, signal fidelity, safety, and usability.

## Non-Invasive EEG

Non-invasive EEG places electrodes on the scalp to measure voltage differences without penetrating tissue.

### Typical Forms

- **Clinical/research scalp EEG** with gel-based electrode caps
- **Dry-electrode EEG** for faster setup
- **Wearable consumer or semi-professional EEG** with fewer channels and simplified placement

### Advantages

- Safe for repeated use
- Suitable for healthy participants
- Practical for large-scale data collection
- Compatible with portable and real-world settings
- Ethically and logistically feasible for affective computing studies

### Limitations

- Lower spatial precision than invasive methods
- Stronger contamination from skull attenuation and artifacts
- Less sensitivity to deep sources
- Greater dependence on preprocessing and robust modeling

## Invasive Recordings

“Invasive EEG” often refers to methods such as:

- **ECoG** (electrocorticography): electrodes placed on the cortical surface
- **SEEG / depth electrodes**: probes inserted into deeper brain structures

These methods are usually performed in clinical settings, for example epilepsy monitoring.

### Advantages

- Higher signal-to-noise ratio than scalp EEG
- Better spatial localization
- Higher-frequency activity can be observed more reliably
- Less distortion from skull and scalp

### Limitations

- Requires surgery
- Not suitable for routine affective computing deployment
- Small and specialized subject populations
- Ethical and clinical constraints limit dataset scale

## Why the Distinction Matters

Even if most EEG affective computing research uses non-invasive systems, invasive recordings provide a useful upper bound on what cleaner and more localized neural signals can reveal. They also remind us that scalp EEG is an indirect, blurred projection of underlying neural processes.

## Tradeoff Summary

| Property | Non-Invasive EEG | Invasive EEG |
|---|---|---|
| **Safety** | High | Low relative to scalp EEG |
| **Ease of deployment** | High | Very low |
| **Spatial precision** | Limited | High |
| **Signal fidelity** | Lower | Higher |
| **Cost and logistics** | Moderate to low | High |
| **Affective computing practicality** | Excellent | Limited |

## Relevance to Wearable Affective Computing

For practical affective computing systems, non-invasive wearable EEG dominates because the target use cases include:

- stress monitoring,
- adaptive interfaces,
- learning analytics,
- entertainment and gaming,
- mental wellness support,
- human-robot interaction.

These settings require comfort, repeatability, and minimal setup burden.

## Beyond the Binary: A Spectrum of Systems

Rather than a strict two-class split, it is more useful to think in terms of a spectrum:

1. invasive intracranial systems,
2. clinical high-density scalp EEG,
3. research-grade portable EEG,
4. wearable dry-electrode systems,
5. consumer low-channel headsets.

As convenience increases, signal quality and spatial detail usually decrease.

## Summary

The invasive vs. non-invasive distinction frames the practical and scientific boundaries of EEG. Affective computing relies mostly on non-invasive EEG because it is deployable and ethical at scale, but understanding invasive systems helps clarify what information is lost when moving to wearable scalp devices.

---

Next: [Signal Characteristics, Noise, and Low SNR](03-signal-characteristics-noise-and-low-snr.md)
