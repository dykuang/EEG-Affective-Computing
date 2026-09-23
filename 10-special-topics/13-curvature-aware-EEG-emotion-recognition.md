# Curvature-aware EEG Emotion Recognition

EEG emotion recognition often uses a graph to represent relations among electrodes, while the classifier treats emotion categories as unrelated labels. Graph curvature offers a way to examine the *local structure* of both graphs: which connections lie within dense neighborhoods, which may bridge different parts of a network, and how those distinctions could guide learning. The idea is to use curvature as a modeling signal, then test whether it adds value beyond the underlying graph itself.

There are two separate spaces. In **X-space**, nodes are EEG channels or estimated sources, and curvature describes the topology of their estimated connections. In **Y-space**, nodes are emotion categories, and curvature describes the structure of an explicitly defined label graph. Their edges have different meanings; a curved label graph is not a map of neural connectivity.

![Overview of curvature-aware EEG emotion recognition. EEG features form a sparse channel graph whose edge curvature can guide an encoder. Emotion categories form a separate label graph whose curvature can shape class costs. Predictions and class costs meet in a structured learning objective, followed by held-out evaluation.](figures/curvature-aware-eeg-overview.svg)

*Figure 1. Two graph spaces for curvature-aware recognition. Channel curvature can shape EEG representation learning; label curvature can shape the cost of classification errors. Each contribution should be evaluated separately and together.*

## The Research Question

A conventional channel graph records whether two EEG signals are connected and how strongly. Curvature adds information about the *neighborhood around an edge*. A connection inside a tightly linked group may play a different role from a connection between groups, even if the two edges have similar weights. On the label side, curvature can express that some emotion confusions traverse a close family of states while others cross a structural boundary.

This leads to three testable questions:

| Question | Possible use of curvature | Evidence needed |
| --- | --- | --- |
| Does channel topology help recognition? | Guide graph message passing, attention, or representation regularization | Improvement over the same graph without curvature |
| Does label topology help handle confusions? | Define class distances or a structured loss | Better held-out class-wise performance and calibration |
| Do the two spaces complement each other? | Combine an X-space model prior with a Y-space loss | An X-only, Y-only, and joint comparison |

Curvature is a structural inductive bias, not an emotion biomarker. Its interpretation depends on how the graph was built and whether its topology is stable.

## From Graphs to Edge Curvature

For a graph $G=(V,E,A)$, curvature assigns a value $\kappa_{ij}$ to an edge $(i,j)$ using the surrounding graph. **Ollivier–Ricci curvature** compares neighborhood distributions around the edge endpoints. One common form is

$$
\kappa_{ij}^{\mathrm{OR}}=1-\frac{W_1(m_i,m_j)}{d_G(i,j)},
$$

where $W_1$ is the transport distance between neighborhood measures $m_i$ and $m_j$, and $d_G$ is graph distance. **Augmented Forman curvature** is a simpler topology-based option. For an unweighted graph,

$$
\kappa_{ij}^{\mathrm{F}}=4-d_i-d_j+3|\mathcal N(i)\cap\mathcal N(j)|,
$$

where $d_i$ and $d_j$ are degrees and the final term counts common neighbors.

Positive curvature often occurs in locally redundant neighborhoods; negative curvature often occurs near bridges or branching regions. These are graph descriptions, not fixed biological meanings. In particular, a bridge between channel groups may carry useful affective information. The effect of strengthening or weakening such edges must be tested rather than assumed.

Curvature is most informative on a defensible **sparse** graph. On a nearly complete graph, local neighborhoods become similar and many edges lose distinct structural roles. The graph construction, sparsity rule, edge weights, and curvature definition should therefore be stated together.

## X-space: Curvature of EEG Connections

An X-space graph can be estimated from temporal EEG features such as differential entropy (DE), from raw-signal connectivity, from electrode geometry, or from a combination of these. The edge meaning must remain clear: correlation indicates association, a temporal predictive score indicates directed prediction under a model, and physical proximity is a spatial prior. None alone proves a direct neural mechanism or an emotional cause.

A practical conceptual pipeline is to estimate channel relations within training trials, aggregate them at the intended subject or dataset scope, retain a stable sparse graph with nonnegative association weights, and compute curvature on that graph. Signed relations can be retained as separate edge information. For a graph fitted in training fold $f$:

$$
\text{training EEG}^{(f)}\longrightarrow A^{(f)}\longrightarrow A_{\mathrm{sparse}}^{(f)}\longrightarrow\kappa^{(f)}.
$$

The original trials, rather than overlapping prediction windows, are the appropriate units for estimating temporal relations and assessing edge stability. Any graph derived from data must be fitted inside the training partition. Using the full dataset to build one graph before cross-validation would leak held-out information.

Curvature can enter an EEG model in several ways. One general form reweights existing edges:

$$
A_{ij}^{\kappa}=A_{ij}^{\mathrm{sparse}}\exp(\gamma\,\widetilde\kappa_{ij}),\qquad (i,j)\in E,
$$

