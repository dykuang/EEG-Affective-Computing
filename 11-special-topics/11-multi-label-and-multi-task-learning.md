# Multi-Label and Multi-Task Learning for Affective EEG

Many affective EEG studies compress supervision into one target: a discrete emotion class, a high/low valence bin, or a single regression score. Real recordings frequently support several related but non-identical targets at once. A trial may be high in arousal, moderately positive in valence, cognitively demanding, and affected by artifacts; the same recording may also have a quality flag, task event label, behavioral outcome, and session context. Multi-label and multi-task learning offer ways to use this structure without pretending that every target has the same meaning, reliability, or temporal resolution.

The distinction matters. **Multi-label learning** predicts multiple labels for one input under one label vocabulary, such as the simultaneous presence of several affect descriptors. **Multi-task learning** shares a representation across different prediction tasks, such as valence-arousal regression, workload classification, signal-quality estimation, and subject-invariant representation learning. Both can improve sample efficiency, but both can also transfer bias, noise, or artifacts from one target to another.

## Problem Formulation

Let $$x$$ be an EEG segment or trial and let $$h_\theta(x)$$ be a shared encoder. In a multi-label problem, the target is a binary vector $$y \in \{0, 1\}^K$$, where several entries can be positive. Independent sigmoid heads produce

$$
\hat{y}_k = \mathrm{sigmoid}(w_k^\top h_\theta(x) + b_k),
$$

and a masked binary cross-entropy objective can accommodate labels that were not annotated:

$$
\mathcal{L}_{\mathrm{ML}} =
-\frac{1}{\sum_k m_k}
\sum_{k=1}^{K} m_k
\left[y_k \log \hat{y}_k + (1-y_k)\log(1-\hat{y}_k)\right],
$$

where $$m_k=1$$ only when label $$k$$ is observed. An unobserved label is not automatically a negative label. This distinction is essential in affective data, where an annotator may report one salient state without ruling out other states.

In multi-task learning, each task $$t$$ has an appropriate head and loss:

$$
\mathcal{L}_{\mathrm{MTL}} = \sum_{t=1}^{T} \lambda_t \mathcal{L}_t,
$$

where tasks can use classification, ordinal, regression, ranking, reconstruction, or contrastive objectives. The weights $$\lambda_t$$ should not be chosen merely because one loss has a larger numeric scale; they encode which tasks the representation is asked to prioritize.

![One quality-controlled EEG recording enters a shared temporal-spatial encoder. Multi-label heads can express co-occurring affect descriptors, while task-specific heads estimate dimensional affect, workload, signal quality, or a trial-level summary. Labels can be missing at different heads, and only observed targets contribute loss.](figures/multi-label-multi-task-eeg.png)

*Figure 1. Shared-representation learning with distinct multi-label and multi-task heads. Target type, annotation source, time scale, and missingness policy must be declared for every head.*

## Multi-Label Learning: Co-Occurring Affect Is Not a Single Class

Multi-label prediction is appropriate when multiple labels can truthfully apply to the same **unit of analysis**. Examples include:

- separate, non-exclusive affect descriptors such as anxious, engaged, frustrated, or calm;
- simultaneous emotion and appraisal descriptors, such as high arousal, positive valence, and high motivational relevance;
- multiple event or symptom flags in clinical or naturalistic recordings;
- and concurrent data-quality annotations, such as eye-movement artifact, muscle artifact, or poor electrode contact.

This differs from mutually exclusive categories, where one softmax class is intended to be selected. It also differs from a trial-level label copied to each short EEG window: that is a weak-supervision and temporal-granularity problem, discussed in the local-segment/global-trial section, rather than proof that every window has multiple known labels.

### Label Correlation, Imbalance, and Thresholds

Independent sigmoid outputs are a baseline, not an assumption that labels are independent. Affect labels often co-occur or exclude one another. Label-graph layers, classifier chains, conditional random fields for temporal sequences, or a low-rank label embedding can model these dependencies. Any learned association must be checked against annotation practices: a label pair may correlate because of the rating interface or dataset design, not because of a stable psychological relationship.

