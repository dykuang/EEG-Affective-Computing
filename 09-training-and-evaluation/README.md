# Training and Evaluation

This chapter explains how to turn an affective EEG task definition into a defensible experimental protocol. Once the problem formulation is fixed, the next step is to decide how data should be split, how leakage should be prevented, what metrics should be reported, and what kind of evidence is actually needed to support a performance claim.

In EEG-based affective computing, evaluation details often matter as much as the model itself. Random window shuffling, normalization across the full dataset, or misuse of future context can make results look stronger than they really are. For that reason, this chapter emphasizes train-test splits and evaluation protocols that preserve the intended generalization challenge.

The discussion in this chapter follows the task taxonomy introduced earlier. Different tasks require different protocols: batch prediction, online prediction, within-subject generalization, cross-session robustness, chronological forecasting, and subject-independent testing should each be evaluated in a way that matches the actual deployment scenario.
