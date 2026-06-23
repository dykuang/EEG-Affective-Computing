# Affective Computing Basics

## Overview

Affective computing sits at the intersection of neuroscience, psychology, and engineering. Before building models that recognize emotion from EEG, we need to understand what emotion is, how it is organized in the brain, how it is measured and labelled, and how it manifests in electrophysiological signals.

This chapter provides that multidisciplinary foundation. It covers the neural substrates of emotion, major psychological theories (discrete and dimensional), the practical difficulty of obtaining reliable emotion labels, and the EEG correlates that make emotion recognition from brain signals possible.

## Chapter Structure

The chapter organizes these topics in a logical chain from brain to measurement to experiment:

1. **Neuroscience Foundations for Affective Computing**
   - Brain regions and circuits involved in emotion
   - The limbic system, prefrontal cortex, and insula
   - Neurotransmitter systems and their emotional roles
   - What fMRI and lesion studies tell us about the emotional brain

2. **Emotion Theory: Discrete and Dimensional Models**
   - Discrete emotion categories (Ekman, Plutchik)
   - Dimensional models (valence, arousal, dominance)
   - Appraisal theories and constructivist views
   - Which frameworks suit EEG-based recognition

3. **Self-Evaluation, Label Noise, and Annotation Challenges**
   - How emotion labels are obtained
   - The fuzziness of self-report
   - Inter-rater and intra-rater reliability
   - Implications for supervised learning from EEG

4. **EEG Correlates of Emotion**
   - Frontal alpha asymmetry and valence
   - Frequency band power and arousal
   - Connectivity and network-level emotional signatures
   - What EEG can and cannot tell us about emotion

5. **Emotion Induction and Experimental Paradigms**
   - Stimulus-based induction (images, video, music, sounds)
   - Interactive and naturalistic paradigms
   - Design choices that affect EEG data quality
   - Common affective computing datasets and their induction methods

## Why This Chapter Matters

Every subsequent chapter depends on assumptions established here:

- The choice between discrete and dimensional emotion models determines the output space for classifiers and regressors.
- Understanding label noise helps interpret why model accuracy has ceilings.
- Knowing EEG-emotion correlates guides feature engineering and architecture design.
- Awareness of induction paradigms clarifies why datasets differ and how to evaluate generalization.

## Reading Strategy

A practical reading order follows the chapter structure:

1. Start with neuroscience to understand where emotion lives in the brain.
2. Study emotion theories to know what we are trying to measure.
3. Confront label noise to appreciate why emotion recognition is hard.
4. Learn EEG-emotion correlates to connect theory to signal.
5. Review experimental paradigms to understand how data is collected.

---

Next: [Neuroscience Foundations for Affective Computing](01-neuroscience-foundations-for-affective-computing.md)
