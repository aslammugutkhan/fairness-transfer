# Research proposal: Problem 5, Fairness Transfer across States and Years with Bayesian Networks

ICT 5143 capstone. Team: Aslam Mugutkhan (261090830010); Rao Ashlesha Padmananda (261090830019).

The original proposal and preregistration were written into Section 1 of the first version of the notebook on 30
October 2026. This file restates them and records every later amendment with its date. Section 5 separates choices made before any transfer result was seen
from choices made afterwards.

## 1. Problem and decision context

A classifier tuned to satisfy equalized odds in one state or year can violate it after transfer to another state or
year. The task is the Folktables ACSIncome benchmark: predict whether a working adult's annual income exceeds
\$50,000. **D = 1 means a high-income prediction.** It is a benchmark task, not a measure of merit, employability or
need, and no decision about any real person is made.

Stakeholders: the people represented in the census data, public-interest researchers, model developers, and auditors of
systems transferred across regions.

A Bayesian network (BN) factorises the joint distribution as P(V) = ∏ P(v | pa(v)), so a domain shift can be
*localised* to the factors whose conditional probability tables (CPTs) change:

* **composition shift**: factors without the outcome Y, for example P(R) or P(E | R, X, C);
* **conditional-mechanism shift**: the outcome factor P(Y | pa(Y)).

With an observed domain indicator S (state and year), a shifted factor is a factor that receives S as an extra parent
(a selection diagram in the sense of Pearl and Bareinboim, 2011). The adapted model is

P(V | S = t) = ∏_{v ∈ F} P_t(v | pa(v)) ∏_{v ∉ F} P_s(v | pa(v)),  with |F| ≤ 2.

## 2. Research question and falsifiable contribution

**Primary question (from the brief).** Can updating a small number of shifted graphical-model factors preserve fairness
more efficiently than global adaptation when only a small labelled target sample is available?

**Contribution sentence (preregistered).** For ACSIncome transfers among CA, TX and NY (2018) and from 2018 to 2019,
with n ∈ {200, 1000} labelled target examples, updating at most two BN factors, chosen by a Bayes-factor ×
decision-impact diagnostic, and re-selecting equalized-odds thresholds on source data reweighted by those factors'
density ratio gives a lower mean target ΔEOdds than (a) updating all factors, (b) target-domain threshold adjustment and
(c) global covariate reweighting at the same label budget, while losing at most 0.02 balanced accuracy.

**Falsification.** The claim is refuted for a comparator and budget if the Holm-adjusted paired household-bootstrap test
does not show a reduction in mean target ΔEOdds, or if the balanced-accuracy difference has a 95% lower bound below
−0.02.

**Secondary (exploratory) questions, from the brief.** Which factor changes predict fairness deterioration? How much
target supervision is required? When does covariate reweighting fail because label relationships change?

## 3. Proposed method (BN-LFA, local factor adaptation)

1. *Shift evidence per factor.* A Bayes factor for "this factor shifted" against "unchanged", by Bayesian model averaging
   over structured shift families: for a composition CPT a global exponential tilt, a tilt per level of one parent, and a
   per-configuration Dirichlet (averaged over a log-uniform α grid); for the log-linear outcome CPD a logit residual with
   an intercept, an intercept plus one feature's main effects, or all main effects (Laplace evidence).
2. *Decision impact.* The exact change in the source fair rule's model-implied ΔEOdds and balanced accuracy when only
   that factor is replaced by its update (exact enumeration, cross-checked by variable elimination).
3. *Selection.* At most two factors with log BF > 0, ranked by P(shift) × (|Δ ΔEOdds| + |Δ BA|).
4. *Factor-ratio threshold selection.* Equalized-odds thresholds are re-selected on the source validation split
   reweighted by ∏_{v ∈ F} θ̂_t(v | pa) / θ_s(v | pa). Factors that are not updated cancel from the ratio; with F empty the
   rule is exactly the source fair rule (EO-S); unlike covariate importance weighting, the ratio can include a change in
   P(Y | x).

