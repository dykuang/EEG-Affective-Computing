# EEG Correlates of Emotion

## Overview

This section bridges the neuroscience and emotion theory of earlier sections with the EEG signal that will be used in later chapters. It describes the main EEG features and patterns that have been linked to emotional states, organized by frequency band, spatial distribution, and connectivity.

These correlates are not deterministic rules — they are statistical regularities observed across studies, subjects, and paradigms. They provide useful starting points for feature engineering and interpretation, but they should not be treated as infallible markers.

## Frontal Alpha Asymmetry

One of the most studied EEG-emotion relationships involves the balance of alpha power between left and right frontal regions.

### The Basic Finding

Greater relative left frontal activity (i.e., lower alpha power on the left) has been associated with:

- positive affect,
- approach motivation,
- higher self-reported valence.

Greater relative right frontal activity has been associated with:

- negative affect,
- withdrawal motivation,
- lower self-reported valence.

### Measurement

Frontal alpha asymmetry is often quantified as:

$$\text{FAA} = \ln(\alpha_{\text{right}}) - \ln(\alpha_{\text{left}})$$

where $\alpha$ denotes alpha-band power at homologous frontal electrode pairs (e.g., F4 and F3).

Positive FAA scores indicate relatively greater left frontal activity.

### Important Caveats

- The effect is statistical, not deterministic at the single-trial level.
- Individual differences in anatomy and alpha generation affect measurement.
- Reference choice influences asymmetry estimates.
- The relationship is more robust for trait affect than for moment-to-moment fluctuations.
- Not all studies replicate the simple left-positive/right-negative mapping.

## Frequency Band Power and Arousal

Arousal is often linked to broader spectral changes rather than lateralized ones.

### General Patterns

- **Higher arousal** is generally associated with:
  - decreased alpha power,
  - increased beta and gamma power,
  - shifts in theta/beta ratio.

- **Lower arousal** (calm, drowsy states) is generally associated with:
  - increased alpha power,
  - increased theta power in some contexts.

### Theta/Beta Ratio

The ratio of theta to beta power has been used as an index of cortical arousal and attention, with higher ratios sometimes associated with lower arousal or attentional deficits.

## Gamma Activity and Emotion

Gamma-band activity (above 30 Hz) has been linked to:

- emotional processing intensity,
- conscious emotional experience,
- integration of emotional information across brain regions.

However, gamma is also highly susceptible to muscle artifacts, so careful preprocessing is essential.

## Spatial Patterns Across Channels

Different emotions may engage different spatial patterns:

- **Frontal regions**: strongly implicated in valence and regulation.
- **Central regions**: involved in arousal and motor preparation associated with emotion.
- **Temporal regions**: involved in processing emotional sounds, faces, and memories.
- **Parietal regions**: involved in emotional attention and representation.
- **Occipital regions**: mainly driven by visual stimulus properties rather than emotion per se.

This spatial distribution is one reason multi-channel EEG is preferable to single-channel systems.

## Connectivity and Network-Level Correlates

Beyond power at individual channels, emotion affects how brain regions communicate:

- **Functional connectivity** measured by correlation, coherence, or phase synchrony between channels,
- **Effective connectivity** attempting to model directed influence,
- **Graph-theoretic measures** that summarize network organization.

Emotional states can shift the balance between different large-scale networks, and these shifts may be detectable in EEG connectivity measures.

## Event-Related Potentials (ERPs) and Emotion

Though affective computing often uses continuous or longer-duration EEG, ERPs provide important evidence about emotional processing:

- **Late Positive Potential (LPP)**: enhanced for emotionally salient stimuli, modulated by arousal,
- **N170 and EPN**: early components modulated by emotional faces and scenes,
- **P300**: modulated by emotional relevance and novelty.

These ERP findings help validate that the EEG signal does carry emotion-relevant information.

## Individual Differences

EEG-emotion relationships vary substantially across individuals due to:

- skull thickness and conductivity,
- cortical folding and anatomy,
- baseline alpha power,
- personality traits,
- emotional reactivity,
- age and sex.

This is a major reason why subject-independent emotion recognition is harder than subject-dependent recognition.

## What EEG Cannot Tell Us About Emotion

It is equally important to recognize the limits:

- EEG cannot directly measure deep brain structures critical for emotion.
- EEG cannot distinguish between different emotions that produce similar cortical patterns.
- EEG-emotion relationships are correlational, not causal.
- Single-trial classification is difficult because the signal is noisy and the emotional signal is weak.
- EEG alone may not be sufficient for fine-grained emotion discrimination without additional context.

## Summary

EEG carries measurable correlates of emotional states, particularly in frontal alpha asymmetry, frequency band power distributions, and connectivity patterns. These correlates provide useful features and interpretive anchors, but they are statistical regularities, not deterministic signatures. Their reliability varies across individuals, paradigms, and recording conditions, which is why data-driven approaches that learn features from data have become popular.

---

Next: [Emotion Induction and Experimental Paradigms](05-emotion-induction-and-experimental-paradigms.md)
