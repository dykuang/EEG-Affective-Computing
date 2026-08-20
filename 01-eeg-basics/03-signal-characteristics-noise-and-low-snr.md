# Signal Characteristics, Noise, and Low SNR

## Overview

A central fact about EEG is that the useful neural signal is weak relative to many nuisance sources. This is the **low signal-to-noise ratio (SNR)** problem. Any serious EEG pipeline, whether classical or deep-learning-based, must be designed with this issue in mind.

## Why EEG Has Low SNR

The measured scalp potential is the result of several attenuating and mixing processes:

- neural activity is spatially aggregated,
- the skull acts as a low-conductivity barrier,
- scalp and environmental sources contaminate recordings,
- reference choices can reshape apparent signal structure.

As a result, emotionally relevant brain activity may be subtle compared with noise and artifacts.

## Common Noise and Artifact Sources

### Physiological Artifacts

- **EOG**: eye blinks and eye movements
- **EMG**: facial and neck muscle activity
- **ECG leakage**: cardiac contamination in some channels
- **Motion artifacts**: electrode shifts and cable movement

### Environmental Artifacts

- line noise at 50/60 Hz,
- poor shielding or grounding,
- nearby electronics,
- impedance instability.

### Experimental and Behavioral Confounds

- subject fatigue,
- attention fluctuation,
- posture changes,
- task noncompliance,
- emotional self-report inconsistency.

<!-- Figure suggestion: Add an artifact gallery with short aligned EEG traces labeled EOG blink, eye movement, EMG, ECG leakage, motion/electrode shift, and 50/60 Hz line noise. Include small sketches of the likely source or recording setup beside each trace, and keep the neural EEG trace visually distinct from the contaminating component. -->

## Signal-to-Noise Ratio

SNR is often described conceptually as

$$\mathrm{SNR} = \frac{\text{power of desired signal}}{\text{power of noise}}.$$

Low SNR means that the neural activity of interest contributes only a small fraction of the observed variance.

In affective computing, this is especially problematic because emotional effects are often subtler than motor or visual evoked responses.

<!-- Figure suggestion: Add a signal-mixture schematic showing a weak neural component combined with EOG, EMG, line noise, and motion artifact to form the observed scalp recording. Include a second panel showing attenuation and spatial mixing through brain tissue, skull, and scalp, ending with a low-SNR waveform. -->

## EEG Frequency Bands

Although exact boundaries vary across studies, EEG is commonly analyzed in bands such as:

- **Delta**: roughly 0.5–4 Hz
- **Theta**: roughly 4–8 Hz
- **Alpha**: roughly 8–13 Hz
- **Beta**: roughly 13–30 Hz
- **Gamma**: above 30 Hz

These bands are useful abstractions, but low SNR means that estimated band power can be strongly affected by artifacts and preprocessing decisions.

<!-- Figure suggestion: Add a frequency-axis diagram spanning 0.5 Hz to the gamma range, with delta, theta, alpha, beta, and gamma bands marked using restrained colors. Pair it with a representative power spectrum and note that boundaries vary across studies and that artifact energy can overlap these bands. -->

## Why Low SNR Matters for Modeling

Low SNR affects downstream learning in several ways:

- models may overfit artifact patterns,
- useful features may be swamped by nuisance variation,
- deeper models may amplify noise if data are limited,
- subject transfer becomes harder because nuisance structure differs across people.

Thus, EEG modeling is never only a modeling problem. It is also a signal quality problem.

## Strategies to Improve Effective SNR

### During Acquisition

- improve electrode contact quality,
- reduce motion and environmental interference,
- use appropriate referencing,
- monitor impedance,
- design tasks that reduce unnecessary movement.

### During Preprocessing

- bandpass filtering,
- notch filtering,
- artifact rejection,
- ICA or related decomposition methods,
- rereferencing,
- segmentation and baseline correction.

### During Modeling

- robust feature extraction,
- channel selection,
- regularization,
- subject-aware validation,
- augmentation that respects signal structure.

<!-- Figure suggestion: Add a three-stage pipeline showing acquisition, preprocessing, and modeling, with the effective SNR improving after contact-quality checks, filtering, artifact handling, and robust validation. Mark the failure mode at each stage: artifact-driven features, nuisance-dominated representations, and subject-specific overfitting. -->

## Event-Related vs. Ongoing EEG

SNR can be improved by averaging when responses are time-locked and repeatable, as in classical event-related potential studies. But affective computing often uses more continuous and naturalistic paradigms, where averaging is less straightforward. This is one reason why low SNR remains a central challenge for emotion-related EEG.

## Summary

EEG has inherently low SNR because weak neural signals are mixed with substantial physiological, environmental, and experimental noise. Understanding this constraint is essential for later decisions about preprocessing, feature representation, and deep model design.

---

Next: [Electrodes, Montages, Wearables, and Source Estimation](04-electrodes-montages-wearables-and-source-estimation.md)
