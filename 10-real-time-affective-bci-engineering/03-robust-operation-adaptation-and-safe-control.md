# Robust Operation, Adaptation, and Safe Control

Once a causal pipeline exists, the remaining failure modes are operational. Channels go flat. The user shifts in the chair. Softmax outputs remain confident after the distribution has moved. Feedback changes the next EEG segment. A classifier that always emits a label is not yet a controller. This section treats the live system as a stateful policy with monitoring, abstention, bounded adaptation, and safe defaults.

Closed-loop co-adaptation as a human-machine learning problem is discussed in [Chapter 11](../11-special-topics/07-active-bci-closed-loop-feedback-and-co-adaptation.md). Longer-term drift, catastrophic forgetting, and governance of consent and agency are discussed in [Chapter 12](../12-Look-into-the-future/05-continual-learning-lifelong-adaptation-and-neural-drift.md) and [Chapter 12](../12-Look-into-the-future/06-neurotechnology-governance-privacy-and-human-agency.md). Here the focus is the session-scale runtime: what the system is allowed to do in the next second, and on what evidence.

## Runtime Signal-Quality Monitoring

Artifact handling in preprocessing asks whether contamination can be reduced. Runtime monitoring asks a different question: whether the *current* prediction should be trusted enough to drive an action. A cleaned-looking window can still be untrustworthy if a channel has gone ohmic, a modality has vanished, or the embedding lies far from the calibration support.

Useful indicators, when the hardware and stream actually provide them, include:

- flat or saturated channels;
- electrode-contact or impedance readings;
- timestamp gaps and duplicate packets;
- sudden changes in line-noise power;
- motion, ocular, and muscle contamination scores;
- loss of a required auxiliary modality;
- implausible feature or embedding values relative to a calibration envelope;
- and abrupt shifts in a simple distributional statistic (for example, a large jump in running covariance that is more consistent with a disconnected ground than with affect).

These indicators should be logged even when they do not change the label. "We removed artifacts" is not evidence that the present estimate is valid. A high-quality ICA reconstruction of a blinky segment does not license a high-stakes intervention if residual contamination or a missing channel remains.

## Uncertainty and Abstention

The maximum softmax coordinate is a convenient score. It is not automatically a calibrated probability of correctness. Guo and colleagues showed that modern networks can be highly confident and poorly calibrated even on in-distribution data. Ovadia and colleagues showed that calibration fitted on identically distributed validation data can degrade under dataset shift—the normal condition for EEG across sessions, devices, and postures. Neither result was obtained on affective EEG; both are reasons not to treat a default confidence head as a safety guarantee.

What a system can do, honestly, is:

- define a confidence or uncertainty functional $$u_t$$ (maximum softmax, temperature-scaled probability, ensemble disagreement, energy score, or another declared method);
- calibrate that functional on held-out data that match the *deployment claim* (same causality, similar shift), not on shuffled windows from the training recording;
- choose a threshold or a selective rule that trades coverage for risk, in the sense of selective classification: predict only when $$u_t$$ satisfies a criterion, otherwise abstain;
- and evaluate coverage–accuracy or coverage–utility curves rather than accuracy alone.

Geifman and El-Yaniv formalize selective classification as a coverage–risk tradeoff. In an affective BCI the analogous tradeoff is coverage versus *utility under false intervention*. A system that labels every second will often look stronger on window-wise F1 and worse on user trust. Some applications should output "insufficient evidence" rather than force an affect label. No single uncertainty method provides a universal safety guarantee. Temperature scaling, in particular, does not restore calibration under strong shift. Abstention is a policy choice backed by a measurement, not a property of softmax.

## A Runtime State Model

A small state machine makes permitted actions explicit. One useful set of modes is:

| Mode | Entry (illustrative) | Permitted outputs | Exit |
| --- | --- | --- | --- |
| `INITIALIZING` | Start of session; empty window; filter warm-up | No affect label; status only | Window full and quality within bounds |
| `CALIBRATING` | Declared calibration protocol running | No user-facing intervention from the live head | Calibration complete or aborted |
| `READY` | Quality and uncertainty within bounds | Policy-gated actions | Quality drop, high $$u_t$$, or operator hold |
| `DEGRADED` | Gap, dropout, missing modality, or overload | Restricted or non-neural defaults | Evidence that the fault cleared *and* a new warm-up succeeded |
| `ABSTAIN` | Uncertainty or quality gate failed | "Insufficient evidence"; no new intervention | $$u_t$$ and quality recover for a declared dwell time |
| `SAFE_FALLBACK` | Repeated faults, model-service failure, corrupted calibration, or operator override | Predeclared safe output (pause, neutral UI, human control) | Explicit recovery procedure, not automatic confidence |

![Runtime state machine for a real-time affective BCI. Initialization and calibration lead to ready operation; quality or uncertainty faults move the system to degraded or abstain modes; severe or repeated faults enter safe fallback; recovery requires new evidence rather than a reset of confidence.](figures/runtime-state-machine.png)

**Figure 10.3: Runtime state machine.** Ready operation is only one mode. Degraded, abstain, and safe-fallback modes restrict action. Recovery is evidence-based: a new warm-up, a passed quality check, or an operator-confirmed calibration, not a softmax value crossing a line once.

Logging is required in every mode: timestamps, quality snapshot, $$u_t$$, mode, action or non-action, model and calibration versions. Recovery must not "magically restore confidence." Returning from `DEGRADED` to `READY` after a reconnect without a warm-up is a hidden noncausal leap: the filter state and spatial statistics are not the same object they were before the gap.

## Drift, Personalization, and Online Adaptation

Within a session, distributions move because of fatigue, drying gel, posture, strategy, and feedback. Across sessions and people they move more. Lotte and colleagues, reviewing a decade of EEG-BCI classifiers, found adaptive classifiers generally superior to static ones, including in some unsupervised cases, while also warning that transfer benefits are not predictable and that adaptation is not a universal upgrade. That evidence supports *bounded* adaptation. It does not support unrestricted self-training on the system's own labels.

Distinguish:

- **Initial calibration** before scored operation, with a declared budget of labeled or structured tasks;
- **Periodic updates** at session boundaries or after confirmed events;
- **Continuous updates** that change $$\theta_t$$ while the user is interacting.

Supervision can be explicit labels, self-supervised objectives on unlabeled EEG, pseudo-labels from the live head, or reward signals. Pseudo-labels are the most dangerous in a closed loop: errors become training data, then become future errors. Shadow updates (compute an adapter, do not deploy it), bounded step sizes, frozen reference models, and rollback when held-out or later-block performance drops are the corresponding controls. Adaptation data must be separated from unbiased future evaluation. A minute used to update $$\theta_t$$ is not a minute that can support the claim that the updated system generalizes.

Catastrophic forgetting and long-horizon continual learning belong primarily to [Chapter 12](../12-Look-into-the-future/05-continual-learning-lifelong-adaptation-and-neural-drift.md). The session-scale rule is simpler: if the system adapts, it must say when, from which confirmed evidence, under which version, and how to undo the change.

## From Prediction to Policy

A classifier output should not directly trigger every intervention. Policy constraints that remain causal include:

- hysteresis and dwell times, so a single noisy window cannot flip the interface;
- rate limits on how often an intervention may fire;
- temporal smoothing of scores with a one-sided kernel;
- confirmation requirements for high-impact actions;
- human override that is faster than the neural loop;
- recovery behavior after a false intervention (acknowledge, revert, and dwell);
- and safe default actions when the mode is not `READY`.

An inferred affective state is not a claim about what the user truly feels. The honest object is a score computed from available signals under a stated model. Interfaces should speak in that register ("uncertain high-arousal evidence") rather than as mind-reading.

## Closed-Loop Effects

