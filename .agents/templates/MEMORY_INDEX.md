# Exploration Memory Index

Use this index to retrieve durable exploration knowledge. It is an index, not a
substitute for source artifacts or run records.

## Tag Guide

- **Lifecycle:** `active`, `potentially-useful`, `not-useful`, `archive-candidate`,
  `delete-pending`, `archived`, `cleaned`
- **Kind:** `observation`, `inference`, `decision`, `question`, `method`, `reference`
- **Confidence:** `high`, `medium`, `low`, `unverified`
- **Topic:** project-defined tags such as `data`, `preprocessing`, `baseline`, or
  `manuscript:introduction`

Apply at least one lifecycle, kind, confidence, and topic tag to every item.
Use `delete-pending` only for a Cleaner proposal; it does not authorize deletion.

## Active Items

| ID | Summary | Kind | Confidence | Topics | Source links | Updated |
| --- | --- | --- | --- | --- | --- | --- |
| [mem-ID] | [summary] | [kind] | [confidence] | [tags] | [links] | [date] |

## Recent Changes

| Date | Item ID | Change | Trigger or source |
| --- | --- | --- | --- |
| [date] | [mem-ID] | [created/updated/reclassified] | [trigger/link] |

## Retained Elsewhere

| Lifecycle | Location | Retrieval route | Notes |
| --- | --- | --- | --- |
| Archived | [path/service] | [index/query] | [retention rule] |
| Delete pending | [path/service] | [cleanup-log ID] | [approval status] |
