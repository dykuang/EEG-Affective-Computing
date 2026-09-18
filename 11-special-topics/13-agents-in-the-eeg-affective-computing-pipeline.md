# Agents in the EEG Affective-Computing Pipeline

An agent is a system that can observe a defined state, reason over an explicit
goal, call authorized tools, and record what it did. In EEG-based affective
computing, this makes agents useful as pipeline operators: they can inspect data
metadata, run approved quality checks, construct reproducible experiments, and
present evidence for review. It does not make them independent scientific
decision makers or reliable readers of unobserved mental content.

The appropriate goal is not to replace the researcher, clinician, or participant.
It is to turn a long, error-prone workflow into a traceable set of bounded tasks
with clear inputs, permissions, stopping conditions, and human approval points.

## Where Agents Fit

An affective-EEG pipeline has several natural agent interfaces:

| Pipeline stage | Useful agent work | Required control |
| --- | --- | --- |
| Acquisition | Check channel names, impedance or quality flags, timestamps, and missing-event rates | Never alter a recording or participant protocol without authorization |
| Preprocessing | Propose and run a declared filter, artifact, interpolation, and segmentation recipe | Fit statistics and artifact models within the training scope; preserve raw data |
| Dataset curation | Audit file manifests, label alignment, class balance, duplicate windows, and split overlap | A researcher approves exclusions and label corrections |
| Modeling | Launch approved baselines, track configurations, and compare planned ablations | Do not change the hypothesis, split, or compute budget silently |
| Evaluation | Aggregate fold-level metrics, calibration, error slices, and uncertainty intervals | Keep test labels and test-driven model selection inaccessible to the agent workflow |
| Deployment | Monitor signal quality, out-of-distribution indicators, latency, and user corrections | Restrict actions, provide abstention, and require confirmation for consequential changes |

An agent should communicate through typed artifacts rather than informal
instructions alone. A preprocessing task, for example, should name the input
recordings, montage, reference, filter parameters, artifact policy, output path,
and split scope. This prevents a seemingly reasonable request such as “clean the
EEG” from hiding choices that materially change an affective result.

## A Bounded Agent Loop

A robust agent loop separates observation, proposal, execution, and review:

1. **Observe.** Read immutable manifests, schema-validated metadata, prior run
   records, and authorized signal-quality summaries.
2. **Plan.** Produce a machine-readable plan containing the hypothesis, tools,
   inputs, expected outputs, cost limit, and failure conditions.
3. **Authorize.** Check that every proposed tool call is permitted by the current
   study protocol. Material changes require a human approval gate.
4. **Execute.** Run deterministic scripts with pinned versions, explicit seeds,
   and a new run identifier.
5. **Verify.** Check expected artifacts, data-split boundaries, metric validity,
   and whether a stop condition was reached.
6. **Report.** Attach logs, configurations, artifacts, and limitations to a
   concise evidence report. The researcher decides the interpretation and next
   action.

This loop is particularly valuable because affective EEG experiments contain many
small dependencies. A wrong event offset, a subject appearing on both sides of a
split, or a normalization statistic fit before cross-validation can create a
convincing but invalid result. Agents can test these invariants repeatedly, but
their checks must be specified and independently reviewable.

## Tool Interfaces and State

Give an agent narrow tools whose input and output types can be validated. A
minimal tool set might include:

- `inspect_dataset_manifest`: returns files, participant identifiers, channels,
  sampling rates, events, consent constraints, and checksum status.
- `run_quality_audit`: reports bad channels, missing events, artifact summaries,
  and coverage without overwriting source data.
- `build_split`: creates subject-, session-, or dataset-level partitions from a
  declared protocol and emits a split manifest.
- `run_experiment`: accepts an approved configuration and writes metrics,
  predictions, logs, and checkpoints under a run identifier.
- `compare_runs`: computes pre-specified paired comparisons and reports missing
  controls or incompatible configurations.
- `prepare_review_packet`: renders tables and figures directly from saved
  artifacts, including null and failed runs.

