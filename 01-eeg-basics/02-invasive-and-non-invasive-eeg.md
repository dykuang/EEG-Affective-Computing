# Invasive and Non-Invasive EEG

## Overview

Not all electrophysiological recordings called “EEG-like” are obtained in the same way. A central distinction is between **invasive** and **non-invasive** recordings. For affective computing, most practical systems use non-invasive scalp EEG, but understanding the full spectrum of recording paradigms helps clarify tradeoffs in spatial accuracy, signal fidelity, safety, and usability.

![Anatomical comparison of recording methods. A shared brain cross-section shows scalp EEG electrodes on the surface, ECoG electrodes resting on the cortical surface, and depth electrodes reaching deeper structures. Callouts summarize invasiveness, spatial precision, signal fidelity, and typical clinical or research use for each method.](figures/anatomical-comparisons.png)

**Figure 1.5: Anatomical comparison of recording methods.** A shared brain cross-section shows scalp EEG electrodes on the surface, ECoG electrodes resting on the cortical surface, and depth electrodes reaching deeper structures. Callouts summarize invasiveness, spatial precision, signal fidelity, and typical clinical or research use for each method.

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

![Recording-system trade-off continuum. From invasive intracranial systems on the left to consumer wearable headsets on the right, spatial precision and signal fidelity decrease while safety, comfort, portability, and scalability increase. Clinical scalp EEG and research-grade portable EEG sit in the middle of the continuum.](figures/tradeoff-continuum.png)

**Figure 1.6: Recording-system trade-off continuum.** From invasive intracranial systems on the left to consumer wearable headsets on the right, spatial precision and signal fidelity decrease while safety, comfort, portability, and scalability increase. Clinical scalp EEG and research-grade portable EEG sit in the middle of the continuum.

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

## References

- Buzsáki, G., Anastassiou, C. A., and Koch, C. (2012). The origin of extracellular fields and currents—EEG, ECoG, LFP and spikes. *Nature Reviews Neuroscience*, 13, 407–420.
- Lachaux, J.-P., Rudrauf, D., and Kahane, P. (2003). Intracranial EEG and human brain mapping. *Journal of Physiology-Paris*, 97(4–6), 613–628.
- Engel, A. K., Moll, C. K. E., Fried, I., and Ojemann, G. A. (2005). Invasive recordings from the human brain: Clinical insights and beyond. *Nature Reviews Neuroscience*, 6, 35–47.
- Lebedev, M. A., and Nicolelis, M. A. L. (2017). Brain–machine interfaces: From basic science to neuroprostheses and neurorehabilitation. *Physiological Reviews*, 97(2), 767–837.
- Casson, A. J., Yates, D. C., Smith, S. J. M., Duncan, J. S., and Rodriguez-Villegas, E. (2010). Wearable electroencephalography. *IEEE Engineering in Medicine and Biology Magazine*, 29(3), 44–56.
