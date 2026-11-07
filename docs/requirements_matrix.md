# Requirement-to-evidence matrix

Sources: the capstone brief (shared protocol rules 1–11, causal and inference safeguards, common deliverables; Problem
5, pages 17–19) and the repository guide. Everything is produced by one notebook, `problem5_fairness_transfer_bn.ipynb` (Run all); "§" numbers below are its sections.
Artifacts are relative to `results/` unless a path is given. Report = `report/AFGML_Report.pdf` (sections §1–§5);
slides = `slides/AFGML_Slides.pdf` (slide numbers). Status: complete, partial, failed or missing; N/A rows are listed
separately at the end.

## Common requirements

| ID | Source requirement | Config and command | Artifact and key finding | Status / limitation | Report / slide |
|---|---|---|---|---|---|
| C-DATA | Rule 1: record dataset version, source, licence, inclusion criteria, label construction, exclusions; keep recorded demographic categories | notebook §1.3, §2.1 | `tables/data_provenance.csv` (URLs, SHA-256, access time, rows); Folktables check passed in all six domains; `docs/data_card.md`, `data/README.md` | complete | §2; slide 4 |
| C-LEAK | Rule 2: fit preprocessing, discretisation, graph, calibration and thresholds on train/validation only; freeze test protocol | frozen `CFG` (§1.1); self-check §2.5 | test labels shuffled → every method's decisions unchanged; adaptation sample drawn only from the pool | complete; smoke tests displayed some transfer metrics during development (`docs/proposal.md` §5) | §3; slide 7 |
| C-BASE | Rule 3: same information, splits and tuning budget; identify methods needing protected attributes at prediction | `run_one` (§1.9) | one run gives every method the same n target persons; column "protected attributes at prediction time" in `tables/exp_transfer_summary.csv` | complete | §3; slides 6–7 |
| C-SPLIT | Rule 4: problem-specific splitting (household) | `split_fracs`, `split_seed` | `household_parts` (§1.3); self-check §2.5 | complete | §2; slide 4 |
| C-SEED | Rule 5: ≥ 5 seeds for stochastic procedures | `seeds`, `seeds_other`, `robust_seeds`, `tradeoff_seeds` | 5 seeds at every budget and robustness setting; seed sd columns in `tables/exp_transfer_summary.csv` | complete; simulation uses 8 replications | §3; slide 7 |
| C-STAT | Rule 6: paired differences and 95 % intervals, ≥ 1,000 bootstrap replicates, resample the independent unit | §2.9, `n_boot = 2000` | `metrics/confirmatory_tests_holm.csv`: paired household bootstrap, fixed decisions, same resamples for all methods; e.g. BN-LFA − TT = −0.065 [−0.070, −0.047] at n = 1,000 | complete; fixed-model (evaluation) uncertainty; training variability reported as seed sd | §4 table note; slide 8 |
| C-SMALL | Rule 7: group sizes and positive / negative denominators; flag < 30 as exploratory; do not drop groups silently | §2.9 | `metrics/per_group_metrics.csv` (persons, positives, negatives, selection rate, TPR, FPR, support label for every group); `tables/per_group_summary_n1000.csv` | complete | §2, §5; slide 4 |
| C-SEL | Rule 8: preregister primary predictive metric, primary fairness metric, acceptable utility loss; trade-off curves; matched-utility operating points; validation-selected operating points | `delta_ba: 0.02` | BA, ΔEOdds, δ = 0.02 (`docs/proposal.md` §4); `figures/fig08_tradeoff_curves.png`, `tables/tradeoff_curves_mean.csv`; BA within 0.003 across compared methods | complete | §2, §4; slides 6, 8 |
| C-HOLM | Rule 9: confirmatory vs exploratory; Holm for the declared family; effect sizes | §2.9 | 6-test family with Holm p; exploratory comparisons unadjusted in `metrics/exploratory_tests_vs_no_adaptation.csv` | complete | §3–§4; slides 7–8 |
| C-GM | Rule 10: normalisation, factor scopes, evidence handling, CI checks; VE and clique-tree agreement on a tractable instance within a stated tolerance | §2.3; self-checks §2.5 | `tables/inference_agreement_checks.csv`: max abs diff 1.3 × 10⁻¹⁵ (tolerance 10⁻⁹) over 20 queries with 0–3 evidence variables, full network and composition sub-network; small random network and factor algebra in §2.5 | complete | §2; slide 5, backup 2 |
| C-CI | Rule 10 / safeguards: relevant conditional independences | §2.3 | `tables/independence_checks.csv`: every local Markov statement is a d-separation; largest bias-corrected CMI 0.047 bits | complete | backup 2 |
| C-ORDER | Safeguard: elimination order changes computation, not the posterior | §2.3 | `tables/elimination_complexity.csv` (peak factor size and operations per order); all orders agree | complete | backup 2 |
| C-SIM | Rule 11: label synthetic experiments | §2.7 | every simulation table and figure says SYNTHETIC | complete | §4; slide 9 |
| C-CAUSAL | Safeguard: learned structure is not causal; interventions and counterfactuals need structural equations | — | graph read associationally (`docs/assumptions.md` A3); no interventions or counterfactuals used | N/A (no causal claim) | §2, §5 |
| D1 | Research proposal and related-work comparison table | — | `docs/proposal.md` (with dated amendments), `docs/related_work.md` (16 papers) | complete | §1; slide 3 |
| D2 | Data card, model card, assumptions register | — | `docs/data_card.md`, `docs/model_card.md`, `docs/assumptions.md` | complete | — |
| D3 | Versioned repository with environment, acquisition instructions, configurations, reproducible scripts | `requirements.txt`, the notebook | one notebook regenerates every result (`results/reproducibility.json`, `results/manifest.csv`) | complete: https://github.com/aslammugutkhan/fairness-transfer (commit 44cd0a4); the guide's src/, scripts/ and tests/ layout was replaced by a single notebook at the team's request | README |
| D4 | Graphical-model diagram with factorisation, inference queries, independence checks | — | `figures/fig01_learned_bns.png`, `report/figures/bn_dag.pdf`; factorisation in report §2 | complete | §2; slide 5 |
| D5 | Results for all mandatory experiments, ablations, robustness tests, including unsuccessful configurations | §2.6–§2.8 | rows below; `logs/failed_configurations.csv` and threshold fallbacks in `logs/*_run.json` | complete | §4 |
| D6 | 8–10 page manuscript with reproducibility appendix and figures | — | manuscript draft (Claude doc linked in the README), Appendix A | partial: students must revise it in their own words | — |
| D7 | Demonstration, oral defence, individual contribution statements | notebook §2.12 | `demo/README.md`, `demo/demo_trace.txt`; `docs/contributions.md` template | partial: statements to be written by the students | backup 3 |

