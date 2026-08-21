# Generative Tasks and Evaluation

Generation is a distinct problem formulation in EEG-based affective computing. Instead of predicting an affect label from EEG, a generative model learns a distribution over signals, features, representations, or related modalities. The task must state what is generated, what conditions are given, and how success is assessed. A visually plausible waveform or a high discriminator score is not sufficient evidence that generated EEG is useful or physiologically credible.

This section defines generation objectives. Chapters on variational autoencoders, adversarial networks, flows, and diffusion models describe architectures that can implement these objectives.

## Define the Generation Contract

A generative study should specify four objects before selecting an architecture:

1. **Representation:** raw waveform, filtered epoch, time-frequency map, feature vector, connectivity matrix, or latent code;
2. **Condition:** affect, subject, session, device, stimulus, partial observation, or no condition;
3. **Preservation target:** which temporal, spectral, spatial, affective, and privacy properties must survive generation; and
4. **Use case:** simulation, augmentation, denoising, imputation, translation, visualization, or scientific hypothesis testing.

These choices determine what counts as a successful sample. Realistic-looking data may be a failure if they copy a participant, remove clinically meaningful variation, or improve a classifier only through leakage.

![Generative-task taxonomy for EEG. The diagram should organize unconditional generation, conditional synthesis, reconstruction and imputation, denoising, cross-device or cross-modal translation, and data augmentation by their inputs, conditions, and preservation targets.](figures/generative-task-taxonomy.png)

**Figure 4.6: Generative-task taxonomy for EEG.** EEG generation can sample, transform, reconstruct, translate, or augment data; the task type determines which conditions and validity criteria must be specified.

## Unconditional EEG Generation

Unconditional generation samples EEG-like data without an explicit affective or subject condition:

$$
\tilde{x} \sim p_\theta(x).
$$

It can be used to model the overall data distribution, support simulation, or study representation learning. Its limitation is that generated samples may not preserve a desired emotion, participant characteristic, channel layout, or recording context. Report the signal representation being generated: raw waveform, filtered epoch, time-frequency map, feature vector, or latent embedding.

Generation at different representation levels makes different claims. Raw-waveform generation must address phase, amplitude, temporal dynamics, and channel relationships. Feature generation may be useful for a downstream model but cannot establish that a physiologically plausible waveform exists. Latent generation is even more dependent on the decoder and should be evaluated after reconstruction or task use.

## Conditional Generation

Conditional generation produces samples under a declared condition $c$:

$$
\tilde{x} \sim p_\theta(x \mid c).
$$

Conditions may include an affect label, valence-arousal value, subject identity, session, stimulus type, electrode montage, or a partial EEG context. This formulation supports class balancing, subject-specific synthesis, controlled simulation, and conditional data augmentation.

The condition must be available at generation time. For example, generating a labeled training sample from a target test participant's data is not equivalent to training on source subjects alone. State whether conditions are observed labels, metadata, inferred latent variables, or outputs of another model.

Conditional validity also requires testing condition control. A generated sample should change when its intended condition changes, while preserving nuisance factors that are supposed to remain fixed. For affective EEG, valence may be entangled with stimulus identity, subject identity, arousal, or session; report which factors are controlled, randomized, or allowed to vary.

## Denoising, Reconstruction, and Imputation

Some generative tasks transform an observed signal rather than sampling from scratch. These include artifact removal, missing-channel reconstruction, masked-sample imputation, super-resolution after downsampling, and cross-device translation. A generic conditional transformation can be written as

$$
\tilde{x} = g_\theta(x_{\mathrm{observed}}, c).
$$

The reference target must be defined carefully. A signal labeled as "clean" may itself contain residual artifacts, and a reconstructed channel should not be evaluated only by similarity to an interpolated target. These tasks should be evaluated for both signal fidelity and their impact on the downstream affective analysis.

For denoising, distinguish removal of nuisance activity from removal of affect-relevant activity. For imputation, distinguish interpolation of a missing sensor from prediction of an unobserved neural source. Use artificial corruption only when its relationship to real failures is documented, and test robustness to corruption types not used during training.

## Cross-Domain and Cross-Modal Generation

