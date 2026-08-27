# Validation, Deployment, and Reference Blueprint

Offline classification accuracy answers a different question from "does this live system work." A model can have a strong AUROC on held-out windows and still be late, overconfident, brittle to dropouts, or harmful once it acts. Validation of a real-time affective BCI is therefore staged. Early stages are cheap and cannot establish closed-loop benefit. Later stages are slower and still cannot be replaced by a leaderboard score.

[Chapter 9](../09-training-and-evaluation/README.md) defines leakage-safe splits, causal evaluation, and reproducibility for models. This section adds the system tests that those protocols do not replace: replay with recorded timestamps, hardware-in-the-loop faults, shadow operation, and bounded deployment with rollback.

## A Staged Validation Ladder

1. **Offline causal replay using recorded timestamps.** Replay the stored stream in timestamp order, using only data available at each historical $$t$$. This can expose noncausal filters, whole-recording normalization, and label lookahead. It cannot expose device delay that was never recorded, operating-system jitter on the target machine, or user reactions to feedback that was not given.

2. **Accelerated and real-time replay.** Play the same log faster than real time to stress queues, then at recorded pace to measure $$L_{\mathrm{total}}$$ on the target software. This tests computational latency and backlog behavior. It still uses yesterday's user, not a user who can adapt to the live output.

3. **Hardware-in-the-loop testing.** Drive the pipeline from the actual amplifier, wireless link, or microcontroller, with a phantom, playback DAC, or known synthetic signal. This can reveal on-device buffering, USB or Bluetooth chunking, and clock behavior that logs omit. It does not establish affective validity.

4. **Fault injection and stress testing.** Deliberately insert the failures listed below. The passing criterion is a specified mode transition and a specified user-visible behavior, not "accuracy stays high."

5. **Shadow-mode operation.** Run the full stack, including policy, without delivering the intervention to the user. Log what *would* have been done. This estimates false-intervention rates and abstention under real acquisition noise. It cannot measure closed-loop effects, because the user does not see the output.

6. **Supervised pilot deployment.** A small, consented user study with monitoring, override, and a predeclared stopping rule. This can measure task-level benefit, burden, and recovery. It is not a license for unbounded scale-up.

7. **Bounded operational deployment.** Versioned configuration, live quality dashboards, rollback, and a change-control process. Expansion of user-facing authority should be an explicit decision, not a silent consequence of a better F1.

Each stage inherits the limits of the previous one. Skipping to a live demo from an offline score leaves the causal, timing, and safety claims untested.

## Fault Injection

A real-time claim is a claim about behavior under faults, not only under clean laboratory EEG. At least the following should be injected, with expected mode and action recorded in advance:

| Fault | What it probes | Typical safe response |
| --- | --- | --- |
| Packet delay and jitter | Queueing, deadline misses | Hold or `DEGRADED`; do not interpolate neural data |
| Clock drift | Alignment of EEG with events and ratings | Recalibrate offsets or abstain on cross-modal actions |
| Missing samples | Continuity logic | Gap markers; reset or hold filter state |
| Channel dropout | Spatial model assumptions | Degraded montage or `DEGRADED` |
| Device disconnects | Reconnect protocol | `SAFE_FALLBACK` until warm-up completes |
| CPU or GPU overload | Computational latency tails | Drop to a cheaper model or fallback; never silently skip quality checks |
| Buffer overflow | Backpressure policy | Log loss; enter `DEGRADED` |
| Artifact bursts | Quality gate versus forced labels | `ABSTAIN` |
| Missing modalities | Fusion policy | Run a declared EEG-only or non-neural default |
| Low confidence | Selective prediction | `ABSTAIN` |
| Model-service failure | Process isolation | `SAFE_FALLBACK` |
| Corrupted or stale calibration | Version checks | Refuse `READY`; require recalibration |

BCI2000 and related real-time platforms were motivated in part by the fact that online operation has timing requirements that offline scripts do not reveal. Fault injection is how those requirements are made testable rather than anecdotal.

## Evaluation Beyond Offline Accuracy

Report, in addition to the causal classification or tracking metrics of [Chapter 9](../09-training-and-evaluation/05-metrics-statistical-evidence-and-reproducibility.md):

- end-to-end latency and jitter (median and upper quantiles of $$L_{\mathrm{total}}$$ and of its terms);
- update-rate stability (fraction of strides that met the deadline);
- uptime and time spent in `DEGRADED`, `ABSTAIN`, and `SAFE_FALLBACK`;
- signal-quality coverage (fraction of decisions with trustworthy input);
- abstention rate and selective-risk or coverage–utility curves;
- calibration diagnostics under in-session shift, not only on i.i.d. validation windows;
- false-intervention rate and time to recover after a false action;
- task-level benefit against a non-neural or sham-feedback baseline;
- user burden, frustration, trust, and perceived agency;
- adaptation stability (did later-block performance rise or fall after updates);
- energy and compute cost where the device is constrained;
- and the same quantities across subjects, sessions, devices, and realistic contexts.

