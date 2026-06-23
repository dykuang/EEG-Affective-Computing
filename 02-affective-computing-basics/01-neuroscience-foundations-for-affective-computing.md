# Neuroscience Foundations for Affective Computing

## Overview

Emotion is not a single brain region or process. It arises from coordinated activity across distributed cortical and subcortical circuits. Understanding this neural architecture is important for EEG-based affective computing because it tells us which brain regions and rhythms are likely to carry emotion-relevant information — and which EEG channels may be most informative.

This section introduces the key brain structures, circuits, and neurotransmitter systems involved in emotion, with emphasis on what is accessible through scalp EEG.

## Core Emotion-Related Brain Regions

### The Amygdala

The amygdala is a subcortical structure strongly associated with emotional processing, especially fear, threat detection, and salience.

**EEG relevance**: The amygdala is deep and its activity is not directly visible in scalp EEG. However, its output modulates cortical activity in ways that may be indirectly reflected in frontal and temporal EEG channels.

### The Prefrontal Cortex (PFC)

The prefrontal cortex is involved in emotion regulation, appraisal, and decision-making. Different subregions contribute differently:

- **Dorsolateral PFC**: cognitive control and reappraisal
- **Ventromedial PFC**: valuation and emotional decision-making
- **Orbitofrontal cortex**: reward processing and expectation

**EEG relevance**: PFC activity contributes strongly to frontal EEG channels (Fp1, Fp2, F3, F4, Fz). Frontal alpha asymmetry — a widely studied EEG emotion marker — is thought to reflect differential PFC engagement.

### The Insula

The insula is involved in interoception — the perception of internal bodily states — and is associated with emotional awareness and visceral feelings.

**EEG relevance**: Insula activity may be reflected in central and temporal electrode regions, though deep sources are always harder to localize with scalp EEG.

### The Anterior Cingulate Cortex (ACC)

The ACC plays roles in conflict monitoring, emotional salience, and autonomic regulation.

**EEG relevance**: ACC activity has been linked to frontal midline theta, a rhythm sometimes associated with cognitive and emotional control.

### The Hypothalamus and Brainstem

These structures regulate autonomic and endocrine responses that accompany emotion (heart rate, respiration, hormonal release).

**EEG relevance**: These are not directly visible in scalp EEG, but their downstream effects may influence peripheral measures and, indirectly, cortical state.

## Emotion Circuits, Not Single Regions

Modern neuroscience views emotion as emerging from distributed circuits rather than isolated "emotion centers." Key circuits include:

- **Salience network**: insula, ACC, amygdala — detects relevant stimuli
- **Default mode network**: medial PFC, posterior cingulate — self-referential processing
- **Executive control network**: dorsolateral PFC, parietal regions — regulation

These networks overlap partially with EEG-observable cortical regions, but the full circuit extends beyond what scalp EEG can resolve.

## Neurotransmitter Systems

Several neurotransmitter systems modulate emotional states:

- **Dopamine**: reward, motivation, pleasure
- **Serotonin**: mood regulation, impulse control
- **Norepinephrine**: arousal, alertness, stress response
- **GABA and Glutamate**: inhibitory/excitatory balance

While EEG cannot directly measure neurotransmitter levels, these systems affect the cortical rhythms EEG does measure.

## What Neuroscience Tells EEG Researchers

Several practical lessons follow from the neuroscience:

1. Emotion is **distributed**, so no single EEG channel or band should be expected to capture it fully.
2. Some key structures are **subcortical** — EEG sees only their cortical projections.
3. **Lateralization** is real but complex; frontal asymmetry is not a simple "left = positive, right = negative" rule.
4. **Individual differences** in anatomy and function mean that group-level findings may not transfer perfectly to single subjects.
5. **Temporal dynamics** matter: emotional processing unfolds over hundreds of milliseconds to seconds.

## Implications for EEG-Based Affective Computing

Given the neuroscience:

- Multi-channel EEG is necessary; single-channel systems discard spatial information.
- Frontal, central, and temporal channels are likely to be most informative.
- Time-frequency representations can capture oscillatory signatures of different circuits.
- Individual calibration or subject-specific modeling may improve performance.
- EEG-based emotion recognition is feasible but fundamentally limited by the deep and distributed nature of emotional circuits.

## Summary

Emotion arises from distributed brain circuits involving both cortical and subcortical structures. EEG can access cortical correlates of these circuits — especially in frontal and central regions — but cannot directly measure key subcortical contributors. This neuroscience perspective sets realistic expectations for what EEG-based affective computing can achieve and guides channel selection, feature design, and interpretation.

---

Next: [Emotion Theory: Discrete and Dimensional Models](02-emotion-theory-discrete-and-dimensional.md)
