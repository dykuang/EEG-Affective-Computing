# Agents for Idea Exploration and Algorithm Development

Before an EEG affective-computing study crystalizes into a formal hypothesis,
the research process is inherently fluid and uncertain. A researcher often begins
not with a clean mathematical objective, but with an intriguing observation, a
persistent practical bottleneck, an unexpected anomaly in a spectrogram, or a
curiosity about whether an emerging deep learning paradigm might unlock subtle
neural dynamics. In this early phase, directions are ill-defined, promising
insights frequently arise from seemingly unrelated tangents or recycled failures,
and the space of possible algorithms is vast.

The purpose of integrating autonomous and semi-autonomous AI agents into this
exploratory phase is not to replace human scientific intuition, nor is it to
unleash an unguided generator of fashionable architectures. Rather, it is to
provide the researcher with an active cognitive partner—a system capable of
organizing uncertainty, formulating competing explanations, executing cheap
diagnostic probes, and faithfully recording both positive and negative evidence.
When designed with care, agentic exploration transforms what is often an ad-hoc,
forgetful trial-and-error process into an auditable, compounding journey of
discovery.

## Navigating the Fog: From Vague Intuition to Testable Direction

Exploratory algorithm development in affective neuroscience is uniquely
challenging. Scalp EEG recordings exhibit notoriously low signal-to-noise
ratios, pronounced non-stationarity across sessions, and substantial individual
variability. Furthermore, emotional ground truth is inherently indirect and
frequently noisy. When an initial model performs poorly, the root cause is rarely
obvious: is it an inadequate neural representation, temporal misalignment of
stimulus and response, label ambiguity, or subtle artifact contamination?

When researchers begin with an open-ended brief—such as "cross-subject transfer
collapses on valence," "high-arousal states are confused with muscular artifacts,"
or "can continuous geometric manifolds better capture transitional affect?"—an
agent should not rush to generate complex models. Instead, its primary value
lies in constructing an **Uncertainty Map**. The uncertainty map unpacks a vague
hunch into its underlying components: separating what has been directly observed
from what is merely assumed, listing rival mechanisms, and identifying the
smallest empirical test that could distinguish between them.

| Dimension | Guiding question for exploration | Early empirical evidence | Trap to avoid |
| --- | --- | --- | --- |
| Observation | What exact phenomenon occurred, under what montage and protocol? | Inspect raw spectrograms, trial logs, confusion matrices, and baseline error distributions | Mistaking a dataset-specific artifact or session drift for a universal property of neural affect |
| Candidate mechanism | What physiological or computational factors could explain this? | Prior neuroscience literature, synthetic signal checks, and competing hypothesis trees | Prematurely crowning the first plausible explanation before considering simpler confounders |
| Discriminating probe | What is the cheapest, most decisive intervention to test this? | Focused feature ablation, synthetic noise injection, or channel-masking pilot | Launching a multi-day training run when a five-minute diagnostic script would suffice |
| Evaluation mission | Under which generalization setting must this idea prove its worth? | Subject-independent (LOSO), cross-session, or cross-dataset validation splits | Optimizing on a random window-level split that leaks subject identity and masks failures |
| Decision threshold | What concrete evidence justifies promoting, parking, or retiring the idea? | Predeclared margin over baseline, calibration gain, or robust failure modes | Moving the goalposts after inspecting test metrics to declare an ambiguous run a success |


![Agent-supported idea exploration for affective EEG. Observations, practical needs, literature gaps, unexpected results, and recycled attempts pass through an uncertainty map and cheap probes before the researcher promotes a direction to a formal study, parks it, or retires it with evidence.](figures/agentic-idea-exploration-funnel.png)

*Figure 1. Exploration as an evidence funnel. Rather than an unconstrained random search, agents help the researcher navigate ambiguity by maintaining an uncertainty map, testing rival explanations with inexpensive probes, and recording explicit reasons for promoting, parking, or retiring every candidate.*

## The Exploration Workshop: Complementary Cognitive Roles

