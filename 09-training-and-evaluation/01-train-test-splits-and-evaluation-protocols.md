# Train-Test Splits and Evaluation Protocols for Affective EEG

Once the prediction task has been defined, the next question is how to evaluate it fairly. This step is sometimes presented as a technical detail, but in practice it is where many affective EEG studies become difficult to interpret. A train-test split is not just a way of dividing data. It is a concrete statement about what kind of generalization the model is supposed to achieve. If the task is subject-independent, then the model must be tested on people it has never seen before. If the task is historical-to-future or online prediction, then the model must be evaluated without peeking into the future. In other words, the evaluation protocol should preserve exactly the challenge claimed by the task formulation.

For beginners, a useful way to think about this chapter is the following: first decide what the model is allowed to know, then decide what kind of novelty it should face at test time, and only then choose a split. This section follows that logic and connects the task taxonomy introduced in Chapter 4 to practical train-test designs and reporting conventions.

> Figure suggestion: Place a mapping figure here linking each task type in Chapter 4 to its corresponding split design and evaluation metrics.

| Task type | Valid held-out unit | Key protocol rule | Typical metrics |
| --- | --- | --- | --- |
| Batch, subject-dependent, cross-trial | Trials | Keep whole trials intact across splits | Accuracy, macro-$F_1$, MAE, RMSE |
| Batch, subject-dependent, cross-session | Sessions | Hold out complete sessions | Accuracy, macro-$F_1$, CCC, session-wise summary |
| Batch, subject-independent | Subjects | Exclude test subjects entirely from training and tuning | Balanced accuracy, macro-$F_1$, MAE, RMSE |
| Online, subject-dependent | Future time blocks or later sessions | Enforce chronology and causality | CCC, time-lag correlation, MAE over time |
| Online, subject-independent | Unseen subjects plus future stream | Enforce both subject exclusion and causal inference | CCC, trajectory error, latency-aware measures |

## General Principles

Before discussing specific protocols, it helps to keep a few general principles in mind. The first is that data should be split according to the unit of generalization, not simply according to the number of windows available. If the claim is cross-subject generalization, subjects should be held out. If the claim is cross-session robustness, sessions should be held out. If the claim is prediction of future affect, then later time blocks should be held out chronologically.

The second principle is that all data-dependent choices should be learned from the training portion only. This includes preprocessing statistics, feature normalization, channel selection, model selection, and hyperparameter tuning. Test data should remain untouched until the final evaluation. Otherwise, the protocol quietly transfers information from test to train and makes performance appear stronger than it really is.

The third principle is especially important in EEG: segmentation can create leakage. When a continuous recording is chopped into overlapping windows, neighboring segments may be extremely similar. If segmentation is done first and the resulting windows are then randomly split, nearly duplicate samples may end up in both training and testing. This is one of the most common reasons that reported results become overly optimistic.

Finally, the protocol should be described in enough detail that another researcher could reproduce it without guessing hidden steps.

| Protocol component | Correct practice | Common mistake |
| --- | --- | --- |
| Segmentation and overlap | Split first or preserve source units across splits | Overlapping windows from one interval appear in both train and test |
| Normalization | Fit statistics on training data only | Compute z-scores or scaling on the full dataset |
| Hyperparameter tuning | Tune on validation data inside the training partition | Tune against test performance |
| Online inference | Use only past and present context | Use future windows or bidirectional smoothing |

## Protocols for Batch Prediction

Batch prediction is the more familiar setting for many readers. Here the model is evaluated on a set of held-out examples, such as windows, trials, or clips, rather than on a continuously unfolding stream. Even in this simpler setting, however, the split still depends on what kind of generalization is being claimed.

### Subject-Dependent Cross-Trial Splits

Cross-trial subject-dependent evaluation asks a relatively narrow question: if a model has already seen some trials from a subject, can it generalize to other trials from that same subject? To answer that question honestly, the split should be made at the trial level. Complete trials should be held out, and all windows derived from those test trials should remain in the test partition. Hyperparameters should be chosen using only the training trials for that subject, or a validation split carved from them. Results are then aggregated within each subject and finally summarized across subjects. Conceptually, this protocol tests whether the model has learned something stable about that person's affective responses, rather than merely memorizing fine-grained trial artifacts.

### Subject-Dependent Cross-Session Splits

Cross-session evaluation is a more demanding version of subject-dependent testing. Instead of asking whether the model generalizes across trials collected under roughly the same conditions, it asks whether the model still works when the same subject is recorded again in a different session. This matters because session-to-session variation can be substantial: electrode placement shifts, impedance changes, fatigue, learning effects, and baseline drift all make the problem harder. In this setting, complete sessions should be held out. Training and validation should happen only on the remaining sessions, and any normalization should be handled carefully so that the held-out session does not influence training statistics. This protocol is often more informative than cross-trial testing because it better reflects repeated real-world use.

### Subject-Dependent Temporal Splits

Temporal or historical-to-future prediction asks an even stronger question: given a subject's earlier data, can the model predict that subject's later affective state? Here the split must be chronological. Random shuffling is not merely suboptimal; it changes the problem into something else by destroying the temporal structure that the model is supposed to respect. A good protocol orders the data by time, trains on an initial block, optionally validates on a later block, and evaluates on a final future block. When long recordings are heavily overlapped, a small temporal gap between train and test can further reduce contamination by near-duplicate segments. This kind of evaluation is especially important for adaptive and longitudinal systems, because it directly tests whether a model trained on history remains useful in the future.

### Subject-Independent Batch Splits