Closest reproduced baseline: a restricted reproduction of the covariate-shift fairness correction line (Shimodaira,
2000; Singh et al., 2021; Rezaei et al., 2021) as IW-EO, a domain-classifier importance-weighted refit followed by
equalized-odds post-processing on the reweighted source validation split. See `docs/related_work.md`.

## 4. Preregistered analysis choices (30 October 2026)

| Item | Choice |
|---|---|
| Decision | D = 1: predicted income > \$50,000 (favourable benchmark prediction) |
| Primary fairness criterion | Equalized odds within each target domain. Metric ΔEOdds over race × sex groups with ≥ 100 positives and ≥ 100 negatives in the target test split |
| Secondary audits | ΔEOdds over groups with ≥ 30 positives and negatives, ΔDP, selection-rate ratio, ΔEOpp, single-attribute gaps, calibration by group |
| Primary predictive metric | Balanced accuracy of the decision; AUROC, AP, log loss and Brier score secondary |
| Acceptable utility loss | δ = 0.02 balanced accuracy (threshold selection and non-inferiority check) |
| Confirmatory family | BN-LFA vs {BN-all, TT, IW-EO} × n ∈ {200, 1000}; statistic = mean target ΔEOdds over the 9 transfer pairs; Holm at α = 0.05 (6 tests) |
| Resampling unit | Household (SERIALNO); paired bootstrap with fixed models, at least 1,000 replicates |
| Seeds | 5 adaptation-sample seeds at the confirmatory budgets (algorithmic variability, not population samples) |
| Estimand | Unweighted benchmark sample; survey-weighted estimates with replicate-weight SEs as a secondary analysis |
| Data rules | Adaptation and test sets disjoint; target test labels never used to diagnose shift, select factors or tune thresholds |

## 5. Amendment log

Choices made **before** the first full transfer run (frozen in the notebook's settings dictionary `CFG`):

| Date | Change | Reason | Before or after seeing transfer results |
|---|---|---|---|
| 2026-10-30 | Outcome CPD changed from a tabular CPT with BIC-selected parents to a log-linear CPD with required R, X → every covariate edges | Within-domain validation showed biased model-implied group error rates (A11) | Before full run; quick smoke tests had displayed some transfer metrics (see note) |
| 2026-10-30 | Structure score changed from BIC to validation log-likelihood of the smoothed CPTs (BIC kept for comparison) | Held-out likelihood on the source validation split | Same |
| 2026-10-30 | Structured shift families added to the factor diagnostic | Simulation showed the per-configuration Dirichlet evidence was underpowered at n = 200 | Same |
| 2026-10-30 | TT keeps the EO-S threshold for groups with < 20 labelled positives or negatives | A pooled fallback moved small groups to the pooled threshold | Same |
| 2026-10-30 | Primary support rule ≥ 100 positives and negatives (≥ 30 secondary) | Max − min gaps over tiny cells are dominated by noise | Same |
| 2026-10-30 | Bootstrap replicates set to B = 2,000 in the frozen configuration (the table above says "at least 1,000") | More replicates, same procedure | Before full run |

Note (disclosed in the notebook, Section 14.4): quick-mode smoke tests printed some transfer-test metrics during
development, so the preregistration is a frozen configuration, not a blind one. The full run is confirmatory only for
that frozen configuration.

Choices made **after** the first full run (retrospective; none changes the confirmatory family, its budgets, seeds,
statistic or test):

| Date | Change | Reason | Effect |
|---|---|---|---|
| 2026-11-06 | Five seeds also at the non-confirmatory budgets (n = 100, 500, 2,000), in every robustness test and in the trade-off curves (previously 3, 3 and 1) | Repository guide: at least five seeds for stochastic procedures | Changes curve points and robustness means slightly; confirmatory numbers unchanged |
| 2026-11-06 | Added per-group denominators / selection rates / TPR / FPR, per-group calibration of the source score models, and held-out factor log-likelihoods of the adapted networks | Brief: probabilistic metrics include calibration by domain and group and held-out factor likelihoods; repository guide: per-group denominators | New tables only |
| 2026-11-06 | Code reorganised (a package with scripts and tests, then, at the team's request, merged back into one notebook with a self-check section); no method or setting changed | Readability and the repository guide | Every method-run of the 30 October notebook reproduced exactly (maximum difference 0) |