Feedback changes attention, behavior, physiology, and strategy, and therefore the input distribution. Fairclough's biocybernetic loop and the co-adaptation literature make this a defining property, not an annoyance. The system can optimize a proxy that is easy to observe—EMG, a stereotyped spectral peak, a reduced motion artifact—rather than the intended human outcome. Evaluation after feedback begins is evaluation of a coupled process. Offline accuracy on pre-feedback recordings does not predict that process.

The following scenarios are **illustrative**. They are not published measured results.

**Adaptive soundscape (illustrative).** A system lowers tempo when causal arousal evidence is high for a dwell of 15 s. Failure mode: jaw EMG is scored as arousal and the music becomes sluggish whenever the user speaks. Safe behavior: freeze the soundscape and enter `ABSTAIN` when EMG or motion indicators exceed a calibration envelope; require quality recovery before further changes.

**Workload-aware interruptions (illustrative).** Notifications are delayed while a workload estimate is high. Failure mode: a single false-high window hides a time-critical message. Safe behavior: rate-limit suppression, cap delay, and never suppress designated emergency channels; show a visible "quiet mode" state the user can cancel.

**Bounded neurofeedback (illustrative).** A bar reflects a causal feature associated with a training target, updating at 2 Hz. Failure mode: the user learns to produce a non-neural artifact that moves the bar. Safe behavior: withhold feedback in `ABSTAIN` or `DEGRADED`, bound the visual gain, and keep a sham or non-contingent comparison in the evaluation protocol rather than treating bar motion as proof of neural control.

Sitaram and colleagues review closed-loop brain training and the need to separate specific neural regulation from non-specific effects of feedback. That caution applies directly to affective neurofeedback: a moving display is not evidence of a moving affective state.

## Practical Recommendations

- Monitor quality as a runtime input to policy, not only as a preprocessing stage.
- Calibrate uncertainty on data that match the causal deployment claim, and re-evaluate under shift.
- Implement abstention as a first-class output.
- Bound, shadow, and roll back online updates; never evaluate adaptation on the data that produced it.
- Put hysteresis, rate limits, and override in the policy, not in the hope that the classifier is already smooth.

---

Next: [Validation, Deployment, and Reference Blueprint](04-validation-deployment-and-reference-blueprint.md)

## References

- Guo, C., Pleiss, G., Sun, Y., and Weinberger, K. Q. (2017). On calibration of modern neural networks. In Proceedings of the 34th International Conference on Machine Learning, PMLR 70, 1321-1330.
- Ovadia, Y., Fertig, E., Ren, J., Nado, Z., Sculley, D., Nowozin, S., Dillon, J., Lakshminarayanan, B., and Snoek, J. (2019). Can you trust your model's uncertainty? Evaluating predictive uncertainty under dataset shift. In Advances in Neural Information Processing Systems.
- Geifman, Y., and El-Yaniv, R. (2017). Selective classification for deep neural networks. In Advances in Neural Information Processing Systems.
- Lotte, F., Bougrain, L., Cichocki, A., Clerc, M., Congedo, M., Rakotomamonjy, A., and Yger, F. (2018). A review of classification algorithms for EEG-based brain-computer interfaces: a 10 year update. Journal of Neural Engineering, 15(3), 031005.
- Sitaram, R., Ros, T., Stoeckel, L., et al. (2017). Closed-loop brain training: The science of neurofeedback. Nature Reviews Neuroscience, 18, 86-100.
- Fairclough, S. H. (2009). Fundamentals of physiological computing. Interacting with Computers, 21(1-2), 133-145.
- Zander, T. O., and Kothe, C. (2011). Towards passive brain-computer interfaces: applying brain-computer interface technology to human-machine systems in general. Journal of Neural Engineering, 8(2), 025005.
- Yuste, R., Goering, S., Arcas, B. A. y., et al. (2017). Four ethical priorities for neurotechnologies and AI. Nature, 551, 159-163.
