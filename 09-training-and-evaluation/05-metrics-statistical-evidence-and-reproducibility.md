# Metrics, Statistical Evidence, and Reproducibility

An evaluation metric summarizes a model's behavior, but it does not by itself establish that one method is better than another or that a result will reproduce. EEG experiments have multiple sources of dependence and variation: windows within a trial, trials within a session, sessions within a subject, and stochastic training runs. Evaluation should preserve these levels rather than treating every window as an independent observation.

## Select Metrics for the Task

| Task | Primary metrics | Supporting evidence |
| --- | --- | --- |
| Balanced classification | Balanced accuracy, macro F1 | Confusion matrix and per-class recall |
| Imbalanced classification | Macro F1, balanced accuracy, precision-recall metrics | Class prevalence and per-class precision/recall |
| Regression | MAE, RMSE, Pearson or Spearman correlation, concordance correlation coefficient | Target range, residual distribution, subject-wise results |
| State tracking | Time-resolved error or concordance, lag-aware correlation | Update rate, latency, smoothing rule, trajectory plots |
| Forecasting | Horizon-specific error or correlation | Persistence baseline and performance by horizon |
| Generative modeling | Distributional, spectral, conditional, privacy, and downstream-utility measures | Evaluation on untouched real data |

Accuracy can obscure minority-class failures, while correlation can be high even when predictions are systematically biased. Use a primary metric suited to the task and report complementary views that make important failure modes visible.

## Aggregate at the Independent Unit

Windows from the same trial or recording are dependent. Compute scores at a unit aligned with the generalization claim, such as subject, session, trial, or chronological test block, then summarize across those independent units. A confidence interval computed over thousands of overlapping windows can be very narrow while saying little about variation across people.

Report the aggregation rule explicitly. For example, state whether window scores are averaged within each subject before computing a population mean, whether subjects are weighted equally, and how sessions or missing classes are handled.

### Segment-Level, Trial-Level, and Subject-Level Metrics

The same model can produce very different reported performance depending on whether the metric is computed at the segment (window), trial, or subject level, and on how predictions are aggregated across those levels.

**Segment-level evaluation** computes a prediction for every sliding window independently, then pools all window predictions to calculate a single metric. This is the most common reporting practice but also the most optimistic: a model that correctly classifies many easy windows from a few subjects can achieve high segment-level accuracy while failing entirely on harder subjects or trials. It also inflates the effective sample size, producing misleadingly narrow confidence intervals.

**Trial-level evaluation** aggregates window predictions within a trial—for example, by majority vote or mean predicted score—and computes the metric across trials. This aligns better with applications where the goal is to recognize the emotion evoked by a whole stimulus or recording segment. However, aggregation rules (vote threshold, how ties are broken, whether all windows are equally weighted) must be specified, as they can change the result.

**Subject-level evaluation** computes one score per subject and then summarizes across subjects. This is the most conservative level: it treats each subject as a single observation and directly answers the question, "How well does the model perform for a new person?" Subject-level metrics are recommended when the generalization claim is subject-independent.

The level should match the intended use case and the generalization claim. A model evaluated only at the segment level cannot claim subject-independent generalization, and a model evaluated only at the subject level may hide useful within-subject dynamics. Whenever feasible, report performance at more than one level—for instance, segment-level detail alongside subject-level aggregates—so that readers can assess both fine-grained behavior and population-level reliability.

## Quantify Uncertainty and Compare Methods

Use uncertainty estimates that resample or vary the correct independent unit. Suitable methods include confidence intervals across held-out subjects or sessions, hierarchical or grouped bootstrap procedures, and repeated outer cross-validation when justified by the data structure.

For a paired model comparison, compute differences on the same independent test units. Report the effect size, uncertainty interval, and test or resampling procedure, not only a p-value. When many models, metrics, or subgroups are compared, control the scope of the analysis or account for multiple comparisons.

Do not claim superiority from a difference that is smaller than the observed variation across subjects, folds, or seeds. Conversely, a non-significant result on a very small dataset should be described as inconclusive rather than evidence of equivalence.

## Perform Error and Robustness Analysis

A pooled mean can hide clinically or scientifically important failures. Break down results by subject, session, class, label range, recording quality, stimulus condition, and time in the session when those variables are relevant and ethically reportable.

Useful analyses include:

- confusion matrices and common error pairs for discrete tasks;
- residual plots, bias, and extreme-error cases for regression;
- trajectory overlays and transition errors for tracking;
- performance after channel dropout, artifact contamination, or reduced data availability;
- sensitivity to preprocessing, window length, threshold, and random seed;
- and ablations that remove one component or information source at a time.

Robustness tests should be planned before inspecting final results where possible. Post hoc analyses can generate hypotheses, but they should not be presented as preregistered confirmation.

## Make Runs Reproducible

A reproducible experiment needs more than source code. Save the dataset version, split manifest, preprocessing configuration, model configuration, trained checkpoint, random seeds, software environment, hardware details when relevant, and the command or workflow used to run the experiment.

Keep a result manifest that links each reported number to its data split, configuration, seed, checkpoint, and metric implementation. This makes it possible to correct a preprocessing error, rerun a result, or compare a new model under the same protocol without reconstructing hidden assumptions.

## Reporting Checklist

Before publishing a result, verify that:

- the primary metric matches the target and class or rating distribution;
- scores are aggregated at the correct independent unit;
- uncertainty reflects variation across subjects, sessions, folds, or seeds;
- comparisons are paired on the same held-out units and include effect sizes;
- error and robustness analyses expose important subgroup or signal-quality failures;
- and dataset, split, preprocessing, code, environment, and checkpoints are traceable.

Transparent evaluation does not guarantee a strong model. It ensures that the reported evidence measures the capability the model actually has.

## References

- Demsar, J. (2006). Statistical comparisons of classifiers over multiple data sets. Journal of Machine Learning Research, 7, 1-30.
- Efron, B., and Tibshirani, R. J. (1994). An Introduction to the Bootstrap. Chapman and Hall/CRC.
- Hesterberg, T. (2015). What teachers should know about the bootstrap: Resampling in the undergraduate statistics curriculum. The American Statistician, 69(4), 371-386.
- Varoquaux, G. (2018). Cross-validation failure: Small sample sizes lead to large error bars. NeuroImage, 180, 68-77.
