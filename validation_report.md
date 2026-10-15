# Validation report — rce-dataset

| Check | OK | Detail |
|---|---|---|
| schema_fields | ✅ | 5 harmonized tables conform |
| raw_checksums | ✅ | all 220 raw files match |
| days_match_lineage | ✅ | 766 days in package, 776 in lineage, 10 listed in daily_qc without harmonized values; unaccounted [] |
| grid_no_duplicates | ✅ | no duplicate grid rows |
| utc_monotonic | ✅ | ts_utc monotonic per day |
| no_secrets | ✅ | no emails/paths/secrets/person names found; Office metadata clean |
| size_summary | ✅ | 253 files, 1.25 GB |

**ALL PASS**
