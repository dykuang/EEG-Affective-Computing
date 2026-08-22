# Curvature-aware EEG Emotion Recognition

## 1. Method overview

This project introduces graph curvature into EEG emotion recognition in two
complementary spaces:

1. **X-space (data-side):** the 62 EEG channels are graph nodes. A channel graph
   is estimated from differential-entropy (DE) sequences using training data
   only, sparsified, and transformed by edge curvature before it is used by a
   graph layer, an attention layer, or a representation regularizer.
2. **Y-space (label-side):** emotion classes are graph nodes. Curvature changes
   the class-to-class ground distance used by the structured losses.

The two graphs answer different questions. The X-space graph describes how EEG
channels interact, whereas the Y-space graph describes the cost of confusing
emotion classes. They therefore enter the same training objective as separate
terms rather than being merged into one adjacency matrix.

The recommended first implementation is:

> Estimate one **fold-specific dataset graph** with robust DE correlation,
> preserve connectivity with a maximum spanning tree (MST), retain about 15% of
> all possible undirected edges as an initial value, compute augmented Forman
> and Ollivier--Ricci curvature, and use the curvature-reweighted graph as both
> a model prior and a hidden-representation smoothness regularizer. Tune graph
> density and all curvature coefficients on the validation split only.

Subject- and emotion-specific graphs should be added as hierarchical residuals
after this baseline is established. They are not interchangeable alternatives;
their valid use depends on the evaluation protocol, as detailed in Section 6.

---

## 2. Notation and leakage-free problem definition

Let a dataset contain $C=62$ channels, $B=5$ DE frequency bands, and $K$
emotion classes. An original trial $r$, before sliding-window segmentation, is
represented in one canonical layout as

$$
\mathbf X^{(r)}\in\mathbb R^{T_r\times C\times B},
$$

where $T_r$ is the number of DE time points. The three current loaders expose
different layouts and must be converted explicitly:

| Dataset / loader | Current DE layout | Canonical conversion |
|---|---:|---:|
| SEED-IV (`SEEDIV_data.py`) | $(C,T,B)$ | transpose to $(T,C,B)$ |
| SEED-V (`SEEDV_data.py`) | $(T,C,B)$ | unchanged |
| SEED-VII (`SEEDVII_data.py`) | $(T,B,C)$ | transpose to $(T,C,B)$ |

Graph estimation must use the full time sequence of each **training trial**. It
must not estimate a correlation matrix independently from a segment of length
one, even though the current experiment files often set `segment_length: 1`.
Sliding-window segments are model samples; original trials are graph-estimation
units.

For a cross-validation fold $f$, denote its training-trial set by
$\mathcal R_{\mathrm{tr}}^{(f)}$. Every preprocessing statistic, adjacency,
threshold, curvature, and class-conditional graph used in that fold is fitted
without validation or test trials:

$$
\mathcal R_{\mathrm{tr}}^{(f)}
\longrightarrow
\mathbf A_{\mathrm{raw}}^{(f)}
\longrightarrow
\mathbf A_{\mathrm{sp}}^{(f)}
\longrightarrow
\boldsymbol\kappa^{(f)}
\longrightarrow
\mathbf A_{\kappa}^{(f)}.
$$

After hyperparameters have been selected, it is acceptable to rebuild the graph
from train plus validation data for the final test run, provided that this rule
is used consistently for every compared method. A graph built once from the
entire dataset before cross-validation is data leakage.

### 2.1 DE normalization

For graph estimation, standardize each channel and band with statistics fitted
on the relevant training scope:

$$
z_{r,t,i,b}
=
\frac{x_{r,t,i,b}-\mu_{i,b}^{\mathrm{tr}}}
{\sigma_{i,b}^{\mathrm{tr}}+\varepsilon},
$$

where $i\in\{1,\ldots,C\}$, $b\in\{1,\ldots,B\}$, and
$\varepsilon>0$. The saved graph metadata must record whether statistics were
dataset-, subject-, or session-level. Correlations should first be computed
within trials and then aggregated; directly concatenating unrelated trials can
turn trial boundaries, stimulus order, or baseline shifts into spurious edges.

---

## 3. Discrete graph curvature

Let $G=(V,E,\mathbf A)$ be a sparse channel or emotion graph. Curvature is an
**edge attribute computed after sparsification**. Computing topology-based
curvature on a complete graph is nearly uninformative because every edge has a
similar, highly redundant neighborhood.

### 3.1 Forman and augmented Forman curvature

For an unweighted simple graph, the ordinary Forman--Ricci curvature of edge
$(i,j)$ is

$$
F_{ij}=4-d_i-d_j,
$$

where $d_i$ and $d_j$ are node degrees. The implementation in
`curvature_label.py` actually uses the triangle-augmented version