Multi-label data are frequently imbalanced. Report per-label prevalence and use class-weighted loss, focal variants, carefully sampled batches, or threshold selection on validation data when justified. A global threshold of $$0.5$$ is rarely appropriate for every label. Thresholds must be selected without looking at the final test partition and should be reported with calibration measures.

### Partial, Positive-Unlabeled, and Ambiguous Labels

The common convention that an absent annotation means ``negative'' can create systematic false negatives. Depending on the protocol, an absent label may be unknown, unasked, below a reporting threshold, or genuinely absent. Choose the training formulation accordingly:

| Annotation condition | Safer formulation | What not to assume |
| --- | --- | --- |
| Explicitly rated all labels | Standard multi-label loss | Ratings are noise-free or equally reliable |
| Only selected labels are recorded | Positive-unlabeled or missing-label learning | Unselected labels are definitely absent |
| Multiple raters disagree | Soft label distributions or annotator-aware models | A majority label is psychological ground truth |
| Labels apply to a whole trial | Multi-instance or sequence-level supervision | Every local window has each trial label |
| Labels are ordered ratings | Ordinal or distributional heads | Adjacent scale values are equally separated |

Positive-unlabeled learning requires assumptions about how positives were selected. If these assumptions are not defensible, a masked loss and explicit uncertainty are usually more honest than fabricated negative labels.

## Multi-Task Learning: Share What Is Shared, Separate What Is Not

Multi-task learning uses one or more shared encoders with task-specific heads. It is useful when tasks draw on partly shared signal structure but retain different targets and decision rules.

| Auxiliary task | Potential contribution to affective EEG | Important constraint |
| --- | --- | --- |
| Valence and arousal regression | Retains dimensional structure instead of only a discrete label | Respect subject-specific scale use and ordinal uncertainty |
| Workload, attention, or fatigue estimation | Learns broad state-related temporal features | Avoid presenting related constructs as interchangeable emotions |
| Signal-quality and artifact detection | Encourages the system to recognize unreliable windows | Quality estimates must not silently remove harder user groups |
| Subject or device adaptation | Supports nuisance-aware representations | Use adversarial invariance only when identity is truly nuisance information |
| Event or stimulus-phase prediction | Aligns representations with experimental timing | Do not mistake stimulus decoding for affect decoding |
| Reconstruction or masked modeling | Adds unlabeled EEG structure | Keep self-supervised pretraining and supervised evaluation partitions separate |

Architectures can share all early layers, share only a backbone with lightweight adapters, or use mixture-of-experts/gated sharing when task relatedness varies. Hard sharing is simple and parameter-efficient; cross-stitch, adapter, or mixture-of-experts designs can reduce negative transfer but introduce more parameters and tuning choices.

### Task Balancing and Negative Transfer

An auxiliary task helps only if its gradients improve the primary task under the intended transfer setting. A high-resource auxiliary task can dominate a small affect dataset, and an artifact or subject-identity task can lead the encoder to rely on exactly the nuisance structure that should be controlled.

Start with a primary-task-only baseline and add one auxiliary target at a time. Compare fixed loss weights with validated dynamic methods such as uncertainty weighting, GradNorm, or gradient-conflict approaches such as PCGrad. Report each task separately, not only a composite loss. If the primary task degrades under the same split and tuning budget, the result is negative transfer, not a successful multi-task model.

## Hierarchical, Multi-View, and Weakly Supervised Extensions

Several related formulations are often confused with multi-label or multi-task learning:

- **Hierarchical classification** predicts labels at multiple levels of one taxonomy, such as broad positive/negative affect and a more specific category. Hierarchical losses can prevent logically inconsistent predictions, but the taxonomy must be justified.
- **Multi-view learning** combines different views of one example, such as raw EEG, spectrograms, connectivity features, or EEG plus peripheral physiology. This concerns inputs, whereas multi-task learning concerns outputs; a system can be both multi-view and multi-task.
- **Multi-instance learning** maps a bag of local segments to a trial-level target. It addresses the time-scale mismatch of global affect labels and should not be described as ordinary multi-label learning unless local co-occurrence is observed.
- **Multi-modal learning** aligns or fuses data from several sensors or context sources. It can provide auxiliary tasks, but modality agreement is not evidence that one modality directly observes the same latent state.
- **Continual and class-incremental learning** adds tasks or classes over time. It needs replay, regularization, or modular adaptation to avoid forgetting; it is not implied by training multiple tasks at once.

