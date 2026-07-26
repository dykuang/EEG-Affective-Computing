# Self-Evaluation, Label Noise, and Annotation Challenges

## Overview

In supervised affective computing, the model learns from labelled EEG-emotion pairs. But where do these labels come from? In most cases, from **self-report** — participants rate how they felt during or after exposure to emotional stimuli. Self-report is practical, but it is also subjective, noisy, and influenced by many factors beyond the target emotional state.

This section examines the fuzziness of self-evaluation, the resulting label noise, and the practical implications for training and evaluating EEG-based emotion recognition systems.

> Figure suggestion: Place a timeline here showing stimulus presentation, a possible time-varying latent affect trajectory, EEG windows, and a delayed post-trial rating. This makes the difference between a local signal window and a global self-report label explicit.

## How Emotion Labels Are Typically Obtained

Common annotation methods include:

- **Post-stimulus rating**: after each trial, the participant rates their emotional state
- **Continuous annotation**: participants move a cursor or joystick along valence/arousal axes in real time
- **Retrospective rating**: after the entire session, participants review recordings and annotate
- **Third-party annotation**: external raters label based on facial expressions or context
- **Stimulus-intended emotion**: the emotion the stimulus was designed to evoke (not necessarily what the participant felt)

Each method introduces different types of uncertainty. A post-stimulus score may summarize the peak, final moment, average, or most memorable part of a trial; continuous annotation offers better temporal alignment but has reporting lag and can change the task itself. Third-party labels describe observable expression or stimulus intent, which may be informative but are not interchangeable with the participant's felt experience.

## Sources of Label Noise

### Psychological Sources

- **Alexithymia**: some individuals have difficulty identifying and describing emotions
- **Social desirability**: participants may report what they think is expected
- **Demand characteristics**: awareness of being studied may alter responses
- **Emotional complexity**: real emotional experience is often mixed or ambiguous
- **Recollection bias**: retrospective ratings are distorted by memory

### Methodological Sources

- **Scale granularity**: 5-point vs. 9-point vs. continuous scales affect precision
- **Scale anchoring**: what "neutral" or "maximum arousal" means varies across people
- **Annotation delay**: ratings obtained long after the experience are less reliable
- **Cognitive load**: continuous annotation during a task may interfere with the experience itself

### Individual Differences

- **Personality**: neuroticism, extraversion affect emotional experience and reporting
- **Cultural norms**: display rules and emotional expression norms vary
- **Age and gender**: may affect both experience and willingness to report
- **Mood state**: pre-existing mood biases emotional responses

## Quantifying Label Reliability

### Inter-Rater Reliability

When multiple raters annotate the same stimulus:

- **Cohen's kappa** or **Fleiss' kappa** for categorical labels
- **Intraclass correlation coefficient (ICC)** for continuous ratings
- **Correlation** between raters on dimensional scales, preferably supplemented by agreement measures such as the concordance correlation coefficient

Interpret reliability in the context of the target. Agreement can be lower for subtle or mixed states than for obvious, high-arousal stimuli. Importantly, agreement among observers does not establish agreement with a participant's internal experience.

### Intra-Rater Reliability

The same person rating the same stimulus at different times:

- agreement is often imperfect,
- mood state, fatigue, and context affect ratings,
- even "ground truth" labels are inherently noisy.

## Consequences for Supervised Learning

Label noise has several effects on EEG-based models:

1. **Performance ceiling**: if labels are unreliable, no model can achieve perfect agreement with the observed labels; irreducible uncertainty raises the attainable error floor.
2. **Overfitting to noise**: a powerful model may learn to predict noisy labels rather than true emotional states.
3. **Evaluation inflation**: if test labels are also noisy, reported accuracy may not reflect true generalization.
4. **Loss function sensitivity**: some losses (e.g., cross-entropy with soft labels) handle noise better than others.
5. **Calibration issues**: models trained on noisy labels may produce overconfident predictions.

## Practical Responses to Label Noise

### During Data Collection

- Use multiple annotation modalities (self-report + external rating + physiological)
- Collect continuous annotation where possible
- Include attention and honesty checks
- Record contextual information (mood, fatigue, personality)

### During Training

- Use **label smoothing** or soft targets only when their assumptions match the rating process; indiscriminate smoothing can conceal meaningful minority states
- Apply **robust loss functions** less sensitive to outliers
- Consider **noise-robust training** strategies (e.g., bootstrapping, co-teaching)
- Use **confidence weighting** of samples

### During Evaluation

- Report reliability alongside model results, but do not treat it as a universal numerical upper bound: model-to-rater and rater-to-rater comparisons may differ in task, aggregation, and metric
- Use **multiple annotators** for test labels where possible
- Consider **ordinal** or **ranking-based** metrics rather than exact-match accuracy
- Be cautious about claiming human-level or super-human performance

## The Fuzzy Boundary Between States

Emotions are not discrete switches. The transition from "calm" to "stressed" or from "slightly happy" to "very happy" is gradual. This fuzziness means that:

- discretizing a continuous experience into classes always loses information,
- adjacent categories are often confused even by humans,
- models should ideally express uncertainty about boundary cases.

> Figure suggestion: Add a rating-distribution figure here: several participants give different valence ratings to the same trial, with a wide or bimodal distribution. Contrast it with the single hard label typically passed to a classifier.

## Implications for EEG Affective Computing

For EEG specifically:

- EEG-based models are learning from fuzzy targets with noisy EEG inputs — a doubly difficult problem,
- cross-subject generalization compounds label noise because different subjects may interpret the same scale differently,
- self-supervised and semi-supervised approaches may help by leveraging unlabeled EEG without requiring noisy labels for every sample,
- uncertainty-aware models (e.g., those predicting distributions rather than point estimates) are better suited to noisy annotation regimes.

## Summary

Self-reported emotion labels are the most common supervision signal in affective computing, but they are inherently fuzzy and noisy. This is not a failure of methodology — it reflects the genuine complexity of emotional experience. Acknowledging label noise is essential for setting realistic performance expectations, choosing appropriate evaluation metrics, and designing models that are robust rather than brittle.

---

## References

- Cowen, A. S., and Keltner, D. (2017). Self-report captures 27 distinct categories of emotion bridged by continuous gradients. *Proceedings of the National Academy of Sciences*, 114(38), E7900-E7909.
- Gwet, K. L. (2014). *Handbook of Inter-Rater Reliability* (4th ed.). Advanced Analytics.
- McHugh, M. L. (2012). Interrater reliability: The kappa statistic. *Biochemia Medica*, 22(3), 276-282.
- Ratner, A., Bach, S. H., Ehrenberg, H., Fries, J., Wu, S., and Re, C. (2017). Snorkel: Rapid training data creation with weak supervision. *Proceedings of the VLDB Endowment*, 11(3), 269-282.
- Zeng, J., Liu, R., Li, R., and Tao, D. (2020). A comprehensive survey of label noise in deep learning. *arXiv:2007.08199*.

---

Next: [EEG Correlates of Emotion](04-eeg-correlates-of-emotion.md)
