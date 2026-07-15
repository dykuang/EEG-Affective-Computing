# Domain Shift, Dataset Bias, and Generalization

Affective EEG models often perform well when training and test recordings come from the same study, but performance can fall when the person, session, device, laboratory, or stimulus set changes. This difference is called domain shift. It is not a minor implementation detail: it determines whether a benchmark score supports a claim about deployment beyond the conditions in which the data were collected.

This section explains the main sources of shift in affective EEG and how to design evaluations that reveal rather than conceal them.

## Sources of Domain Shift

EEG is shaped by neural activity, but also by anatomy, electrode placement, skin impedance, recording hardware, environmental noise, task context, and preprocessing decisions. Emotion labels add further variability because they depend on self-report, culture, language, stimulus interpretation, and annotator timing.

| Source of shift | Example | Consequence for a model |
| --- | --- | --- |
| Subject shift | Different anatomy, baseline rhythms, or affect expression | A subject-specific feature may not transfer |
| Session shift | Electrode repositioning, fatigue, mood, or adaptation | Performance may decrease on a later recording |
| Device and montage shift | Different channel sets, references, amplifiers, or sampling rates | Spatial and spectral representations may become incompatible |
| Site shift | Different laboratory, operator, or electrical environment | Artifact and acquisition distributions can change |
| Stimulus shift | Different videos, music, tasks, or interaction context | The learned response may be stimulus-specific rather than affect-specific |
| Label shift | Different rating scales, thresholds, cultures, or annotators | The target definition changes across datasets |
| Preprocessing shift | Different filters, artifact rules, or normalization | A method may exploit pipeline-specific structure |

Several shifts often occur together. For example, moving to a wearable device in a home setting may simultaneously change the montage, motion artifacts, participant behavior, task context, and label reliability.

## Match Evaluation to the Intended Claim

Within-dataset validation estimates performance under a limited distribution. It is useful for controlled method comparison, but it should not be described as evidence that a system will work in a new study or real-world setting.

A stronger evaluation ladder is:

1. Test on held-out trials from known subjects to assess trial-level stability.
2. Test on held-out sessions to assess short-term recording drift.
3. Test on held-out subjects to assess person-independent transfer.
4. Test on a held-out device, site, stimulus set, or dataset to assess external validity.

Each level answers a different question. A model may be excellent for personalized calibration yet unsuitable for zero-shot deployment to an unseen person. Report the strongest claim actually tested, not the strongest claim suggested by the model architecture.

## Detect Dataset Bias Before Modeling

Dataset bias is present when properties unrelated to the intended affective construct predict the target. Examples include one emotion category appearing primarily in a particular stimulus set, all data from one condition being recorded in a single session, or labels being correlated with channel quality or recording order.

Before training, inspect the association between labels and:

- participant, session, run, and stimulus identifiers;
- recording date, device, montage, and sampling rate;
- retained duration, rejected-channel count, and artifact rate;
- demographic and contextual variables when ethically available;
- and preprocessing or annotation versions.

These analyses do not prove that a confound is absent, but they can expose a benchmark that would reward shortcut learning. Simple baseline models using metadata alone are often informative: unexpectedly high metadata-only performance is a warning that the signal benchmark needs closer inspection.

## Design Cross-Dataset Studies Carefully

Cross-dataset evaluation is valuable because it tests several shifts at once, but it requires a common task definition. Harmonize only the aspects that can be made comparable without erasing the scientific differences between datasets.

State explicitly how studies were aligned:

- the common label space and any discarded categories;
- rating-scale transformations or thresholding rules;
- retained channels, reference, sampling rate, and frequency range;
- stimulus and trial inclusion criteria;
- preprocessing applied to each source;
- and whether target-dataset labels or unlabeled data were used during adaptation.

Do not pool datasets and call the result cross-dataset generalization unless one dataset, site, device, or population remains completely held out. Pooled training can be useful for robustness, but it evaluates a different question.

## Adaptation Must Respect the Deployment Setting

Domain adaptation, calibration, normalization, and test-time adaptation can improve transfer, but their allowed information must be declared. There is an important difference between:

- zero-shot transfer, with no target-subject or target-dataset data during training;
- unsupervised adaptation, using target data without labels;
- few-shot personalization, using a limited labeled calibration set;
- and full supervised retraining on target labels.

Report the amount, identity, and timing of target data used by every method. A personalized method should be compared with other methods under the same calibration budget, not with a zero-shot baseline presented as though both solve the same problem.

## Report Generalization Transparently

For every transfer result, report the source and target populations, independent split unit, source-only baseline, adaptation setting, and subject- or dataset-level variation. A performance drop is not necessarily a failure: it may reveal the true difficulty of the intended use case and identify where better data collection or modeling is needed.

A useful table for a generalization study includes the training domain, held-out domain, shifts present, target data available during training, number of independent target units, metric, and uncertainty interval. This makes it possible to compare transfer claims without confusing them with ordinary within-dataset validation.

## References

- Ben-David, S., Blitzer, J., Crammer, K., and Pereira, F. (2010). A theory of learning from different domains. Machine Learning, 79, 151-175.
- Kwon, O. Y., Lee, M. H., Guan, C., and Lee, S. W. (2020). Subject-independent brain-computer interfaces based on deep convolutional neural networks. IEEE Transactions on Neural Networks and Learning Systems, 31(10), 3839-3852.
- Lotte, F., Bougrain, L., Clerc, M., et al. (2018). A review of classification algorithms for EEG-based brain-computer interfaces: A 10 year update. Journal of Neural Engineering, 15(3), 031005.
- Varoquaux, G., and Cheplygina, V. (2022). Machine learning for medical imaging: Methodological failures and recommendations for the future. npj Digital Medicine, 5, 48.