where $\widetilde\kappa$ is a scaled curvature value and $\gamma$ controls its influence. The resulting graph may guide message passing, serve as an attention bias, or regularize channel representations. A learned graph can also use it as a prior while retaining the ability to adapt. The choice and sign of $\gamma$ are empirical questions; $\gamma=0$ recovers the same graph without curvature and is an essential control.

A fold-level graph is a natural starting point for cross-subject evaluation. Subject-specific graphs may be useful when training data from the same subject are available. Emotion-conditioned channel graphs require particular care: at inference time, the true test emotion is unknown, so it cannot be used to select a graph. These are alternative research extensions, not prerequisites for the core idea.

## Y-space: Curvature of Emotion Relations

A Y-space graph connects emotion categories according to a declared relation, such as psychological similarity, transition tendency, or an independently specified taxonomy. Curvature describes the topology of *that chosen relation*. A graph learned from model confusions answers a different question from one grounded in psychological theory; its origin should be reported.

One use is to turn the label graph into a cost of confusing classes. If $\ell_{ab}^{\kappa}$ is a curvature-adjusted length for a label edge, the cost between classes $a$ and $b$ can be the shortest-path distance:

$$
M_{ab}^{\kappa}=\min_{P:a\leadsto b}\sum_{(u,v)\in P}\ell_{uv}^{\kappa}.
$$

A classifier can then penalize probability assigned to classes far from the target more than probability assigned to nearby classes. For predicted probabilities $p_a$ and target class $y$, a simple structured term is $\mathcal L_Y=\sum_a p_a M_{ay}^{\kappa}$. Graph-Laplacian or optimal-transport losses are other possibilities, but the public idea does not depend on choosing one particular loss.

Label distances are assumptions about the task, not universal distances between emotions. With only a few categories, curvature can be sensitive to individual edges, so the label graph should be varied or independently justified. If the graph is estimated from labeled data, it too must be fitted without test labels. A structured loss should be compared with ordinary cross-entropy and with the same label graph before curvature adjustment.

## Bringing the Two Spaces Together

The two signals can be combined without merging their graphs:

$$
\mathcal L=\mathcal L_{\mathrm{CE}}+\lambda_X\mathcal L_X(A^\kappa)+\lambda_Y\mathcal L_Y(M^\kappa).
$$

Here $\mathcal L_{\mathrm{CE}}$ is the standard classification loss; $\mathcal L_X$ denotes an optional X-space graph or representation constraint; and $\mathcal L_Y$ uses the label cost. If X-space curvature is built directly into the encoder, $\mathcal L_X$ can be omitted. The coefficients express how strongly the two priors influence learning and should be selected on validation data.

The central research design is a small factorial comparison:

| Variant | Channel curvature | Label curvature |
| --- | --- | --- |
| Baseline | No | No |
| X only | Yes | No |
| Y only | No | Yes |
| X + Y | Yes | Yes |

This comparison reveals whether the spaces contribute separately, reinforce one another, or interfere. Each curved variant also needs a corresponding *uncurved* graph control; otherwise a gain could come from graph structure alone.

## Evaluation and Interpretation

| Claim | Useful check |
| --- | --- |
| Curvature adds information beyond connectivity | Compare the same sparse channel graph with and without curvature reweighting |
| The graph structure matters | Compare with graphs matched for size or degree but with altered edges |
| Label geometry improves error quality | Inspect class-wise recall, confusion patterns, and calibration alongside accuracy or macro-F1 |
| The result generalizes | Use held-out subjects, sessions, or datasets and report variation across splits |
| The topology is interpretable | Check edge and curvature stability across training resamples and reasonable graph-building choices |

Graph estimation, sparsity selection, curvature scaling, and loss selection belong inside the training and validation workflow. Test data should only assess the chosen design. Performance should also be reported at the unit relevant to the claim, such as trials or subjects, because many EEG windows may share one label.

A high-curvature edge is not automatically a true neural circuit, and a curved emotion graph does not establish a universal affective taxonomy. The strongest conclusion would be narrower: under a stated graph construction and evaluation protocol, curvature provides a stable, useful structural signal beyond uncurved graph baselines.

## References

- Forman, R. (2003). Bochner's method for cell complexes and combinatorial Ricci curvature. *Discrete and Computational Geometry*, 29, 323–374.
- Ollivier, Y. (2009). Ricci curvature of Markov chains on metric spaces. *Journal of Functional Analysis*, 256(3), 810–864.
- Sreejith, R. P., et al. (2016). Forman curvature for complex networks. *Journal of Statistical Mechanics: Theory and Experiment*, 2016(6), 063206.
- Samal, A., et al. (2018). Comparative analysis of two discretizations of Ricci curvature for complex networks. *Scientific Reports*, 8, 8650.
- Topping, J., et al. (2022). Understanding over-squashing and bottlenecks on graphs via curvature. *International Conference on Learning Representations*.
- Zhong, P., et al. (2020). EEG-based emotion recognition using regularized graph neural networks. *IEEE Transactions on Affective Computing*.
