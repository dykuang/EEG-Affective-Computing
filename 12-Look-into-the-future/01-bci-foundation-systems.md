# BCI Foundation Systems

The future of EEG-based BCI is unlikely to depend on one monolithic decoder. A useful foundation system will be a reusable neural representation and interface stack that supports bounded tasks across people, devices, sessions, and contexts. It should turn uncertain neural evidence into user-controlled assistance, not into unrestricted claims about private mental content.

The durable systems lesson is that progress requires more than model architecture: governed data, interoperable interfaces, adaptation methods, tools, evaluation suites, deployment infrastructure, and explicit reliability and safety controls must reinforce one another.

## A Layered Foundation-System Blueprint

A practical BCI foundation system can combine several specialized layers:

1. **Governed data commons:** Raw or access-controlled recordings with montage, reference, sampling rate, filters, hardware, quality measures, task events, labels, consent scope, and artifact metadata.
2. **Signal and model interfaces:** Adapters that expose channel coordinates, missing-channel masks, reference, sampling rate, synchronization, artifact flags, uncertainty, embeddings, calibration state, provenance, and abstention.
3. **Neural backbones:** Self-supervised encoders specialized for regimes such as wearable low-channel EEG, high-density laboratory recordings, clinical EEG, longitudinal monitoring, or active BCI control.
4. **Task adapters:** Bounded outputs such as workload, attention, affect, intended command, anomaly, or signal quality rather than unrestricted mental-state claims.
5. **Adaptation and personalization:** Reversible adapters, low-rank updates, prototype heads, calibration-aware normalization, and confirmed user feedback.
6. **Bounded agents and interfaces:** User-facing systems that propose options, request confirmation, execute permitted actions, or explain uncertainty.
7. **Evaluation and governance:** Cross-device, cross-session, closed-loop, safety, agency, privacy, and fairness evaluation spanning every layer.

![A layered BCI foundation-system blueprint: governed data and harmonization support self-supervised neural encoders; adapters, uncertainty, and personalization support bounded task services; agents and interfaces act through consent, confirmation, and safety monitoring; evaluation and governance span every layer.](figures/bci-foundation-blueprint.png)

**Figure 12.1: Layered BCI foundation-system blueprint.** A BCI foundation system is a stack rather than a monolithic decoder. Reusable data and representations create reuse, while explicit adaptation, evaluation, consent, and safety protect against overreach.

The goal is an interoperable family of encoders, adapters, and applications whose assumptions and evidence remain visible at every stage. A larger encoder that fails under a new montage or session has not achieved meaningful reuse.

## Governed Data and Interoperable Interfaces

The first bottleneck is not model size but usable, governed data. Federated learning, secure enclaves, controlled access, and privacy-preserving evaluation may be necessary because raw data cannot always be centralized. The objective is a documented training substrate in which data rights, provenance, and representational coverage are measurable.

BCI also needs a more explicit substitute for the relatively stable text interface used by LLM applications. Signal adapters should map incoming recordings into a declared canonical representation while preserving what cannot be normalized away. They should not conceal preprocessing choices or device differences behind one opaque EEG-token format.

## Specialized Backbones and Bounded Services

Pretraining should exploit broad unlabeled recordings, but scaling should be empirical. Report how transfer changes with data diversity, parameters, compute, and context length, and compare large models with smaller models trained using strong harmonization. Useful services may include workload-aware interfaces, active commands, artifact support, adaptive training, and assistive communication.

The BCI analogue of fine-tuning should be light, reversible, and auditable. A system should record when it learned, from which confirmed examples, under which device configuration, and how to roll back. Continual learning should distinguish confirmed labels from weak feedback and use holdout periods to detect harmful drift.

## Bounded Agents and Human-Directed Interfaces

BCI systems can become useful through interfaces that transform uncertain evidence into low-risk support. A workload estimate can offer a pacing option; an active command can select from a constrained interface; an artifact detector can request electrode adjustment. The agent should distinguish reporting a state estimate, executing a direct command, and proposing an action that requires consent.

