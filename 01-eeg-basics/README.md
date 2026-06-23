# EEG Basics

## Overview

This chapter introduces the measurement, signal, and tooling foundations needed before discussing affective computing tasks or deep learning models. EEG is powerful because it offers a practical window into neural dynamics, but it is also difficult because the measured signal is indirect, noisy, and highly sensitive to acquisition choices.

For EEG-based affective computing, understanding these basics is essential. A reader who knows what EEG measures, how signal quality is limited, how wearable systems differ from laboratory systems, and which software ecosystems support reproducible workflows will be much better prepared for later chapters.

## Chapter Structure

The chapter is organized from physical measurement to practical workflow:

1. **What EEG Measures**
   - Biophysical origin of scalp EEG
   - Population-level neural activity and voltage measurement
   - Strengths and limitations of EEG relative to other modalities

2. **Invasive and Non-Invasive EEG**
   - Scalp EEG, ECoG, and depth recordings
   - Safety, usability, and deployment tradeoffs
   - Why non-invasive EEG dominates affective computing

3. **Signal Characteristics, Noise, and Low SNR**
   - Physiological and environmental noise sources
   - Why EEG is difficult to model reliably
   - Implications for preprocessing and machine learning

4. **Electrodes, Montages, Wearables, and Source Estimation**
   - 10-20 system, referencing, and channel density
   - Tradeoffs of wearable low-channel devices
   - Source estimation and why it is hard for sparse wearable EEG

5. **Software Ecosystem and Practical Tooling**
   - Python tools such as MNE, SciPy, scikit-learn, Braindecode, and PyTorch
   - MATLAB tools such as EEGLAB and FieldTrip
   - Reproducible workflows for EEG affective computing

## Why This Chapter Matters for Later Chapters

These concepts directly affect everything that follows:

- Chapter 05 depends on understanding acquisition and dataset limitations.
- Chapter 06 depends on signal quality and artifact structure.
- Chapter 07 depends on how EEG can be represented in time, frequency, and space.
- Chapters 08 and 09 depend on realistic expectations about what models can infer from noisy scalp measurements.

## Reading Strategy

A practical order is:

1. start with the physical meaning of EEG,
2. understand the invasive vs. non-invasive distinction,
3. study low SNR and artifact issues,
4. examine wearable systems and source estimation limits,
5. finish with practical software tooling.

---

Next: [What EEG Measures](01-what-eeg-measures.md)