$$
F^{\#}_{ij}=4-d_i-d_j+3\,\tau_{ij},
\qquad
\tau_{ij}=|\mathcal N(i)\cap\mathcal N(j)|.
$$

Thus, the existing function named `compute_forman_curvature` should be described
as **augmented Forman curvature**. It uses only the binary topology: numerical
edge weights in the input adjacency do not affect $F^{\#}_{ij}$.

If edge weights must be retained, weighted Forman curvature can instead be used.
For node weights $w_i,w_j>0$ and edge weight $w_{ij}>0$,

$$
F^{w}_{ij}
=w_{ij}\left[
\frac{w_i}{w_{ij}}+\frac{w_j}{w_{ij}}
-\sum_{e_{ik}\sim i,\,e_{ik}\ne e_{ij}}
\frac{w_i}{\sqrt{w_{ij}w_{ik}}}
-\sum_{e_{j\ell}\sim j,\,e_{j\ell}\ne e_{ij}}
\frac{w_j}{\sqrt{w_{ij}w_{j\ell}}}
\right].
$$

Augmented Forman is the recommended fast baseline because it is deterministic
and inexpensive. Weighted Forman should be treated as a separate ablation, not
silently substituted for the current formula.

### 3.2 Ollivier--Ricci curvature

For each node $i$, define an idleness-$\alpha$ probability measure. For a
weighted nonnegative graph,

$$
\mu_i(k)=
\begin{cases}
\alpha, & k=i,\\[2mm]
(1-\alpha)\dfrac{A_{ik}}{\sum_{u\in\mathcal N(i)}A_{iu}},
& k\in\mathcal N(i),\\[3mm]
0, & \text{otherwise}.
\end{cases}
$$

Turn association strength into edge length with

$$
\ell_{ij}=\frac{1}{A_{ij}+\varepsilon},
$$

and let $d_G$ be the resulting shortest-path metric. The 1-Wasserstein
distance is

$$
W_1(\mu_i,\mu_j)
=
\min_{\boldsymbol\pi\ge 0}
\sum_{u,v}d_G(u,v)\pi_{uv},
$$

subject to

$$
\sum_v\pi_{uv}=\mu_i(u),
\qquad
\sum_u\pi_{uv}=\mu_j(v).
$$

Ollivier--Ricci curvature is then

$$
\kappa^{\mathrm{OR}}_{ij}
=1-\frac{W_1(\mu_i,\mu_j)}{d_G(i,j)}.
$$

The current `curvature_label.py` distributes neighbor mass uniformly rather
than proportionally to edge weight. That behavior is suitable for its current
unweighted emotion graph but should not be reused unchanged for weighted DE
graphs. A new X-space implementation should support weighted measures and
inverse-strength ground lengths explicitly. Use $\alpha=0.5$ as the initial
value and validate $\alpha\in\{0,0.25,0.5,0.75\}$.

### 3.3 Robust curvature normalization

Raw augmented Forman values and Ollivier--Ricci values live on different scales.
Exponentiating raw Forman values can also overflow or collapse edge weights. For
either curvature, normalize within each training-fold graph:

$$
z^{\kappa}_{ij}
=
\\mathrm{clip}\left(
\frac{\kappa_{ij}-\\mathrm{median}_{e\in E}\kappa_e}
{\\mathrm{IQR}_{e\in E}(\kappa_e)+\varepsilon},
-c,c
\right),
\qquad
\bar\kappa_{ij}=\frac{z^{\kappa}_{ij}}{c},
$$

with $c=3$ initially, so $\bar\kappa_{ij}\in[-1,1]$.

Two distinct curvature transforms are needed:

- **Conductance for message passing**

  $$
  A^{\kappa}_{ij}
  =A^{\mathrm{sp}}_{ij}\exp(\gamma\bar\kappa_{ij}).
  $$

- **Length for shortest paths or optimal transport**

  $$
  \ell^{\kappa}_{ij}
  =\frac{\exp(-\gamma\bar\kappa_{ij})}
  {A^{\mathrm{sp}}_{ij}+\varepsilon}.
  $$

For $\gamma>0$, positive-curvature redundant edges become stronger/shorter
and negative-curvature bridge edges become weaker/longer. This is a hypothesis,
not a universal biological rule: a negative-curvature inter-region bridge may
be discriminative for emotion. Therefore include $\gamma=0$ and negative
$\gamma$ in validation, for example
$\gamma\in\{-1,-0.5,0,0.5,1,2\}$, or learn a bounded scalar
$\gamma=\gamma_{\max}\tanh\theta$.

---

## 4. Y-space: curvature of the emotion graph

Let $G_Y=(V_Y,E_Y,\mathbf A_Y)$ contain $K$ emotion nodes. The current
configuration provides a hand-designed adjacency $\mathbf A_Y$. After
computing $\bar\kappa^Y$, define edge length

$$
\ell^Y_{ab}=\exp(-\beta_Y\bar\kappa^Y_{ab}),
$$

and obtain the all-pairs class cost matrix by shortest paths:

$$
M^Y_{ab}
=
\min_{P:a\leadsto b}\sum_{(u,v)\in P}\ell^Y_{uv},
\qquad
M^Y_{aa}=0.
$$

Normalize finite off-diagonal distances to $[0,1]$ before passing them to a
loss. This corresponds to `CurvatureDistance.compute_distance_matrix` in
`curvature_label.py`, with the recommended addition of robust curvature
normalization.

For prediction probabilities $\mathbf p_n$ and one-hot target
$\mathbf y_n$, `loss_curv.py` currently supports the following relevant
structured objectives.

### 4.1 Curvature-modified graph-Laplacian loss

First reweight the emotion adjacency,

$$
A^{Y,\kappa}_{ab}
=A^Y_{ab}\exp(\beta_Y\bar\kappa^Y_{ab}),
\qquad
\mathbf L_Y^{\kappa}
=\mathbf D_Y^{\kappa}-\mathbf A_Y^{\kappa},
\qquad
\mathbf H_Y^{\kappa}
=(\mathbf L_Y^{\kappa})^{\dagger}.
$$

The `diff` form in the current implementation is

$$
\mathcal L_{Y,\mathrm{GL}}
=\frac{1}{N}\sum_{n=1}^N
(\mathbf p_n-\mathbf y_n)^\top
\mathbf H_Y^{\kappa}
(\mathbf p_n-\mathbf y_n),
$$

whereas the `direct` form is

$$
\mathcal L_{Y,\mathrm{direct}}
=-\frac{1}{N}\sum_{n=1}^N
\mathbf p_n^\top\mathbf H_Y^{\kappa}\mathbf y_n.
$$

### 4.2 Curvature-aware optimal-transport loss

The entropy-regularized transport loss uses $\mathbf M_Y$ as ground cost:

$$
\mathcal L_{Y,\mathrm{OT}}
=\frac{1}{N}\sum_{n=1}^N
\min_{\mathbf P_n\in\Pi(\mathbf p_n,\mathbf y_n)}
\left[
\langle\mathbf P_n,\mathbf M_Y\rangle
-\varepsilon_H\mathcal H(\mathbf P_n)
\right],
$$

where

$$
\Pi(\mathbf p,\mathbf y)
=\{\mathbf P\ge0:\mathbf P\mathbf 1=\mathbf p,
\mathbf P^\top\mathbf 1=\mathbf y\},
\qquad
\mathcal H(\mathbf P)=-\sum_{a,b}P_{ab}\log(P_{ab}+\varepsilon).
$$

With an exactly one-hot target, unregularized OT reduces to the expected class
cost $\sum_a p_{n,a}M^Y_{a,y_n}$. This simple expected-cost baseline should be
included because it is faster and helps determine whether Sinkhorn iterations
provide additional value.

The label-side objective is

$$
\mathcal L_Y
\in
\{\mathcal L_{Y,\mathrm{GL}},
\mathcal L_{Y,\mathrm{direct}},
\mathcal L_{Y,\mathrm{OT}}\}.
$$

Do not enable several highly correlated Y-space losses simultaneously in the
first experiment. Select one with validation data and compare it with ordinary
cross-entropy.

---

## 5. X-space: estimating an EEG channel adjacency from DE

An adjacency estimator must specify four things: the observation unit, edge
score, aggregation scope, and conversion from a signed statistic to a
nonnegative graph. Curvature and the current DGCNN Laplacian require
nonnegative weights. If the estimator produces signed associations, retain the
sign as a separate edge feature or split it into

$$
A^+_{ij}=\max(S_{ij},0),
\qquad
A^-_{ij}=\max(-S_{ij},0),
$$

while using $|S_{ij}|$ as the curvature graph in the first implementation.

### 5.1 Recommended baseline: trial-wise robust correlation

For trial $r$ and band $b$, compute the Pearson correlation across time:

$$
\rho_{ijb}^{(r)}
=
\frac{
\sum_{t=1}^{T_r}(z_{r,t,i,b}-\bar z_{r,i,b})
(z_{r,t,j,b}-\bar z_{r,j,b})
}{
\sqrt{\sum_t(z_{r,t,i,b}-\bar z_{r,i,b})^2}
\sqrt{\sum_t(z_{r,t,j,b}-\bar z_{r,j,b})^2}
+\varepsilon
}.
$$

For resistance to outliers, Spearman correlation uses the same equation after
replacing observations by within-trial ranks:

$$
\rho_{ijb}^{S,(r)}
=\rho_P\!\left(
\\mathrm{rank}(\mathbf z_{r,:,i,b}),
\\mathrm{rank}(\mathbf z_{r,:,j,b})
\right).
$$

Aggregate trials in Fisher-$z$ space:

$$
u_{ijb}^{(r)}
=\\mathrm{arctanh}
\left(\\mathrm{clip}(\rho_{ijb}^{(r)},-1+\epsilon,1-\epsilon)\right),
$$

$$
\bar\rho_{ijb}
=\tanh\left(
\frac{\sum_{r\in\mathcal R_{\mathrm{scope}}}\omega_r u_{ijb}^{(r)}}
{\sum_{r\in\mathcal R_{\mathrm{scope}}}\omega_r}
\right),
\qquad
A^{\mathrm{corr}}_{ij}
=\sum_{b=1}^{B}\eta_b|\bar\rho_{ijb}|,
$$

where $\eta_b\ge0$ and $\sum_b\eta_b=1$. Start with
$\eta_b=1/B$. Use equal subject weight and then equal trial weight within each
subject so a subject with more windows does not dominate:

$$
\bar u_{ijb}
=\frac{1}{|\mathcal S|}\sum_{s\in\mathcal S}
\frac{1}{|\mathcal R_s|}\sum_{r\in\mathcal R_s}u_{ijb}^{(r)}.
$$

Pearson is the simplest primary baseline; Spearman is the robust ablation.

### 5.2 Conditional-dependence graph: partial correlation / Graphical Lasso

Marginal correlation can connect $i$ and $j$ only because both covary with a
third channel. Stack standardized training observations without crossing trial
boundaries, compute covariance $\widehat{\boldsymbol\Sigma}$, and estimate a
sparse precision matrix:

$$
\widehat{\boldsymbol{\Theta}}
=
\underset{\boldsymbol{\Theta}\succ 0}{\mathrm{arg\,min}}\;
\left[
\\mathrm{tr}\!\left(
\widehat{\boldsymbol{\Sigma}}\boldsymbol{\Theta}
\right)
-\log\det\!\left(\boldsymbol{\Theta}\right)
+\lambda_{\mathrm{gl}}
\sum_{i\ne j}|\Theta_{ij}|
\right].
$$

The partial correlation and nonnegative adjacency are

$$
\rho_{ij\mid V\setminus\{i,j\}}
=-\frac{\widehat\Theta_{ij}}
{\sqrt{\widehat\Theta_{ii}\widehat\Theta_{jj}}},
\qquad
A^{\mathrm{pcorr}}_{ij}
=\left|\rho_{ij\mid V\setminus\{i,j\}}\right|.
$$

Graphical Lasso produces sparsity through $\lambda_{\mathrm{gl}}$; do not also
force an arbitrary top percentage unless a common-density comparison is needed.
Select $\lambda_{\mathrm{gl}}$ by validation performance and graph stability.
Because DE bands differ, estimate one graph per band and average them, or use a
multi-task/group penalty; do not flatten the five bands and treat them as
independent time samples.

### 5.3 Nonlinear dependence: mutual information

For channel variables $Z_i,Z_j$, mutual information is

$$
I(Z_i;Z_j)
=\int p_{ij}(u,v)
\log\frac{p_{ij}(u,v)}{p_i(u)p_j(v)}\,du\,dv.
$$

A bounded monotone edge score is

$$
A^{\mathrm{MI}}_{ij}
=1-\exp\left(-\frac{I(Z_i;Z_j)}{\tau_I+\varepsilon}\right),
\qquad
\tau_I=\\mathrm{median}_{u<v,\;I_{uv}>0} I(Z_u;Z_v).
$$

Use a $k$-nearest-neighbor estimator and estimate within bands. MI can capture
nonlinear dependence, but with short trials it has higher variance than
correlation; it is therefore a secondary ablation rather than the first graph.

### 5.4 Directed predictive graph: sparse VAR / Granger score

For band $b$, model the channel vector $\mathbf z_t\in\mathbb R^C$ within
each trial by a vector autoregression (VAR):

$$
\mathbf z_t
=\sum_{\ell=1}^{p}\mathbf B_{\ell}\mathbf z_{t-\ell}
+\boldsymbol\epsilon_t.
$$

Since $C=62$ is large relative to a trial, use a group-sparse estimate:

$$
\min_{\{\mathbf B_\ell\}}
\sum_{r}\sum_{t=p+1}^{T_r}
\left\|
\mathbf z_t^{(r)}-
\sum_{\ell=1}^{p}\mathbf B_\ell\mathbf z_{t-\ell}^{(r)}
\right\|_2^2
+\lambda_{\mathrm{VAR}}
\sum_{i\ne j}
\left\|(B_{1,ij},\ldots,B_{p,ij})\right\|_2.
$$

Do not create lagged pairs across trial boundaries. A predictive Granger edge
from $j$ to $i$ can be scored by comparing reduced and full residuals:

$$
S_{j\rightarrow i}^{G}
=\log\frac{\\mathrm{Var}(\epsilon_{i}^{\mathrm{reduced}\;j})}
{\\mathrm{Var}(\epsilon_i^{\mathrm{full}})+\varepsilon},
\qquad
A^{G}_{ji}=\max(S_{j\rightarrow i}^{G},0).
$$

This is a directed **predictive-connectivity** graph. Calling it a causal graph
requires stronger assumptions about stationarity, omitted confounders, model
order, and interventions. DE sequences are temporally coarse and trials are
short, so sparse VAR should follow the correlation and partial-correlation
baselines rather than replace them.

For the current undirected curvature implementation, first use

$$
A^{G,\mathrm{sym}}_{ij}
=\frac{A^G_{ij}+A^G_{ji}}{2}
$$

to compute curvature, while retaining $A^G_{ij}-A^G_{ji}$ as an optional
directional edge feature. A genuinely directed Ollivier/Forman implementation
must be treated as a separate future method.

### 5.5 Raw-signal connectivity is not recoverable from DE alone

If the raw EEG is available, phase- and spectrum-based graphs are useful
controls. Phase-locking value (PLV) is

$$
\\mathrm{PLV}_{ij}
=\left|
\frac{1}{T}\sum_{t=1}^{T}
\exp\left(\mathrm i[\phi_i(t)-\phi_j(t)]\right)
\right|,
$$

and magnitude-squared coherence is

$$
\\mathrm{Coh}_{ij}(f)
=\frac{|S_{ij}(f)|^2}{S_{ii}(f)S_{jj}(f)+\varepsilon}.
$$

Neither phase difference $\phi_i-\phi_j$ nor cross-spectrum $S_{ij}$ can be
reconstructed from DE features. These methods therefore require the signal
loaders and must not be described as DE-derived adjacency estimators.

### 5.6 Spatial prior and learned residual

Electrode geometry can stabilize a noisy functional graph. If $\mathbf r_i$
is the 3-D coordinate of electrode $i$, define

$$
A^{\mathrm{geo}}_{ij}
=\exp\left(-\frac{\|\mathbf r_i-\mathbf r_j\|_2^2}{2\sigma_r^2}\right),
\qquad i\ne j.
$$

Fuse it with a statistical graph by a convex mixture

$$
\mathbf A^{0}
=\lambda_{\mathrm{stat}}\mathbf A^{\mathrm{stat}}
+(1-\lambda_{\mathrm{stat}})\mathbf A^{\mathrm{geo}},
$$

or by spatially damping unlikely long-range edges,

$$
A^{0}_{ij}
=A^{\mathrm{stat}}_{ij}(A^{\mathrm{geo}}_{ij})^{\nu}.
$$

Long-range inter-hemispheric edges can be real, so the convex mixture is the
safer initial choice. An instance-adaptive residual may later be learned from
channel embeddings $\mathbf h_i$:

$$
A^{\mathrm{inst}}_{ij}
=\\mathrm{softplus}\left(
\frac{(\mathbf W\mathbf h_i)^\top(\mathbf W\mathbf h_j)}{\sqrt d}
\right),
\qquad
\mathbf A^{\mathrm{eff}}
=(1-\lambda_a)\mathbf A^\kappa
+\lambda_a\mathbf A^{\mathrm{inst}}.
$$

The fixed DE graph supplies an identifiable prior; the learned residual handles
sample dynamics.

### 5.7 Estimator priority

| Priority | Estimator | Main benefit | Main risk |
|---:|---|---|---|
| 1 | Pearson / Spearman | stable, interpretable, easy to aggregate by trial | marginal and undirected |
| 2 | Graphical Lasso | conditional graph and built-in sparsity | sensitive to regularization and covariance estimation |
| 3 | sparse VAR / Granger score | directed temporal prediction | high sample demand; not automatically causal |
| 4 | mutual information | nonlinear dependence | high variance on short trials |
| control | geometry, PLV, coherence | biological/spatial or raw-signal baselines | not a DE-only graph |

---

## 6. Which scope should the graph and curvature use?

There is no universally correct scope. The graph must match the generalization
claim and the information available at inference.

### 6.1 Dataset-specific curvature: primary cross-subject choice

For fold $f$, aggregate all training subjects, trials, and emotions with equal
subject weighting:

$$
\mathbf A_D^{(f)}
=\\mathrm{Aggregate}
\left(\{\mathbf X^{(r)}:r\in\mathcal R_{\mathrm{tr}}^{(f)}\}\right),
\qquad
\boldsymbol\kappa_D^{(f)}
=\\mathrm{Curv}
\left(\\mathrm{Sparse}(\mathbf A_D^{(f)})\right).
$$

It is shared by every sample in the fold. This is the lowest-variance estimate
and the recommended default for x-subject evaluation. “Dataset-specific” must
mean **training-fold-specific**, not estimated once from all subjects.

It can participate in training as:

1. a fixed adjacency for graph convolution;
2. an initialization/prior for a learnable adjacency;
3. an attention bias;
4. a curvature-weighted representation regularizer.

### 6.2 Subject-specific curvature: valuable in x-trial, restricted in x-subject

For training trials belonging to subject $s$, estimate $\mathbf A_s$, then
shrink it toward the stable dataset graph:

$$
\widetilde{\mathbf A}_s
=\lambda_s\mathbf A_s+(1-\lambda_s)\mathbf A_D,
\qquad
\lambda_s=\frac{n_s}{n_s+\tau_s},
$$

where $n_s$ is the number of valid subject-level training trials and
$\tau_s>0$ controls shrinkage. Sparsify and compute curvature **after** this
mixture so the curvature corresponds to the graph actually used.

- In **x-trial**, training and test trials contain the same subjects. A graph
  for subject $s$ may be built from that subject's training trials and then
  used for their validation/test trials. This is valid and is the preferred
  place to test subject-specific curvature.
- In strictly inductive **x-subject**, no subject graph exists for an unseen
  test subject. Use $\mathbf A_D$ at test time. A graph estimated from
  unlabeled test-subject calibration data is a transductive/domain-adaptation
  setting and must be reported separately; test labels must never be used.

Subject ID must therefore be returned as sample metadata whenever subject graphs
are enabled.

### 6.3 Emotion-specific curvature: auxiliary multi-graph, never oracle routing

For each emotion $y$, estimate $\mathbf A_y$ from training trials whose label
is $y$:

$$
\mathbf A_y
=\\mathrm{Aggregate}
\left(\{\mathbf X^{(r)}:
r\in\mathcal R_{\mathrm{tr}},\;y_r=y\}\right).
$$

Shrink it toward $\mathbf A_D$ when class samples are limited:

$$
\widetilde{\mathbf A}_y
=\lambda_y\mathbf A_y+(1-\lambda_y)\mathbf A_D,
\qquad
\lambda_y=\frac{n_y}{n_y+\tau_y}.
$$

At inference, the true emotion is unknown. Selecting $\mathbf A_y$ using the
ground-truth test label is label leakage. Two valid uses are:

1. **Training-only conditional regularization:** use the known training label to
   select an emotion graph for an auxiliary loss; inference uses
   $\mathbf A_D$.
2. **Prediction-routed mixture:** obtain preliminary probabilities
   $q_y(\mathbf x)$ from a dataset-graph branch and form

   $$
   \mathbf A_{\mathrm{mix}}(\mathbf x)
   =\sum_{y=1}^{K}q_y(\mathbf x)\widetilde{\mathbf A}_y.
   $$

   During early training use scheduled teacher forcing,

   $$
   \widetilde q_y^{(e)}
   =\xi_e\,\mathbb 1[y=y_r]
   +(1-\xi_e)\\mathrm{stopgrad}(q_y),
   \qquad \xi_e\downarrow0,
   $$

   so training converges to the same predicted routing used at test time.

An emotion graph is most defensible if its topology is stable across subjects.
Report between-subject edge-selection frequency for every class.

### 6.4 Subject-emotion-specific curvature: only with hierarchical shrinkage

A direct $\mathbf A_{s,y}$ often has too few trials. If explored, use nested
shrinkage:

$$
\widetilde{\mathbf A}_{s,y}
=\lambda_{s,y}\mathbf A_{s,y}
+(1-\lambda_{s,y})
\left[
\omega\widetilde{\mathbf A}_s
+(1-\omega)\widetilde{\mathbf A}_y
\right],
$$

with all coefficients chosen on validation data. This is a later ablation, not
the primary method.

### 6.5 Scope recommendation by protocol

| Protocol | Main graph | Optional extension | Invalid shortcut |
|---|---|---|---|
| x-subject, inductive | fold-specific dataset graph | training-subject emotion graphs with prediction routing | constructing a test-subject graph from labeled or full test data |
| x-subject, adaptation | dataset graph | unlabeled calibration subject graph, reported separately | mixing calibration and evaluation samples without disclosure |
| x-trial | shrunken subject graph plus dataset backbone | emotion-conditioned residual | using held-out trials to estimate the subject graph |
| dataset transfer | source-dataset graph or shared geometric prior | target unlabeled calibration graph | pooling target test labels into graph construction |

---

## 7. Sparsification: how many edges should be retained?

For an undirected graph with $C$ nodes, define density

$$
q=\frac{2|E|}{C(C-1)}.
$$

With $C=62$, there are

$$
M=\frac{62\times61}{2}=1891
$$

possible edges. Thus 10%, 15%, and 20% correspond to approximately 189, 284,
and 378 edges, with average degrees 6.1, 9.2, and 12.2 respectively.

There is no theoretically universal “best top percentage.” The answer depends
on estimator variance, scope, model depth, and protocol. The recommended search
is

$$
q\in\{0.05,0.10,0.15,0.20,0.25,0.30\},
$$

with **15% as the initial engineering default**, selected on validation macro-F1
or balanced accuracy. Never choose $q$ from test accuracy.

### 7.1 MST plus global top-density rule

Plain global thresholding can disconnect weak but important channels, causing
infinite shortest paths and unstable curvature. Let $T_{\max}$ be the maximum
spanning tree of the nonnegative association matrix and

$$
m_q=\max\left(C-1,\left\lfloor qM+\tfrac12\right\rfloor\right).
$$

Keep

$$
E_q
=E(T_{\max})
\cup
\\mathrm{Top}_{m_q-(C-1)}
\left(E\setminus E(T_{\max});A_{ij}\right).
$$

This fixes density while guaranteeing connectivity. The MST alone has only
$(C-1)/M\approx3.2\%$ density and should be a lower-bound ablation, not the
default graph.

### 7.2 Local top-$k$ alternative

Global top-$q$ can over-concentrate edges around hubs. A local alternative
keeps the $k$ strongest neighbors of each node:

$$
(i,j)\in E_k
\iff
j\in\\mathrm{TopK}_k(A_{i,:})
\lor
i\in\\mathrm{TopK}_k(A_{j,:}).
$$

Validate $k\in\{4,6,8,10,12\}$, add an MST if needed, and report the resulting
density. Use the union rule above for coverage; the intersection rule is much
sparser and often disconnects the graph.

### 7.3 Stability selection

Bootstrap original training trials (not individual overlapping windows) $R$
times. If $I_{ij}^{(r)}$ indicates that edge $(i,j)$ is selected in bootstrap
$r$, its stability is

$$
\pi_{ij}
=\frac{1}{R}\sum_{r=1}^{R}I_{ij}^{(r)}.
$$

Prefer edges with $\pi_{ij}\ge\pi_0$, where
$\pi_0\in\{0.6,0.7,0.8,0.9\}$ is validated, and then apply the MST+density rule.
This is more defensible than claiming that one percentage is universally
optimal.

Graph-selection criteria should combine downstream validation performance with
topological quality:

$$
J(q)
=\\mathrm{MacroF1}_{\mathrm{val}}(q)
-\lambda_{\mathrm{var}}\\mathrm{SD}_{\mathrm{bootstrap}}(q)
-\lambda_{\mathrm{disc}}N_{\mathrm{components}}(q).
$$

---

## 8. How X-space curvature enters model training

### 8.1 Curvature-aware normalized adjacency

After curvature reweighting, add self-loops and normalize:

$$
\widetilde{\mathbf A}_{\kappa}
=\mathbf A_{\kappa}+\mathbf I,
\qquad
\widehat{\mathbf A}_{\kappa}
=\widetilde{\mathbf D}_{\kappa}^{-1/2}
\widetilde{\mathbf A}_{\kappa}
\widetilde{\mathbf D}_{\kappa}^{-1/2}.
$$

A graph-convolution layer is then

$$
\mathbf H^{(\ell+1)}
=\sigma\left(
\widehat{\mathbf A}_{\kappa}
\mathbf H^{(\ell)}\mathbf W^{(\ell)}
\right).
$$

For the current `DGCNN.py`, the safest integration is not to overwrite its
learnable adjacency. Blend a symmetric learned residual with the prior:

$$
\mathbf A_{\theta}
=(1-\lambda_a)\mathbf A_{\kappa}
+\lambda_a\\mathrm{softplus}
\left(\frac{\boldsymbol\Theta+\boldsymbol\Theta^\top}{2}\right),
\qquad
\\mathrm{diag}(\mathbf A_\theta)=0.
$$

The current DGCNN adjacency is unconstrained before ReLU and may be asymmetric;
symmetrization is important when using a symmetric normalized Laplacian and
undirected curvature.

### 8.2 Adjacency-prior regularization

Allow adaptation while keeping the learned graph near its DE/curvature prior:

$$
\mathcal L_{\mathrm{adj}}
=\frac{\|\mathbf A_\theta-\mathbf A_\kappa\|_F^2}
{\|\mathbf A_\kappa\|_F^2+\varepsilon}
+\lambda_1\|\mathbf A_\theta\|_1.
$$

An epoch-dependent $\lambda_a$ can start near zero and increase so the model
first learns from the stable prior and later fits residual connections.

### 8.3 Curvature-weighted representation smoothness

Let $\mathbf H_n\in\mathbb R^{C\times d}$ be channel embeddings for sample
$n$, and let

$$
\mathbf L_\kappa
=\mathbf D_\kappa-\mathbf A_\kappa.
$$

The smoothness loss is

$$
\mathcal L_{X,\mathrm{smooth}}
=\frac{1}{N}\sum_{n=1}^{N}
\frac{\\mathrm{tr}(\mathbf H_n^\top
\mathbf L_\kappa\mathbf H_n)}
{\sum_{i,j}A^\kappa_{ij}+\varepsilon}
=\frac{1}{2N}\sum_n
\frac{\sum_{i,j}A^\kappa_{ij}
\|\mathbf h_{n,i}-\mathbf h_{n,j}\|_2^2}
{\sum_{i,j}A^\kappa_{ij}+\varepsilon}.
$$

For $\gamma>0$, this smooths strongly inside positive-curvature neighborhoods
and relaxes smoothing across negative-curvature bottlenecks, which may reduce
over-smoothing. Validate the sign of $\gamma$ because bridge communication can
also carry emotional information.

### 8.4 Curvature as a node feature

Summarize incident edge curvature at node $i$:

$$
\kappa_i
=\frac{\sum_{j\in\mathcal N(i)}A^{\mathrm{sp}}_{ij}\bar\kappa_{ij}}
{\sum_{j\in\mathcal N(i)}A^{\mathrm{sp}}_{ij}+\varepsilon},
\qquad
d_i^w=\sum_jA^{\mathrm{sp}}_{ij}.
$$

Augment the initial channel feature with static topology:

$$
\mathbf h_{n,i}^{(0)}
=\left[\mathbf x_{n,i}\,\|\,\kappa_i\,\|\,\log(1+d_i^w)\right].
$$

This is useful for non-graph backbones as well, but it should be tested
separately from adjacency reweighting to identify the source of any gain.

### 8.5 Curvature bias for channel attention

For Transformer-style channel attention, define

$$
B_{ij}^{\kappa}=\log(A_{ij}^{\kappa}+\varepsilon)
$$

and use

$$
\\mathrm{Attn}(\mathbf Q,\mathbf K,\mathbf V)
=\\mathrm{softmax}\left(
\frac{\mathbf Q\mathbf K^\top}{\sqrt d}
+\lambda_B\mathbf B^{\kappa}
\right)\mathbf V.
$$

This provides a direct integration path for Conformer/Transformer-like models
without forcing them into a GCN architecture.

### 8.6 Full objective

With ordinary cross-entropy

$$
\mathcal L_{\mathrm{CE}}
=-\frac1N\sum_{n=1}^N\log p_{n,y_n},
$$

the complete training objective is

$$
\boxed{
\mathcal L
=\mathcal L_{\mathrm{CE}}
+\lambda_Y\mathcal L_Y
+\lambda_X\mathcal L_{X,\mathrm{smooth}}
+\lambda_A\mathcal L_{\mathrm{adj}}
}
$$

with absent terms set to zero for models that do not learn an adjacency. Warm up
with cross-entropy alone for $E_0$ epochs and then ramp each regularizer:

$$
\lambda_m(e)
=\lambda_m^{\max}
\min\left(1,\frac{\max(0,e-E_0)}{E_{\mathrm{ramp}}}\right),
\qquad m\in\{X,Y,A\}.
$$

This prevents an initially inaccurate classifier or learned adjacency from
dominating optimization.

---

## 9. End-to-end method algorithm

For every fold and every graph scope enabled by the experiment:

1. Split subjects/trials into train, validation, and test **before** graph
   estimation.
2. Load complete DE trials, convert them to $(T,C,B)$, and attach
   `subject_id`, `session_id`, `trial_id`, and `emotion_id` metadata.
3. Fit normalization statistics using the training scope only.
4. Estimate trial-wise band graphs with Pearson correlation as the primary
   method; aggregate in Fisher-$z$ space with equal subject weighting.
5. If subject/emotion graphs are enabled, shrink them toward the dataset graph.
6. Build a nonnegative association matrix and preserve signs separately if
   required.
7. Sparsify with MST plus top-$q$, initially $q=0.15$; choose $q$ from
   the validation grid.
8. Compute augmented Forman and/or weighted Ollivier--Ricci curvature on the
   sparse graph, robustly normalize it, and construct $\mathbf A_\kappa$ and
   $\boldsymbol\ell_\kappa$.
9. Save graph artifacts and all construction metadata. Do not recompute a
   static graph inside every minibatch.
10. Train with the curvature adjacency/prior and the selected X- and Y-space
    losses.
11. Select adjacency estimator, density, curvature type, $\gamma$, and loss
    coefficients with validation data only.
12. Evaluate segment- and trial-level predictions on the held-out test split.

---

## 10. Experimental design and ablations

### 10.1 Required incremental comparison

The central $2\times2$ comparison is:

| Model | X-space curvature | Y-space curvature |
|---|---:|---:|
| baseline | no | no |
| X only | yes | no |
| Y only | no | yes |
| X + Y | yes | yes |

Do not compare only baseline versus X+Y; otherwise the contribution of each
space is unidentifiable.

### 10.2 X-space ablation order

1. no graph prior versus raw sparse adjacency versus curvature-reweighted
   adjacency;
2. Pearson versus Spearman versus Graphical Lasso;
3. density $q\in\{5,10,15,20,25,30\}\%$;
4. no connectivity constraint versus MST+top-$q$;
5. augmented Forman versus Ollivier--Ricci;
6. $\gamma=0$, positive $\gamma$, and negative $\gamma$;
7. fixed graph versus learned residual plus adjacency regularization;
8. dataset versus shrunken subject versus emotion-routed graph;
9. graph convolution versus attention bias versus smoothness loss;
10. real graph versus a random graph matched for density and, preferably,
    degree distribution.

The random matched control is important: it tests whether a gain comes from the
estimated geometry rather than merely regularizing with any sparse graph.

### 10.3 Metrics and graph diagnostics

Report accuracy, macro-F1, balanced accuracy, and per-class recall. Because many
overlapping segments share a trial label, also average segment probabilities
within each `trial_id` and report trial-level metrics.

For every learned graph, report:

$$
\text{density},\quad
N_{\mathrm{components}},\quad
\text{mean degree},\quad
\text{degree distribution},\quad
\\mathrm{median}(\kappa),\quad
\\mathrm{IQR}(\kappa),
$$

plus bootstrap edge stability and graph overlap between folds. The Jaccard
overlap of two edge sets is

$$
J(E_a,E_b)=\frac{|E_a\cap E_b|}{|E_a\cup E_b|}.
$$

Use subjects, not segments, as the independent units for cross-subject
confidence intervals and paired significance tests. Repeat training with
multiple seeds and keep graph construction deterministic for a fixed fold and
seed.

---

## 11. Recommended research sequence

1. **Stage A -- verify the graph:** implement fold-specific Pearson graphs,
   MST+15%, augmented Forman, and graph diagnostics without changing a model.
2. **Stage B -- isolate curvature:** compare raw adjacency with
   curvature-reweighted adjacency in DGCNN, including $\gamma=0$ and a
   density-matched random graph.
3. **Stage C -- add training regularization:** add
   $\mathcal L_{X,\mathrm{smooth}}$, then the learned adjacency residual.
4. **Stage D -- compare estimators:** add Spearman and Graphical Lasso. Add
   sparse VAR only if complete DE trials provide enough temporal observations.
5. **Stage E -- compare scopes:** use shrunken subject graphs for x-trial and
   prediction-routed emotion graphs for x-subject.
6. **Stage F -- combine spaces:** add the best validated Y-space loss and run
   the full $2\times2$ X/Y ablation.

The minimum defensible paper claim is not “curvature always improves EEG emotion
recognition.” It is:

> Under a leakage-free fold protocol, a sparse DE-derived channel graph supplies
> a reproducible topology; curvature then changes message passing or
> regularization beyond what the same raw adjacency provides. Its value is
> established only if it outperforms the raw-adjacency, density-matched random,
> and $\gamma=0$ controls across subjects and seeds.

---

## 12. References

1. R. P. Sreejith et al., “Forman curvature for complex networks,” *Journal of
   Statistical Mechanics: Theory and Experiment*, 2016.
   <https://doi.org/10.1088/1742-5468/2016/06/063206>
2. A. Samal et al., “Comparative analysis of two discretizations of Ricci
   curvature for complex networks,” *Scientific Reports*, 2018.
   <https://doi.org/10.1038/s41598-018-27001-3>
3. Y. Ollivier, “Ricci curvature of Markov chains on metric spaces,” *Journal of
   Functional Analysis*, 2009.
   <https://doi.org/10.1016/j.jfa.2008.11.001>
4. J. Friedman, T. Hastie, and R. Tibshirani, “Sparse inverse covariance
   estimation with the graphical lasso,” *Biostatistics*, 2008.
   <https://doi.org/10.1093/biostatistics/kxm045>
5. C. W. J. Granger, “Investigating Causal Relations by Econometric Models and
   Cross-spectral Methods,” *Econometrica*, 1969.
   <https://doi.org/10.2307/1912791>
6. T. Song et al., “Instance-Adaptive Graph for EEG Emotion Recognition,”
   *AAAI*, 2020. <https://doi.org/10.1609/aaai.v34i03.5656>
7. P. Zhong et al., “EEG-Based Emotion Recognition Using Regularized Graph
   Neural Networks,” *IEEE Transactions on Affective Computing*, 2020/2022.
   <https://doi.org/10.1109/TAFFC.2020.2994159>
8. Z. Wang et al., “Phase-Locking Value Based Graph Convolutional Neural
   Networks for Emotion Recognition,” *IEEE Access*, 2019.
   <https://doi.org/10.1109/ACCESS.2019.2927768>
9. J. Topping et al., “Understanding over-squashing and bottlenecks on graphs
   via curvature,” *ICLR*, 2022. <https://arxiv.org/abs/2111.14522>
10. M. G. Tewarie et al., “Graph Analysis and Modularity of Brain Functional
    Connectivity Networks: Searching for the Optimal Threshold,” *Frontiers in
    Neuroscience*, 2017. <https://doi.org/10.3389/fnins.2017.00441>
