# Data

No record-level data are stored in this repository. The notebook (section 2.1) downloads the public files and builds
the extracts; both folders below are created next to the notebook and are git-ignored.

| Item | Content |
|---|---|
| Dataset | U.S. Census Bureau, American Community Survey (ACS) Public Use Microdata Sample, **1-Year person files**, 2018 and 2019, California, Texas, New York |
| URLs | `https://www2.census.gov/programs-surveys/acs/data/pums/2018/1-Year/csv_pca.zip` (and `csv_ptx.zip`, `csv_pny.zip`; same for 2019) |
| Access date | 2026-10-30 (first download) and 2026-11-06 (re-download for the reported run; identical SHA-256). Exact UTC times per file: `results/tables/data_provenance.csv` |
| Checksums | `data/checksums.sha256`; every run records the SHA-256 of the files it downloaded in `results/tables/data_provenance.csv`, so a re-released file shows up as a different hash |
| Size | 300 MB of zip files in total (67, 47, 35 MB for CA, TX, NY per year) |
| Access requirements | None: public files, no account, key or approval |
| Licence and terms | Works of the U.S. Government are not subject to copyright (17 U.S.C. § 105); cite the Census Bureau. Do not attempt to identify any person or household (Title 13 confidentiality). We do not redistribute extracts |
| Benchmark definition | Folktables ACSIncome (Ding et al., 2021; folktables 0.0.12, MIT): population `AGEP > 16`, `PINCP > 100`, `WKHP > 0`, `PWGTP >= 1`; target `PINCP > 50000` (nominal dollars) |
| Verification | `prepare_data.py` compares our filtered rows, labels and the ten ACSIncome features with `folktables.ACSIncome.df_to_pandas` for every domain (result in `data_provenance.csv`, column `folktables_check`) |
| Columns kept | `SERIALNO` (household, used for splits and the bootstrap), `SPORDER`, `ST`, `PWGTP` and `PWGTP1`–`PWGTP80` (person and replicate weights), `AGEP`, `COW`, `SCHL`, `MAR`, `OCCP`, `POBP`, `RELP`, `WKHP`, `SEX`, `RAC1P`, `PINCP`. In 2019 `RELP` was renamed `RELSHIPP`; it is aliased only for the Folktables check |
| Exclusions | Only the ACSIncome filter (no other rows removed) |
| Preprocessing | `encode` (notebook section 1.3): R = RAC1P − 1, X = SEX − 1, age, education (SCHL), class of worker (COW), occupation major group (OCCP; ≥ 9920 missing) and hours (WKHP) binned as in `docs/data_card.md`; Y = 1[PINCP > 50,000]. Household split P1/P2/P3 = 60/20/20 % with seed 5143 + domain index |

## Local layout (git-ignored)

```
data/raw/2018_csv_pca.zip ... 2019_csv_pny.zip            downloaded files
data/processed/acsincome_<DOMAIN>.pkl                    filtered extract (KEEP_COLS), one per domain
data/processed/repweights_<DOMAIN>.pkl                   80 replicate weights (float32), same row order
data/processed/provenance_<DOMAIN>.json                  URL, SHA-256, access time, row counts, Folktables check
```

Delete `data/processed/` to rebuild the extracts (for example after installing `folktables`, so the definition check
runs instead of being skipped). The environment variable `P5_BASE_DIR` moves both `data/` and `results/`.
