# Dataset Documentation, Governance, and Reuse

A dataset is reusable only when its data, metadata, permissions, and derivation history can be understood together. In affective EEG, this requirement is especially important because recordings may be linked to video, audio, behavior, demographics, health information, or highly personal self-reports. Good documentation and governance protect participants while allowing other researchers to reproduce and extend the work.

This section describes the practical information that should accompany an EEG-affect dataset throughout its lifecycle.

## Document the Dataset as a Research Object

A dataset landing page or README should explain what was collected, why it was collected, and how the released files relate to the experiment. It should let a new user determine whether the data fit a research question before downloading or preprocessing them.

At minimum, document:

- the study purpose, target population, recruitment approach, and inclusion criteria;
- the elicitation protocol, stimuli, instructions, timing, and event codes;
- EEG device, montage, reference, sampling rate, units, and auxiliary sensors;
- label definitions, collection procedure, scales, timing, and aggregation rules;
- subject, session, run, trial, and channel identifiers;
- raw, cleaned, and derived data products, including their relationship;
- known limitations, missing data, artifacts, and exclusions;
- and the recommended citation, release version, and contact route for corrections.

A data dictionary should define each metadata field, its units, permitted values, missing-value convention, and relation to other files. This is often more useful than a narrative description when users construct reproducible pipelines.

## Use Interoperable Organization

Consistent organization reduces ambiguity and enables tools to validate the dataset. Brain Imaging Data Structure (BIDS) and its EEG extension provide a useful pattern: recordings are arranged by subject, session, task, and run, with sidecar metadata files describing channels, events, electrodes, and acquisition settings.

Interoperability does not require every dataset to use one file format. It does require stable identifiers, machine-readable metadata, unambiguous units, and a documented mapping from source-device channels and events to released names and codes. If data are converted from a proprietary format, retain the conversion procedure and checksums or provenance that link the release back to the source recordings.

## Separate Raw, Derived, and Benchmark Data

Raw recordings should remain immutable after release. Artifact-corrected signals, re-referenced recordings, epochs, windows, labels, features, and benchmark splits are derived products and should be stored separately or labeled with an explicit derivation path.

For each derived product, record:

- the parent dataset release and input files;
- preprocessing code, configuration, and software version;
- random seed when a stochastic procedure is used;
- quality-control criteria and excluded data;
- and the date or version that produced the artifact.

Versioned manifests are particularly useful for benchmark datasets. A manifest can list every sample identifier, source trial, preprocessing version, quality flag, label source, and assigned split. This supports exact reproduction without distributing many redundant copies of large EEG arrays.

## Protect Participants and Respect Permissions

Data sharing must match what participants consented to and what the ethics approval permits. Before release, assess whether direct or indirect identifiers appear in raw files, event logs, videos, audio, free-text responses, or combinations of demographic variables. Removing names is not always sufficient when audiovisual data or rare participant attributes remain available.

State the access model clearly: open download, registered access, controlled access, or no redistribution. Include the license or data-use agreement, required acknowledgements, prohibited uses, and instructions for reporting privacy or data-quality concerns. Derived data and trained models can also disclose information, so governance should cover them when relevant.

Researchers reusing public data should follow the same conditions. Public availability is not a general waiver of consent, attribution, or restrictions on commercial, clinical, or redistributive use.

## Plan for Corrections and Version Changes

Datasets evolve. Channels can be discovered to be mislabeled, event timing can be corrected, files can be removed for privacy, and metadata can be improved. Treat these changes as releases rather than silently replacing files.

A release note should state what changed, why it changed, which files are affected, and whether benchmark results are expected to differ. Preserve earlier versions where permissions allow, assign stable version identifiers, and provide a recommended migration path. Papers should cite the exact version used, not only the dataset name.

## Reuse Checklist

Before publishing or reusing a dataset, verify that:

- the protocol, acquisition setup, labels, and file structure are documented;
- metadata have stable identifiers, units, and missing-value conventions;
- raw and derived products can be distinguished and linked by provenance;
- access conditions, consent limitations, license, and citation requirements are clear;
- quality exclusions and preprocessing versions are reproducible;
- benchmark split manifests identify the independent source units;
- and release notes describe corrections and version changes.

Good documentation cannot remove the limitations of a dataset, but it makes those limitations visible. That visibility is a prerequisite for fair reuse, meaningful comparison, and cumulative research.

## References

- Pernet, C. R., Appelhoff, S., Gorgolewski, K. J., Flandin, G., Phillips, C., Delorme, A., and Oostenveld, R. (2019). EEG-BIDS, an extension to the BIDS specification for electroencephalography. Scientific Data, 6, 103.
- Wilkinson, M. D., Dumontier, M., Aalbersberg, I. J., et al. (2016). The FAIR guiding principles for scientific data management and stewardship. Scientific Data, 3, 160018.
- Gebru, T., Morgenstern, J., Vecchione, B., et al. (2021). Datasheets for datasets. Communications of the ACM, 64(12), 86-92.
- European Commission. (2021). Ethics guidelines for trustworthy AI. Publications Office of the European Union.