In an effective research group, creative progress thrives on cognitive diversity:
some researchers propose bold, unconventional connections, while others maintain
rigorous skepticism, build rapid prototypes, or safeguard the historical record.
When designing an agentic exploration workflow, you can emulate this collaborative
tension by establishing specialized agent roles with complementary perspectives
and explicit boundaries.

Rather than thinking of these as separate, heavy software systems, it is more
fruitful to view them as **distinct cognitive stances** that an individual
researcher or student can summon—either through distinct agents in a multi-agent
framework or through structured system prompts in an interactive LLM session.
Dividing the exploratory workflow into discrete functional stances prevents
premature consensus, curbs confirmation bias, and enforces critical cross-examination:

| Cognitive Stance | Driving Question & Inner Stance | Value to the Exploration Loop | What Goes Wrong Without It |
| --- | --- | --- | --- |
| Exploration Coordinator | *"What are our active questions, budgets, and dependencies?"* | Keeps the portfolio organized, tracks GPU hours, and prevents random rabbit holes | Exploration becomes scattered, unrecorded, and prone to endless drift |
| Problem Framer | *"What is directly observed versus merely assumed?"* | Deconstructs vague hunches into concrete uncertainty maps and rival mechanisms | The team rushes to build complex models for poorly understood symptoms |
| Literature & Evidence Scout | *"What physiological priors and empirical baselines exist?"* | Ground-truths ideas in verified neuroscience and prior art with exact citations | Agents or researchers hallucinate baselines or reinvent known techniques |
| Divergent Ideator | *"What non-obvious analogy or representation could apply?"* | Proposes creative, cross-disciplinary connections and non-standard baselines | Exploration gets trapped in incremental, obvious hyperparameter tweaks |
| Adversarial Skeptic | *"What is the simplest confound that could fake this result?"* | Searches relentlessly for leakage, ocular shortcuts, and trivial baseline explanations | The lab celebrates phantom breakthroughs caused by EOG artifacts or split leakage |
| Rapid Prototyper | *"What is the 10-line script that can test this today?"* | Builds lightweight, self-contained diagnostic probes and fast micro-benchmarks | Weeks are wasted on full multi-GPU training before verifying basic sanity |
| Memory Curator | *"Have we seen this failure mode or pattern before?"* | Protects the lab from cyclical amnesia by organizing static and dynamic evidence | Researchers repeatedly stumble into the same forgotten dead ends |
| Human Guide | *"Is this question scientifically meaningful and ethically sound?"* | Steers vision, judges conceptual plausibility, and approves formal study promotion | AI optimizes for artificial benchmark numbers detached from scientific reality |

By pitting the Divergent Ideator against the Adversarial Skeptic, you create an
automated peer-review dialogue. Before you spend hours training a deep model,
the skeptic forces the ideator to defend why an apparent gain is not merely an
ocular artifact, a temporal autocorrelation shortcut, or a quirk of a single
cross-validation fold.

## Cultivating the Graveyard: Failed Experiments as Intellectual Capital

In the search for novel affective computing models, the vast majority of initial
attempts do not yield immediate benchmark improvements. In a field governed by
low SNR and complex biology, negative outcomes are the norm rather than the
exception. In traditional workflows, these failed attempts are often discarded
and forgotten—leading future researchers to unknowingly repeat the same dead ends.

In an agent-assisted laboratory, however, **a negative result is treated as an
epistemic constraint**. An algorithm that fails to improve average classification
accuracy may nonetheless reveal essential structural properties: it may exhibit
exceptional calibration under high uncertainty, demonstrate resilience against
electrode impedance degradation, or fail exclusively on a distinct demographic
subgroup, exposing an unmodeled physiological covariate.

To harness this intellectual capital, the exploration team maintains a candidate
registry where every idea resides in an explicit, tracked lifecycle state:

| State | Epistemic meaning | Guiding criterion | Permitted next action |
| --- | --- | --- | --- |
| Seed | A nascent observation, curiosity, or analogy lacking empirical backing | Plausible connection to affective neuroscience or representation learning | Query literature, articulate assumptions, and construct uncertainty map |
| Probe | An active candidate undergoing a low-cost, discriminating feasibility test | Falsifiable prediction testable with minimal computational cost | Execute micro-benchmark or targeted diagnostic on training/validation splits |
| Promising | A candidate whose core mechanism survived diagnostic probes and rival critiques | Consistent improvement or compelling qualitative insight on validation data | Draft formal research charter, lock controls, and prepare full pipeline |
| Recycle | A previously shelved idea whose underlying mechanism answers a newly emerged question | Changed operational context, updated dataset version, or revised task framing | Retrieve historical run artifacts, verify changed assumptions, and re-probe |
| Parked | A conceptually sound idea currently deferred due to resource limits or missing prerequisites | Clear potential value contingent on future data, tooling, or theoretical clarity | Document re-entry triggers and preserve state in portfolio memory |
| Retired | A candidate decisively refuted by empirical evidence, leakage audits, or ethical constraints | Irreparable theoretical flaw, insurmountable confounder, or persistent failure | Archive negative evidence, update anti-patterns, and block repetitive retrials |

### The Art of Recycling: Transforming Past Dead Ends into New Hypotheses

Serendipity in scientific research often occurs when a method developed for one
purpose unexpectedly solves another. In EEG affective computing, an attention
mechanism that failed to improve temporal emotion classification may prove to be
an outstanding detector of ocular artifact bursts. Similarly, a Riemannian
manifold representation that proved too computationally heavy for real-time
inference might provide the exact geometric inductive bias needed for zero-shot
cross-subject alignment.

When recycling past work, agents must maintain strict intellectual provenance.
The Memory Curator retrieves the original run's exact configuration, software
environment, random seeds, and performance profiles. It then generates an explicit
**Delta Note**: identifying exactly which assumption has changed, why the
previous failure mode is benign under the new problem formulation, and what
new controls are required. This ensures that serendipitous connections are
grounded in verifiable evidence rather than wishful thinking.

## Memory as Living Research Infrastructure

Human memory is remarkably flexible, but it suffers from cognitive fatigue,
confirmation bias, and recency effects. A standard conversational AI buffer,
on the other hand, is prone to context drift, catastrophic forgetting, and
sycophancy. To support rigorous long-term exploration, an agentic research system
requires a structured, multi-tiered memory architecture that mirrors the
distinction between enduring scientific facts and ephemeral working thoughts.

<!-- ```
+-------------------------------------------------------------------------+
|                          HUMAN RESEARCHER                               |
|        (Sets vision, evaluates meaning, approves formal studies)        |
+------------------------------------+------------------------------------+
                                     |
                                     v
+-------------------------------------------------------------------------+
|                     DYNAMIC WORKING & PORTFOLIO MEMORY                  |
|    Active tasks, evolving hypotheses, probe logs, candidate registry     |
+------------------------------------+------------------------------------+
                   |                 |                 ^
                   | write raw       | retrieve        | consolidated
                   | observations    | context         | knowledge
                   v                 |                 |
+------------------------------------+-----------------+------------------+
|                      DYNAMIC EPISODIC RUN MEMORY                        |
|   Append-only ledger: runs, seeds, metrics, failed scripts, diagnostic   |
|   artifacts, error traces, and negative evidence                        |
+------------------------------------+------------------------------------+
                                     |
                    curator review & | formal validation
                    human approval   |
                                     v
+-------------------------------------------------------------------------+
|                          STATIC KNOWLEDGE BASE                          |
|   Versioned dataset cards, locked split manifests, mathematical lemmas,  |
|   verified literature baselines, and peer-reviewed laboratory canons    |
+------------------------------------+------------------------------------+
                                     |
                                     v
+-------------------------------------------------------------------------+
|                        RESTRICTED DATA STORE                            |
|   Access-controlled raw biosignals, participant consent, and identity   |
+-------------------------------------------------------------------------+
``` -->

