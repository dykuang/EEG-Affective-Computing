# Causal and Mechanistic Affective Neurotechnology

Most affective EEG systems learn correlations: patterns that predict a rating, stimulus category, or behavioral outcome in a particular dataset. Correlation can support useful prediction, but it does not establish why a signal changes or whether changing the signal would change affect, behavior, or a system outcome. Causal and mechanistic approaches aim to make these distinctions explicit.

## From Association to Intervention

A correlational model may learn that a spectral pattern accompanies high arousal in one task. The pattern could reflect arousal itself, task difficulty, muscle activity, stimulus properties, or a response strategy. A causal question asks what would happen under an intervention, such as changing task difficulty, feedback timing, a stimulus, or neural stimulation.

A simplified causal graph can include task context $C$, latent affective state $A$, EEG measurement $X$, behavior $B$, and system action $U$. The aim is not simply to estimate $p(A \mid X)$, but to reason about quantities such as the effect of an action:

$$
p(B \mid do(U = u)).
$$

The $do(\cdot)$ notation emphasizes that an intervention differs from observing a naturally occurring variable. Strong causal claims require study designs and assumptions that are rarely available from passive benchmark data alone.

![Causal graph for affective neurotechnology. The diagram should show task context, latent affective state, EEG measurement, behavior, and system action, with confounding paths distinguished from the intervention path used to estimate the effect of an action.](figures/affective-neurotechnology-causal-graph.png)

**Figure 12.13: Causal graph for affective neurotechnology.** EEG is a measurement of underlying processes, not automatically a cause; causal analysis must distinguish observed associations from intervention effects involving context, affect, behavior, and system action.

## Why Mechanisms Matter

Mechanistic understanding can improve robustness and safety. A model that depends on a stimulus-specific visual artifact may score well but fail when the stimulus changes. A model that captures a reproducible relationship among task demand, neural dynamics, and behavior may transfer more reliably and support better interventions.

For affective technology, causal reasoning can help distinguish:

- a user's changing affect from an artifact caused by the system's action;
- short-term arousal from a beneficial or harmful long-term outcome;
- a predictor of disengagement from a controllable cause of disengagement;
- and a useful neurofeedback target from a merely correlated biomarker.

## Research Designs for Causal Evidence

Causal progress depends on experimental design as much as model choice. Useful approaches include randomized stimulus or interface interventions, counterbalanced task conditions, within-subject crossover studies, longitudinal measurements, natural experiments, and preregistered hypotheses. Closed-loop systems add another challenge because the system's action changes the next neural observation.

When direct intervention is not possible, causal models can still clarify assumptions and identify confounders, but they should not be interpreted as proof of mechanism. Sensitivity analysis, negative controls, external replication, and explicit alternative causal graphs are valuable safeguards.

**Example scenario:** A tutoring agent randomizes whether optional hints are offered after evidence of high workload. It measures immediate EEG changes, task performance, user-reported frustration, and later retention. This design can test whether the hint policy improves outcomes, rather than merely observing that high workload and hint use co-occur.

![Closed-loop causal evaluation design. The diagram should show randomized tutoring hints or interface actions, immediate EEG and behavioral responses, delayed outcomes, user reports, and later policy decisions, with arrows showing how an intervention changes subsequent observations.](figures/closed-loop-causal-evaluation.png)

**Figure 12.14: Closed-loop causal evaluation.** Randomized interventions, time-aligned outcomes, and delayed follow-up help test whether an affect-aware action improves user-defined outcomes rather than merely correlating with neural state.

## Causal Models in Closed Loops

In an embodied or BCI system, action, feedback, user strategy, and neural state influence one another. A policy that adapts task difficulty based on inferred arousal creates feedback: later arousal is partly an effect of the system's earlier action. Offline models trained on these logs can be biased if they ignore the policy that generated the data.

Future work should combine causal inference with conservative offline reinforcement learning, uncertainty estimation, and explicit safety constraints. The goal is not maximal automated intervention, but reliable evidence about which actions support user-defined outcomes under specified conditions.

## Mechanistic Validation

Mechanistic claims require multiple kinds of evidence. These can include reproducible temporal relationships, source-informed or connectivity analyses with stated limitations, response to controlled perturbations, convergent behavioral and physiological measures, and replication across datasets or laboratories.

Avoid reverse inference: observing a neural pattern associated with an emotion-related region or frequency band does not prove that a particular emotion caused the pattern. The appropriate conclusion is usually conditional and limited to the measurement, task, and population studied.

## Future Direction

Causal and mechanistic work can move affective EEG beyond the question, "Can this signal predict a label?" toward questions such as, "Which interventions help this person achieve their goal, why might they work, and under what uncertainty should the system refrain from acting?" This direction is demanding, but it is essential for trustworthy neurotechnology that influences real people rather than only scoring offline datasets.

## References

- Pearl, J. (2009). Causality: Models, Reasoning, and Inference. Cambridge University Press.
- Hernan, M. A., and Robins, J. M. (2020). Causal Inference: What If. Chapman and Hall/CRC.
- Bareinboim, E., and Pearl, J. (2016). Causal inference and the data-fusion problem. Proceedings of the National Academy of Sciences, 113(27), 7345-7352.
- Sitaram, R., Ros, T., Stoeckel, L., et al. (2017). Closed-loop brain training: The science of neurofeedback. Nature Reviews Neuroscience, 18, 86-100.