Subject-independent batch prediction is the setting most people have in mind when they ask whether a model generalizes. In this case, the model is trained on some subjects and evaluated on entirely different subjects. The split therefore has to be performed by subject identity, not by window or trial. Leave-one-subject-out cross-validation is common for small datasets, while grouped $k$-fold evaluation is often more practical for larger ones. Whatever the exact design, hyperparameters must be selected without touching the held-out subjects. If the method allows a calibration phase for new users, that should be reported as a separate protocol rather than quietly merged into the pure subject-independent setting.

## Protocols for Online and Continuous Prediction

Online prediction is stricter than batch prediction because time itself becomes part of the task. The model is no longer evaluated on a bag of held-out examples. Instead, it is asked to follow an evolving affective process as new EEG arrives. This changes what counts as a valid protocol.

### Causal Evaluation

If a system is described as online, then each prediction at time $t$ should depend only on information available up to time $t$, or up to a clearly declared short delay in a near-causal design. This requirement sounds obvious, but it is easy to violate in practice. Bidirectional encoders, centered windows, and smoothing procedures that use future samples may all be acceptable in offline analysis, but they do not support a strict online claim. A proper online evaluation processes the test stream in chronological order, makes clear whether the model needs a warm-up period, and reports practical details such as update frequency, latency, and prediction horizon.

### Online Subject-Dependent Evaluation

Within-subject online evaluation is often the natural choice for personalized monitoring and adaptive interfaces. The model is trained, fine-tuned, or calibrated on earlier data from a person and then evaluated on a later stream from that same person. The key point is that the split should remain chronological and, when relevant, session-aware. A random split of windows does not answer the right question, because it fails to test whether the model can maintain performance during genuine sequential use.

### Online Subject-Independent Evaluation

Online subject-independent evaluation is the hardest of the common settings because it combines both kinds of novelty at once. The subject must be unseen, and the test data must be processed as a causal stream. This is also one of the most realistic protocols for general-purpose deployment, because it approximates what happens when a new user begins interacting with a real system. If the method allows personalization after deployment, the adaptation budget should be stated explicitly. For example, the protocol should say how many initial seconds, trials, or labeled calibration samples are available before the real evaluation begins.

> Figure suggestion: Add a protocol diagram here showing chronological streaming evaluation, optional warm-up, and the distinction between pure subject-independent inference and calibrated subject-adaptive inference.

| Online protocol element | What should be reported |
| --- | --- |
| Causality | Whether inference is strictly causal or near-causal |
| Update schedule | Window size, stride, and prediction frequency |
| Delay | Prediction horizon, annotation lag handling, and buffering latency |
| Warm-up | Whether the model needs an initial history before scoring |
| Adaptation | Whether test-time calibration or online updating is allowed |

## Metrics and Reporting

Metrics should be chosen to match the prediction target rather than copied mechanically from earlier papers. For batch classification, common choices include accuracy, balanced accuracy, macro-$F_1$, unweighted average recall, and sometimes area under the ROC curve. When label imbalance is substantial, balanced metrics are usually more informative than plain accuracy. For batch regression, typical measures include mean absolute error, root mean square error, Pearson correlation, Spearman correlation, and concordance correlation coefficient.

For online continuous prediction, the situation is slightly different. The goal is not only to be locally accurate at each time point, but also to follow the trajectory of affect in a reasonable way. For that reason, concordance correlation coefficient, time-lag-aware correlation, and trajectory-level error measures are often more informative than a single point-wise score alone. If the task emphasizes state transitions, then event-oriented measures may also be appropriate. In all cases, it is good practice to report performance at more than one level, such as per subject, per session, and overall, together with variability estimates such as standard deviations or confidence intervals.

## Common Pitfalls

Several protocol mistakes recur so often in affective EEG that they are worth emphasizing explicitly. The first is random splitting over overlapping windows, which can create train and test sets that are much less independent than they appear. The second is computing normalization statistics on the full dataset rather than on the training partition only. The third is allowing feature design, channel selection, or hyperparameter tuning to be influenced by test performance. A fourth is making an online claim while still using future context at inference time. Finally, some studies place subject-dependent and subject-independent results side by side without clearly distinguishing them, even though they correspond to meaningfully different tasks.

Avoiding these pitfalls is as important as designing a strong model, because a small evaluation mistake can easily create a large and misleading performance gain.

| Pitfall | Why it is harmful |
| --- | --- |
| Random split over overlapping windows | Creates near-duplicate train-test samples and inflates scores |
| Full-dataset normalization | Leaks test distribution information into training |
| Test-set-driven tuning | Converts the test set into a hidden validation set |
| Non-causal online inference | Makes online claims invalid |
| Mixed subject settings in one summary | Prevents fair interpretation across studies |

## Recommended Reporting Template

For clarity and reproducibility, every experiment should make a few things explicit: whether the task is batch or online, whether the target is classification, regression, tracking, or forecasting, whether the generalization claim is subject-dependent or subject-independent, what unit is held out, how overlap and leakage are controlled, whether calibration is allowed at test time, which metrics are primary, and how results are aggregated in the final report. When these details are stated clearly, comparisons across studies become much more meaningful. Readers can then judge whether a reported improvement comes from a genuinely stronger method or simply from an easier evaluation setting.

## References

- Chaouachi, M., Frasson, C., and Gouin-Vallerand, C. (2010). Affective tutoring systems using physiological signals: A review. In Intelligent Tutoring Systems.
- Soleymani, M., Asghari-Esfeden, S., Pantic, M., and Fu, Y. (2016). Continuous emotion detection using EEG signals and facial expressions. 2015 IEEE International Conference on Multimedia and Expo Workshops.
- Vielzeuf, V., Pateux, S., and Jurie, F. (2018). CentralNet: A multilayer approach for multimodal fusion. European Conference on Computer Vision Workshops.