![Multi-tiered research memory hierarchy for agentic EEG exploration. Epistemic authority flows downward: the human researcher governs vision and approvals; dynamic working and portfolio memory orchestrates transient hypotheses and probe queues; append-only episodic memory logs raw execution traces and negative evidence; peer-reviewed findings are consolidated into the immutable static knowledge base; and sensitive participant biosignals are sequestered in an access-controlled restricted vault.](figures/agentic-memory-hierarchy.png)

*Figure 2. Multi-tiered research memory hierarchy for agentic EEG exploration. Epistemic authority and data flow are explicitly decoupled: the human researcher directs scientific vision, ethical boundaries, and formal promotion decisions; dynamic working memory coordinates active exploration tasks and candidate hypothesis queues; append-only episodic memory captures raw execution artifacts, random seeds, and negative evidence; formal consolidation promotes replicated findings into the versioned static knowledge base; and sensitive participant biosignals are isolated within an access-controlled vault.*

### Static versus Dynamic Memory Stores

The foundation of reliable research memory rests on a principle of cognitive hygiene:
**never allow your working scratchpad to silently overwrite your laboratory's ground truth.**

When researchers explore without structured boundaries, self-deception creeps in:
an agent might treat an unverified validation spike from yesterday's exploratory script
as an established baseline, or subtly alter a dataset's declared channel count to match
a buggy data-loading function. To prevent this, we divide research memory into two
fundamentally different spheres:

- **Static Memory is your scientific anchor.** It preserves what your laboratory
  has rigorously established: curated dataset cards, verified 10-20 electrode
  coordinates, standard reference montages, mathematical lemmas, and frozen baseline
  results. When an agent plans an experiment, it reads from static memory to know
  what is already certain. This store changes only through deliberate, peer-reviewed
  consolidation—never as an incidental side effect of an active run.
- **Dynamic Memory is your active workbench.** It holds the fluid, rapidly evolving
  reality of everyday exploration: half-formed hunches, terminal outputs, diagnostic
  confusion matrices, failed prototype runs, and active task queues. Dynamic memory
  is designed to fluctuate, be pruned, and occasionally be discarded as understanding
  deepens.

| Memory Sphere | Natural Analogy | Storage Medium | Write Policy | Retrieval & Safety Rule |
| --- | --- | --- | --- | --- |
| Static Knowledge Base | The Lab Archive | Versioned Markdown/YAML files & dataset cards | Append-only; requires researcher review | Available to all agents; strictly filtered by protocol version |
| Dynamic Working Memory | The Whiteboard | In-memory task state or scratchpad | Updated as immediate task progresses | Scoped strictly to the active probe and immediate parent task |
| Dynamic Episodic Store | The Flight Recorder | Append-only run ledger linked to code commits | Automatic event logging on every probe | Queried by run ID, error trace, random seed, or candidate |
| Dynamic Portfolio Memory | The Strategy Board | Structured registry with state tags (Seed, Probe, etc.) | Updated upon gate reviews or probe completions | Queried by research question, target affect dimension, or mechanism |
| Restricted Data Vault | The Secure Safe | Access-controlled storage for sensitive biosignals | Authorized acquisition tools only | Enforces participant consent, privacy rules, and access logs |

Under no circumstances should a dynamic observation silently graduate into static
memory. When an empirical finding consistently replicates across multiple seeds,
subjects, and adversarial probes, it undergoes formal **Consolidation**: the
Memory Curator synthesizes the accumulated evidence into a review dossier, and
the human researcher verifies the paper trail before welcoming the insight into
the laboratory's permanent canon.

### Anatomy of a Verifiable Memory Object

For research memories to remain interpretable across weeks of exploration, they
must be structured as self-contained, typed records. A memory is not an informal
prose snippet; it is an evidentiary claim coupled with its operational context:

```yaml
memory_id: mem-probe-2026-09-18-042
kind: empirical-probe-result
store: dynamic-episodic
status: provisional
scope:
  dataset_id: SEED-IV
  data_version: v2.1-clean
  montage: standard-10-20-62ch
  split_protocol: LOSO-fold-03
  evaluation_task: 4-class-discrete-emotion
claim_summary: >
  Dynamic channel dropout along frontal sites (Fp1, Fp2, Fz) during pretraining
  reduces validation confusion between fear and disgust by 14% without degrading
  neutral classification.
evidence:
  run_manifest_hash: 7f8a9b2c3d4e5f6a
  artifact_paths:
    - artifacts/probes/dropout-042/confusion_matrix.png
    - artifacts/probes/dropout-042/per_class_f1.json
  script_commit: 89ee65b
  random_seed: 42
confidence_score: 0.72
rival_interpretations:
  - Frontal dropout acts as a regularizer against residual electrooculographic (EOG) artifacts.
  - The observed improvement is a stochastic outlier specific to fold 03.
valid_until: 2026-10-31T23:59:59Z
access_class: internal-team
supersedes: mem-probe-2026-09-10-019
```

Notice that this object explicitly records the evaluation scope, random seeds,
artifact paths, and rival explanations. If fold 03 is later discovered to have
contained subtle ocular leakage, all memories carrying this split hash can be
immediately identified, invalidated, and quarantined.

### Contextual Retrieval: Beyond Naive Semantic Similarity

In traditional conversational LLMs, retrieval relies almost entirely on dense
vector embedding similarity. In scientific algorithm exploration, however, naive
semantic retrieval is treacherous.

Imagine you ask an exploration assistant: *"How should we regularize spatial
feature covariance for cross-subject emotion transfer on SEED-IV?"* If the
system relies strictly on semantic similarity, it might retrieve a highly cited
paper claiming 96% accuracy that secretly evaluated on an unverified random
window split, or an algorithm developed for 128-channel motor imagery that makes
assumptions inapplicable to your 62-channel montage. In literature and code
search, semantic eloquence easily masquerades as scientific relevance.

To protect against this, agentic research systems use a **two-stage contextual sieve**:

1. **Hard Categorical Filtering:** Before comparing text embeddings, the engine
   applies non-negotiable boolean filters. Does the memory match the required
   dataset family? Does it respect the target split protocol (e.g., Leave-One-Subject-Out)?
   Does it share an aligned electrode montage? If not, it is filtered out
   regardless of how superficially relevant its wording sounds.
2. **Multi-Factor Evidentiary Ranking:** Surviving candidate memories are ranked
   not by keyword density, but by scientific reliability:

$$
S(m, q) = w_e E(m) + w_s S_{\text{scope}}(m, q) + w_r R(m) + w_c C(m) - w_x X(m),
$$

This equation formalizes standard scientific common sense:
- **Evidence Quality $E(m) \in [0, 1]$:** How thoroughly tested is the finding?
  A probe run replicated across 10 random seeds with proper baseline controls
  heavily outranks an anecdotal, single-seed spike.
- **Scope Alignment $S_{\text{scope}}(m, q) \in [0, 1]$:** Do the underlying
  neuroscience assumptions match? An empirical finding using differential entropy
  on SEED-IV receives high alignment; a study on visual ERPs receives low alignment.
- **Temporal Recency $R(m) = \exp(-\lambda \Delta t)$:** Prioritizes insights
  from your current codebase iteration over experiments run months ago under
  deprecated pre-processing scripts.
- **Review Confidence $C(m) \in [0, 1]$:** Rewards memories that have undergone
  human review and formal validation.
- **Contradiction Penalty $X(m) \in [0, 1]$:** Heavily penalizes candidates that
  have been challenged by subsequent critiques, superseded by cleaner reruns, or
  flagged for subtle artifact leakage.

Under this objective, a two-day-old negative result from your own laboratory with
verified subject isolation will appropriately outrank an unverified, overly
optimistic claim from an external paper.

![Memory architecture for EEG research agents. A versioned static store is separate from dynamic working, episodic, and portfolio stores, while restricted data is protected by access control. Validation, retrieval filters, reviewed consolidation, invalidation, and expiry preserve provenance throughout the lifecycle.](figures/agentic-research-memory-layers.png)

