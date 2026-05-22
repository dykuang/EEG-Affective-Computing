# Evaluation Tasks in EEG-Based Affective Computing

How an EEG affect model is evaluated depends first on what prediction task it is supposed to solve. At a high level, at least two branches should be distinguished. The first is batch prediction, where each sample is treated as a segment, trial, or clip to be predicted without explicit use of temporal dependence at inference time. The second is online prediction, where the model continuously estimates the evolving emotional state and must respect causal time order. A second axis cuts across both branches: whether the task is subject-dependent or subject-independent. This distinction is often as important as the model architecture itself, because it changes the meaning of generalization.

> Figure suggestion: Place a task taxonomy figure here with two main branches, batch prediction and online prediction, crossed with subject-dependent and subject-independent settings.

| Axis | Branch | Core question | Typical constraint |
| --- | --- | --- | --- |
| Temporal formulation | Batch prediction | Can the model predict an affect label or score for a predefined segment? | No strict causal requirement at inference |
| Temporal formulation | Online prediction | Can the model track the evolving emotional state over time? | Causal or near-causal inference |
| Generalization target | Subject-dependent | Can the model generalize within the same person? | Training and test come from the same subject but different units |
| Generalization target | Subject-independent | Can the model generalize to unseen people? | Test subjects are excluded from training |

## Batch Prediction

Batch prediction treats EEG examples as pre-segmented units. A sample may be a fixed-length window, a trial-level summary, a clip-level representation, or a collection of windows aggregated before inference. The key point is that the model is evaluated on a set of examples without the requirement that predictions be produced causally in time.

This formulation is common when labels are available per trial or per clip, such as binary valence classification, multi-class emotion recognition, or regression to valence-arousal scores computed for a segment. The model may still use temporal information internally during feature extraction, but the evaluation task itself does not require online state tracking.

Typical properties of batch prediction are:

- each example is scored independently at evaluation time,
- future context may be available inside the example definition,
- performance is usually summarized over a test set rather than over a live trajectory,
- and latency or real-time causality is not part of the task definition.

Batch prediction is appropriate when the goal is clip-level recognition, benchmark comparison under fixed windows, or offline analysis of affective states.

## Online Continuous Prediction

Online prediction assumes that the model observes EEG as a stream and continuously updates its estimate of the current emotional state. Here the temporal order is part of the task, not just a feature of the data. The model should infer the present state using information available up to the current time point, without relying on future samples that would not be accessible in deployment.

This setting is natural for affect tracking, adaptive human-computer interaction, neurofeedback, and closed-loop systems. Labels may be continuous trajectories, delayed self-reports aligned to time, or slowly varying latent affect states inferred from annotations. Compared with batch prediction, online evaluation imposes stronger constraints:

- inference should be causal or explicitly near-causal,
- prediction delay and update frequency matter,
- temporal consistency becomes part of task success,
- and the model should be evaluated on evolving trajectories rather than isolated examples.

In practice, online prediction often overlaps with sequence modeling, state estimation, filtering, or forecasting. Even when the final output is discrete, such as stress versus non-stress, the evaluation setup is fundamentally temporal.

> Figure suggestion: Add a streaming timeline here showing a causal prediction pipeline, where EEG up to time $t$ is used to estimate affect at time $t$ or $t + \Delta$.

## Subject-Dependent and Subject-Independent Settings

Both batch and online prediction can be studied in subject-dependent or subject-independent form.

Subject-dependent prediction means that training and test data come from the same subject, although from different trials, sessions, or time periods. This setting evaluates how well a model personalizes within a person. It is often easier because the model does not need to bridge strong inter-subject variability in EEG rhythms, emotional baselines, and physiological response patterns.

Subject-independent prediction means that test subjects are unseen during training. This is a more demanding but often more deployment-relevant setting, because it asks whether a model can generalize across individuals. Performance is typically lower, but the results say more about population-level robustness.

These two settings should never be mixed implicitly. A paper that reports high within-subject performance is not necessarily solving the same problem as one reporting lower cross-subject performance.

