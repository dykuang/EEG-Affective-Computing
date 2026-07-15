# Training, Validation, and Model Selection

A defensible EEG experiment separates fitting a model from deciding which model to keep. Training estimates parameters from training data. Validation selects preprocessing choices, architectures, hyperparameters, checkpoints, and decision thresholds. Final testing estimates the performance of the complete frozen procedure on data that have not influenced any of those choices.

## Separate the Data Roles

| Partition | Permitted uses | Must not be used for |
| --- | --- | --- |
| Training | Parameter fitting, training-only augmentation, fitting normalization and feature transforms | Reporting final performance |
| Validation | Hyperparameter search, early stopping, threshold selection, calibration selection | Updating the final claim after repeated inspection |
| Test | One final evaluation of the frozen pipeline | Model selection, feature design, or tuning |

For small EEG datasets, a validation partition can waste valuable independent units. Nested cross-validation addresses this by placing model selection in an inner loop and performance estimation in an outer loop. The outer held-out subject, session, trial, or chronological block must not influence any inner-loop decision.

## Build a Leakage-Safe Training Pipeline

Every data-dependent transform belongs inside the training pipeline. Fit normalization statistics, channel selection, artifact-correction models, dimensionality reduction, feature selection, label thresholds, and augmentation parameters using the training partition only. Apply the fitted transform unchanged to validation and test data.

This rule extends to self-supervised pretraining and pseudo-labeling. Pretraining on held-out subjects or sessions is a different, transductive protocol and should be declared as such. A test label must never influence a pseudo-label, stopping criterion, class weight, or threshold.

## Handle Imbalance and Label Noise

Affective datasets can be imbalanced by class, subject, session, stimulus, rating range, or retained recording duration. The remedy should be selected from the training partition and reported with the resulting class distribution.

Common training-time approaches include class-weighted losses, balanced sampling, focal loss, and conservative data augmentation. Oversampling windows cannot replace additional independent subjects or sessions, and it can amplify repeated artifacts. Keep validation and test distributions natural so that evaluation reflects the intended application.

For noisy labels, consider robust losses, soft targets, label smoothing, or confidence-based sample weighting. These choices should be validated against a held-out partition, not selected because they improve the final test result.

## Select Hyperparameters Deliberately

A hyperparameter search space encodes the researcher's degrees of freedom. State the parameters searched, their ranges, search method, number of trials, validation metric, and stopping rule. Search budgets should be comparable across competing methods whenever possible.

Useful search strategies include informed grids for small spaces, random search for broad independent ranges, and Bayesian optimization for expensive models. The method matters less than keeping all search feedback inside validation data and reporting the budget honestly.

## Use Early Stopping and Checkpoints Correctly

Early stopping selects the checkpoint with the best validation performance or lowest validation loss. It is a form of model selection, so the validation rule, patience, maximum epochs, and selected checkpoint should be reported. Do not select an epoch by looking at test performance across training history.

When training is stochastic, repeat training with several random seeds. A single favorable seed does not estimate expected performance. Report the seed policy, aggregation method, and variation across seeds in addition to variation across independent subjects or sessions.

## Calibration and Personalization

Calibration changes the protocol because it grants information from the target user, session, or domain. Distinguish zero-shot inference from unlabeled adaptation, few-shot labeled calibration, and full retraining. State how much target data are available, whether labels are supplied, when they are collected, and whether the same budget is given to all compared methods.

**Example scenario:** A population model is trained on source subjects. For a new user, it receives two labeled calibration trials before testing on later trials. This is a few-shot personalized protocol, not pure subject-independent evaluation. The two calibration trials must not also appear in the reported test set.

## Training Checklist

Before final evaluation, verify that:

- transforms and label rules were fitted only on training data;
- every model and preprocessing choice was selected on validation data or an inner loop;
- early stopping and checkpoints use a declared validation rule;
- imbalance remedies and augmentation are training-only;
- calibration data are separated from evaluation data and budgeted explicitly;
- and seed, code, configuration, and checkpoint information can reproduce the run.

## References

- Bergstra, J., and Bengio, Y. (2012). Random search for hyper-parameter optimization. Journal of Machine Learning Research, 13, 281-305.
- Cawley, G. C., and Talbot, N. L. C. (2010). On over-fitting in model selection and subsequent selection bias in performance evaluation. Journal of Machine Learning Research, 11, 2079-2107.
- Goodfellow, I., Bengio, Y., and Courville, A. (2016). Deep Learning. MIT Press.
- Varoquaux, G. (2018). Cross-validation failure: Small sample sizes lead to large error bars. NeuroImage, 180, 68-77.
