# Project Harness Template

> Copy this file into any project repository and fill in the bracketed fields.
> Treat it as the single source of truth for what the project is, how it is
> evaluated, and what the AI agent is allowed to do. Delete this line after copying.

---

## 1. Project Identity

- **Project name:** [Short, unique identifier]
- **One-line summary:** [What this project does in one sentence]
- **Status:** [Planning / Baseline / Tuning / Final / Archived]
- **Code revision:** [Commit hash or tag pinned for current experiments]

---

## 2. Research Question

- **Question:** [Falsifiable question: population, input, task, outcome]
- **Motivation:** [Why it matters scientifically or practically]
- **Primary hypothesis:** [Direction and expected mechanism]
- **Non-goals:** [What this study will *not* establish]
- **Success criterion:** [Minimum practically meaningful result, not just a better number]

---

## 3. Prior Evidence

| Source | Relevant finding | Gap addressed here | Design consequence |
| --- | --- | --- | --- |
| [Citation] | [Finding] | [Gap] | [Decision] |

---

## 4. Data

- **Datasets:** [Name, version, access date, license]
- **Population:** [Participants, demographics, known biases]
- **Label definition:** [How labels are defined, class balance, known noise]
- **Unit of analysis:** [Subject / session / trial / window]
- **Preprocessing (ordered):** [Exact steps; note which are fit-per-fold]

---

## 5. Experimental Protocol

- **Input:** [Shape, sampling rate, representation]
- **Target:** [Discrete class / continuous score / distribution]
- **Split protocol:** [LOSO / cross-session / subject-independent / chronological]
- **Random seeds:** [List all seeds; generated how]
- **Leakage controls:** [Where leakage is most likely and how it is prevented]
- **Exclusion rules:** [Fixed before result inspection]

---

## 6. Methods

- **Baseline:** [Name, paper, why credible]
- **Proposed method:** [One-sentence change vs. baseline, hypothesized benefit]
- **Controlled factors:** [Everything held constant across comparisons]
- **Tunable choices:**

| Parameter | Search space | Budget | Selection metric |
| --- | --- | --- | --- |
| [e.g., learning rate] | [1e-4, 1e-2] | [20 trials] | [val accuracy] |

- **Compute budget:** [Hardware, max runs, max wall-clock, stop rule]
- **Required ablations:**

| Component removed | Question answered |
| --- | --- |
| [e.g., attention module] | [Does attention help beyond the CNN backbone?] |

---

## 7. Metrics and Evidence

- **Primary metric:** [Metric, aggregation level, justification]
- **Secondary metrics:** [Calibration, per-class, efficiency, etc.]
- **Reporting unit:** [Fold / subject / session]
- **Uncertainty:** [CI method, resampling unit]
- **Statistical test:** [Test, null hypothesis, pairing, alpha, correction]
- **Decision rule:** [What counts as support / no evidence / inconclusive]

---

## 8. Automation Route

> The AI agent may execute within these phases. It must pause at each gate for human approval. No material experiment advances without its gate.

### Phase A — Baseline

1. Inspect repo; create run manifest (code revision, env, data version, splits, seeds, config, output paths).
2. Audit data pipeline for leakage, duplicates, label misalignment.
3. Run baseline for all planned seeds/folds.
4. Save raw predictions, metrics, logs, checkpoints.
5. **Gate:** Present baseline report. Do not tune until baseline is accepted.

### Phase B — Improve

*For each candidate: one hypothesis, one changed factor, expected failure mode, comparison against locked baseline.*

1. Tune hyperparameters on validation only.
2. Evaluate approved training strategies (augmentation, regularization, scheduling).
3. Evaluate model/loss changes only after simpler options exhausted.
4. Run ablations and robustness checks for promising candidates.
5. Update experiment ledger after every run; keep failed trials.
6. **Stop when:** budget exhausted, success criterion met with planned evidence, or cost no longer justified.

### Phase C — Final Evaluation

1. Freeze configuration, code, preprocessing, analysis script.
2. **Gate:** Obtain approval, then evaluate test set *once*.
3. Run pre-specified uncertainty and statistical analyses.
4. Generate tables and figures directly from saved artifacts.
5. **Gate:** Report supported claims, null findings, limitations, deviations.

---

## 9. Experiment Ledger

> One row per run. A run without a ledger entry is not evidence.

| Run ID | Date | Revision | Hypothesis | Changed factor | Seed | Metric | Path | Status |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| [001] | [date] | [commit] | [claim] | [only change] | [seed] | [value] | [path] | [done/fail] |

---

## 10. Claim-to-Evidence Map

> Before writing, map every claim to its evidence and limitation.

| Claim | Evidence (Run IDs) | Analysis | Limitation |
| --- | --- | --- | --- |
| [Claim] | [Runs] | [Metric/test] | [Scope/caveat] |

---

## 11. Review Checklist

Before declaring results, verify:

- [ ] Code, env, config, seed, and data version reproduce results within expected variation.
- [ ] Split unit matches claimed generalization; no leakage or overlap.
- [ ] Baselines and proposed methods received comparable tuning and compute.
- [ ] Metrics, aggregation, uncertainty, and tests match the plan (or disclose deviations).
- [ ] Tables and figures are generated from saved artifacts; no overstatement.
- [ ] Ablations explain each material design choice.
- [ ] Limitations, biases, failed experiments, and unaddressed issues are recorded.
- [ ] Code is readable and rerunnable; cleanup only after final approval.

---

## 12. Open Issues

| Issue | Severity | Owner | Notes |
| --- | --- | --- | --- |
| [Description] | [blocker / concern / note] | [Name] | [Context] |

---

*Template version: 1.0. Aligned with the AI Collaboration Protocol (Chapter 10).*
