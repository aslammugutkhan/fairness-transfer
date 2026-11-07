# Contributions and accountability

**Fill in every bracketed field before submission.** The guide asks for concrete work items, artifact or commit links,
cross-review and an individual statement from each student. Commit counts are not a proxy for contribution, and both
students must be able to explain the question, graphical model, fairness choice, validation, findings and limitations.

Team: Aslam Mugutkhan (261090830010, Student 1) and Rao Ashlesha Padmananda (261090830019, Student 2).

## Work items

| Work item | Lead | Support / reviewer | Artifact / commit | What was verified |
|---|---|---|---|---|
| Literature review and related-work table | [S1/S2] | [S2/S1] | `docs/related_work.md` | [e.g. each row against the paper; read the closest-method papers in full] |
| Proposal, preregistration, amendment log | [ ] | [ ] | `docs/proposal.md` | [ ] |
| Data acquisition, ACSIncome check, data card | [ ] | [ ] | notebook 1.3 and 2.1, `docs/data_card.md` | [e.g. checksums, Folktables row-by-row check] |
| BN structure learning and CPT smoothing | [ ] | [ ] | notebook 1.4 and 2.2 | [ ] |
| Exact inference and correctness checks | [ ] | [ ] | notebook 1.5, 2.3 and 2.5, `results/tables/inference_agreement_checks.csv` | [e.g. traced one VE computation by hand] |
| Fairness metrics and EO post-processing | [ ] | [ ] | notebook 1.6 and 2.5 | [ ] |
| BN-LFA (diagnostic, updates, factor-ratio rule) | [ ] | [ ] | notebook 1.8 | [ ] |
| Baselines (RW-S, GBT-S, EO-S, TT, IW-EO, LR-T, GBT-T, BN-S, BN-all) | [ ] | [ ] | notebook 1.7 and 1.9 | [ ] |
| Simulation (Experiment 4) | [ ] | [ ] | notebook 1.10 and 2.7 | [ ] |
| Robustness, bootstrap, Holm, survey weights | [ ] | [ ] | notebook 1.11, 2.8 and 2.9 | [ ] |
| Report | [ ] | [ ] | `report/AFGML_Report.tex` | [ ] |
| Slides and demonstration | [ ] | [ ] | `slides/AFGML_Slides.tex`, notebook 2.12, `demo/` | [ ] |

## Individual statements (about 100–150 words each)

**Student 1 (Aslam Mugutkhan).** One technical decision I made and why: [ ]. One difficulty or failure and what I learned: [ ].
One cross-check of Student 2's work and its result: [ ].

**Student 2 (Rao Ashlesha Padmananda).** One technical decision I made and why: [ ]. One difficulty or failure and what I learned: [ ].
One cross-check of Student 1's work and its result: [ ].

## External code, data and assistance

* Data: U.S. Census Bureau ACS PUMS 1-Year 2018 and 2019 (public domain); benchmark definition from Folktables
  (Ding et al., 2021; MIT licence).
* Libraries: numpy, pandas, scipy, scikit-learn, matplotlib, networkx; pgmpy (exact-inference cross-check),
  folktables (definition check) and fairlearn (metric cross-check) are optional. Licences in `LICENSE`.
* No code was copied from the cited papers; all methods are implemented in the notebook (`problem5_fairness_transfer_bn.ipynb`).
* **Assistance disclosure (complete as your course policy requires):** [state which parts, if any, were developed with
  AI assistance or other help, and how you verified them].
