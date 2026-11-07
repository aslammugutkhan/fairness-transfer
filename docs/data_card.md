# Data card: ACSIncome, CA / TX / NY, 2018 and 2019

| Field | Content |
|---|---|
| Source | U.S. Census Bureau, American Community Survey (ACS) Public Use Microdata Sample (PUMS), 1-Year person files: `https://www2.census.gov/programs-surveys/acs/data/pums/<year>/1-Year/csv_p<state>.zip` |
| Release | 2018 and 2019 1-Year PUMS, states CA (FIPS 6), TX (48), NY (36) |
| Access | Public, no account or approval. Downloaded by the notebook (section 2.1); SHA-256 of every file in `data/checksums.sha256` and `results/tables/data_provenance.csv` (with access time) |
| Licence / terms | U.S. Government work, public domain (17 U.S.C. § 105); the Census Bureau asks users to cite the source. Census confidentiality (Title 13): do not attempt to identify individuals or households. Record-level extracts are not redistributed in this repository |
| Benchmark definition | Folktables ACSIncome (Ding et al., 2021): population AGEP > 16, PINCP > 100, WKHP > 0, PWGTP ≥ 1; label Y = 1 iff PINCP > 50,000 (nominal dollars, ADJINC not applied). Our filter and label are verified row by row against `folktables.ACSIncome` (folktables 0.0.12) for all six domains |
| 2019 note | The Census renamed and recoded RELP as RELSHIPP in 2019. It is aliased as RELP only to run the Folktables check; RELP is not used by any model |
| Population (intended estimand) | Persons aged 17+ who worked in the past 12 months (WKHP > 0) with income > \$100, in the ACS samples of the three states. Primary analyses describe the **unweighted benchmark sample**; survey-weighted estimates are secondary |
| Sampling unit and dependence | ACS samples housing units; persons in one household are dependent. Splits and the bootstrap use households (`SERIALNO`). The ACS stratified design is represented only through the 80 replicate weights in the survey-weighted analysis |
| Protected attributes | Recorded race (`RAC1P`, 9 codes: White, Black, American Indian, Alaska Native, AIAN not specified, Asian, Native Hawaiian / Pacific Islander, Some other race, Two or more races) and sex (`SEX`: male, female). Self-reported ACS answers, used as recorded; nothing is inferred. The binary sex item does not represent gender identity |
| Intersectional groups | 18 race × sex cells; g = 2·R + X |
| Variables used by the models | R (race), X (sex), C (age, 5 bins: < 25, 25–34, 35–49, 50–64, 65+), E (education from SCHL, 5 bins: < 16, 16–17, 18–20, 21 = bachelor's, ≥ 22 = master's or higher), W (class of worker from COW, 4 groups: private for-profit, private non-profit, government, self-employed / family), O (occupation from OCCP, 8 major groups; codes ≥ 9920 treated as missing), H (usual hours from WKHP, 4 bins: < 20, 20–34, 35–44, 45+), Y |
| Not used | MAR, POBP, RELP (part of ACSIncome's feature list, excluded to keep about eight substantive variables as the brief asks) |
| Exclusions | Only the ACSIncome filter. No further row exclusions; there are no missing values in E, W or O in these extracts |
| Sizes | CA18 195,665 persons / 111,426 households; TX18 135,924 / 83,087; NY18 103,021 / 60,916; CA19 197,193 / 111,639; TX19 138,293 / 84,469; NY19 104,330 / 61,292 (`results/tables/data_card_domains.csv`) |
| Positive rate | 0.37–0.45 unweighted (0.34–0.43 weighted), lowest in TX, higher in 2019 than 2018 |
| Group support | Test splits have 7–10 race × sex groups with ≥ 100 positives and negatives (10–13 with ≥ 30). American Indian, Alaska Native, AIAN-other and NHPI cells are small in every domain (Alaska Native has fewer than 15 persons per domain) and are exploratory only (`data_card_group_counts.csv`, `per_group_metrics.csv`) |
| Shifts present | Race composition differs strongly (e.g. Asian 17 % of persons in CA vs 5 % in TX; Black 4 % in CA vs 11 % in NY); positive rates differ by state and rise from 2018 to 2019 partly through nominal wage growth (`data_card_group_positive_rates.csv`) |
| Storage | `data/raw/` (zip files) and `data/processed/` (filtered pickles, replicate weights, provenance JSON) are git-ignored and stay on the user's machine |
| Limitations | Income above a fixed nominal threshold ignores cost of living, household context and wealth; recorded race and sex are limited measurements; three states and two years only; survey non-response and imputation are inherited from the ACS |
