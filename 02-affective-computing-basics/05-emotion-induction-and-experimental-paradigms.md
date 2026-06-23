# Emotion Induction and Experimental Paradigms

## Overview

The EEG data used in affective computing must come from somewhere. How emotions are induced during recording strongly affects the nature, quality, and ecological validity of the resulting data. This section reviews common induction methods, their strengths and weaknesses, and their implications for EEG-based emotion recognition.

## Stimulus-Based Induction

The most common approach uses carefully selected stimuli to evoke target emotional states.

### Visual Stimuli

**International Affective Picture System (IAPS)** and similar databases provide normed images rated on valence, arousal, and dominance.

Pros:
- well-controlled,
- large normed databases available,
- easy to standardize across studies.

Cons:
- static images may produce weaker emotional responses than dynamic stimuli,
- short duration limits temporal dynamics,
- cultural and individual variability in responses.

### Video Clips

Film clips are widely used because they combine visual, auditory, and narrative elements.

Pros:
- stronger and more sustained emotional induction,
- more ecologically valid than static images,
- well-suited for studying temporal emotion dynamics with EEG.

Cons:
- harder to control the exact timing of emotional peaks,
- individual engagement with narrative varies,
- clips may evoke mixed or evolving emotions.

### Music and Auditory Stimuli

Music can evoke strong emotions without visual input.

Pros:
- modality-specific (useful for studying auditory emotion processing),
- strong arousal induction,
- relatively independent of visual artifacts.

Cons:
- personal music preference strongly affects response,
- genre and cultural familiarity matter,
- harder to norm universally.

### Multi-Modal Stimuli

Combining video, audio, and sometimes tactile or olfactory stimuli often produces the strongest and most reliable emotional responses.

## Interactive and Naturalistic Paradigms

### Game-Based Induction

Video games can induce frustration, excitement, flow, or achievement emotions.

Pros:
- high engagement,
- naturalistic emotional dynamics,
- suitable for studying emotion during active tasks.

Cons:
- motor artifacts in EEG,
- difficult to control the exact timing of emotional events,
- individual skill differences affect experience.

### Social Interaction

Real or simulated social interactions can evoke emotions such as embarrassment, pride, empathy, or social anxiety.

Pros:
- high ecological validity,
- engages social-emotional circuits strongly.

Cons:
- hard to standardize,
- strong individual differences,
- EEG artifacts from speech and movement.

### Recall and Imagery

Participants are asked to recall or imagine emotional experiences.

Pros:
- no external equipment needed beyond EEG,
- can target specific emotions.

Cons:
- hard to verify what the participant is actually experiencing,
- weaker physiological responses than direct stimulation,
- susceptible to demand characteristics.

## Key Experimental Design Choices

### Within-Subject vs. Between-Subject

- **Within-subject**: same participants experience all conditions, reducing individual differences but risking carryover effects.
- **Between-subject**: different participants in different conditions, cleaner comparisons but larger individual variability.

### Trial Duration and Inter-Stimulus Interval

- Short trials (1–5 seconds) are common for ERP studies.
- Longer trials (30 seconds to several minutes) are better for capturing sustained emotional states relevant to affective computing.
- Inter-stimulus intervals should allow emotional responses to return to baseline.

### Baseline Measurement

Always record a pre-stimulus baseline. This allows:

- normalization of individual differences in resting EEG,
- subtraction of pre-existing state effects,
- computation of change scores relative to baseline.

### Counterbalancing and Randomization

Emotion induction order matters because:

- earlier emotions may carry over to later trials,
- fatigue reduces emotional responsiveness,
- practice effects can change task engagement.

Counterbalancing or randomization reduces order confounds.

## Common Affective Computing Datasets

Several well-known datasets use these paradigms:

- **DEAP**: music video clips with valence/arousal/dominance self-ratings
- **SEED / SEED-IV / SEED-V**: film clips inducing discrete emotions
- **MAHNOB-HCI**: video clips with continuous annotation and multiple physiological signals
- **DREAMER**: film clips with EEG and ECG recordings
- **AMIGOS**: combined short and long video clips with personality and mood data

Each dataset makes different choices about induction method, annotation style, and EEG hardware, which affects what models can be trained and how results should be compared.

## Implications for EEG-Based Affective Computing

### Data Quality

- Stronger induction produces larger EEG effects and easier classification.
- But very strong induction may not represent everyday emotional experience.

### Generalization

- Models trained on passive viewing may not transfer to active tasks.
- Models trained on one induction modality (e.g., video) may not work well on another (e.g., music).
- Cross-dataset generalization remains a major challenge.

### Annotation Alignment

- If labels are obtained post-stimulus but EEG is continuous, there is a temporal alignment problem: when exactly did the emotion occur?
- Continuous annotation reduces but does not eliminate this issue.

### Practical Recommendations

1. Choose induction methods that match the target application.
2. Record baselines and demographic/psychological metadata.
3. Use multiple trials per condition for statistical power.
4. Consider both categorical and dimensional annotation.
5. Be transparent about induction methods when reporting results — they strongly affect what conclusions can be drawn.

## Summary

Emotion induction paradigms shape the EEG data that affective computing systems learn from. Stimulus-based methods offer control but may lack ecological validity; interactive paradigms offer realism but introduce artifacts and variability. Understanding these tradeoffs is essential for interpreting model performance and for designing new data collection efforts.

---

**Related Reading**: See [EEG Correlates of Emotion](04-eeg-correlates-of-emotion.md) for how different induction methods engage different EEG signatures.
