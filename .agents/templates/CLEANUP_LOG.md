# Cleanup Log

Append one entry for every approved archive, move, deletion, overwrite, or other
irreversible cleanup action. A dry-run proposal may be recorded here, but it must
remain `pending` until the researcher explicitly approves the exact scope.

## Entries

### [cleanup-ID] - [YYYY-MM-DD]

- **Status:** [pending / approved / rejected / completed / reverted]
- **Trigger:** [user request, milestone, or documented threshold]
- **Candidate paths or IDs:** [exact list or manifest path]
- **Reason:** [staleness, duplicate, retention rule, storage pressure]
- **Reference check:** [files and records checked; unresolved references]
- **Proposed or completed action:** [archive / move / delete / overwrite]
- **Destination or recovery path:** [path, snapshot, or `none` with justification]
- **Researcher approval:** [verbatim decision or durable reference, name, date]
- **Reviewer result:** [not required / approved / findings reference]
- **Indexes and READMEs updated:** [paths]
- **Outcome and residual risk:** [summary]
