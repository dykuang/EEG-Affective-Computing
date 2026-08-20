# What EEG Measures

## Overview

Electroencephalography (EEG) records electrical activity associated with neural population dynamics, mainly from cortical pyramidal cells whose synchronized post-synaptic potentials create measurable voltage fluctuations at the scalp. For affective computing, EEG is attractive because it offers millisecond-level temporal resolution, is relatively affordable, and can be used in both laboratory and increasingly wearable settings.

This section introduces what EEG physically measures, why it differs from other neuroimaging modalities, and why it is particularly relevant for emotion-related brain analysis.

![From cortical activity to scalp EEG. Synchronized synaptic input to aligned pyramidal neurons creates a macroscopic electrical field that propagates through brain tissue, cerebrospinal fluid, skull, and scalp before being recorded as a multichannel voltage trace. The inset contrasts a single-neuron action potential with the slower population-level postsynaptic activity that dominates scalp EEG.](figures/biophysical-origin.png)

## The Biophysical Origin of EEG

Individual neurons generate action potentials, but scalp EEG is dominated less by isolated spikes and more by the aggregate synchronous activity of large populations of neurons. When many similarly oriented pyramidal neurons in cortex receive synaptic input, their extracellular currents sum and create dipole-like fields.

A simplified view is:

1. Synaptic activity occurs in cortical tissue.
2. Large-scale alignment and synchrony produce a macroscopic electrical field.
3. That field propagates through brain tissue, cerebrospinal fluid, skull, and scalp.
4. Electrodes measure voltage differences between locations.

EEG is therefore a **population-level surface measurement**, not a direct recording of single-neuron firing.

## Why EEG Is Useful

EEG is widely used because it combines several desirable properties:

- **High temporal resolution**: millisecond-scale changes can be tracked.
- **Relatively low cost** compared with MRI or MEG.
- **Portability**: systems range from clinical high-density caps to wearable headsets.
- **Non-invasive deployment** in most affective computing settings.
- **Compatibility with naturalistic experiments** such as video watching, music listening, gaming, and human-computer interaction.

For emotion recognition, these strengths matter because emotional responses evolve quickly and are often embedded in ongoing cognition and behavior.

## What EEG Does Not Measure Well

EEG also has clear limitations:

- **Poor spatial resolution** relative to fMRI or invasive recordings.
- **Sensitivity to artifacts** from eye motion, muscle activity, and line noise.
- **Volume conduction** blurs the relationship between neural sources and measured scalp signals.
- **Weak deep-brain sensitivity** compared with cortical surface activity.

These limitations shape later modeling and preprocessing choices.

![Modality trade-off map. Temporal resolution increases to the right and spatial resolution increases upward; marker size reflects typical cost. EEG offers excellent temporal resolution and moderate-to-low spatial resolution at relatively low cost, making it practical for affective computing compared with MEG, fMRI, ECoG/intracranial EEG, and fNIRS.](figures/modality-comparison.png)

## EEG Compared with Other Brain Measurement Modalities

| Modality | Temporal Resolution | Spatial Resolution | Invasiveness | Typical Cost | Affective Computing Relevance |
|---|---|---|---|---|---|
| **EEG** | Excellent | Moderate to low | Usually non-invasive | Low to moderate | Strong |
| **MEG** | Excellent | Better than EEG | Non-invasive | Very high | Strong but less portable |
| **fMRI** | Poor | Excellent | Non-invasive | Very high | Useful for localization, weak for real-time affect |
| **ECoG / intracranial EEG** | Excellent | High | Invasive | Clinical/research only | Rare but valuable |
| **fNIRS** | Moderate | Moderate | Non-invasive | Moderate | Portable but slower than EEG |

EEG occupies a practical middle ground: it sacrifices spatial precision in exchange for speed, accessibility, and deployability.

## EEG in Affective Computing

Emotion-related EEG studies often focus on signals associated with:

- **valence**, often linked to frontal asymmetry,
- **arousal**, often linked to global power redistribution,
- **dominance** or related multidimensional affect scales,
- discrete categories such as joy, fear, calmness, or stress.

EEG supports both:

- **offline analysis**, where signals are processed after acquisition, and
- **online inference**, where emotion is estimated during interaction.

![Affective-computing workflow. A stimulus or interaction is followed by EEG acquisition, preprocessing and artifact handling, feature representation, model inference, and finally an emotion estimate (valence, arousal, dominance, or discrete category). The lower branch separates offline batch analysis from online low-latency inference.](figures/affective-computing-workflow.png)

## Core Measurement Concepts

A few basic terms are important early:

- **Channel**: a recorded signal corresponding to a specific electrode configuration.
- **Reference**: the baseline against which voltages are measured.
- **Montage**: the arrangement and combination of recorded channels.
- **Sampling rate**: how often the signal is recorded per second.
- **Bandwidth**: the frequency range retained after acquisition and preprocessing.

These choices strongly affect data quality and downstream model behavior.

![Core measurement concepts. Top left: electrode positions on a 10-20 layout with a reference electrode highlighted. Top right: a montage expressed as bipolar channel combinations. Bottom left: the same continuous waveform sampled at high and low rates. Bottom right: a power spectrum with the retained bandwidth shaded and the 50/60 Hz notch removed.](figures/core-measurements.png)

## Summary

EEG measures large-scale electrical activity at the scalp produced primarily by synchronized cortical population dynamics. Its high temporal resolution and practical accessibility make it a central modality for affective computing, but its limitations in spatial precision and susceptibility to noise require careful experimental design and processing.

---

Next: [Invasive and Non-Invasive EEG](02-invasive-and-non-invasive-eeg.md)