The agent state should be append-only where practical. A run record needs the
code revision, environment, data version, split manifest, configuration, random
seed, tool calls, outputs, and operator identity. Free-form conversation may
explain a decision, but it cannot substitute for these artifacts.

## Multi-Agent Roles

Several agents can divide work, but more agents do not automatically improve
reliability. Start with a single orchestrator and use specialized roles only when
their handoffs are explicit.


![Multi-agent EEG affective-computing pipeline. A researcher approves material decisions while the data steward, signal-quality, preprocessing, experiment, analysis, and runtime agents exchange versioned artifacts; rejected artifacts are returned to the responsible agent and escalated when necessary.](figures/multi-agent-eeg-pipeline.png)

*Figure 1. A multi-agent EEG affective-computing pipeline. Agents collaborate through versioned artifacts and declared contracts; the researcher retains authority over scientific, privacy, and consequential operational decisions.*

| Role | Responsibility | Must not decide |
| --- | --- | --- |
| Data steward | Access policy, provenance, retention, and manifest checks | Whether a participant or label is scientifically valid |
| Signal-quality agent | Deterministic quality and preprocessing audits | Which quality threshold is appropriate without a declared rule |
| Experiment agent | Approved training, ablations, and artifact capture | Test-set tuning or unapproved architecture changes |
| Analysis agent | Planned metrics, uncertainty, and error analysis | Causal, clinical, or psychological conclusions beyond the evidence |
| Runtime supervisor | Online quality, latency, abstention, and rollback | High-impact user actions outside its policy |

Every handoff should transfer a versioned artifact, not just a summary. For
example, the experiment agent receives a split manifest from the data steward and
returns prediction files and a frozen configuration to the analysis agent. This
makes it possible to trace a reported effect back to the exact data and code that
produced it.

### Blueprint: A Full Offline Pipeline

The following blueprint is a practical division of responsibility for a study
that trains and evaluates an emotion-recognition model. It uses a coordinator to
route work; the coordinator does not gain authority to change the study design.

| Agent | Inputs | Actions and outputs | Handoff and approval rule |
| --- | --- | --- | --- |
| Coordinator | Approved project card, agent registry, and prior artifacts | Assigns an approved task, tracks dependencies, enforces budgets and stop rules, and collects status | May route only validated artifacts; escalates blocked, conflicting, or high-impact requests to the researcher |
| Data steward | Raw-data inventory, consent and license constraints, label files | Creates an immutable dataset manifest with checksums, participant/session identifiers, access policy, and label provenance | Sends the manifest to the split and quality agents; blocks use of unconsented, incomplete, or untraceable data |
| Split auditor | Dataset manifest and declared generalization mission | Produces a subject-, session-, or dataset-level split manifest; tests for identity and window overlap | Researcher approves the split before preprocessing or modeling begins |
| Signal-quality agent | Read-only recordings, montage metadata, and declared quality rules | Reports missing channels, event integrity, artifact burden, sampling-rate conflicts, and rejected-recording candidates | Returns only a report and candidate exclusion list; a researcher approves exclusions or threshold changes |
| Preprocessing and feature agent | Approved split, recipe, and quality decisions | Fits fold-local transforms, filters and segments recordings, extracts features or tensors, and writes provenance for every output | Emits a preprocessing manifest to the experiment agent; must never fit using validation or test data |
| Experiment agent | Preprocessed training data, validation data, baseline specification, and run budget | Runs approved baselines and variants, saves configurations, seeds, checkpoints, predictions, logs, and failures | May select only with validation data; sends a frozen candidate and artifacts to the analysis agent |
| Analysis agent | Frozen predictions, metrics plan, and statistical plan | Computes planned metrics, calibration, uncertainty, error slices, and comparisons; drafts an evidence report from artifacts | Cannot rerun a search after seeing held-out test results; researcher approves interpretation and final test release |
| Runtime supervisor | Validated deployed model, live quality summaries, latency budget, and action policy | Detects degraded signal or distribution shift, applies approved abstention or rollback, and records user corrections | May take only low-risk policy actions; escalates adaptation, sensitive inference, and consequential actions |

