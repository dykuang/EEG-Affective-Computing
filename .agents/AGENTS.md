# Research Exploration Collaboration Contract

> Copy this file to the root of a target project as `AGENTS.md`. Replace all
> bracketed fields after creating `.agents/COORDINATION.md`.

## Project Context

- **Project:** [Name]
- **Exploration objective:** [Current question or design space]
- **Researcher authority:** [Person or team]
- **Coordination record:** `.agents/COORDINATION.md`
- **Role contracts:** `.agents/roles/`

Read the coordination record before acting. It names the physical paths and
current status of the logical `code`, `artifacts`, `manuscript`, and `memory`
areas. Do not assume these areas share a repository or storage system.

## Non-Negotiable Rules

1. The researcher owns scientific judgment, data use, material protocol changes,
   external communication, and irreversible operations. Ask rather than assume
   when their intent, evidence, or instructions are ambiguous.
2. Treat observations, inferences, decisions, and open questions as distinct.
   Do not invent runs, measurements, citations, approvals, or completed work.
3. Link every durable finding and manuscript claim to a source: run or
   investigation ID, artifact path, code revision, cited source, or clearly
   labeled rationale. Unverified material remains explicitly unverified.
4. Keep the four areas independently navigable. Every area root and meaningful
   standalone subdirectory has a concise README with purpose, status, entry
   points, and links to the next detail.
5. Exploration may be provisional. It does not justify confirmatory claims,
   test-set-driven selection, or a change to a locked protocol.
6. Do not delete, archive, relocate, overwrite, publish, or externally release
   material without explicit, scoped, recorded researcher approval. A dry run,
   inventory, tag update, index update, or proposal is allowed when in scope.

## Delegation Protocol

The Manager is the only role that routes work between the researcher and
specialists. For a bounded request, the Manager selects the smallest applicable
role card from `.agents/roles/`, passes the request, relevant paths, constraints,
and expected output, then consolidates its report.

Each specialist report states:

- scope and sources inspected;
- observations and evidence links;
- changes made, if any;
- risks, ambiguities, and work not performed; and
- the next decision, handoff, or approval required.

Use an explicit user command, a documented milestone, or a documented trigger
from the coordination record to activate maintenance. Schedules and external
orchestration adapters are optional and cannot bypass this contract.

## Required Approval Record

Before an irreversible action, the Manager presents the exact scope, reason,
reference check, recovery path if practical, and proposed operation. Proceed
only after the researcher gives an unambiguous approval. Record the approval
verbatim or by durable reference in `.agents/COORDINATION.md` or
`memory/CLEANUP_LOG.md`.

The following always require approval:

- archiving, moving, deleting, or overwriting memory or artifacts;
- modifying a locked experiment plan, split, evaluation procedure, or budget;
- finalizing a manuscript section or making external claims; and
- publishing, sharing, or sending material outside the project boundary.

## Role Selection

| Need | Role card |
| --- | --- |
| Route work, consolidate reports, request a decision | `roles/manager.md` |
| Capture, tag, retrieve, or connect durable knowledge | `roles/memory-curator.md` |
| Inventory stale material and propose a cleanup | `roles/cleaner.md` |
| Batch-refresh memory or navigation documents | `roles/updater.md` |
| Audit code, artifacts, claims, or a cleanup proposal | `roles/reviewer.md` |
| Draft a user-requested manuscript section | `roles/writer.md` |

## Formal Study Handoff

When a candidate question, accessible code and data route, baseline route,
evaluation boundary, and open-risk list exist, ask the researcher whether to
create a formal project charter. A researcher-approved charter starts the
Project Harness and its G0 gate; its experiment ledger and approval sequence
govern all claim-bearing work from then on.