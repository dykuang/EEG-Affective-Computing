# AI Collaboration Protocol

This protocol turns an AI-assisted research idea into an auditable sequence of
decisions, experiments, and claims. It is intended to complement the research
routes checklist: use the checklist to formulate a study and use this protocol
to govern how a researcher and AI agent carry it out.

## Operating Principles

- **Human ownership of scientific judgment.** The researcher approves the
  research question, data use, evaluation protocol, material design changes,
  conclusions, external communication, and destructive operations.
- **Bounded AI execution.** The agent may inspect code and data metadata,
  implement approved experiments, run approved commands, summarize evidence, and
  report uncertainty. It must distinguish observations from inferences and never
  invent citations, results, or completed experiments.
- **Evidence before claims.** A claim requires a linked run, immutable
  configuration, saved outputs, and the pre-specified statistical analysis.
- **Fair comparisons.** Variants use identical data splits, preprocessing,
  training budget, evaluation code, and reporting rules unless the approved
  design explicitly changes one of them.
- **No silent optimization.** Model selection uses training and validation data.
  Test-set results are reserved for the approved final evaluation.

## Project Card

Create one copy of this card for every study. Replace bracketed text with
project-specific content; mark unknown items as `TBD` rather than guessing.

### Research Question and Motivation

- **Question:** [A falsifiable question with population, input, task, and outcome.]
- **Motivation:** [Why the problem matters scientifically or practically.]
- **Primary hypothesis:** [Direction and expected mechanism, if applicable.]
- **Scope and non-goals:** [What the study will not establish.]
- **Success criterion:** [The minimum practically meaningful result, not merely a
  better point estimate.]

### Prior Evidence and Research Gap

For each influential paper or benchmark, record the citation, task, data split,
main result, limitations, and exact gap addressed here. The agent may prepare the
table, but the researcher verifies citations and interpretations.

| Source | Relevant finding | Limitation or gap | Consequence for this study |
| --- | --- | --- | --- |
| [Citation] | [Finding] | [Gap] | [Design decision] |

### Data and Governance

- **Datasets:** [Name, version, source, access date, license or use constraints.]
- **Population and labels:** [Participants, label definition, class balance, and
  known label uncertainty.]
- **Unit of analysis:** [Participant, trial, window, session, or recording.]
- **Split protocol:** [Subject-dependent, LOSO, cross-session, cross-dataset, or
  other protocol; include random seeds and leakage controls.]
- **Preprocessing:** [Exact ordered operations, fitted statistics, and whether
  each operation is fit within each training fold.]
- **Exclusions and missing data:** [Rules fixed before result inspection.]
- **Risks:** [Privacy, consent, demographic representation, misuse, and dataset
  limitations.]

### Experimental Design

- **Input and prediction target:** [Representation, shape, sampling rate, target.]
- **Baseline:** [A credible published or simple in-repository baseline, with its
  original or justified training recipe.]
- **Proposed method:** [One-sentence change and its hypothesized benefit.]
- **Controlled factors:** [Items held constant across compared methods.]
- **Tunable choices:** [Search space, search budget, selection metric, and
  validation-only decision rule.]
- **Compute budget:** [Hardware, maximum runs, maximum runtime, and stop rule.]
- **Required ablations:** [Each component removed or substituted, and the question
  each ablation answers.]

### Metrics, Uncertainty, and Statistical Plan

- **Primary metric:** [Metric, aggregation level, and justification.]
- **Secondary metrics:** [Calibration, per-class performance, efficiency, or other
  decision-relevant measures.]
- **Reporting unit:** [Fold, participant, session, or independent run.]
- **Uncertainty:** [Confidence interval method and resampling unit.]
- **Statistical test:** [Test, null hypothesis, pairing structure, alpha, multiple
  comparison correction, and effect size.]
- **Decision rule:** [What counts as support, no evidence of improvement, or an
  inconclusive result.]
- **Desired outcome:** [Target effect size and practical interpretation.]

### Open Issues

List assumptions, threats to validity, failed approaches, and follow-up questions.
Do not convert an unresolved issue into a positive claim.

## Roles and Approval Gates

The agent must pause and request approval at these gates. Approval can be a short
written decision recorded in the experiment log.

