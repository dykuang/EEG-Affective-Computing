# Useful Platforms and Tools for Training and Evaluation

A small set of well-maintained tools can remove much of the engineering overhead from affective EEG experiments. The tools do not, however, determine whether an experiment is scientifically valid. A library may provide a convenient data loader, window splitter, or training loop while still allowing a protocol that leaks information across subjects, sessions, trials, or time. Treat software defaults as implementation choices to inspect, not as a substitute for defining the generalization claim.

## A Layered EEG Software Stack

Different projects are strongest at different layers of the workflow. The following tools are useful starting points rather than mutually exclusive alternatives:

| Tool or platform | Main ecosystem | Useful capabilities | Important qualification |
| --- | --- | --- | --- |
| [MNE-Python](https://mne.tools/stable/index.html) | Python | Reading, preprocessing, visualization, time-frequency analysis, source estimation, connectivity, statistics, and decoding | A general neurophysiology toolkit; the researcher still defines the task-specific split and evaluation protocol |
| [EEGLAB](https://eeglab.org/) | MATLAB | Interactive and scripted EEG preprocessing, ICA-based artifact analysis, epoching, visualization, and a large plugin ecosystem | GUI operations should be recorded in scripts or configuration files when the analysis must be reproduced |
| [FieldTrip](https://www.fieldtriptoolbox.org/) | MATLAB | Flexible preprocessing, time-frequency analysis, source reconstruction, connectivity, and statistical testing | Its flexibility is valuable but makes explicit configuration and version recording especially important |
| [BIDS](https://bids-specification.readthedocs.io/en/stable/eeg.html) and [MNE-BIDS](https://mne.tools/mne-bids/stable/index.html) | Cross-language / Python | Standardized dataset organization, metadata, conversion, and validation workflows | BIDS improves traceability but does not by itself prevent labels or split assignments from leaking |
| [TorchEEG](https://torcheeg.readthedocs.io/en/latest/) | PyTorch | Dataset and I/O abstractions, offline and online transforms, emotion datasets, models, trainers, and cross-subject or cross-trial split utilities | Verify that its dataset and split semantics match the unit of generalization in the study |
| [Braindecode](https://braindecode.org/stable/) | PyTorch and scikit-learn | MNE-compatible preprocessing, windowing, EEG-specific augmentation, published deep-learning architectures, and training wrappers | It is primarily a general neural decoding toolkit; affective tasks may require custom datasets and labels |
| [libEER](https://github.com/XJTU-EEG/LibEER) | Python | Benchmarking and algorithm implementations for EEG-based emotion recognition | Reproduce the documented protocol and inspect whether preprocessing and partitioning match the intended claim |
| [EEGEmoLib](https://eegemolib.github.io/) | Python | Configurable emotion datasets, handcrafted feature extraction, feature selection, recognition models, and visualization | A practical baseline and teaching toolkit; report its version and configuration rather than treating integrated defaults as canonical |
| [MOABB](https://moabb.neurotechx.com/docs/) | Python, MNE, and scikit-learn | Standardized BCI datasets, paradigms, pipelines, within-session, cross-session, and cross-subject evaluations, and benchmark summaries | It focuses mainly on BCI paradigms such as motor imagery and P300, so it is a protocol and benchmarking reference rather than a direct affective-EEG benchmark |

MNE-Python, EEGLAB, and FieldTrip are primarily signal-analysis environments. TorchEEG, Braindecode, libEER, and EEGEmoLib move closer to dataset handling and model training. BIDS and MNE-BIDS organize the data and metadata around those analyses, while MOABB provides a useful example of how standardized pipelines and evaluation interfaces can support comparison. A project can combine several of these layers; it should not assume that choosing one framework solves the others.

## Choosing a Tool for the Research Question

The intended use case should determine the starting point:

- For careful preprocessing, visualization, and classical features, begin with MNE-Python, EEGLAB, or FieldTrip according to the team's language and existing workflow. MNE-Python is a natural choice for a Python and scikit-learn pipeline, whereas EEGLAB and FieldTrip remain strong options for MATLAB-based analysis and established laboratory procedures.
- For an end-to-end PyTorch experiment with custom datasets, transforms, and neural models, compare TorchEEG and Braindecode. TorchEEG is especially convenient when its EEG dataset and model abstractions fit the task; Braindecode is useful when MNE-compatible windows and published neural decoding architectures are central.
- For affective-EEG baselines and comparisons with established emotion-recognition algorithms, inspect libEER and EEGEmoLib. Use them to make baseline construction easier, but audit their data preprocessing, feature normalization, subject handling, and aggregation rules before interpreting a result.
- For a standardized benchmark, use MOABB where its datasets and paradigms match the question. For emotion recognition, its evaluation design can still be instructive, but it should not be presented as evidence on affective labels that it does not contain.
- For collaboration and reuse, place recordings and metadata in a BIDS-compatible structure, keep split manifests under version control, and connect preprocessing scripts to the dataset identifiers and software versions that produced them.

A practical Python workflow might use BIDS and MNE-BIDS for organization, MNE-Python for inspection and preprocessing, scikit-learn for grouped baselines, and TorchEEG or Braindecode for neural models. libEER or EEGEmoLib can provide affective-EEG baselines. This combination is often more transparent than adopting a single large framework for every stage, because each component can be replaced and tested independently.

## Tool-Assisted Reproducibility

The following records are worth saving with every experiment:

- the exact tool and package versions, including the operating system, Python or MATLAB version, and accelerator libraries;
- the dataset release or commit, file checksums where practical, channel montage, reference, sampling rate, and unit conventions;
- the preprocessing and segmentation configuration, including filters, resampling, artifact handling, window length, stride, and any augmentation;
- the subject, session, trial, or chronological split manifest, together with the rule used to produce it;
- the fitted normalization, feature-selection, and dimensionality-reduction objects, which must be learned inside the training partition;
- model configurations, random seeds, checkpoints, training logs, and the command that generated each reported result; and
- software citations, licenses, and any local modifications to an upstream project.

Configuration files and experiment trackers can make these records easier to maintain. A tracker is useful only when it logs the split identity and data-processing configuration as well as the final score. A reproducible dashboard containing scores without the held-out units, preprocessing parameters, or checkpoint provenance is still difficult to audit.

## Common Failure Modes

Convenience APIs can hide choices that matter for affective EEG. Check at least the following before using a tool in a paper or benchmark:

| Question | Why it matters |
| --- | --- |
| Does the splitter hold out subjects, sessions, trials, or only windows? | The held-out unit must match the generalization claim |
| Are overlapping windows generated before or after the split? | Splitting generated windows can place near-duplicates in training and test sets |
| Where are normalization and feature-selection statistics fitted? | Full-dataset fitting leaks information from validation or test data |
| Can the pipeline use future samples? | Centered filters, bidirectional models, and smoothing can invalidate a causal online claim |
| Are labels and metadata carried through every transform? | Misaligned labels can create silent errors that look like model performance |
| Are the defaults and versions recorded? | A later release or hidden default can change the result |
| Is the tool designed for the target paradigm? | A strong motor-imagery benchmark may not transfer to emotion recognition |

The best tool is therefore the one that makes the intended protocol easy to express, inspect, and rerun. Convenience is valuable, but transparent data flow and explicit evaluation boundaries are more important than the number of built-in models.

## Summary

MNE-Python, EEGLAB, and FieldTrip support signal analysis; BIDS and MNE-BIDS support data organization; TorchEEG, Braindecode, libEER, and EEGEmoLib support EEG modeling and affective-EEG experimentation; and MOABB demonstrates standardized benchmarking. These tools can accelerate research and improve reuse, but only an explicit split manifest, leakage-safe preprocessing, versioned configurations, and unit-appropriate metrics make the resulting evidence interpretable.

## References

- Gramfort, A., Luessi, M., Larson, E., et al. (2013). MEG and EEG data analysis with MNE-Python. *Frontiers in Neuroscience, 7*, 267. https://doi.org/10.3389/fnins.2013.00267
- Delorme, A., & Makeig, S. (2004). EEGLAB: An open source toolbox for analysis of single-trial EEG dynamics including independent component analysis. *Journal of Neuroscience Methods, 134*(1), 9–21. https://doi.org/10.1016/j.jneumeth.2003.10.009
- Oostenveld, R., Fries, P., Maris, E., & Schoffelen, J.-M. (2011). FieldTrip: Open source software for advanced analysis of MEG, EEG, and invasive electrophysiological data. *Computational Intelligence and Neuroscience*, 2011, 156869. https://doi.org/10.1155/2011/156869
- Gorgolewski, K. J., Auer, T., Calhoun, V. D., et al. (2016). The brain imaging data structure, a format for organizing and describing outputs of neuroimaging experiments. *Scientific Data, 3*, 160044. https://doi.org/10.1038/sdata.2016.44
- Appelhoff, S., Sanderson, M., Brooks, T. L., et al. (2019). MNE-BIDS: Organizing electrophysiological data into the BIDS format and facilitating their analysis. *Journal of Open Source Software, 4*(44), 1896. https://doi.org/10.21105/joss.01896
- Schirrmeister, R. T., Springenberg, J. T., Fiederer, L. D. J., et al. (2017). Deep learning with convolutional neural networks for EEG decoding and visualization. *Human Brain Mapping, 38*(11), 5391–5420. https://doi.org/10.1002/hbm.23730
- Jayaram, V., & Barachant, A. (2018). MOABB: Trustworthy algorithm benchmarking for BCIs. *Journal of Neural Engineering, 15*(6), 066011. https://doi.org/10.1088/1741-2552/aadea0
- TorchEEG contributors. (n.d.). *TorchEEG documentation*. https://torcheeg.readthedocs.io/en/latest/
- Li, X., Xie, Y., Wang, Z., et al. (n.d.). *LibEER: Library for EEG-based emotion recognition* [Source code]. GitHub. https://github.com/XJTU-EEG/LibEER
- EEGEmoLib contributors. (n.d.). *EEGEmoLib documentation*. https://eegemolib.github.io/
