# Assumptions register

Every claim in the report is **associational** unless a row below says otherwise. No causal effect of race, sex or any
covariate on income is estimated or claimed. The tiers of the graph are an ordering assumption used to constrain
structure learning, not a causal model; nothing in this project uses interventions or counterfactuals.

| ID | Assumption | Type | Used in | Justification | How it is checked | Consequence if violated | Sensitivity test |
|---|---|---|---|---|---|---|---|
| A1 | The ACSIncome population filter (AGEP > 16, PINCP > 100, WKHP > 0, PWGTP ≥ 1) and the nominal \$50,000 threshold are fixed for all domains (ADJINC not applied) | definitional | all | Folktables benchmark definition (brief: keep the threshold and filter fixed) | Row-by-row check against `folktables.ACSIncome` (`results/tables/data_provenance.csv`) | 2018 → 2019 transfers partly reflect nominal wage growth; reported as part of the shift | none needed (definition) |
| A2 | Recorded `RAC1P` and `SEX` codes are used as recorded; nothing is inferred | measurement | groups | brief, shared protocol rule 1 | codes mapped one-to-one (`encode`, notebook section 1.3) | Recorded categories are limited measurements of identity (ethics) | — |
| A3 | Tier constraints {R, X} → C → E → {W, O} → H → Y; the learned graph is associational | associational (ordering) | BN | temporal and definitional ordering | d-separation statements and G-tests of the local Markov statements (`results/tables/independence_checks.csv`) | Approximate conditional independences bias model-implied group error rates | BIC vs held-out structure (`structure_learning.csv`); model-based audit (`model_based_audit_accuracy.csv`) |
| A4 | The structure is shared across domains; shift happens in CPT parameters (modularity) | associational | adaptation | selection-diagram view | identical learned structure in all six domains (`learned_edges_by_domain.csv`); held-out factor likelihoods (`factor_heldout_loglik.csv`) | A shift needing a new edge cannot be represented by a factor update | simulation "unselected"; forced exclusion of the top factor |
| A5 | The n labelled adaptation persons are a simple random sample of the target pool (P1 + P2) | design | adaptation | label-budget model | nested, seed-specific permutations of the pool (`run_one`) | Results do not cover targeted or biased labelling | 5 seeds |
| A6 | Missing E, W, O values are ignorable (complete-case counts; marginalisation at prediction) | missingness | BN | no missing values occur in these extracts (data card: 0.0 %) | `data_card_domains.csv` | — | marginalisation checked against variable elimination (`inference_agreement_checks.csv`) |
| A7 | Households are the independent sampling units | design | bootstrap | ACS samples housing units | household bootstrap; replicate-weight SEs | Ignoring the ACS stratification makes intervals approximate | survey-weighted estimates (`survey_weighted_estimates.csv`) |
| A8 | Group-specific thresholds use recorded race and sex at decision time | normative | EO post-processing | standard for equalized-odds post-processing | listed per method (`exp_transfer_summary.csv`, column "protected attributes at prediction time") | May be unlawful in real decision settings | — |
| A9 | A group enters the primary gap with ≥ 100 positives and ≥ 100 negatives in the evaluated split (≥ 30 for the secondary gap) | statistical | metrics | max − min statistics over tiny cells are noise-dominated (shared protocol rule 7) | per-group tables keep every group with its denominators and support label (`per_group_metrics.csv`) | Small groups (AIAN, NHPI, Alaska Native) are not covered by the primary gap | secondary ≥ 30 gap (`d_eodds_30`) |
| A10 | Simulated shifts are synthetic | labelling | simulation | known ground truth | every simulation table and figure is labelled SYNTHETIC | Never presented as facts about the real population | — |
| A11 | The outcome CPD is log-linear with main effects and sex interactions; composition factors carry R and X as parents | modelling | BN | needed for unbiased group-conditional audits | model-implied vs empirical group TPR/FPR (`model_based_audit_accuracy.csv`); calibration by group (`group_calibration.csv`) | No race interactions in the outcome CPD | BN-LFA-model ablation; coarse and fine bins |
| A12 | Target test labels are used only for evaluation | design | all | brief scope restriction | the notebook's self-check (section 2.5) shuffles the target test labels and checks that every method's decisions are unchanged | — | — |

## Which target data each stage may use (brief: "declare exactly which target data are available at each stage")

| Stage | Source data | Target data |
|---|---|---|
| Structure learning, CPTs, LR-S/GBT-S/RW-S, source fair rules (EO-S, BN-S) | P1 (train), P2 (validation) | none |
| Zero-label transfer (n = 0) | as above | none |
| Factor diagnostic and updates (BN-LFA, BN-LL, BN-rand, BN-all, ablations) | source BN, P2 | the n labelled pool persons (and, in +U variants, 5,000 further unlabelled pool persons' covariates) |
| TT, LR-T, GBT-T, BN-LFA-emp | source models | the same n labelled pool persons |
| IW-EO | P2 and a 30,000-row subsample of P1 | covariates of the same adaptation sample |
| Factor-shift explanation (Section "explain_shift") | source BN | the whole target pool P1 + P2 (exploratory; never the test split) |
| Evaluation | — | target test split P3 (labels used only here) |