| Gate | Agent prepares | Researcher decides |
| --- | --- | --- |
| G0: research charter | Project card, feasibility risks, and proposed protocol | Question, hypothesis, scope, data use, success criterion |
| G1: baseline lock | Runnable baseline, split audit, configuration, and pilot result | Baseline is valid and comparable |
| G2: search plan | Ordered candidate changes, budget, and expected risks | Search space, budget, and which changes are material |
| G3: final evaluation | Validation-selected configuration and frozen test procedure | Permission for final test evaluation |
| G4: interpretation | Results table, uncertainty analysis, and claim-to-evidence map | Scientific interpretation and manuscript wording |
| G5: cleanup | Candidate files, sizes, provenance, and retention plan | Any deletion or irreversible cleanup |

The agent may proceed without a new gate only for a previously approved,
reversible run that stays within the locked protocol and compute budget.

## Automation Route

### Phase A: Establish a Trustworthy Baseline

1. Inspect the repository and create a run manifest containing code revision,
   environment, data version, split identifiers, random seeds, configuration, and
   output paths.
2. Audit the data pipeline for train-validation-test leakage, duplicated samples,
   label alignment, and preprocessing fit on non-training data.
3. Run the approved baseline for the planned independent seeds or folds.
4. Save raw predictions, aggregate metrics, logs, checkpoints as needed for
   reproduction, and an initial uncertainty estimate.
5. Present the baseline report at G1. Do not tune the proposed method until the
   baseline is accepted.

### Phase B: Improve Through Controlled Experiments

For every candidate, define one hypothesis, one changed factor, expected failure
mode, and a comparison against the locked baseline.

1. Tune hyperparameters using validation performance only.
2. Evaluate approved training strategies such as augmentation, regularization,
   scheduling, or class balancing.
3. Evaluate approved model or loss changes after simpler choices are exhausted.
4. Run required ablations and robustness checks for any promising method.
5. Update the experiment ledger after every run; retain failed and inconclusive
   trials.
6. Stop when the budget is exhausted, the practical success criterion is met with
   the planned uncertainty evidence, or results no longer justify the next cost.

New ideas are welcome, but the agent must propose their rationale, expected cost,
affected controls, and validation plan at G2 before running a material change.

### Phase C: Final Evaluation and Interpretation

1. Freeze the selected configuration, code revision, preprocessing, and analysis
   script.
2. Obtain G3 approval, then evaluate the held-out test set. Do not use that result
   to choose another model or configuration. Any additional test evaluation must
   be documented and treated as exploratory analysis.
3. Run the pre-specified uncertainty and statistical analyses.
4. Produce tables and figures directly from saved result artifacts.
5. At G4, report supported claims, null or inconclusive findings, limitations, and
   any deviation from the original plan.

## Required Records

### Experiment Ledger

Maintain one row per run. A run without a ledger entry is not evidence.

| Run ID | Date | Code revision | Data/split version | Hypothesis | Changed factor | Seed/fold | Metrics | Artifact path | Status | Notes |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| [ID] | [Date] | [Commit] | [Version] | [Claim tested] | [Only intended change] | [Seed] | [Values] | [Path] | [Complete/failed] | [Interpretation] |

### Claim-to-Evidence Map

Before a report or manuscript is finalized, map every substantive claim to its
evidence and its limitation.

| Claim | Evidence artifact | Analysis | Limitation or qualifier | Approved by |
| --- | --- | --- | --- | --- |
| [Claim] | [Run IDs/path] | [Metric/test] | [Scope] | [Name/date] |

## Review Checklist

Before declaring a result, the agent and researcher jointly verify:

- The code, environment, configuration, seed, and data version can reproduce the
  reported result within the expected stochastic variation.
- The split unit matches the claimed generalization setting and contains no known
  leakage or overlap.
- Baselines and proposed methods received comparable tuning opportunity and
  computational budget.
- Metrics, aggregation, uncertainty intervals, and statistical tests match the
  project card or disclose deviations.
- Tables, figures, and manuscript statements are generated from saved artifacts
  and do not overstate statistical or practical significance.
- Ablations explain the contribution of each material design choice.
- Limitations, data bias, failed experiments, and unaddressed issues are recorded.
- Code is readable enough to rerun, and cleanup candidates are only removed after
  G5 approval.