*Figure 3. Static and dynamic memory architecture for exploratory algorithm research. The system enforces an epistemic boundary: raw episodic run events and evolving working thoughts reside in dynamic storage, while only peer-reviewed, evidenced findings are consolidated into the immutable static knowledge base.*

## The Memory Lifecycle: From Ephemeral Insight to Validated Knowledge

Research memory is not a static repository where notes are dumped and forgotten;
it is a dynamic circulatory system. As algorithmic ideas are conceived, tested,
refined, or refuted, the memory objects that represent them undergo a continuous,
principled lifecycle across six distinct stages:

<!-- Diagram-generation prompt: Create a clean academic state and lifecycle flowchart titled "Lifecycle of research memory in agentic EEG exploration." Show six sequential stages: 1. Ingestion & Provenance Tagging, 2. Invariant Validation (branching to Quarantine on failure), 3. Scoped Indexing & Retrieval, 4. Active Utilization in Exploration, 5. Human Review & Consolidation (promoting to Static Knowledge Base), and 6. Cascade Invalidation & Retirement. Use clear directional flow arrows, decision diamonds for validation and replication checks, and an alert node for invalidation propagation. Retrained palette (blue for standard flow, teal for validated states, amber for human review, red for quarantine/invalidation, and gray for retirement) on a white background with sharp, legible academic typography; no decorative icons or gradients. -->

![Lifecycle of research memory in agentic EEG exploration. Episodic events and probe results are ingested with provenance, validated against scientific invariants, filtered and retrieved for active reasoning, consolidated into static knowledge through human review, or retired when superseded.](figures/agentic-memory-lifecycle.png)

*Figure 4. Six-stage lifecycle of research memory in exploratory EEG algorithm development. From initial ingestion and provenance logging through automated invariant audits (with fail-closed quarantine), scoped indexing, active utilization, and peer-reviewed consolidation into the static knowledge base, or cascade invalidation and graceful retirement when an idea is superseded.*

1. **Ingestion: Catching the Spark Without Losing Context.**
   When an agent runs a quick two-minute channel-correlation probe or plots an
   unexpected loss curve, that observation easily vanishes into a scrolling terminal
   buffer. In an agentic workflow, every exploratory action automatically records
   an immutable digital paper trail: the git commit, the random seed, the dataset
   hash, and the code version are stamped onto the observation before it is even
   discussed.
2. **Invariant Validation: The Automated Scientific Conscience.**
   In the excitement of discovering a model that achieves 92% cross-subject
   accuracy, human researchers easily overlook subtle bugs—such as a z-score
   normalizer fit across the entire recording before windowing, or a subject's
   windows leaking into both train and test partitions. Before an observation is
   indexed as an active finding, automated invariant checkers audit the underlying
   data pipeline. If subject boundaries were crossed, the finding is not celebrated;
   it is quarantined with an explicit diagnostic trace, saving the researcher weeks
   of chasing a phantom breakthrough.
3. **Scoped Indexing: Filtering by Experimental Reality.**
   Once validated, memories are not dumped into an undifferentiated vector store.
   They are indexed by their operational context: task type, channel montage,
   feature extraction parameters, and evaluation mission. When an agent later seeks
   inspiration, these categorical facets prevent cross-paradigm contamination.
4. **Active Utilization & Cross-Examination: Putting Memories on Trial.**
   During brainstorming and diagnostic loops, relevant memories are surfaced to
   guide model tweaks or warn against documented pitfalls. Crucially, the
   Adversarial Skeptic actively cross-examines new observations against past
   memories: *"You observed a 5% gain from frontal attention, but Memory #042
   showed that frontal sites in this subject had excessive blink power. Are we
   learning affect, or are we learning blink rates?"*
5. **Human-in-the-Loop Consolidation: Promoting Insight to Canon.**
   When an exploratory finding demonstrates consistent value across multiple seeds,
   distinct subjects, and adversarial probes, it earns promotion. The Memory
   Curator compiles an evidentiary dossier summarizing the replication trail. The
   human researcher reviews the evidence and formally approves its graduation into
   the static knowledge base as an established laboratory canon.