This blueprint permits parallel work without allowing parallel assumptions. The
data steward and signal-quality agent can inspect their authorized inputs at the
same time, but preprocessing cannot start until the split and relevant quality
decisions are accepted. Similarly, the analysis agent may prepare code before a
run ends, but it must analyze the exact frozen artifacts delivered by the
experiment agent.

### Collaboration Contract

Each task should carry a small contract, stored with its artifacts:

```yaml
task_id: preprocess-fold-03
owner: preprocessing-feature-agent
approved_by: researcher-or-gate-id
inputs:
   dataset_manifest: manifests/dataset-v2.json
   split_manifest: splits/loso-fold-03.json
   quality_decision: reviews/quality-v2-fold-03.json
invariants:
   - no participant overlap across split partitions
   - fit transforms on training partition only
outputs:
   - artifacts/fold-03/preprocessing-manifest.json
   - artifacts/fold-03/features/
on_failure: quarantine-output-and-escalate
```

The receiver validates the contract before consuming an output: checksums match,
schemas are valid, required provenance fields are present, and the input versions
match the approved plan. A receiver must reject an artifact that fails validation;
it must not repair data, guess a missing parameter, or substitute a newer input
without recording a new task.


![Collaboration sequence for an offline affective-EEG study. Versioned manifests move from the data steward through split auditing, fold-local preprocessing, experimentation, and analysis. Approval gates lock the split and release the held-out test only after review; invalid artifacts return to their producer.](figures/multi-agent-collaboration-sequence.png)

*Figure 2. Collaboration contract for a multi-agent offline experiment. Every handoff is a validated, versioned artifact, and approval gates prevent later stages from changing the protocol retrospectively.*

## Error Handling and Recovery

An agentic pipeline needs a common failure protocol. A failure is not merely a
tool exception: it includes a violated scientific invariant, ambiguous input,
policy conflict, resource limit, or unreliable model output. Agents should fail
closed for data access, test-set isolation, and user-facing actions. They should
preserve diagnostic information while preventing the failed output from becoming
an input to the next stage.

| Failure class | Example | Detecting agent | Immediate action | Recovery and collaboration |
| --- | --- | --- | --- | --- |
| Schema or provenance failure | Recording lacks channel map; manifest checksum changes | Data steward or receiver | Quarantine the artifact and mark the task failed | Producer repairs the source or manifest; steward reissues a new version |
| Split leakage | A participant, trial, or overlapping window occurs in two partitions | Split auditor or preprocessing agent | Stop downstream jobs and invalidate derived artifacts | Researcher approves a corrected split; preprocessing and experiments restart from that split |
| Signal-quality failure | Event stream is shifted or too many channels are unusable | Signal-quality agent | Produce a report, not a silent repair | Researcher accepts a predeclared fallback, revises the rule, or excludes the recording with justification |
| Tool or compute failure | Training job crashes, disk fills, or a timeout is reached | Experiment agent or coordinator | Save logs and partial state; do not report partial metrics as results | Retry only within the approved policy and budget; otherwise escalate with the failure record |
| Conflicting recommendations | Quality and modeling agents propose incompatible resampling rules | Coordinator | Block both dependent tasks | Present the conflict, evidence, affected artifacts, and alternatives to the researcher |
| Evaluation-policy violation | Agent requests another search after test metrics appear | Analysis agent or coordinator | Deny the request and preserve the audit event | Researcher may authorize a separately labeled exploratory analysis; it cannot overwrite the locked result |
| Online safety failure | Low confidence, sensor loss, or an action outside policy | Runtime supervisor | Abstain, retain the prior safe state, and notify the user when appropriate | Request recalibration or human review; roll back adaptation and log the event |