## Problem 5 requirements

| ID | Source requirement | Config and command | Artifact and key finding | Status / limitation | Report / slide |
|---|---|---|---|---|---|
| P5-E1 | Mandatory experiment 1: within-domain predictive and fairness performance | §2.4 | `metrics/exp1_within_domain.csv`: EO-S mean ΔEOdds 0.124, BA 0.771; LR-S unmitigated 0.423; AUROC 0.869 | complete | §4; slide 8 |
| P5-E2 | Experiment 2: transfer between states (2018) and 2018 → 2019 within a state | `pairs` | `tables/domain_transfer_matrix.csv`, `tables/year_transfer.csv`, `figures/fig02_transfer_matrix.png`: zero-label degradation of EO-S +0.028 on average, worst +0.096 | complete | §4 |
| P5-E3 | Experiment 3: zero-label transfer vs fixed label budgets (200, 1,000) | `budgets` | `tables/exp_transfer_summary.csv`, `figures/fig03_budget_curves.png`: budgets 0–2,000 | complete | §4, Fig. 1b |
| P5-E4 | Experiment 4: simulate isolated group-composition and conditional-factor shifts | `sim_*` | `metrics/sim_all_runs.csv`, `tables/sim_recovery_summary.csv`, `figures/fig07_simulation.png`: composition shift repaired with unlabelled covariates (0.134 vs IW-EO 0.220) | complete (synthetic); recall of the shifted factor low at n = 200 for mechanism shift | §4; slide 9 |
| P5-E5 | Experiment 5: local factor updates vs global reweighting vs threshold adaptation vs full parameter updating | confirmatory family | `metrics/confirmatory_tests_holm.csv`: fairer than TT (both budgets) and BN-all (n = 200); not significant vs IW-EO | complete; contribution partially supported | §4; slide 8 |
| P5-A1 | Ablation: random factors instead of diagnosed factors | BN-rand | `tables/ablation_d_eodds.csv`: 0.152 vs 0.140 at n = 1,000 | complete; exploratory | §4; slide 9 |
| P5-A2 | Ablation: remove fairness-aware model selection | BN-LL (log BF only) | 0.143 vs 0.140 | complete; exploratory, small difference | §4; slide 9 |
| P5-A3 | Ablation: update all factors | BN-all | 0.147 vs 0.140 (confirmatory: significant at n = 200 only) | complete | §4 |
| P5-A4 | Extra ablations (not required) | BN-LFA-dir, BN-LFA-model, BN-LFA-emp | 0.144, 0.145, 0.212 | complete | — |
| P5-R1 | Robustness: different source states | transfer matrix, pooled sources | `tables/robustness_summary.csv` ("pooled two-state source"), matrix rows | complete | §4 |
| P5-R2 | Robustness: label budgets | budgets 0–2,000 | budget curves | complete | Fig. 1b |
| P5-R3 | Robustness: feature bins | `robust_schemes: [coarse, fine]` | coarse: BN-LFA 0.150 vs EO-S 0.145 (n = 1,000); fine: 0.151 vs 0.149 | complete; BN-LFA no better than EO-S | §4; slide 9 |
| P5-R4 | Robustness: group mixtures | `robust_pooled`; simulation "demographic" | pooled two-state sources: 0.141 vs EO-S 0.138 | complete | — |
| P5-R5 | Robustness: a shift affecting a factor the method did not select | forced exclusion; simulation "unselected" | top factor excluded: +0.009 ΔEOdds; unselected scenario: 0.187 vs EO-S 0.184 | complete; documented failure case | §4; slide 9 |
| P5-M1 | Predictive metrics: AUROC, balanced accuracy, average precision, worst-domain utility | `KEY` metrics | `tables/exp_transfer_summary.csv` (auroc, ba, ap, worst_target_ba) | complete | §4 table |
| P5-M2 | Fairness metrics: domain-specific ΔEOdds, worst intersectional gap, degradation after transfer | — | per-pair ΔEOdds (`tables/per_pair_d_eodds_n1000.csv`), worst_pair_d_eodds, transfer-matrix degradation | complete | §4 |
| P5-M3 | Secondary audits: demographic parity and calibration | — | d_dp, r_sel (undefined when no group is selected), `metrics/group_calibration.csv` | complete | backup 2 |
| P5-M4 | Probabilistic metrics: log loss, Brier, calibration by domain and group, held-out factor likelihoods | — | logloss / brier columns; `metrics/group_calibration.csv`; `tables/factor_heldout_loglik.csv`, `tables/adapted_bn_heldout_loglik_summary.csv` | complete | §4 table |
| P5-S1 | Statistical validation: adaptation and test sets disjoint; household clustering; sample vs survey-weighted population | — | self-check §2.5; household bootstrap; `metrics/survey_weighted_estimates.csv` with replicate-weight SEs | complete; estimand is the unweighted sample | §2, §5 |
| P5-I1 | Interpretability: which factors changed, by how much, related to fairness deterioration; graph associational | §2.6 | `tables/factor_shift_explanation.csv`, `tables/factor_shift_full_pool_*.csv`, `figures/fig05_factor_shift_diagnostic.png`: Spearman 0.88 predicted vs observed change | complete; 9 points, exploratory | §4; slide 9 |
| P5-D1 | Deliverables: domain-transfer matrix, adaptation-budget curves, factor-shift diagnostic | §2.10 | `figures/fig02`, `fig03`/`fig04`, `fig05`/`fig06` | complete | §4 |
| P5-SC1 | Scope: three states, two years, about eight variables; declare target data per stage; no test labels to diagnose shift or select factors | — | `docs/assumptions.md` (target-data table); 8 BN variables | complete | §2–§3 |
| P5-ETH | Ethical questions: generalisation, the income threshold, whose outcomes worsen | — | `docs/model_card.md`; report §5; manuscript §9 | complete | §5; slide 10 |
| P5-GM | Graphical-model family: compact BN with observed domain indicator S and domain-dependent tables | — | per-domain BNs; the adapted network P(V ∣ S = t) gives S as an extra parent of the ≤ 2 updated factors (`docs/proposal.md` §1) | complete; S is represented implicitly by domain-specific CPTs | §1–§2 |
| P5-INF | Required inference/learning: constrained structure learning, Bayesian parameter smoothing, variable elimination, local CPT updating | — | notebook §1.4, §1.5, §1.8 | complete | §2 |
| P5-ADV | Optional advanced component: Bayesian model averaging | — | Bayes factors by model averaging over shift families | complete (optional) | §2 |

## Not applicable

| Item | Reason |
|---|---|
| Interventions / counterfactuals (safeguards) | The project makes no causal claim; the graph is associational |
| Markov-network joint independence and positivity | No Markov network is used |
| Optional third year (Problem 5 optional secondary dataset) | Not added; listed as the next experiment |