6. **Cascade Invalidation & Graceful Retirement: Learning When We Were Wrong.**
   Science progresses by disproving hypotheses. When a finding is refuted—or when
   a dataset provider issues a retraction—the system executes an audited
   invalidation sweep. Dependent working summaries are flagged, derived embedding
   caches are purged, and the memory transitions into a retired archive. It
   remains searchable as a historical lesson, ensuring the laboratory never
   repeats the debunked rationale.

## Guiding Heuristics for the Reader's Laboratory

For researchers, students, and engineers seeking to establish agent-assisted
exploration pipelines in their own neurotechnology projects, the following
principles provide a foundation for creative yet rigorous discovery:

### 1. Probe Cheaply Before Training Deeply

Resist the temptation to deploy large-scale architectures at the first sign of a
new idea. Instruct your exploration agents to design **micro-probes**: minimal,
inexpensive diagnostic experiments that directly interrogate the hypothesized
mechanism. If a novel spatial attention mechanism is hypothesized to capture
bilateral asymmetry, test whether it correctly distinguishes synthetic hemispheric
power differentials before training it on the full SEED or DEAP corpus.

### 2. Never Allow an Agent to Fall in Love with a Hypothesis

Language models are inherently cooperative and prone to confirmation bias; when
asked to explore an idea, they will naturally elaborate on why it could succeed.
Always pair an ideation prompt with an explicit requirement for falsification:
task the Adversarial Critique Agent with articulating at least two competing
explanations (such as muscle artifact shortcuts or temporal autocorrelation)
and proposing a control experiment designed specifically to dislodge the
preferred hypothesis.

### 3. Treat the Experiment Graveyard as an Asset

Do not erase failed prototype scripts or discard inconclusive notebooks.
Encourage your agents to document the precise conditions under which an algorithm
degraded. Building a rich episodic index of negative results protects the
laboratory from cyclical amnesia and provides the exact contrastive evidence
needed to recognize genuine breakthroughs.

### 4. Guard Invariants with Hard Code, Not Soft Prompts

Prompting an agent to "remember not to leak subject data" is an invitation to
subtle methodological errors. Enforce scientific invariants—such as subject-level
split isolation, fold-local normalizer fitting, and feature extraction boundaries—using
deterministic code assertions, schema validators, and read-only file systems.
Let the agents operate with freedom inside the sandbox, but ensure the sandbox
walls are mathematically rigid.

### 5. Automate the Mechanics of Exploration, but Cherish Human Meaning

Agents excel at scanning vast parameter landscapes, cross-referencing literature
corpora, parsing complex signal logs, and verifying syntactic boilerplate.
However, deciding which research questions are fundamentally worth asking,
interpreting whether an empirical correlation possesses genuine physiological
plausibility, and evaluating the ethical consequences of affective monitoring
remain deeply human responsibilities. By delegating the mechanics of exploration
to disciplined agent workflows, researchers free their cognitive capacity to
focus on what matters most: scientific insight, creative synthesis, and the
responsible advancement of neurotechnology.

## References and Related Reading

- EEGAgent. *Proceedings of the Fortieth AAAI Conference on Artificial
  Intelligence and Thirty-Eighth Conference on Innovative Applications of
  Artificial Intelligence and Sixteenth Symposium on Educational Advances in
  Artificial Intelligence*. [DOI: 10.1609/aaai.v40i21.38867](https://dl.acm.org/doi/abs/10.1609/aaai.v40i21.38867)
- [11.12 AI Collaboration Protocol](11-special-topics/12-AI-collaboration-protocol.md) for
  formal approval gates, experiment ledgers, and claim-to-evidence records.
- [11.13 Agents in the EEG Affective-Computing Pipeline](11-special-topics/13-agents-in-the-eeg-affective-computing-pipeline.md) for
  the end-to-end multi-agent execution blueprint from acquisition through deployment.
- [A.2 Exploration Harness Template](Appendix/02-exploration-harness-template.md) for
  runnable scaffolding designed to run bounded, hypothesis-driven probes.