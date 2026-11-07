**Run mode:** `full` · config `problem5_fairness_transfer_bn.ipynb` · seeds [0, 1, 2, 3, 4] · budgets [0, 100, 200, 500, 1000, 2000] · bootstrap B = 2000.

**Within-domain (Experiment 1).** Mean over the six domains: BN-S (model-based EO) ΔEOdds 0.140, BA 0.769; EO-S (empirical EO, same scores) ΔEOdds 0.124, BA 0.771; LR AUROC 0.869, GBT AUROC 0.876. The model-implied group TPRs were within the 95% sampling half-width of the test TPRs for 89% of group cells (mean |TPR error| 0.022).

**Zero-label transfer, EO-S.** Mean change in ΔEOdds relative to the source's own test split: 0.028 (range -0.029 to 0.096); worst target ΔEOdds 0.197.
**Zero-label transfer, BN-S.** Mean change in ΔEOdds relative to the source's own test split: 0.016 (range -0.069 to 0.068); worst target ΔEOdds 0.188.

**Label-budgeted transfer (mean over 9 pairs).** n = 200: BN-LFA ΔEOdds 0.153 (BA 0.770), BN-all 0.167, TT 0.192, IW-EO 0.159, EO-S 0.150, LR-T 0.521. n = 1000: BN-LFA 0.140 (BA 0.770), BN-all 0.147, TT 0.205 (BA 0.772), IW-EO 0.156, LR-T 0.453, GBT-T 0.417.

**Confirmatory tests (household bootstrap, Holm).**
* n = 200, BN-LFA − BN-all: Δ = -0.014 [-0.020, -0.004], Holm p = 0.032 → **supported**; ΔBA = 0.002 (non-inferior at −0.02: True).
* n = 200, BN-LFA − TT: Δ = -0.039 [-0.045, -0.030], Holm p = 0.006 → **supported**; ΔBA = 0.002 (non-inferior at −0.02: True).
* n = 200, BN-LFA − IW-EO: Δ = -0.006 [-0.011, 0.003], Holm p = 0.246 → **not supported**; ΔBA = -0.000 (non-inferior at −0.02: True).
* n = 1000, BN-LFA − BN-all: Δ = -0.008 [-0.013, 0.001], Holm p = 0.246 → **not supported**; ΔBA = 0.001 (non-inferior at −0.02: True).
* n = 1000, BN-LFA − TT: Δ = -0.065 [-0.070, -0.047], Holm p = 0.006 → **supported**; ΔBA = -0.002 (non-inferior at −0.02: True).
* n = 1000, BN-LFA − IW-EO: Δ = -0.016 [-0.022, 0.001], Holm p = 0.222 → **not supported**; ΔBA = 0.001 (non-inferior at −0.02: True).

**Exploratory (unadjusted, not preregistered): adaptation vs. no adaptation.** n = 200, BN-LFA − EO-S: Δ = 0.004 [-0.004, 0.010], p = 0.436; n = 200, BN-LFA − BN-S: Δ = 0.004 [-0.013, 0.019], p = 0.609; n = 1000, BN-LFA − EO-S: Δ = -0.010 [-0.018, 0.004], p = 0.221; n = 1000, BN-LFA − BN-S: Δ = -0.010 [-0.024, 0.012], p = 0.521.

**Ablations (n = 1000, mean ΔEOdds):** EO-S 0.150, BN-LFA 0.140, BN-LL 0.143, BN-rand 0.152, BN-all 0.147, BN-LFA-dir 0.144, BN-LFA-model 0.145, BN-LFA-emp 0.212.

**Simulation (synthetic).** Exact target ΔEOdds averaged over budgets — null: EO-S 0.112, IW-EO 0.112, TT 0.198, BN-LFA 0.115; demographic: EO-S 0.106, IW-EO 0.117, TT 0.243, BN-LFA 0.102; composition: EO-S 0.241, IW-EO 0.240, TT 0.298, BN-LFA 0.212; mechanism: EO-S 0.109, IW-EO 0.109, TT 0.177, BN-LFA 0.103; mixed: EO-S 0.140, IW-EO 0.150, TT 0.228, BN-LFA 0.149; unselected: EO-S 0.184, IW-EO 0.170, TT 0.272, BN-LFA 0.182. LFA recall of the truly shifted factors: demographic 1.000, composition 0.375, mechanism 0.500, mixed 0.469, unselected 0.438.

**Factor-shift explanation.** Spearman correlation between model-predicted and observed change in ΔEOdds across the 9 pairs: 0.883 (p = 0.002; exploratory).