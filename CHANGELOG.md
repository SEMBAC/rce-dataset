# Changelog

## 0.2.0
Corrections after an independent audit of the first build (0.1.0 was withdrawn).
- Office files in `raw/`: personal and machine metadata removed from every part (path of the author's computer,
  revision pointer, printer settings), not only the author fields; the validation now inspects every part.
- 2019: logger files with a shifted header are read by position (five variables were swapped in 0.1.0); the RCE inlet
  temperature (`t_rc_inlet_c`) is recovered; the placeholder `1e-30` is removed before averaging.
- 2025: units A and B are kept separate in every month (`RCE-a`, `RCE-b`); the October–November 2025 export is
  included.
- Days and variables present in the WinCC exports but missing from the processed workbooks are now harmonized
  (August–October 2024, December 2025, radiometer-only days of 2022–2025).
- New `quality/cell_flags`: readings the SCADA marked invalid, frozen recordings, probes at 0 °C, stuck sensors and
  periods where a channel measured something else; error codes are removed before averaging.
- Per-group counts `n_<group>` and per-group usable coverage; `n_samples` counts only readings with a value.
- Labels: `mode` replaced by `flow_period` (empty without usable flow data); experiment type with confidence and
  source; `diary_flag` for periods the laboratory declared unreliable; empty cover columns removed.
- Published `quality/known_events.csv` and `quality/lineage.csv`; `raw/index.csv` gains `kind` and
  `used_in_harmonized`; reader hints describe separators, encodings and the WinCC trailer.

## 0.1.0 (withdrawn)
First build, published on 2026-10-15 and withdrawn the same day.