## EEG-Specific Design and Evaluation

### Align Targets Before Sharing a Model

For every target, document the unit of analysis, annotation source, time stamp, temporal support, scale, uncertainty, and missingness mechanism. A trial-level self-report, an event-aligned workload probe, and a window-level artifact flag should not be treated as interchangeable labels attached to one window.

When temporal scales differ, use an architecture that exposes the aggregation path: local encoders can feed segment-level heads, while attention, pooling, or a temporal decoder feeds trial-level heads. This avoids leaking global labels into all local windows and makes each loss correspond to evidence that was actually observed.

### Prevent Cross-Task Leakage

Multi-task learning can create hidden shortcuts. For example, a model may infer emotion from stimulus identity, session number, device type, or subject identity if those are correlated with labels. It can also learn a quality head from data collected only in one class. Split data by subject, session, recording, and stimulus as required by the deployment claim; fit normalization and all learned task weights on training data only.

When using self-supervised pretraining, auxiliary datasets, or calibration examples, state whether identities, recordings, or near-duplicate trials overlap with downstream validation or test data. A clean final test requires those overlaps to be excluded when the claim is transfer to unseen people, sessions, sites, or devices.

### Metrics That Match the Claim

Report more than a micro-averaged score dominated by common labels:

- for multi-label tasks: per-label precision-recall curves, macro and micro F1, average precision, calibration, and threshold-selection protocol;
- for continuous tasks: MAE/RMSE with concordance or rank correlation where appropriate, plus calibration or uncertainty quality;
- for ordinal ratings: ordinal-aware losses and metrics such as quadratic weighted kappa when the scale assumptions fit;
- for multi-task systems: every task's metric, primary-task change from a matched single-task baseline, and compute/latency cost;
- and for all settings: confidence intervals or subject-level variation, split details, missing-label policy, and performance under signal-quality degradation.

## Practical Recommendations

1. Choose multi-label learning only when labels can co-occur for the same documented unit; otherwise use a categorical, ordinal, continuous, hierarchical, or weak-supervision formulation that matches the annotation process.
2. Treat missing labels as unknown unless the data-collection protocol explicitly defines absence as negative.
3. Use task-specific heads and losses for targets with different scales, granularities, or temporal support.
4. Establish matched single-task and single-label baselines before claiming benefit from sharing.
5. Audit auxiliary tasks for shortcut learning, label leakage, and negative transfer under subject-, session-, and context-held-out splits.
6. Report per-label and per-task results, calibration, threshold selection, missingness policy, and the additional inference cost of every head.

Multi-label and multi-task models are valuable because they can respect the fact that affective EEG is structured and imperfectly observed. Their value comes from representing that structure explicitly, not from attaching more labels to a dataset without documenting what each label actually means.

## References

- Caruana, R. (1997). Multitask learning. Machine Learning, 28, 41-75.
- Zhang, Y., and Yang, Q. (2021). A survey on multi-task learning. IEEE Transactions on Knowledge and Data Engineering, 34(12), 5586-5609.
- Tsoumakas, G., Katakis, I., and Vlahavas, I. (2010). Mining multi-label data. In Data Mining and Knowledge Discovery Handbook.
- Zhou, Z.-H. (2018). A brief introduction to weakly supervised learning. National Science Review, 5(1), 44-53.
- Kendall, A., Gal, Y., and Cipolla, R. (2018). Multi-task learning using uncertainty to weigh losses for scene geometry and semantics. IEEE/CVF Conference on Computer Vision and Pattern Recognition.
- Yu, T., Kumar, S., Gupta, A., et al. (2020). Gradient surgery for multi-task learning. Advances in Neural Information Processing Systems, 33.