Retries must be idempotent: repeating a task with the same approved inputs must
either reproduce the same artifact or create a clearly identified stochastic run
with a new seed. The coordinator should use bounded retries, exponential backoff
for transient infrastructure errors, and a circuit breaker that pauses repeated
failures. These mechanisms address infrastructure reliability; they do not turn a
scientific ambiguity into an automatically solvable error.


![Error-handling flow for a multi-agent EEG pipeline. Each artifact or runtime event passes provenance, scientific-invariant, policy, and safety checks. Failures are quarantined or abstained from, recorded immutably, routed to the responsible agent, and escalated to the researcher when a decision is required.](figures/multi-agent-error-handling.png)

*Figure 3. Error handling in a multi-agent EEG pipeline. The system preserves evidence and stops unsafe or invalid work before it can contaminate downstream artifacts or user-facing actions.*

## Online Affective BCI Use

In an online system, agents can help coordinate sensing, decoding, context, and
interaction. The decoder should produce a bounded estimate such as an affective
dimension, confidence, quality score, and out-of-distribution flag. An agent may
then select a low-risk response from an approved policy: request recalibration,
offer a break, reduce interface complexity, or abstain.

Do not permit a language-capable agent to turn uncertain EEG estimates into
unbounded claims about a person's feelings, intentions, health, or preferences.
The system must distinguish sensor observations, model outputs, participant
feedback, and agent proposals. It should make uncertainty visible and allow the
participant to correct or decline an inferred state.

For adaptive systems, update policies need a rollback path. Store the prior model
state, record the feedback used for adaptation, delay irreversible changes, and
evaluate updates on later data rather than only on the calibration segment that
triggered them.

## Evaluation of Agentic Pipelines

Evaluate both the affect model and the agent workflow. Offline classification
accuracy cannot show whether an agent improved a study or deployment.

| Question | Example measure |
| --- | --- |
| Does the agent preserve scientific validity? | Detected leakage, split violations, and configuration mismatches in seeded audits |
| Does it improve reproducibility? | Fraction of runs replayed from their manifests; artifact completeness |
| Does it use resources responsibly? | Compute spent per accepted experiment; duplicate-run rate; stop-rule adherence |
| Does it improve operational reliability? | Quality-failure detection, abstention precision, latency, and rollback success |
| Does it respect people? | Confirmation rate, correction burden, consent compliance, and privacy-audit findings |

Test adversarial and mundane failure cases: missing channels, corrupted events,
ambiguous instructions, stale metadata, conflicting tool outputs, unauthorized
data paths, failed jobs, and a request to tune after test results are visible. A
useful agent stops, reports the ambiguity, and asks for authorization rather than
quietly filling in the gap.

## Practical Starting Point

1. Choose one reversible task, such as generating a dataset manifest or checking
   a declared subject-level split.
2. Define typed inputs, allowed directories and commands, expected artifacts,
   and failure conditions.
3. Require a run manifest and append-only experiment ledger for each execution.
4. Add a human approval gate before data modification, large compute use,
   test-set evaluation, adaptation, publication, or user-facing action.
5. Measure the agent against the same reproducibility, leakage, privacy, and
   user-control criteria applied to the rest of the system.

The AI Collaboration Protocol in the preceding section provides an approval and
evidence framework for this workflow. Agent-oriented EEG systems, including
recent work such as [EEGAgent](https://dl.acm.org/doi/abs/10.1609/aaai.v40i21.38867),
motivate studying this pipeline layer explicitly. Their value must be established
through controlled, auditable evaluations rather than inferred from autonomous
behavior or fluent explanations.

## Further Reading

- EEGAgent. *Proceedings of the Fortieth AAAI Conference on Artificial
  Intelligence and Thirty-Eighth Conference on Innovative Applications of
  Artificial Intelligence and Sixteenth Symposium on Educational Advances in
  Artificial Intelligence*. [DOI: 10.1609/aaai.v40i21.38867](https://dl.acm.org/doi/abs/10.1609/aaai.v40i21.38867)
- The AI Collaboration Protocol in this chapter for approval gates, experiment
  ledgers, and claim-to-evidence records.