A better AUROC or F1 does not automatically produce a better closed-loop system. A slightly weaker detector that abstains under motion and never hides an emergency notification can be the better controller. Conversely, a high F1 model that fires on EMG and cannot be overridden can be worse than no BCI.

Pernet and colleagues' EEG-BIDS extension, and the current BIDS EEG specification, do not define a real-time runtime. They do define the metadata that later replay depends on: sampling rate, filters, channels, events, and provenance. A live system that cannot write an auditable record cannot be replayed, and a real-time claim that cannot be replayed cannot be checked.

## Reference Architecture Checklist

A compact implementation contract covers the objects that must exist, not the framework that must be used:

| Record | Purpose |
| --- | --- |
| Data contracts | Units, channel names, sampling rate, reference, allowed dtypes |
| Timestamp provenance | Device, arrival, synchronized, and residual uncertainty |
| Configuration and model version | Immutable identifiers for code, weights, and policy rules |
| Calibration version | Which session and statistics produced $$\theta$$ |
| Runtime state | Current mode and the evidence that entered it |
| Quality and uncertainty records | Indicators and $$u_t$$ at each decision |
| Prediction and policy output | Raw scores versus committed action |
| Action or abstention | What the user-facing system actually did |
| User correction | Explicit confirmations, rejects, and overrides |
| Safety override | Operator or user stop, and the mode it forced |
| Audit log | Append-only lineage sufficient for replay |
| Reproducible replay | Ability to rebuild each decision from the log and versions |

If any row is missing, debugging a false intervention becomes speculation.

## Minimum Credible Real-Time Claim

A reader who claims a real-time affective BCI should be able to state, for the reported system:

1. What information was causally available at each decision.
2. How clocks and streams were synchronized, including on-device delay bounds.
3. What the end-to-end latency distribution was, not only the mean or the model runtime.
4. How gaps, artifacts, uncertainty, and device failures were handled.
5. When the system abstained or entered a fallback state.
6. Whether feedback altered subsequent data, and how that was evaluated.
7. How adaptation was bounded, versioned, and held out from later evaluation.
8. What task-level benefit was observed against an appropriate baseline.
9. Which system version and configuration produced each action.

Items that cannot be answered should be reported as unknown, not implied by a streaming demo or an offline score.

## Practical Recommendations

- Treat causal replay as a gate before any live user-facing test.
- Write expected fault responses before injecting faults.
- Keep shadow mode until false-intervention and abstention rates are acceptable under real acquisition.
- Store lineage in a form that an independent analyst can replay.
- Do not describe a system as real-time solely because inference is fast or because a window is short.

The engineering goal is modest and demanding: a system that makes only the decisions its clocks, buffers, and evidence can support, that can stop, and that can show its work.

---

Next: [Special Topics](../11-special-topics/README.md)

## References

- Schalk, G., McFarland, D. J., Hinterberger, T., Birbaumer, N., and Wolpaw, J. R. (2004). BCI2000: A general-purpose brain-computer interface (BCI) system. IEEE Transactions on Biomedical Engineering, 51(6), 1034-1043.
- Kothe, C., Shirazi, S. Y., Stenner, T., Medine, D., Boulay, C., Grivich, M. I., Artoni, F., Mullen, T., Delorme, A., and Makeig, S. (2025). The lab streaming layer for synchronized multimodal recording. Imaging Neuroscience, 3, IMAG.a.136.
- Pernet, C. R., Appelhoff, S., Gorgolewski, K. J., Flandin, G., Phillips, C., Delorme, A., and Oostenveld, R. (2019). EEG-BIDS, an extension to the BIDS specification for EEG. Scientific Data, 6, 103.
- Brain Imaging Data Structure contributors. (n.d.). Electroencephalography (EEG) specification. https://bids-specification.readthedocs.io/en/stable/modality-specific-files/electroencephalography.html
- Wolpaw, J. R., and Wolpaw, E. W. (Eds.). (2012). Brain-Computer Interfaces: Principles and Practice. Oxford University Press.
- Millan, J. del R., Rupp, R., Muller-Putz, G. R., et al. (2010). Combining brain-computer interfaces and assistive technologies: State-of-the-art and challenges. Frontiers in Neuroscience, 4, 161.
- Lotte, F., Larrue, F., and Muehl, C. (2013). Flaws in current human training protocols for spontaneous brain-computer interfaces: Lessons learned from instructional design. Frontiers in Human Neuroscience, 7, 568.
