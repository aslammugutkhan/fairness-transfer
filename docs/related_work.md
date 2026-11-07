# Related work

Sixteen papers, each checked against its published record on 31 October 2026 (links below). The characterisations, and
the last column in particular, are ours. Both students must have read the papers they discuss in the defence.

| # | Work | Shift assumption | Data | Graphical model | Fairness notion | Method / inference | Limitation for this question |
|---|---|---|---|---|---|---|---|
| 1 | [Ding, Hardt, Miller, Schmidt (NeurIPS 2021)](https://arxiv.org/abs/2108.04884), "Retiring Adult" | Empirical shift across U.S. states and from 2014 to 2018 | ACS PUMS via Folktables (ACSIncome etc.) | none | DP (main), TPR gaps | gradient-boosted trees with fairness interventions; cross-state and cross-year evaluation | Documents variation; no label-budgeted adaptation |
| 2 | [Shao et al. (IJCAI 2024)](https://www.ijcai.org/proceedings/2024/909), survey | Covariate, label, concept, demographic, dependence and hybrid shift | survey | taxonomy | many | survey of six method families | Motivates the gap; no factor-level BN adaptation |
| 3 | [Hardt, Price, Srebro (NeurIPS 2016)](https://arxiv.org/abs/1610.02413) | i.i.d. | FICO case study | none | EOdds, EOpp | post-processing to group-specific thresholds | Thresholds fitted on one distribution |
| 4 | [Agarwal et al. (ICML 2018)](https://proceedings.mlr.press/v80/agarwal18a.html) | i.i.d. | benchmark datasets | none | DP, EOdds | reduction to cost-sensitive classification | Constraint certified only in the training distribution |
| 5 | [Kamiran & Calders (KAIS 2012)](https://doi.org/10.1007/s10115-011-0463-8) | i.i.d. | benchmark datasets | none | DP (discrimination score) | massaging, reweighing, sampling | Weights computed on the source only |
| 6 | [Shimodaira (2000)](https://doi.org/10.1016/S0378-3758(00)00115-4) | Covariate shift, P(Y∣X) invariant | — | none | — | importance-weighted likelihood | Invalid when P(Y∣X) changes |
| 7 | [Singh, Singh, Mhasawade, Chunara (FAccT 2021)](https://doi.org/10.1145/3442188.3445865) | Covariate shift described by a causal graph | synthetic flu diagnosis; MIMIC-III kidney injury | causal DAG | EOdds (also DP, EOpp) | feature-subset selection by d-separation, then a fair reduction; reweighting with unlabelled target data | Needs a causal graph; no label-budget factor update |
| 8 | [Rezaei, Liu, Memarrast, Ziebart (AAAI 2021)](https://doi.org/10.1609/aaai.v35i11.17135) | Covariate shift, unlabelled target | COMPAS, German credit | none | EOpp (TPR parity) | distributionally robust classifier with a target fairness penalty | Assumes P(Y∣X) invariant |
| 9 | [Giguere et al. (ICLR 2022)](https://openreview.net/forum?id=wbPObLm6ueA) | Demographic shift (subgroup probabilities change) | UCI Adult; UFRGS entrance exam | none | DP, disparate impact (EOpp, EOdds, predictive equality in appendix) | high-confidence guarantees (Shifty) without target data | Only subgroup proportions change |
| 10 | [Chen, Raab, Wang, Liu (NeurIPS 2022)](https://proceedings.neurips.cc/paper_files/paper/2022/hash/4937610670be26d651ecdb4f2206d95f-Abstract.html) | Bounded covariate or label shift | deterministic models + real data | none | DP, EOdds | transferability bounds | Bounds, not adaptation |
| 11 | [Schrouff et al. (NeurIPS 2022)](https://proceedings.neurips.cc/paper_files/paper/2022/hash/7a969c30dc7e74d4e891c8ffb217cf79-Abstract.html) | Shift structure characterised by conditional-independence tests | dermatology images; MIMIC-III ICU stay | causal DAG | subgroup accuracy gaps, DP, EOdds | diagnosis; source-fitted DP/EOdds post-processing failed to transfer | Closest idea; diagnosis only, no CPT-level repair |
| 12 | [Pearl & Bareinboim (AAAI 2011)](https://doi.org/10.1609/aaai.v25i1.7861) | Selection diagrams (S-nodes) | — | causal DAG + S | — | transport formulas | Identification theory; no fairness, no finite samples |
| 13 | [Subbaswamy, Schulam, Saria (AISTATS 2019)](https://proceedings.mlr.press/v89/subbaswamy19a.html) | Unstable mechanisms marked in a known selection diagram | simulated + real | DAG | — | graph surgery (surgery estimator) | Invariance instead of adaptation; no fairness |
| 14 | [Magliacane et al. (NeurIPS 2018)](https://proceedings.neurips.cc/paper/2018/hash/39e98420b5e98bfbdc8a619bef7b8f61-Abstract.html) | Interventions with unknown targets across domains | simulated + real | causal structure inferred from pooled data | — | invariant feature-set selection | No fairness; no label budget |
| 15 | [Zhang, Schölkopf, Muandet, Wang (ICML 2013)](https://proceedings.mlr.press/v28/zhang13d.html) | Target, conditional and generalised target shift | — | Y → X | — | kernel mean matching | No fairness |
| 16 | [Lipton, Wang, Smola (ICML 2018)](https://proceedings.mlr.press/v80/lipton18a.html) | Label shift | — | — | — | black-box shift estimation | Label shift only |

Recent work (2021 or later): rows 1, 2, 7, 8, 9, 10, 11.

## Verified gap

Fairness under distribution shift is established research (row 2). Graph-based work diagnoses shift (row 11), uses a
causal graph under covariate shift (row 7), or learns predictors that transport (rows 13, 14). We did not find a study
that (i) updates only a few BN factors under a fixed target-label budget, (ii) re-selects equalized-odds thresholds
using only the density ratio of those factors instead of the few labelled target rows or a global covariate-shift
weight, and (iii) compares this with global reweighting, threshold adaptation and full re-estimation at equal budgets
with controlled composition-vs-mechanism shifts.

## Closest reproduced baseline and how the others are covered

| Line of work | Our implementation | Notes |
|---|---|---|
| Covariate-shift fairness correction (rows 6–8) | **IW-EO**: domain classifier (logistic, C = 0.1) between source validation and adaptation covariates, weights capped at the 99th percentile, importance-weighted warm-start refit on 30,000 source rows, EO post-processing on the reweighted source validation split | Restricted reproduction: no distributionally robust objective (row 8) and no d-separation feature selection (row 7) |
| Group-threshold post-processing (row 3) | **EO-S** (source), **TT** (target labels), and the shared deterministic EO routine | Deterministic thresholds, no randomisation |
| Reweighing (row 5) | **RW-S** | Single threshold |
| Graph-based shift diagnosis (row 11) | factor-shift diagnostic (`results/tables/factor_shift_explanation.csv`, Fig. 5) | Associational graph, CPT-level |
| Selection diagrams (row 12) | the adapted network P(V ∣ S = t) with S as an extra parent of the updated factors | Interpretation only |
