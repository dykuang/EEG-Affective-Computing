# Exploration Harness Template

> Copy this template into a research project that is still exploring ideas,
> implementations, and evidence. It supports fast, reversible work before a
> formal study is locked. Fill in bracketed fields and delete this note.

---

## 1. Purpose and Boundary

- **Exploration objective:** [Question, design space, or capability being explored]
- **Current status:** [Idea / feasibility / prototype / evidence review / ready to formalize]
- **Researcher:** [Name or team responsible for scientific and publication decisions]
- **Manager agent:** [Role or implementation responsible for routing work]
- **Scope boundary:** [What may be explored and what is out of scope]

Exploration permits provisional code, incomplete evidence, and failed trials. It
does not permit invented results, unsupported scientific claims, test-set-driven
model selection, or irreversible cleanup without approval. A result becomes
evidence only after it is recorded with enough provenance to inspect or rerun.

Use this harness before a study has a locked question, protocol, and baseline.
Once those are ready, create a project card and move to the Project Harness and
the AI Collaboration Protocol. Do not treat exploratory outcomes as confirmatory
results.

## 2. Four Independent Areas

Keep the following logical areas independently navigable. They may live in one
repository or in separate local, remote, or tracked locations. Record their
physical paths in the coordination record.

| Area | Purpose | Independence requirement |
| --- | --- | --- |
| `code` | Source, configurations, notebooks, and runnable entry points | A reader can install dependencies and run the documented route without reading the manuscript. |
| `artifacts` | Checkpoints, logs, raw outputs, result summaries, and run records | Items are organized by a stable run or investigation ID and retain provenance. |
| `manuscript` | Notes, outlines, figures, and manuscript text | A reader can understand its argument, evidence, and limitations without reading implementation details. |
| `memory` | Durable observations, decisions, questions, and links accumulated during exploration | Items are machine-friendly, traceable, and retrievable without becoming the sole record of evidence. |

Each area root, and each standalone subdirectory that needs an entry point, must
have a concise `README.md`. A README explains purpose, status, entry points,
and links to the next detail; it should not duplicate the material it indexes.

## 3. Shared Operating Rules

- **Researcher authority.** The researcher approves the research question,
	data use, material protocol changes, external communication, and irreversible
	operations.
- **Query before assuming.** When instructions, data, or evidence are ambiguous,
	the Manager asks for clarification instead of silently choosing an
	interpretation.
- **Traceability.** Memory notes and manuscript statements link to their source:
	a run ID, artifact path, code revision, source document, or explicitly marked
	rationale.
- **Separate observation from inference.** Record what was observed, what is
	inferred, confidence, and open questions separately.
- **Reversible by default.** Agents may inspect, organize, tag, index, and
	prepare proposals within scope. Moving, archiving, deleting, publishing, or
	changing a locked protocol requires explicit recorded researcher approval.
- **Batch maintenance.** Update and cleanup agents work at meaningful milestones
	or defined triggers, not after every chat turn.

## 4. Memory Model

Create a `memory/` area early, even when its content is more useful to agents
than to readers. Store durable items as small files or structured records with a
stable ID, summary, tags, timestamps, confidence, and source links. Free-form
scratch work is welcome, but the updater should promote durable findings into
indexed records.

Use at least one tag from each applicable group:

| Tag group | Values | Purpose |
| --- | --- | --- |
| Lifecycle | `active`, `potentially-useful`, `not-useful`, `archive-candidate`, `delete-pending`, `archived`, `cleaned` | Whether and how an item should be retained. |
| Kind | `observation`, `inference`, `decision`, `question`, `method`, `reference` | What the item represents. |
| Confidence | `high`, `medium`, `low`, `unverified` | Strength of support. |
| Topic | [project-defined tags] | Retrieval by task, data, model, or manuscript section. |

`cleaned` records an action in the cleanup log; it does not by itself justify
deletion. `delete-pending` marks a candidate only. Preserve an archived copy or
recovery path where practical until a user-approved retention period ends.

## 5. Agent Roles

The Manager is the single interface between the researcher and specialist
agents. A tool implementation may call these roles through prompts, commands,
or scheduled jobs, but the responsibilities remain the same.

| Role | Main responsibility | Required output |
| --- | --- | --- |
| Manager | Triage requests, select bounded work, collect reports, and request decisions | Concise status, delegated work, findings, risks, and next approval needed |
| Memory Curator | Create, tag, retrieve, and link durable memory items | Updated item/index and source references |
| Updater | Batch-refresh memory and navigation documents after meaningful changes | Change summary and stale-item list |
| Cleaner | Inventory stale or oversized material and prepare safe cleanup proposals | Candidate list, reference check, recovery plan, and approval request |
| Reviewer | Audit code, artifacts, manuscript evidence, and cleanup proposals | Findings ordered by severity, evidence checked, and unresolved risks |
| Writer | Draft requested manuscript content from approved sources | Draft plus claim-to-evidence map and limitations |

The Writer runs only at the researcher's request. The Cleaner must not perform
an archive, move, or deletion until the researcher explicitly approves its
scoped proposal. The Reviewer may block a cleanup or writing handoff when the
evidence chain is missing; the researcher resolves the decision.

## 6. Records and Triggers

Maintain the following compact records:

- **Coordination record:** objective, area locations, active roles, current
	investigations, known risks, pending approvals, and next user decision.
- **Memory index:** active and recently changed memory items, tag guide, and
	links to archives or deletion candidates.
- **Cleanup log:** candidate list, reference check, approval text and time,
	action taken, destination or recovery path, and reviewer result.
- **Run or investigation record:** for any result discussed externally or used
	to support a decision, capture code revision, configuration, data/split
	version, command, output path, and status.

Choose triggers deliberately. Useful examples are an explicit user command, an
exploration milestone, a defined growth or staleness threshold, a failed run, or
a pending manuscript request. Record the trigger policy in the coordination
record. A schedule is optional; no schedule should silently create irreversible
changes.

## 7. Approval Points

| Decision | Agent prepares | Researcher approves |
| --- | --- | --- |
| New material direction | Rationale, cost, affected controls, and expected failure mode | Whether to pursue it |
| Formal-study handoff | Candidate question, baseline route, evaluation boundary, and open risks | Project charter / G0 scope |
| Cleanup | Candidates, reference check, retention reason, and recovery plan | Exact files and permitted action |
| Manuscript finalization or external release | Draft, evidence map, limitations, and reviewer findings | Wording and release |

Approval may be a short written decision, but it must be recorded in the
coordination record or cleanup log. A broad prior approval does not authorize an
unrelated destructive operation.

## 8. Handoff to a Governed Study

Exploration is ready to hand off when the following are available:

- a candidate falsifiable question and scope;
- an accessible data and code route;
- a proposed baseline, evaluation boundary, and leakage risks;
- an initial evidence and artifact trail; and
- an explicit list of unresolved assumptions and failed approaches.

At that point, the researcher approves a G0 charter using the Project Harness.
The AI Collaboration Protocol then governs baseline lock, controlled search,
final evaluation, claims, and final cleanup. Retain exploratory records; label
them clearly rather than rewriting them as pre-registered evidence.

## 9. Implementation Companion

The accompanying [agent collaboration kit](../.agents/README.md) supplies
copyable role contracts, a root `AGENTS.md` template, and templates for the
coordination, memory, cleanup, and four-area navigation records. It is designed
for Codex-style projects but does not require a specific orchestration framework
or scheduler.

*Template version: 1.0. Complements the Project Harness Template and the AI
Collaboration Protocol.*