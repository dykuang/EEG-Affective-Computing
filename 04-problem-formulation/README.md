# Problem Formulation

This chapter defines what it means to formulate an EEG-based affective computing problem rigorously. In this book, problem formulation goes beyond naming a label space. It also includes the prediction target, the temporal assumptions, the unit of inference, the deployment scenario, and the kind of generalization that the model is expected to achieve.

For affective EEG, this step is especially important because superficially similar studies may in fact solve very different tasks. A clip-level offline classifier, a causal affect tracker, a within-subject personalized model, and a subject-independent population model are not interchangeable. Their assumptions, difficulty, and claims should be separated clearly.

This chapter therefore focuses on evaluation tasks and task definitions. It distinguishes batch from online prediction, subject-dependent from subject-independent evaluation, and several important within-subject missions such as cross-trial, cross-session, and historical-to-future prediction. These distinctions will later determine how train-test splits, metrics, and validation protocols should be designed.
