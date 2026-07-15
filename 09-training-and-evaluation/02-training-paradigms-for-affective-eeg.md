# Training Paradigms for Affective EEG

A training paradigm specifies what supervision a model receives and what learning signal shapes its parameters. The same EEG dataset can support supervised classification, self-supervised representation learning, unsupervised discovery, or reinforcement learning, but these formulations require different data, validation procedures, and claims. Selecting a paradigm should follow the available labels and deployment objective rather than the popularity of a model family.

## Supervised Training

Supervised learning trains a model from EEG inputs $x_i$ and target labels $y_i$. For classification, the target may be a discrete emotion category; for regression, it may be valence, arousal, stress intensity, or another continuous rating. The model minimizes a loss that compares its prediction $f_\theta(x_i)$ with the target, for example cross-entropy for classes or mean squared error for a continuous score.

Supervised training is the standard choice when labels are sufficiently reliable and match the intended prediction unit. It requires careful handling of label imbalance, inherited trial labels, noisy self-reports, and subject or session variation. All label preprocessing, including thresholding continuous ratings into classes, should be defined using training data and held fixed for validation and testing.

**Example scenario:** A researcher uses the DEAP dataset to predict high versus low arousal from full trials. The median threshold is determined from training subjects only, the classifier is trained on their EEG, and the final test evaluates unseen subjects. This is supervised subject-independent classification, not continuous affect tracking.

## Unsupervised Training

Unsupervised learning uses EEG inputs without affect labels to discover structure in the data. The goal may be clustering participants or signal states, estimating a latent representation, detecting unusual recordings, or modeling the density of normal EEG segments. Its outputs are not automatically emotion categories; they require scientific interpretation and, when possible, validation against independent labels or experimental conditions.

Typical methods include clustering, dimensionality reduction, density estimation, and unsupervised representation learning. The number of clusters, preprocessing choices, and selection criteria should be determined without inspecting test labels. Cluster stability across subjects, sessions, preprocessing variants, and random seeds is often more informative than a single clustering score.

**Example scenario:** In a naturalistic study with no trial-level emotion labels, a researcher clusters spectral-spatial embeddings of EEG windows to identify recurring arousal-related states. The discovered clusters are then compared with independently collected behavioral reports that were not used to construct the clusters.

## Self-Supervised Training

Self-supervised learning constructs a training signal from the data itself, then transfers the learned representation to a labeled downstream task. It is useful when unlabeled EEG is plentiful but affect annotations are scarce, expensive, delayed, or noisy. The pretraining corpus must be separated from downstream test data according to the intended generalization claim: using test-subject recordings during pretraining may be acceptable only when transductive or target-domain adaptation is part of the declared protocol.

### Generative Self-Supervision

Generative self-supervision learns to reconstruct, predict, or model part of the EEG signal from another part. Examples include masked-sample reconstruction, masked-channel prediction, future-window prediction, denoising, and autoencoding. The objective encourages representations that preserve temporal, spectral, and spatial structure.

**Example scenario:** A masked autoencoder receives a 10-second multichannel EEG segment with several time spans and channels hidden. It learns to reconstruct the missing values from visible context, then its encoder is fine-tuned to classify valence using a small labeled training set.

### Contrastive Self-Supervision

Contrastive self-supervision brings embeddings of related EEG views closer together and separates unrelated views. Related views can be two augmentations of the same window, neighboring windows from the same trial, or recordings from the same subject under a carefully defined invariant. The choice of positive and negative pairs encodes the scientific assumption of what should remain stable.

Augmentations must preserve the property needed by the downstream task. An augmentation that destroys phase, a critical frequency band, or a spatial relationship may produce an invariant representation that is unsuitable for the target. False negatives are also a concern: windows from different trials may share the same affective state even if the contrastive objective treats them as unrelated.

**Example scenario:** Two mild augmentations of the same artifact-screened EEG window, such as small amplitude scaling and limited channel dropout, form a positive pair. Windows from other subjects form negatives. The pretrained encoder is later evaluated for cross-subject emotion classification using only labeled source-subject data.

### Pseudo-Label Self-Supervision

Pseudo-label methods construct provisional targets from the data or from a teacher model. Examples include predicting temporal order, identifying which channel was masked, clustering embeddings and predicting cluster assignments, or training a student model on high-confidence predictions from a teacher. Pseudo-labels can increase the effective training signal, but they can also amplify early model mistakes or encode artifacts rather than affective structure.

Use confidence thresholds, teacher-student consistency checks, or repeated clustering stability to control pseudo-label quality. Pseudo-label generation must be fitted on training data or on explicitly allowed unlabeled target data; it must never use held-out test labels.

**Example scenario:** A teacher encoder clusters unlabeled training EEG windows into stable prototype groups. Only windows assigned consistently across augmentations receive a pseudo-label. A student model is trained on those pseudo-labels and then fine-tuned using the available trial-level emotion labels.

| Self-supervised direction | Pretext target | Strength | Main risk |
| --- | --- | --- | --- |
| Generative | Missing, corrupted, future, or reconstructed signal | Preserves signal structure | Reconstruction may emphasize noise or easy local patterns |
| Contrastive | Agreement between related views | Learns invariances and can use large unlabeled corpora | Positive-pair and augmentation assumptions may be wrong |
| Pseudo-label | Data-derived or teacher-derived target | Can exploit latent structure and teacher confidence | Confirmation bias and artifact-driven labels |

## Reinforcement Learning

Reinforcement learning learns a policy that selects actions from observations in order to maximize cumulative reward. In affective EEG, the observation may include current EEG features and interaction context; an action may adapt a stimulus, select a training exercise, change feedback, or request a calibration measurement. The objective is not simply to classify emotion accurately at each step, but to choose interventions that improve a longer-term outcome.

A reinforcement-learning formulation should define the state, action space, reward, transition assumptions, safety constraints, and evaluation environment. Offline datasets typically contain logged behavior rather than outcomes for every possible action, so naive off-policy evaluation can be biased. Initial work should favor simulation, conservative offline reinforcement learning, or tightly controlled studies before adaptive policies affect real users.

**Example scenario:** In a neurofeedback application, a policy observes a participant's causal arousal estimate, recent task performance, and session time. It chooses between lowering task difficulty, maintaining it, or inserting a short break. Reward combines sustained engagement and task performance while penalizing excessive interruptions. Evaluation compares the learned policy with fixed difficulty schedules under the same safety constraints.

## Choosing a Paradigm

| Available evidence and goal | Suitable starting paradigm |
| --- | --- |
| Reliable labeled trials or windows | Supervised learning |
| Large unlabeled corpus plus a small labeled target set | Self-supervised pretraining followed by supervised fine-tuning |
| No labels and an exploratory scientific objective | Unsupervised discovery with independent validation |
| Sequential interventions with an explicit long-term outcome | Reinforcement learning or conservative offline reinforcement learning |

These paradigms can be combined. For example, an encoder may be pretrained with contrastive learning, fine-tuned with supervised emotion labels, and used within a reinforcement-learning policy. Each stage must still have its own training data, validation criteria, and leakage controls.

## References

- Banville, H., Chehab, O., Hyvarinen, A., Engemann, D. A., and Gramfort, A. (2021). Uncovering the structure of clinical EEG signals with self-supervised learning. Journal of Neural Engineering, 18(4), 046020.
- Lotte, F., Bougrain, L., Clerc, M., et al. (2018). A review of classification algorithms for EEG-based brain-computer interfaces: A 10 year update. Journal of Neural Engineering, 15(3), 031005.
- Sutton, R. S., and Barto, A. G. (2018). Reinforcement Learning: An Introduction. MIT Press.
