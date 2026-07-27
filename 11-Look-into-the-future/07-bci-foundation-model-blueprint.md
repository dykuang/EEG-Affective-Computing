# A Blueprint for BCI Foundation Systems

The success of current LLM systems did not come from one architecture alone. It came from a reinforcing ecosystem: broad data, scalable pretraining, reusable interfaces, adaptation methods, tools, evaluation suites, deployment infrastructure, and sustained attention to reliability and safety. EEG-based BCI can borrow this systems lesson without borrowing the misleading premise that neural signals are simply another language.

A plausible future is a **BCI foundation system**: a reusable neural representation and interface stack that supports bounded tasks across people, devices, and contexts. Such a system would turn uncertain neural evidence into user-controlled assistance, not into unrestricted claims about private mental content.

## The Analogy and Its Boundary

LLMs and BCIs differ at the level of data, meaning, and agency. Text is a discrete, communicative artifact that users intentionally produce; EEG is an indirect physical measurement that often reflects multiple processes at once. An EEG system should be judged by calibration, robustness, usefulness, and user control, not by fluency.

| Ingredient behind LLM progress | BCI analogue | Critical difference |
| --- | --- | --- |
| Large, diverse corpora | Governed multi-site EEG collections with rich recording metadata | EEG data are heterogeneous, scarce, sensitive, and not semantically self-labeling |
| Tokenizer and common interface | Montage-aware temporal-spatial patching and signal adapters | Channel layout, reference, and sampling rate alter measurement semantics |
| General pretrained model | Self-supervised neural encoder with quality and uncertainty heads | No universal neural vocabulary or stable brain-to-language mapping |
| Instruction tuning | Bounded task adaptation with explicit targets and calibration | A textual instruction cannot specify a person's internal state |
| Retrieval and tools | Consented calibration memory, device tools, and task-context services | Retrieved neural history requires strong privacy and access controls |
| Benchmarks and red teaming | Cross-device, cross-session, closed-loop, safety, and agency evaluations | Offline decoding scores are insufficient evidence of real-world value |

The goal is not a single universal model. It is an interoperable family of encoders, adapters, and applications whose assumptions and evidence are visible at every stage.

## A Layered Blueprint

![A layered BCI foundation-system blueprint: governed data and harmonization support self-supervised neural encoders; adapters, uncertainty, and personalization support task services; bounded agents and interfaces act only through consent, confirmation, and safety monitoring; evaluation and governance span every layer.](figures/bci-foundation-blueprint.svg)

*Figure 1. A BCI foundation system is a stack, not a monolithic decoder. Shared data and representations create reuse, while explicit adaptation, evaluation, consent, and safety protect against overreach.*

### 1. Governed Data Commons

The first bottleneck is not model size but usable, governed data. A BCI data commons should retain raw or access-controlled recordings alongside the metadata needed to interpret them: montage, reference, sampling rate, filters, hardware, impedance or quality measures, task events, participant population, session timing, labels, consent scope, and known artifacts.

Federated learning, secure enclaves, controlled access, and privacy-preserving evaluation may be necessary because raw data cannot always be centralized. The objective is not indiscriminate aggregation. It is a documented training substrate in which data rights, provenance, and representational coverage are measurable.

### 2. Interoperable Signal and Model Interfaces

LLM applications benefited from a relatively stable text interface. BCI needs a more explicit substitute: signal adapters that map incoming recordings into a declared canonical representation while preserving what cannot be normalized away. These adapters should expose channel coordinates, missing-channel masks, reference, sampling rate, time synchronization, artifact flags, and uncertainty.

Open model interfaces should standardize inputs and outputs where possible: embeddings, quality estimates, task predictions, calibration state, uncertainty, provenance, and abstention. They should not erase device differences or conceal preprocessing choices behind one opaque ``EEG token'' format.

### 3. Self-Supervised Pretraining and Specialized Backbones

Pretraining should exploit broad unlabeled recordings through masked modeling, cross-channel prediction, temporal prediction, contrastive views, and multimodal alignment. Rather than pursuing one giant general model immediately, a credible route is a collection of backbones for related regimes: wearable low-channel EEG, high-density laboratory recordings, clinical EEG, sleep and longitudinal monitoring, or active BCI control.

Scaling should be empirical. Report how quality changes as data diversity, parameters, compute, and context length increase, and compare against smaller models trained with strong harmonization. A larger encoder that fails under a new montage or session has not achieved the kind of reuse that makes foundation systems valuable.

### 4. Adaptation, Personalization, and Continual Calibration

The BCI analogue of fine-tuning should be light, reversible, and auditable. Promising mechanisms include adapters, low-rank updates, prototype heads, calibration-aware normalization, subject-specific latent variables, and meta-learned fast adaptation. A system should record when it learned, from which confirmed examples, under which device configuration, and how to roll back.

Continual learning needs partitions that protect evaluation. User corrections, interaction outcomes, and model confidence are not automatically correct labels. Update policies should distinguish confirmed labels from weak feedback, use holdout periods to detect harmful drift, and offer users a way to pause or reset personalization.

### 5. Bounded Agents and Human-Directed Interfaces

