# Demonstration

Run section 2.12 of the notebook (after the earlier sections, in the same session). It takes a few seconds and shows:

1. One exact query, P(Y = 1 | R = Black, X = female, E = master's or higher) on the CA18 network, answered by variable
   elimination (with its elimination order and largest intermediate factor), the clique tree and enumeration of the
   115,200-entry joint; all three agree to about 1e-16.
2. One BN-LFA adaptation CA18 → TX18 with n = 200 labelled TX18 persons from the adaptation pool: the factor diagnostic
   (log Bayes factor, P(shift), exact change of the source rule's ΔEOdds and balanced accuracy, expected impact, chosen
   shift family) and the target-test ΔEOdds, balanced accuracy and AUROC of EO-S, TT, IW-EO, BN-all and BN-LFA on the
   same 200 labels.

Saved trace of the reported run (offline fallback): [demo_trace.txt](demo_trace.txt). In it BN-LFA updates E and R and
reaches ΔEOdds 0.143 at balanced accuracy 0.773 on TX18, against 0.156 / 0.767 for the unchanged source rule (EO-S),
0.237 / 0.758 for target thresholds (TT) and 0.184 / 0.759 for updating every factor. One seed of one pair, for
illustration only; the evidence is in `results/` and the report.

For the defence, each student should be able to trace the query by hand (which factors are multiplied and which
variable is summed out at each step of the printed order) and explain why R is selected although its decision impact is
small: its shift is certain (log BF ≈ 28), but a pure race-composition shift barely moves group error rates.