High-bandwidth language remains a complement to low-bandwidth neural control. Capable future systems will combine explicit user input, gaze, switches, speech, environmental context, and neural evidence under shared autonomy. They should increase the user's effective control rather than use an uncertain neural estimate to displace it.

## Cross-Device Transfer and Multimodal Systems

Models that transfer across channel counts and consumer or laboratory hardware would make BCI development more cumulative. Geometry-aware encoders, channel-set transformers, missing-channel objectives, and source-informed representations are promising, but evidence requires truly unseen devices and held-out channel subsets.

EEG gains meaning from context. Aligning it with task events, behavior, eye tracking, peripheral physiology, speech, and environmental sensors can support more useful and less ambiguous estimates. The correct comparison includes context-only and EEG-only baselines; otherwise a multimodal model may receive credit for information EEG did not supply.

## Closed-Loop Deployment and Evaluation

BCI creates feedback: the system changes the task, which changes the user and the next EEG observation. Logged offline data and simulators can help test policies, but prospective, controlled, longitudinal studies are ultimately required. Safe testbeds should model dropped channels, artifacts, neural drift, delayed feedback, incorrect confidence, and user disagreement.

A foundation system needs layered evaluation rather than one leaderboard:

| Layer | Questions to test |
| --- | --- |
| Representation | Does pretraining transfer across tasks, people, sessions, devices, and low-label settings? |
| Signal robustness | Does it abstain or degrade safely with artifacts, missing channels, changed references, and poor contact? |
| Personalization | Does adaptation improve future sessions without overfitting or increasing correction burden? |
| Interaction | Does the complete interface improve throughput, task outcome, workload, and user-defined benefit over non-BCI baselines? |
| Safety and agency | Are actions bounded, explanations calibrated, overrides effective, and user control preserved? |
| Privacy and fairness | Do performance, leakage risk, and access remain acceptable across populations, devices, and consent states? |

Shared benchmarks should include no-EEG, context-only, fixed-rule, and user-controlled baselines. They should reward calibrated abstention and reproducible behavior, not only a model's ability to return a label for every window.

## A Staged Research Program

1. **Build reliable vertical applications** with measurable outcomes, such as assistive communication, adaptive training, signal-quality support, or workload-aware interfaces.
2. **Publish interoperable data and adapter specifications** covering acquisition, preprocessing, montage handling, labels, and consent assumptions.
3. **Pretrain across curated regimes** and measure transfer under held-out subjects, sites, devices, and tasks.
4. **Develop reversible personalization** using parameter-efficient updates, calibration budgets, user feedback, and later-session validation.
5. **Standardize closed-loop and human-centered evaluation** with latency, error recovery, agency, privacy, and long-term outcomes.
6. **Scale only after evidence of reuse** produces reliable transfer and lower user burden.

BCI can become compositional, reusable, and evidence-driven, but its distinctive success criterion is more demanding than benchmark performance: systems must make people more capable while staying honest about uncertainty, sensitive to privacy, and answerable to user control.

## References

- Bommasani, R., Hudson, D. A., Adeli, E., et al. (2021). On the opportunities and risks of foundation models. arXiv:2108.07258.
- Kostas, D., Aroca-Ouellette, S., and Rudzicz, F. (2021). BENDR: Using transformers and a contrastive self-supervised learning task to learn from massive amounts of EEG data. *Frontiers in Human Neuroscience*, 15, 653659.
- Millan, J. del R., Rupp, R., Muller-Putz, G. R., et al. (2010). Combining brain-computer interfaces and assistive technologies: State-of-the-art and challenges. *Frontiers in Neuroscience*, 4, 161.
- Yuste, R., Goering, S., Arcas, B. A. y., et al. (2017). Four ethical priorities for neurotechnologies and AI. *Nature*, 551, 159-163.
- Shneiderman, B. (2020). Human-centered artificial intelligence: Reliable, safe and trustworthy. *International Journal of Human-Computer Interaction*, 36(6), 495-504.