# Benchmark Protocols and Reporting Standards

A benchmark is more than a dataset and a metric. It is a precisely defined task, data partition, preprocessing boundary, model-selection procedure, and reporting convention. When any of these choices is unclear, two published scores may not be comparable even if they use the same dataset and nominal task name.

This section explains how to define benchmarks that make model comparisons interpretable in EEG-based affective computing.

## Define the Prediction Task First

Every benchmark should state the target, input availability, prediction time, and intended unit of inference. A model that predicts one trial-level valence label from an entire clip is solving a different task from a model that predicts a continuous arousal trajectory from causal EEG windows.

A concise task specification should identify:

- the target variable and whether it is discrete or continuous;
- the label source, temporal resolution, and any thresholding rule;
- the input interval available to the model;
- whether future signal, labels, or annotations are permitted;
- the prediction unit: subject, session, trial, epoch, window, or time point;
- and the deployment claim: offline analysis, delayed feedback, or real-time prediction.

When a continuous rating is converted into high and low classes, report the threshold and whether it is global, subject-specific, or learned from training data. A threshold fitted using the full dataset changes the task and can leak information into evaluation.

## Choose the Independent Split Unit

The split unit must match the generalization claim. EEG samples sharing a participant, session, trial, or overlapping time interval are statistically dependent and should not be treated as independent examples.

| Claim | Appropriate held-out unit | What the result supports |
| --- | --- | --- |
| Personalized prediction | Windows or trials within a training subject, with grouped temporal separation | Adaptation to a known person |
| Cross-trial prediction | Entire trials | Generalization to new stimuli or trial instances |
| Cross-session prediction | Entire recording sessions | Robustness to session drift |
| Subject-independent prediction | Entire subjects | Generalization to unseen people |
| Cross-dataset prediction | Entire external dataset | Robustness to a new study, device, or protocol |
| Online tracking | Later chronological blocks | Prediction from past and current information only |

Create the split before applying windowing, data augmentation, normalization, component selection, or other data-dependent transformations. Propagate each source unit's split assignment to all derived samples.

## Separate Model Selection from Final Testing

Use validation data only for choices that may improve performance: architectures, hyperparameters, preprocessing variants, thresholds, early stopping, feature selection, and calibration. The test set should remain untouched until these choices are fixed.

For limited subject counts, nested cross-validation is often preferable. An outer loop estimates performance on held-out subjects or sessions, while an inner loop selects settings using only the remaining data. Report which loop controls each decision. Repeated use of a held-out test fold for model selection turns that fold into validation data and makes its final score optimistic.

## Report Metrics That Match the Task

Metric choice should reflect the target distribution and error consequences. Accuracy alone is inadequate for many affective EEG benchmarks because classes can be imbalanced and labels can be noisy.

| Task | Useful primary metrics | Useful supporting information |
| --- | --- | --- |
| Balanced discrete classification | Balanced accuracy, macro F1 | Per-class precision and recall, confusion matrix |
| Imbalanced discrete classification | Macro F1, balanced accuracy, AUROC when appropriate | Class prevalence, per-class results, calibration |
| Continuous regression | MAE, RMSE, Pearson correlation, concordance correlation coefficient | Target range, error distribution, subject-wise results |
| Temporal tracking | Time-resolved error or correlation, lag-aware agreement | Prediction latency, smoothing rule, causal availability |
| Personalized adaptation | Performance before and after adaptation | Amount and identity of adaptation data |

Report results per subject or per session in addition to a pooled score. This reveals whether a model performs consistently or is driven by a small number of easy recordings. Where feasible, report uncertainty across folds, subjects, and random seeds using confidence intervals or a clearly described dispersion statistic.

## Document the Complete Benchmark Configuration

A reproducible result should make the following available in the paper, repository, or supplementary material:

- dataset release and download date;
- inclusion and exclusion criteria, with retained sample counts;
- split identifiers for subjects, sessions, trials, and derived segments;
- preprocessing configuration and whether parameters were fitted within training data;
- label construction, delay compensation, and thresholding rules;
- model-selection procedure, search space, and stopping criterion;
- random seeds, implementation version, and hardware or runtime details when relevant;
- primary metrics, aggregation rule, and uncertainty estimate;
- and code or pseudocode sufficient to regenerate the benchmark manifest.

A benchmark table in a publication is especially useful when it lists the split unit, number of independent test units, target construction, input duration, causal status, and metric. Those fields are often more informative than a single headline score.

## Avoid Comparison Traps

Several comparisons are invalid or weak even when each experiment is individually well implemented:

- comparing window-level random splits with subject-level splits as though they measured the same generalization;
- comparing trial-label classification with continuous-label tracking as though both were emotion recognition in the same sense;
- selecting the best method after observing test-set performance;
- reporting only the maximum result across seeds, folds, or preprocessing variants;
- averaging scores over dependent windows without reporting subject-level variation;
- and comparing methods trained on different retained subsets after artifact rejection.

The remedy is not necessarily a single universal protocol. Instead, define the protocol precisely, match it to the scientific claim, and compare methods only when they share the same task and independent evaluation units.

## Reporting Checklist

Before presenting a benchmark result, verify that a reader can answer:

- What exact target is predicted, and when is it available?
- Which unit was kept independent between training and test data?
- Which choices were made using validation data rather than test data?
- How many subjects, sessions, trials, and segments remain in each partition?
- Which metric is primary, and why does it suit the target distribution?
- How variable is performance across independent test units and random seeds?
- Can the split manifest and preprocessing configuration be reconstructed?

Clear answers make a benchmark useful beyond the original study: they let later researchers reproduce it, challenge it, and improve on it without accidentally changing the question.

## References

- Baker, M. (2016). 1,500 scientists lift the lid on reproducibility. Nature, 533, 452-454.
- Liem, C. C. S., and Panichella, A. (2020). Toward a standardized protocol for EEG-based emotion recognition. Frontiers in Human Neuroscience, 14, 128.
- Poldrack, R. A., et al. (2020). Establishment of best practices for evidence for prediction: A review. JAMA Psychiatry, 77(5), 534-540.
- Varoquaux, G. (2018). Cross-validation failure: Small sample sizes lead to large error bars. NeuroImage, 180, 68-77.
