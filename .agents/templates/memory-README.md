# Exploration Memory

## Purpose

This area stores durable, traceable observations, inferences, decisions,
questions, methods, and references from exploration. It helps agents retrieve
context, but source code, artifacts, and manuscript records remain authoritative.

## Start Here

- **Index:** `MEMORY_INDEX.md`
- **Item schema:** `items/[memory-item].md`, based on `memory-item.md`
- **Cleanup audit:** `CLEANUP_LOG.md`
- **Coordination record:** `.agents/COORDINATION.md`

## Lifecycle

Items are tagged `active`, `potentially-useful`, `not-useful`,
`archive-candidate`, `delete-pending`, `archived`, or `cleaned`. A lifecycle tag
describes review status; it never authorizes a destructive action. The Cleaner
must record scoped researcher approval before a move, archive, deletion, or
overwrite.

## Retrieval

Search by stable ID, lifecycle, kind, confidence, topic, related run, source
path, or related decision. Prefer the smallest source-backed set of items that
answers the current question.

## Connections

- **Code:** [path or URL]
- **Artifacts:** [path or URL]
- **Manuscript:** [path or URL]
