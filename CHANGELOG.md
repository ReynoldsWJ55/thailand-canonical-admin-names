# Changelog

All notable changes to the Thailand Canonical Administrative-Names Reference are documented here. This project follows [Semantic Versioning 2.0.0](https://semver.org/) per Section 13 of the methodology PDF. Each release ships as a Zenodo deposit with a version-specific DOI under a single concept-DOI.

## [1.1.0] — Planned

### Planned additions

- `num_muban` (village count) and `num_thesaban` (municipality count) columns at ADM1, populated from DOPA Local Administration Department statistics. v1.0 deferred this column population because DOPA's online stats portal requires interactive Thai-language navigation that is impractical to automate at v1.0 release timeline. Community contributions via the verification-upgrade issue template can populate these columns earlier as a patch release if a Thai-language contributor with DOPA archive access volunteers.
- ADM2 and ADM3 cross-source spelling cross-check. v1.0 ships single-source provenance from `thailand-geography-data` for the lower administrative levels; v1.1 introduces parallel cross-checking against kongvut and GeoThai at those levels.
- Wikipedia article URLs at ADM2 (where they exist).

## [1.0.2] — 2026-05-06

### Patch — methodology clarification, three populated predecessor codes, build-pipeline fixes

#### Data and methodology

- `data/historical_mappings.csv`: added three rows for the 1972 Yasothon, 1977 Phayao, and 1982 Mukdahan splits, each citing the corresponding CONFIRMED-grade entry in `data/established_years.csv`. After rebuild, `predecessor_tis1099_code` is populated at TIS-35 (parent 34), TIS-49 (parent 48), and TIS-56 (parent 57). 4 of 77 ADM1 rows now carry predecessor codes.
- Methodology PDF §4.1: replaced "Empty otherwise." with explicit population policy — populated when the row's `established_year` is CONFIRMED grade and the primary-source citation names the parent province; empty otherwise. Documents the rule the build script already enforces.
- Methodology PDF §17.1: added a coverage row for `predecessor_tis1099_code`, naming the three PARTIAL-grade splits in the backlog (Sa Kaeo, Amnat Charoen, Nong Bua Lam Phu) and the verification-upgrade path that would unlock them.
- Resolves issue #2 (undocumented `predecessor_tis1099_code` policy and gap for TH35/TH49/TH56).

#### Build pipeline

- `bin/build_v0_3_0.py` now writes the v1.0.0 ADM1 release outputs (`thailand-adm1-provinces-v1.0.0.csv` and `.parquet`) in addition to the historical v0.3.0 path. Prior releases hand-promoted the ADM1 file at ship time, which left the smoke-test byte-identical contract hollow for ADM1. The orchestrator's `adm1` stage now produces the released outputs end-to-end.
- Parquet engine switched from `fastparquet` to `pyarrow` project-wide (`build_v0_3_0.py`, `build_adm2_v1_0_0.py`, `build_adm3_v1_0_0.py`). Reason: `fastparquet 2026.3.0` raises a numpy-2.x compatibility error when writing object-dtype columns containing empty cells in the current Python environment; `pyarrow` is more widely available and produces identical logical content. All three parquet files (`adm1`, `adm2`, `adm3`) regenerate at v1.0.2 with the new engine. Logical content is unchanged from v1.0.1 except for the three new ADM1 predecessor cells; binary hashes and file sizes change.
- Smoke-test snapshot bytes (`data/v1.0.0/smoke_test_report.md`) refresh with the new pyarrow-engine outputs; downstream consumers reading the parquet files see no schema or value changes beyond the documented ADM1 predecessor additions.

## [1.0.1] — 2026-05-06

### Patch — documentation only, no data or code changes

- Methodology PDF: added Zenodo concept-DOI (`10.5281/zenodo.20049930`) to the title-page date line and to the citation block in Section 18 ("How to cite this artifact"). Updated PDF metadata (pdftitle, pdfauthor, pdfsubject, pdfkeywords).
- README: switched the DOI badge from version-pinned (`zenodo.20049931`) to concept-DOI (`zenodo.20049930`) so future versions inherit the badge automatically.
- README: populated the previously empty "See also" section with links to methodology PDF, NOTICE, CHANGELOG, CONTRIBUTING, the Zenodo deposit, RTGS, and the HDX RTSD dataset.

Data, validation outputs, and build code are byte-identical to v1.0.0.

## [1.0.0] — 2026-05-06

### Initial release

- Three administrative levels in one release: ADM1 (77 provinces), ADM2 (928 amphoe), ADM3 (7,436 tambon)
- Tabular outputs in CSV and Parquet (Snappy-compressed) at all three levels
- Bundled polygon geometry: GeoJSON for each level (derived from `mapthai`) plus HDX RTSD shapefiles
- ADM1 schema covers 36 columns: cross-system identifiers (TIS-1099, ISO 3166-2, HASC, FIPS, Wikidata, GeoNames, OSM, Wikipedia URL), normalized English and Thai names, override registry with audit trail, computed geographic columns (UTM-projected centroids, areas, bounding boxes, international borders, coastline lengths), operational lookups (postal prefixes, telephone area codes), administrative subunit counts
- Methodology PDF with 18 sections covering composition pass, override resolution, geographic computation, validation, license posture, community review model and limitations
- Override registry with one founding entry (Lopburi / Loburi)
- Historical-mappings registry with one founding entry (Bueng Kan / Nong Khai 2011 split)
- Established-year registry with 4 CONFIRMED entries (Yasothon 1972, Mukdahan 1982, Phayao 1977, Bueng Kan 2011) and 5 PARTIAL entries
- Name alternates populated for 50 of 77 ADM1 rows with 71 total alternates (spacing variants, "buri" split / merged forms, historical names, transliteration variants)
- Validation suite with 36 checks covering every column
- Mutation test suite with 54 tests proving the validator catches deliberate errors
- License: CC BY 4.0 on data and methodology, MIT on code, layered upstream licenses on bundled polygons (see `NOTICE.md`)