Generation may translate between domains, such as low-density and high-density EEG montages, one device and another, EEG and peripheral physiology, or EEG features and an affective representation. These tasks are useful when paired observations exist, but they can also produce convincing-looking outputs that do not preserve the individual event or affective state.

Specify whether training pairs are aligned in time and subject, whether target-domain data are available during training, and which properties must remain invariant. A translation model intended to preserve valence while changing device domain requires a different evaluation from a model intended to reconstruct the original waveform.

Cross-domain claims should include a domain-invariant property test and a domain-specific property test. For example, a device-translation model should preserve an affect-relevant target while changing hardware-specific signatures. It should not be credited for translation if it simply reproduces the source device or relies on a device-label shortcut.

## Generation for Data Augmentation

Synthetic EEG is often proposed to increase training data or reduce class imbalance. The actual task is not merely generating samples; it is improving a downstream model without contaminating evaluation.

Generate synthetic data using training partitions only. Keep validation and test recordings entirely real and independent. Compare against non-generative remedies such as class weighting, resampling, conventional augmentation, or collecting additional data. Report the number of synthetic samples, generation conditions, and the real-data baseline so that an apparent gain cannot be attributed to a larger effective training set alone.

## Evaluate Generated EEG on Multiple Levels

Generation quality should be assessed at several levels because no single metric captures distributional, signal, and task validity.

| Level | Example question | Example evidence |
| --- | --- | --- |
| Distributional fidelity | Does the generated data resemble the real distribution? | Feature-space distances, coverage, diversity, held-out discriminator tests |
| Signal and spectral fidelity | Are amplitudes, spectra, channel relationships, and temporal dynamics plausible? | Power spectral density, band-power distributions, connectivity or covariance comparisons |
| Conditional fidelity | Does the requested affect or domain condition appear in generated data? | Performance of an independently trained condition classifier or regressor |
| Privacy and memorization | Are samples novel rather than copied from training data? | Nearest-neighbor analysis, membership-inference or similarity checks |
| Downstream utility | Does synthetic data help a real-data task? | Evaluation on an untouched real held-out set |

Use real held-out recordings as the reference distribution whenever possible. Visual inspection of generated traces can be helpful, but it cannot establish physiological validity, diversity, privacy, or downstream utility by itself.

Evaluators should be independent of the generator. If the same discriminator, feature extractor, or classifier is used to train and judge the generator, it may reward the generator's own biases. Fit auxiliary evaluators on training data only, report uncertainty across subjects or sessions, and compare generated samples with both real training data and untouched real test data.

![Multi-level evaluation of generated EEG. The diagram should connect generated samples to distributional, signal and spectral, conditional, privacy, and downstream-utility evaluations, with real training data and untouched real test data kept as separate references.](figures/generative-evaluation-ladder.png)

**Figure 4.7: Multi-level evaluation of generated EEG.** A credible generative study evaluates distributional fidelity, physiological signal properties, condition control, privacy, and downstream utility rather than relying on visual realism or one discriminator score.

## Generation Task Checklist

A well-defined generative study should answer:

- What representation is generated or transformed?
- Is generation unconditional, conditional, reconstructive, or translational?
- Which conditions are supplied, and when are they available?
- What data partitions train the generator and any auxiliary evaluators?
- Which signal properties, affective properties, and privacy constraints must be preserved?
- Is downstream utility evaluated on entirely real, untouched test data?

For deployment-oriented studies, also ask:

- Does generation improve the target task compared with simpler baselines at the same real-data budget?
- Can a downstream analyst detect when a generated sample is synthetic, uncertain, or outside the supported condition range?

These answers connect a generative architecture to a scientific or practical objective rather than treating sample realism as the objective by itself.

## References

- Bendaoud, I., et al. (2021). Generative adversarial networks for EEG data augmentation: A review. Journal of Neural Engineering, 18(6), 061001.
- Goodfellow, I., Pouget-Abadie, J., Mirza, M., et al. (2014). Generative adversarial nets. Advances in Neural Information Processing Systems, 27.
- Kingma, D. P., and Welling, M. (2014). Auto-Encoding Variational Bayes. International Conference on Learning Representations.
- Pinaya, W. H. L., Graham, M. S., Kerfoot, E., et al. (2022). Generative AI for medical imaging: Extending the MONAI framework. arXiv:2212.07501.
