# Practical Segmentation Pipeline

Window length and overlap describe what a segment looks like, but a reproducible study also needs a clear procedure for producing segments from the recording. A practical segmentation pipeline connects recording metadata, preprocessing, event timing, labels, quality control, and dataset splitting. Making these steps explicit helps prevent accidental information leakage and makes results easier to reproduce.

## Start from Recording Structure

Before selecting a window, identify the temporal units already present in the data. Depending on the experiment, a recording may contain subjects, sessions, trials, stimulus periods, rest periods, and event markers. These units should be preserved in the segment metadata rather than discarded when the signal is converted into arrays.

At minimum, each segment should be traceable to:

- subject and session,
- trial or recording identifier,
- start and end sample or timestamp,
- preprocessing configuration,
- event or stimulus condition,
- target label and label source,
- and quality-control status.

This information makes it possible to group segments correctly during splitting and to investigate unusual predictions later. A segment without provenance is difficult to audit, even when its signal values are valid.

## Define the Pipeline Order

The order of operations should be stated explicitly because some steps change the signal boundaries or use statistics estimated from data. A typical workflow is:

1. Import the continuous recording and synchronize its timestamps with event markers.
2. Detect or annotate unusable intervals, gross artifacts, missing samples, and recording interruptions.
3. Apply the chosen reference, filtering, and other signal-level preprocessing operations.
4. Define eligible trial or state intervals and exclude margins that should not contribute to the target.
5. Generate windows using a declared length, stride, and alignment rule.
6. Assign labels using only information permitted by the task definition.
7. Compute quality-control measures and remove or flag invalid segments.
8. Split segments by the appropriate independent unit before fitting data-dependent transforms or training a model.

This is not a universal ordering. For example, an artifact detector may need broadband data, while a model-specific quality threshold may be applied after filtering. The important point is to document the order and apply the same logic to every partition.

## Handle Boundaries Explicitly

Windows should not silently cross boundaries that change the meaning of the target. Examples include stimulus transitions, trial pauses, rest-to-task transitions, missing-data intervals, and changes in experimental condition. A window that combines two conditions may be numerically convenient but semantically ambiguous.

For event-based experiments, define whether windows may begin before an event, how much post-event data is included, and whether a transition interval is discarded. For continuous experiments, use annotated change points or a documented policy for ambiguous transitions. If a window crosses a boundary by design, record that fact and define how its label is computed.

## Make Quality Control Segment-Aware

Artifact rejection should be considered at the segment level as well as at the recording level. A recording can be usable overall while containing individual windows dominated by eye movements, muscle activity, electrode displacement, saturation, or missing samples.

Useful segment-level checks include:

- the fraction of rejected or interpolated samples,
- peak-to-peak amplitude and unusually large excursions,
- channel dropouts or near-zero variance,
- power concentrated in frequencies associated with known artifacts,
- and the number of valid channels available to the model.

Quality control should produce a rule and a record, not only a visual impression. For example, a study may exclude a segment when more than 20% of its samples are marked bad, while retaining the segment identifier and rejection reason in a manifest. Thresholds should be selected without inspecting the test labels.

## Prevent Leakage from Preprocessing

Segmentation can leak information even when the train-test split appears correct. Filters with improperly handled boundaries can use future samples, normalization can use statistics from the complete dataset, and artifact-repair procedures can be fitted using held-out recordings. These risks are separate from overlap leakage and should be checked independently.

For an offline benchmark, fit data-dependent operations such as scaling, channel selection, imputation, or learned artifact correction on the training partition only. Apply the resulting parameters to validation and test partitions. For an online system, use causal filters and update statistics only with information available up to the current time.

## Build a Segment Manifest

A segment manifest is a table that separates signal storage from bookkeeping. One row can describe one segment, while the signal itself is stored in an array, file, or dataset index. A useful manifest may contain columns such as:

| Field | Purpose |
| --- | --- |
| `segment_id` | Stable identifier for the segment |
| `subject_id` and `session_id` | Grouping for evaluation and analysis |
| `trial_id` | Prevents related windows from being split incorrectly |
| `start_time` and `end_time` | Reconstructs temporal location |
| `label` and `label_source` | Records the target and how it was obtained |
| `split` | Stores the preassigned train, validation, or test partition |
| `quality_status` and `quality_reason` | Makes exclusions auditable |

The split assignment should be generated at the independent-unit level, such as subject, session, trial, or time block, and then propagated to its segments. Assigning a split independently for every row is unsafe when rows share a source recording.

## Verify the Result Before Training

Before fitting a model, inspect the segment collection as a dataset rather than only as a tensor. Check that:

- segment counts are reported per subject, session, trial, and split;
- no subject, trial, or source interval appears in incompatible partitions;
- labels are distributed plausibly after quality filtering;
- the first and last windows obey the declared boundary policy;
- timestamps are monotonic and sample counts match the intended duration;
- and online windows do not depend on future samples or future annotations.

A small audit table or automated assertion can catch many errors earlier than model evaluation. It is also useful to rerun the manifest-generation step twice and confirm that stable identifiers, counts, and split assignments are reproducible.

## Report Segmentation Precisely

A segmentation description should allow another researcher to reconstruct the examples without guessing. Report the sampling rate, preprocessing order, window duration, stride or overlap, boundary policy, label rule, artifact criteria, discarded intervals, and split unit. Also report how many segments were created and retained at each stage.

When comparing models, keep the segment manifest fixed unless segmentation is itself the experimental variable. Otherwise, a performance difference may reflect different examples or different leakage opportunities rather than a better representation or architecture.

## Practical Checklist

Before treating a segmented dataset as ready for training, ask:

- Can every segment be traced to a subject, recording, trial, and time interval?
- Are event, trial, and artifact boundaries handled by an explicit rule?
- Is the label available at the time the segment would be used?
- Are data-dependent preprocessing parameters fitted within the training partition?
- Are overlapping or neighboring windows kept within the same evaluation group when required?
- Can the segment count and rejection reasons be reproduced from a manifest?

Segmentation is complete only when the signal, target, provenance, and evaluation assignment agree. A short windowing script may create valid arrays, but a documented pipeline creates defensible examples.

## References

- Delorme, A., and Makeig, S. (2004). EEGLAB: An open source toolbox for analysis of single-trial EEG dynamics. Journal of Neuroscience Methods, 134(1), 9-21.
- Keil, A., Debener, S., Gratton, G., Junghöfer, M., Koldack, P., and Lauener, C. (2014). Committee report: Publication guidelines and recommendations for EEG and MEG studies. Psychophysiology, 51(1), 1-21.
- Pernet, C. R., Appelhoff, S., Gorgolewski, K. J., Flandin, G., Phillips, C., Delorme, A., and Oostenveld, R. (2019). EEG-BIDS, an extension to the BIDS specification for EEG. Scientific Data, 6, 103.