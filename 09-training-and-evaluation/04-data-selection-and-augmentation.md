# Data Selection and Augmentation

Training data are not simply all available windows placed into an optimizer. In affective EEG, recordings can differ sharply in signal quality, label confidence, class coverage, session conditions, and temporal redundancy. Data selection determines which examples contribute to learning; augmentation determines which controlled variations the model should ignore. Both can improve robustness, but both can also change the task or create leakage when applied carelessly.

## Select Data for the Intended Claim

Selection should follow explicit, label-independent criteria whenever possible. Excluding an example because of a documented acquisition failure, missing channels, saturation, or a predefined artifact threshold is different from removing difficult examples because the current model predicts them incorrectly.

Useful selection dimensions include:

| Dimension | Example rule | Main risk if ignored |
| --- | --- | --- |
| Signal quality | Exclude a window exceeding a predeclared artifact or missing-sample threshold | The model learns device failures or artifacts |
| Label confidence | Downweight ratings with low agreement or uncertain pseudo-labels | Noisy targets dominate the loss |
| Temporal redundancy | Subsample heavily overlapping windows or weight source trials equally | Long trials and dense windows dominate training |
| Subject and session coverage | Maintain representation across available subjects and sessions | A few high-volume recordings dominate the model |
| Class or rating coverage | Balance classes or preserve the continuous target range in training | Rare affective states are poorly learned |
| Channel and montage compatibility | Select a predefined common montage or retain a missing-channel mask | Hidden device differences become shortcuts |

Define these rules before examining final test performance. Report the number of retained subjects, sessions, trials, windows, and class or rating values at each selection stage. Retaining fewer examples is not inherently problematic, but it changes the population to which results apply.

## Preserve Independent Units

Selection must respect the same source grouping as the train-test split. If the split unit is a subject, session, or trial, perform quality filtering and balancing within the training partition without allowing selection statistics from held-out units to influence training decisions. All windows from an excluded source interval should be handled consistently when the exclusion is based on recording-level failure.

Do not equalize data by copying or selectively removing test samples. Validation and test partitions should retain their natural prevalence, quality distribution, and temporal structure unless the benchmark itself defines a fixed evaluation subset. Otherwise, reported performance may describe a curated subset rather than the intended deployment population.

## Augmentation Principles for EEG

An augmentation is appropriate only when it preserves the target-relevant signal properties while simulating variation that may occur in deployment. The correct augmentation depends on the task, representation, montage, sampling rate, and generalization claim. A transformation that is useful for within-subject classification can be harmful for event timing, connectivity analysis, source localization, or online tracking.

Apply stochastic augmentation to training data only. Fix augmentation ranges before final testing, choose them with validation data, and include an ablation showing whether each augmentation improves performance on untouched real recordings. Never use test recordings to estimate augmentation statistics or to tune transformation strength.

## Common EEG Augmentations

| Augmentation | Intended invariance | Suitable use | Main caution |
| --- | --- | --- | --- |
| Small amplitude scaling | Gain and impedance variation | Robustness to modest amplitude changes | Large scaling can erase subject or condition differences |
| Additive sensor noise | Low-level acquisition noise | Robustness to realistic noise levels | Synthetic noise should reflect the device, not arbitrary corruption |
| Temporal masking | Brief missing or unreliable samples | Self-supervised reconstruction and robust encoders | Can remove short event-related effects |
| Channel masking or dropout | Temporary channel failure | Models designed to tolerate missing electrodes | Do not mask critical channels without a realistic deployment rationale |
| Frequency-band masking | Partial spectral corruption | Representation learning or spectral robustness | May destroy the frequency content that defines the affective target |
| Limited time shift or crop | Small onset uncertainty | Long steady-state windows | Invalid for precise event-locked tasks |
| Segment mixing | Regularization for trial-level labels | Large, homogeneous labeled trials | Mixed segments may have ambiguous affect labels |
| Spatial perturbation | Small electrode-placement variation | Dense montages with known geometry | Requires physiologically plausible interpolation |

Augment raw EEG, features, and time-frequency representations differently. For example, time-frequency masking may be reasonable for a spectrogram encoder but does not correspond directly to a physical electrode perturbation. State the representation on which an augmentation is applied.

## Selection and Augmentation for Labels

Labels determine which transformations are valid. With trial-level labels inherited by short windows, aggressive temporal crops, time shifts, or segment mixing can increase the mismatch between the local EEG and the global label. With continuous labels, any time transformation must transform the label trajectory consistently and preserve causal availability.

For class imbalance, prefer class-weighted losses or balanced training batches before duplicating highly overlapping windows. When synthetic augmentation is used to balance a class, report the real and augmented counts separately. Generated EEG from a generative model is also augmentation: train the generator on training data only, and evaluate downstream benefit on a real, untouched test set.

## Selection and Augmentation for Self-Supervision

In self-supervised learning, augmentations define the learning objective. Contrastive positive pairs should differ only in properties that the downstream representation is intended to ignore. Generative masking should conceal information that can be predicted from meaningful context rather than trivial acquisition artifacts. Pseudo-label selection should use confidence, consistency, or cluster-stability rules fitted without test labels.

**Example scenario:** For cross-subject emotion classification with a wearable 14-channel montage, training windows are first filtered by a predeclared artifact-quality rule. A contrastive encoder then receives two mild views of each retained window: small amplitude scaling and one randomly masked noncritical channel. The same quality rule and channel ordering are applied to validation and test data, but no augmentation is applied there. The augmentation strengths are selected using only source-subject validation folds.

## Evaluate the Effect of Data Choices

Data selection and augmentation are modeling choices, so evaluate them with controlled comparisons. Keep the model, split, and training budget fixed while comparing a no-augmentation baseline, individual transformations, and the final augmentation policy. Report retained-data counts, class balance, augmentation probability and magnitude, and results by independent subject or session.

A useful sensitivity analysis asks whether the result remains stable when quality thresholds, sampling ratios, or augmentation strengths change moderately. A large gain that disappears under a small threshold change may indicate that the method depends on a narrow curation choice rather than learning a robust affective representation.

## Checklist

Before training with selected or augmented data, verify that:

- selection criteria are predeclared, auditable, and not based on final test predictions;
- counts and exclusions are reported by subject, session, trial, and class or target range;
- balancing and augmentation operate only on the training partition;
- transformations preserve the target, timing, spatial structure, and causal assumptions of the task;
- validation selects selection thresholds and augmentation strengths;
- final evaluation uses untouched real data with natural prevalence; and
- ablations quantify the contribution and sensitivity of each data choice.

## References

- Iwana, B. K., and Uchida, S. (2021). An empirical survey of data augmentation for time series classification with neural networks. PLOS ONE, 16(7), e0254841.
- Lashgari, E., Liang, D., and Maoz, U. (2020). Data augmentation for deep-learning-based electroencephalography. Journal of Neuroscience Methods, 346, 108885.
- Shorten, C., and Khoshgoftaar, T. M. (2019). A survey on image data augmentation for deep learning. Journal of Big Data, 6, 60.
- Um, T. T., Pfister, F. M. J., Pichler, D., et al. (2017). Data augmentation of wearable sensor data for Parkinson's disease monitoring using convolutional neural networks. arXiv:1706.00527.
