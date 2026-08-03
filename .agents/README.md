# Agent Collaboration Kit

This directory is a copyable starter kit for a research project in its
exploration stage. It implements the conceptual model in the
[Exploration Harness Template](../Appendix/02-exploration-harness-template.md).
It is not active automation for this book repository.

## Adopt in a Project

1. Copy `AGENTS.md` to the target project root.
2. Copy `roles/` into `.agents/roles/` in that project.
3. Copy `templates/COORDINATION.md` to `.agents/COORDINATION.md`, replace the
   placeholders, and record the physical locations of the four areas.
4. Copy the four README templates to the roots of `code/`, `artifacts/`,
   `manuscript/`, and `memory/`. Adapt names or paths if the project stores an
   area elsewhere.
5. Copy `memory-item.md`, `MEMORY_INDEX.md`, and `CLEANUP_LOG.md` into the
   target `memory/` area. Use one memory-item copy for each durable finding.

The root `AGENTS.md` is the shared contract. The Manager reads a user request,
delegates a bounded task to a role card, gathers its report, and asks for any
decision that requires researcher approval. Role cards are prompts and operating
contracts, not background services; connect them to Codex commands, a scheduler,
or another orchestrator only when the project explicitly needs that adapter.

## Layout

```text
target-project/
  AGENTS.md
  .agents/
    COORDINATION.md
    roles/
    templates/
  code/
  artifacts/
  manuscript/
  memory/
    MEMORY_INDEX.md
    CLEANUP_LOG.md
    items/
```

`code`, `artifacts`, `manuscript`, and `memory` are logical areas. Their actual
paths may point to other repositories, experiment trackers, or durable storage,
provided `.agents/COORDINATION.md` records the locations and each area remains
independently navigable.

## Contents

| Path | Scope | Purpose |
| --- | --- | --- |
| `AGENTS.md` | Reusable | Shared authority, traceability, delegation, and approval rules |
| `roles/` | Reusable | Bounded contracts for Manager, Memory Curator, Cleaner, Updater, Reviewer, and Writer |
| `templates/` | Reusable | Starting records for one project's coordination, memory, cleanup, and navigation files |
| `.agents/COORDINATION.md` | Project instance | Current objective, locations, active roles, triggers, and pending decisions |
| `memory/` | Project instance | Durable, tagged observations, decisions, and retrieval index |

For a study with a locked question and formal evaluation, move to the
[Project Harness Template](../Appendix/01-project-harness-template.md) and the
[AI Collaboration Protocol](../10-special-topics/12-AI-collaboration-protocol.md).
The exploration kit remains useful for recording side investigations, but it
must not replace the governed experiment ledger or approval gates.
