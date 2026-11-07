# Evaluation protocol

All settings are frozen in the notebook's settings dictionary `CFG` (section 1.1); a `RUN_MODE = "quick"` run writes to
a separate folder and is never reported.

## Data, splits and target-data accounting

* Six domains: CA, TX, NY × 2018, 2019 (ACS PUMS 1-Year person files, ACSIncome population).
* Each domain is split **by household** (`SERIALNO`) into P1 train (60 %), P2 validation (20 %) and P3 test (20 %); seed
  5143 + domain index. A household never spans two parts (checked in notebook section 2.5).
* Sources: CA18, TX18, NY18. Nine transfers: the six ordered pairs of 2018 states, and 2018 → 2019 within each state.
* A target's adaptation pool is P1 + P2. Labelled budgets n ∈ {0, 100, 200, 500, 1,000, 2,000} are the first n persons
  of a seed-specific random permutation of the pool (nested budgets). The target test split P3 is used only to evaluate.
* Every adaptation method in a run receives the same n labelled persons; IW-EO uses their covariates. The table in
  `docs/assumptions.md` lists the target data each method may use.

## Fitting and selection (training and validation data only)

* Discretisation bins, structure learning (validation log-likelihood score), CPT smoothing, the outcome CPD, all
  baselines and every source rule are fitted on P1 and selected on P2 of the source.
* The operating point of every equalized-odds rule is chosen by the same deterministic routine: sweep a pooled
  threshold; move every supported group's threshold to the pooled (TPR, FPR) target in L∞; among pooled thresholds whose
  estimated balanced accuracy is within δ = 0.02 of the best single threshold, keep the smallest estimated ΔEOdds. Only
  the estimate of the joint law of (score, Y, group) differs between methods.
* Groups with fewer than 20 positives or negatives in the data used to fit thresholds keep a fallback threshold (the
  pooled one for source rules; the EO-S threshold for TT and BN-LFA-emp).

## Metrics (direction)

* **Primary fairness:** ΔEOdds = max(TPR range, FPR range) across race × sex groups with ≥ 100 positives and ≥ 100
  negatives in the target test split (lower is fairer).
* **Primary utility:** balanced accuracy of the decision (higher is better). Acceptable loss δ = 0.02.
* Secondary: ΔEOdds over groups with ≥ 30 positives and negatives, ΔEOpp, ΔDP, selection-rate ratio min/max (reported as
  undefined when the largest selection rate is 0), sex-only and race-only gaps, AUROC, average precision, log loss,
  Brier score, maximum group ECE, worst-pair ΔEOdds and worst-target balanced accuracy, degradation after transfer
  (target minus the source's own test ΔEOdds), per-group calibration, held-out factor log-likelihoods.
* Every group's persons, positives, negatives, selection rate, TPR and FPR are kept (`results/metrics/per_group_metrics.csv`).
  Groups with fewer than 30 positives or negatives are labelled exploratory.

## Seeds and repetitions

* Five adaptation seeds (0–4) at every budget above 0, in every robustness test and in the trade-off curves. A seed
  controls which target persons are labelled, BN-rand's factor draw and the GBT random state: it measures
  adaptation-sample and algorithmic variability, not independent populations.
* Deterministic given the split: structure learning, CPTs, logistic models, source rules, exact inference and the
  model-based audit. The split is fixed (one household split per domain).
* Simulation: 8 replications per scenario with independently sampled source and target data.

## Uncertainty and the confirmatory family

* **Confirmatory family (six tests):** BN-LFA minus each of BN-all, TT and IW-EO, at n = 200 and n = 1,000. Statistic:
  mean over the nine pairs of the seed-averaged target ΔEOdds. One-sided claim "BN-LFA is fairer" if Δ < 0 with Holm-
  adjusted p < 0.05; balanced accuracy is non-inferior if the 95 % lower bound of ΔBA exceeds −0.02.
* **Interval method:** paired household bootstrap of each target test split, B = 2,000, decisions held fixed (fixed-model
  evaluation uncertainty). The same resampled households are used for every method, so differences are paired. Within a
  replicate, metrics are averaged over the five seeds and the nine pairs. Two-sided bootstrap p-values
  p = 2 min{(#Δ* ≤ 0 + 1)/(B + 1), (#Δ* ≥ 0 + 1)/(B + 1)}.
* Training/adaptation uncertainty is reported separately as the across-seed standard deviation (`*_sd_seeds` columns,
  error bars in Figs. 3–4). Graph and scenario uncertainty are addressed by the robustness tests (bins, pooled sources,
  forced exclusion, unlabelled covariates) and by the simulation; these are exploratory.
* Method-level ΔEOdds intervals are reported as basic (reflected) bootstrap intervals because a max − min gap is biased
  upward under resampling (`bootstrap_method_means.csv`).
* Everything outside the confirmatory family is exploratory and unadjusted (per-pair results, ablations, BN-LFA vs EO-S
  and BN-S, robustness, simulation).

## Estimand

The primary estimand is the **unweighted benchmark sample** of each target test split. A secondary analysis reports
person-weighted (PWGTP) ΔEOdds and balanced accuracy with successive-difference replicate-weight standard errors,
SE = sqrt(4/80 Σ_r (θ_r − θ)²), at n = 1,000 and seed 0 (`survey_weighted_estimates.csv`). Population claims are made
only from that table.

## Correctness checks

* Exact inference: variable elimination under min-fill, min-degree, min-weight and random orders, the clique tree,
  brute-force enumeration and pgmpy (VE and belief propagation) must agree within 1e-9 on 20 random queries with 0–3
  evidence variables, on the full network and on the composition sub-network (`inference_agreement_checks.csv`); the
  run stops if they do not.
* CPT normalisation, factor scopes, evidence handling and missing-value marginalisation are checked in
  the notebook's self-checks (section 2.5); d-separation of every local Markov statement and G-tests on validation data (`independence_checks.csv`).
* Folktables ACSIncome definition: rows, labels and features of the filtered extract are compared with
  `folktables.ACSIncome` for every domain; per-group TPR/FPR are cross-checked against fairlearn's `MetricFrame`.