As LLMs became useful through tools and application layers, BCI systems can become useful through interfaces that transform evidence into low-risk support. A workload estimate can offer a pacing option; an active BCI command can select from a constrained interface; an artifact detector can request electrode adjustment. The agent should know whether it is reporting a state estimate, executing a direct command, or proposing an action that requires consent.

High-bandwidth language remains an important complement to low-bandwidth neural control. The most capable future systems will combine explicit user input, gaze, switches, speech, environmental context, and neural evidence under shared autonomy. They should increase the user's effective control, not use an uncertain neural estimate to displace it.

## Promising Routes

### Cross-Montage and Cross-Device Transfer

Models that transfer across channel counts and consumer or laboratory hardware would make BCI development much more cumulative. Geometry-aware encoders, channel-set transformers, missing-channel objectives, and source-informed representations are promising. Evidence requires testing on truly unseen devices and held-out channel subsets, not only resampling the same recordings.

### Multimodal Foundation Systems

EEG gains meaning from context. Aligning it with task events, behavior, eye tracking, peripheral physiology, speech, and environmental sensors can support more useful and less ambiguous estimates. The correct comparison includes context-only and EEG-only baselines; otherwise a multimodal model may be credited for information that EEG did not supply.

### Low-Burden Personalization

Few-shot calibration, passive quality monitoring, and confirmed interaction feedback can lower setup time. Useful targets include stable active commands, device adaptation, workload-aware interfaces, and long-term assistive communication. Success must include calibration burden, later-session retention, error recovery, and the user's ability to inspect or reverse adaptation.

### Closed-Loop Evaluation and Simulation

BCI creates feedback: the system changes the task, which changes the user and the next EEG observation. Logged offline data and simulators can help test policies, but prospective, controlled, longitudinal studies are ultimately required. Safe testbeds should model dropped channels, artifacts, neural drift, delayed feedback, incorrect confidence, and user disagreement before a system is used in a consequential setting.

### Efficient Edge-Cloud Partitioning

Wearable use requires on-device signal-quality checks, low-latency decoding, and privacy-preserving buffering. Larger context models or adaptation services may run locally or remotely only when consent, connectivity, latency, and data-handling rules permit. Distillation and compact adapters can bridge high-capacity offline research models and reliable device-side inference.

## Evaluation: The BCI Equivalent of Capability and Safety Suites

A BCI foundation system needs layered evaluation rather than one leaderboard:

| Layer | Questions to test |
| --- | --- |
| Representation | Does pretraining transfer across tasks, people, sessions, devices, and low-label settings? |
| Signal robustness | Does it abstain or degrade safely with artifacts, missing channels, changed references, and poor contact? |
| Personalization | Does adaptation improve future sessions without overfitting or increasing correction burden? |
| Interaction | Does the complete interface improve effective throughput, task outcome, workload, and user-defined benefit over non-BCI baselines? |
| Safety and agency | Are actions bounded, explanations calibrated, overrides effective, and user control preserved? |
| Privacy and fairness | Does performance, leakage risk, and access remain acceptable across populations, devices, and consent states? |

Shared benchmarks should include negative controls and meaningful baselines: no-EEG, context-only, fixed-rule, and user-controlled interfaces. They should reward calibrated abstention and reproducible behavior, not only a model's ability to return a label for every window.

## A Staged Research Program

1. **Build reliable vertical applications.** Focus on bounded high-value tasks with measurable outcomes, such as assistive communication, adaptive training, signal-quality support, or workload-aware interfaces.
2. **Publish interoperable data and adapter specifications.** Make acquisition, preprocessing, montage handling, labels, and consent assumptions inspectable.
3. **Pretrain across curated regimes, not indiscriminately.** Measure transfer and scaling under held-out subjects, sites, devices, and tasks.
4. **Develop reversible personalization.** Use parameter-efficient updates, calibration budgets, user feedback, and later-session validation.
5. **Standardize closed-loop and human-centered evaluation.** Include latency, error recovery, agency, privacy, and long-term outcomes alongside decoding quality.
6. **Scale only after evidence of reuse.** Increase model and dataset scale when it produces reliable transfer and lower user burden, not merely stronger in-distribution benchmarks.

BCI can mirror the durable lessons of the LLM era by becoming compositional, reusable, and evidence-driven. Its distinctive success criterion is more demanding: the system must make people more capable while staying honest about uncertainty, sensitive to privacy, and answerable to user control.

## References

- Bommasani, R., Hudson, D. A., Adeli, E., et al. (2021). On the opportunities and risks of foundation models. arXiv:2108.07258.
- Kostas, D., Aroca-Ouellette, S., and Rudzicz, F. (2021). BENDR: Using transformers and a contrastive self-supervised learning task to learn from massive amounts of EEG data. Frontiers in Human Neuroscience, 15, 653659.
- Millan, J. del R., Rupp, R., Muller-Putz, G. R., et al. (2010). Combining brain-computer interfaces and assistive technologies: State-of-the-art and challenges. Frontiers in Neuroscience, 4, 161.
- Yuste, R., Goering, S., Arcas, B. A. y., et al. (2017). Four ethical priorities for neurotechnologies and AI. Nature, 551, 159-163.
- Shneiderman, B. (2020). Human-centered artificial intelligence: Reliable, safe and trustworthy. International Journal of Human-Computer Interaction, 36(6), 495-504.