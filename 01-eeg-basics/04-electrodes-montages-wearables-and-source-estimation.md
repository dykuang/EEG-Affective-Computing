# Electrodes, Montages, Wearables, and Source Estimation

## Overview

EEG quality depends strongly on how and where signals are recorded. Electrode placement, referencing, channel density, and hardware design all affect what can be inferred from the data. For wearable affective computing, this becomes even more important because comfort and portability often force reduced channel counts and less controlled acquisition conditions.

This section introduces the standard electrode system, wearable EEG tradeoffs, and the role and limits of source estimation in low-channel portable settings.

## Electrode Placement and the 10-20 System

The international 10-20 system defines standardized electrode positions across the scalp. Labels such as Fp1, F3, Cz, P4, and O2 indicate approximate anatomical regions:

- **Fp**: frontal pole
- **F**: frontal
- **C**: central
- **P**: parietal
- **O**: occipital
- **T**: temporal
- **z**: midline

This standardization improves reproducibility across studies and makes channel-level interpretations easier.

## Montages and Referencing

EEG measures voltage differences, so the reference matters.

Common choices include:

- linked ears or mastoids,
- common average reference,
- single reference electrode,
- bipolar derivations.

Reference choice affects:

- apparent amplitude,
- channel correlation structure,
- spatial interpretability,
- downstream model features.

## Channel Density Tradeoffs

### High-Density Systems

Advantages:

- better spatial sampling,
- better support for source localization,
- richer connectivity analysis.

Disadvantages:

- longer setup time,
- higher cost,
- reduced comfort,
- less practicality for wearable settings.

### Low-Channel Wearable Systems

Advantages:

- fast setup,
- higher participant comfort,
- practical for real-world deployment,
- lower cost.

Disadvantages:

- reduced spatial coverage,
- weaker support for source estimation,
- higher sensitivity to missing or noisy channels,
- more limited interpretability.

## Wearable EEG in Affective Computing

Wearable EEG is attractive because affective computing often aims for ecological settings rather than tightly controlled laboratory environments.

Typical wearable challenges include:

- dry electrodes with less stable contact,
- frontal-only or sparse channel layouts,
- motion contamination,
- consumer-grade sampling and hardware limits,
- reduced spatial richness.

Nonetheless, wearable systems are often the most relevant for deployment-focused emotion recognition.

## Source Estimation

### What It Is

Source estimation attempts to infer the underlying brain sources that generated the observed scalp EEG.

This is often framed as an **inverse problem**:

- **forward problem**: given sources, predict scalp potentials,
- **inverse problem**: given scalp potentials, infer likely sources.

The inverse problem is ill-posed because many source configurations can produce similar scalp measurements.

## Why Source Estimation Is Difficult

Source estimation depends on:

- accurate head models,
- electrode positions,
- conductivity assumptions,
- sufficient channel coverage,
- suitable regularization.

Errors in any of these reduce reliability.

## Source Estimation for Wearable Devices

For wearable low-channel EEG, source estimation is possible only in a limited and highly constrained sense.

### Main Challenges

- too few channels for stable inverse inference,
- sparse or uneven electrode placement,
- uncertain electrode locations,
- simplified head modeling,
- stronger motion and contact noise.

### What Is Still Reasonable

In wearable settings, source analysis is often used more cautiously for:

- coarse regional inference,
- constrained cortical priors,
- model-based interpretation rather than precise localization,
- fusion with other modalities or prior anatomical information.

### Practical Recommendation

For most low-channel affective computing systems, it is safer to interpret models in terms of:

- scalp regions,
- frequency content,
- channel-level asymmetry,
- connectivity proxies,

rather than claiming precise neural localization.

## Summary

Electrode design and channel configuration strongly shape EEG analysis. High-density systems support richer spatial analysis, while wearable systems favor usability at the cost of spatial precision. Source estimation is theoretically appealing but becomes much less reliable in sparse wearable settings, so conclusions should be framed cautiously.

---

Next: [Software Ecosystem and Practical Tooling](05-software-ecosystem-and-practical-tooling.md)
