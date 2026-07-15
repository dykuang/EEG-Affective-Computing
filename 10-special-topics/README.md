# Special Topics

This chapter collects topics that do not fit neatly into the standard pipeline of EEG-based affective computing, but that become important once the field moves beyond benchmark-style closed-world classification. The common theme is that real affective systems must often reason with extra structure: psychological structure over time, incomplete label vocabularies, uncertainty about unseen states, and deployment conditions that differ from the assumptions used during training.

The sections in this chapter therefore emphasize modeling assumptions that are easy to ignore in small offline studies but become central in realistic applications.

## Chapter Structure

1. **Psychological Priors**: Why affect should often be modeled as a structured temporal process rather than a sequence of independent labels
2. **General Class Discovery**: How to detect and organize affective states that were not represented in the training label space
3. **EEG Foundation Models**: How large-scale pretraining and reusable neural representations may improve transfer, personalization, and open-world affective inference
4. **Robust Learning Under Noisy Labels**: How to train affective EEG models when supervision is ambiguous, inconsistent, or partially wrong
5. **Local-Segment and Global-Trial Label Inconsistency**: How to reason about segment-level predictions when only trial-level affect labels are available
6. **Source Estimation and Inverse Modeling**: How to map scalp EEG back to brain sources for improved spatial specificity and anatomical interpretability in affective computing
7. **Active BCI: Closed-Loop Feedback and Human-Machine Co-Adaptation**: How intentional neural control, assistive feedback, calibration, and mutual learning change the problem from passive inference to a coupled human-machine system

Taken together, these topics point toward a broader open-world view of affective EEG: models should be temporally plausible, uncertainty-aware, capable of handling emotional structure beyond the benchmark taxonomy, and designed for the human adaptation that arises in closed-loop use.
