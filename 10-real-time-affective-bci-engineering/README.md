# Real-Time Affective BCI Engineering

An offline affect classifier estimates performance on stored recordings. A real-time affective BCI is a different object: a causal, time-constrained, stateful, safety-relevant system embedded in a feedback loop with a changing human user. The same windows and the same model weights can support a valid offline result and an invalid real-time claim if the deployed pipeline uses information that would not have been available at decision time, or if it acts without accounting for latency, signal quality, or the effects of its own feedback.

This distinction is easy to miss after [Chapter 4](../04-problem-formulation/README.md), [Chapter 6](../06-preprocessing-and-segmentation/README.md), and [Chapter 9](../09-training-and-evaluation/README.md), which already define causal tasks, online preprocessing, and chronological evaluation. Those chapters specify *what* a causal claim requires. This chapter specifies *how to build and validate the system that must honor that claim while the user is still in the loop*.

The central engineering problem is not to classify a batch of windows as quickly as possible. It is to produce, at a declared decision time, an action or an abstention that is justified by the information that had already arrived, that respects a latency budget appropriate to the application, and that remains safe when clocks drift, channels drop, artifacts burst, or the user's physiology and strategy change. An affective BCI that estimates slowly varying mood, tracks arousal, detects a workload transition, or drives neurofeedback will tolerate different delays. There is no universal millisecond threshold that makes a system "real-time."

The deployed system is therefore a pipeline with clocks, buffers, and policies, not a model file with a score. The chapter asks, in order: what information had arrived at decision time; how streams were timestamped and aligned; which operations were causal; when the system abstained; how session-scale drift was bounded; how feedback changed the next observation; and what staged tests support the claim.

Passive affective BCI, in the sense of Zander and Kothe, uses ongoing brain activity as implicit input to a human-machine system rather than as an intentional command. Physiological computing, as Fairclough describes it, further treats the user and the adaptive interface as a coupled loop. Those scientific framings remain essential. They do not replace clocks, quality gates, or rollback. Closed-loop human-machine co-adaptation is treated conceptually in [Chapter 11](../11-special-topics/07-active-bci-closed-loop-feedback-and-co-adaptation.md); longer-term continual learning and governance are treated in [Chapter 12](../12-Look-into-the-future/README.md). This chapter stays with the integrated runtime: how a classifier becomes a controller that can be observed, delayed, degraded, and stopped.

## Chapter Structure

1. **Streaming Architecture and Time Semantics**: the path from sensors to action, the several clocks that a sample can carry, and a latency budget reported as a distribution rather than a mean.
2. **Causal Online Signal Processing and Inference**: windows, state, normalization, filtering, and batch-size-one inference without future information.
3. **Robust Operation, Adaptation, and Safe Control**: signal-quality monitoring, abstention, a runtime state machine, bounded personalization, and policy constraints that keep inference from becoming an unchecked intervention.
4. **Validation, Deployment, and Reference Blueprint**: staged replay and hardware tests, fault injection, operational metrics, and a minimum credible real-time claim.

A reader who has trained a strong offline model should leave this chapter able to say whether that model, placed in a live loop, would still be making the decision it appears to make.

## References

- Zander, T. O., and Kothe, C. (2011). Towards passive brain-computer interfaces: applying brain-computer interface technology to human-machine systems in general. Journal of Neural Engineering, 8(2), 025005.
- Fairclough, S. H. (2009). Fundamentals of physiological computing. Interacting with Computers, 21(1-2), 133-145.

---

Next: [Streaming Architecture and Time Semantics](01-streaming-architecture-and-time-semantics.md)