| Setting | Training-test relation | What it measures | Common risk if defined poorly |
| --- | --- | --- | --- |
| Subject-dependent | Same subject, different held-out units | Personalization and within-person stability | Inflated scores from trial or temporal leakage |
| Subject-independent | Different subjects in train and test | Cross-person robustness | Hidden calibration or identity leakage |

## Subject-Dependent Missions

Within subject-dependent evaluation, several distinct missions should be separated because they test different kinds of generalization.

### Cross-Trial Prediction

Cross-trial prediction uses some trials of a subject for training and different trials of the same subject for testing, usually within the same recording session or within sessions pooled together. This is the most common subject-dependent setting for trial-based datasets.

It mainly measures whether the model can generalize across repeated stimuli or repeated elicitation events for the same person. It does not, by itself, establish robustness to day-to-day drift.

### Cross-Session Prediction

Cross-session prediction trains on one or more sessions and tests on a different session from the same subject. This is harder than cross-trial prediction because session effects include electrode placement changes, impedance variation, fatigue, learning, adaptation, and baseline drift.

For practical EEG systems, cross-session performance is often more informative than within-session cross-trial performance because it better reflects repeated real-world use.

### Temporal Split or Historical-to-Future Prediction

A temporal split uses earlier data to predict later data from the same subject. This can be defined within a single long recording or across multiple sessions ordered in time. The key requirement is chronological separation: the model must learn from the past and predict the future.

This setting is particularly useful for online affect tracking, longitudinal monitoring, and adaptive systems. It addresses a question that random train-test splits cannot answer: whether the model remains valid as the subject's state and recording conditions evolve over time.

### Other Within-Subject Missions

Depending on the dataset, other missions may also be meaningful, such as cross-stimulus prediction, cross-task prediction, or adaptation from a short calibration period to later unconstrained use. These should be defined explicitly rather than folded into a generic subject-dependent label.

| Mission | Held-out unit | Main question | Difficulty driver |
| --- | --- | --- | --- |
| Cross-trial | Trials from the same subject | Does the model generalize across repeated elicitation episodes? | Trial-specific variability |
| Cross-session | Sessions from the same subject | Does the model survive recording drift and day-to-day variation? | Session shift and baseline drift |
| Temporal split | Later time blocks from the same subject | Can history predict the future state? | Chronology and nonstationarity |
| Cross-stimulus or cross-task | Stimuli or tasks from the same subject | Does the model transfer across elicitation contexts? | Context shift |

## Subject-Independent Missions

Subject-independent evaluation asks whether the model generalizes to unseen people. The most common form is leave-one-subject-out or leave-several-subjects-out testing, but the precise design can vary depending on sample size and class balance.

This setting is crucial when the intended application is plug-and-play affect recognition without extensive personal calibration. It is also the right setting for studying subject-invariant representations, domain adaptation, and fairness across users.

For online tasks, subject-independent evaluation can be combined with streaming constraints. In that case the model must both generalize to a new person and operate causally over time.

## Why This Taxonomy Matters

These task definitions are not cosmetic. They determine what counts as leakage, which train-test split is valid, which metrics are meaningful, and what claims can be made from the results. A model that performs well under randomly shuffled batch prediction may fail under chronological online prediction. Likewise, a model that is strong in cross-trial within-subject testing may still be weak in cross-session or cross-subject deployment.

For that reason, the problem formulation should always specify at least:

- whether prediction is batch or online,
- whether evaluation is subject-dependent or subject-independent,
- what unit of generalization is being held out, such as trials, sessions, or future time,
- and whether the goal is classification, regression, tracking, or forecasting.

## References

- Calvo, R. A., and D'Mello, S. (2010). Affect detection: An interdisciplinary review of models, methods, and their applications. IEEE Transactions on Affective Computing, 1(1), 18-37.
- Shu, L., Xie, J., Yang, M., Li, Z., Li, Z., Liao, D., Xu, X., and Yang, X. (2018). A review of emotion recognition using physiological signals. Sensors, 18(7), 2074.
- Stober, S., Sternin, A., Owen, A. M., and Grahn, J. A. (2015). Towards music imagery information retrieval: Introducing the OpenMIIR dataset of EEG recordings from music perception and imagination. Proceedings of ISMIR 2015.