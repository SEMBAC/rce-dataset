# Methodology: how the harmonized data were built

## Layers
1. **Original files (`raw/`)**: the files the harmonized values come from, deduplicated by SHA-256 and listed in
   `raw/index.csv` with their original file names, a reader hint derived from each file's header, their `kind`
   (`instrument_export`, `processed_workbook`, `spreadsheet_copy`, `hand_edited`, `logger_binary`) and whether they
   contributed values (`used_in_harmonized`). Documents (diaries, reports, drawings), analysis workbooks and legacy
   `.xls` workbooks are excluded (`raw/excluded.csv` gives the reason per file).
   **Office files are published without personal or machine metadata**: author fields of `docProps/*.xml` are blanked,
   and the path of the author's computer (`x15ac:absPath`), the revision pointer and the printer settings are removed;
   every cell value is identical to the original. `index.csv` therefore carries two hashes: `sha256` (the original
   content, used for deduplication and provenance) and `sha256_published` (the shipped file, the one that
   `checksums.sha256` verifies); `sanitized` and `sanitized_fields` say what was removed.
2. **Source layer** (project internal, not shipped): one wide CSV per day, produced by `standardize.py` (v2) from the
   original files. `quality/lineage.csv` lists, for each day, the published files that contributed values.
3. **Harmonized layer (`harmonized/`)**: one table per year (Parquet and CSV) with canonical English variable names
   and a common 5-minute grid. This is the layer most users want.

## Sources and precedence
Per day and per variable, the first source that has the variable wins; later sources only fill variables (or whole
days) that are missing:
1. processed monthly workbooks `Data_YYYY_MM.xlsx` (2023–2024 and 1–5 September 2025); the workbook of the day's own
   month wins over a workbook that spills into another month;
2. the 2019 data logger (`RCE.CSV`, `RCE_IR.CSV`, one reading per minute);
3. the WinCC long-format exports (`Lecturas0`, `Solarimeter0`, `Pyrgeometer0`, 2022–2025), read from the unique
   files of the catalogue; overlapping exports repeat records and the first file (by path) is kept.

The WinCC SCADA logs **cyclically, every 300 s**, all tags at the same instant. The processed workbooks are copies of
those records arranged by month.

## Readings removed
Only readings that are not measurements are removed:
- WinCC records with `Validity != 1` and the `$RT_OFF$` / `$RT_COUNT$` lines, when the value comes from a WinCC export;
- 2019 logger channels without a sensor (`3276.7`; values ≥ 3000) and the logger placeholder `1e-30`;
- error codes listed in `docs/variable_map.yaml` (`codes`): the raw pyrgeometer overflow `996298.6` and the transmitter
  limit codes −20 °C and −10 °C. They are removed **before** averaging; the count per day is `codes_removed_n` in
  `quality/daily_qc.csv`.

Readings that the processed workbooks copied although the SCADA had marked them invalid are **kept and flagged**
(`scada_invalid`, see below), because the workbooks do not carry the validity column.

## 2019 logger
The 2019 tags were mapped to the modern names (`docs/variable_map.yaml`):

| 2019 tag | Source layer | Canonical | Note |
|---|---|---|---|
| `COL_TNK_TE` | `TempT6` | `t_hot_tank_c` | hot tank |
| `RC_TNK_TEM` | `TempT9` | `t_cold_tank_c` | cold tank |
| `COL_IN_TEM` | `TempT1` | `t_circuit_1_c` | approximate equivalence; no sensor connected in 2019 |
| `RC_OU_TEM` | `TempT2` | `t_circuit_2_c` | RCE inlet (T2); approximate equivalence |
| `RC_IN_TEMP` | `TempRCin` | `t_circuit_4_c` | RCE outlet (T4); approximate equivalence |
| `FLOW_RC` | `CauQ1` | `flow_rce_l_min` | |
| `FLOW_COL` | `CauQ_col` | `flow_collector_l_min` | 2019 only |
| `PYRG_NRAD` | `ir_net` | `ir_net_w_m2` | negative = net loss to the sky (see `known_issues.md` #5) |
| `SOLARIM` | `solar` | `solar_global_w_m2` | mounting plane not documented |
| `T_EXT`, `HR_EXT` | `TempExt`, `HumExt` | `t_ambient_c`, `rh_ambient_pct` | |

In the files `RCE.CSV` 00–08 (10–25 July 2019) the header has an empty column and every following label sits one
position too far to the right, while the data keep the normal order. These files are read **by position** in the
order `COL_IN_TEM, COL_TNK_TE, FLOW_COL, FLOW_RC, RC_OU_TEM, RC_TNK_TEM, RC_IN_TEMP` (the order the later files carry
in their header).

The 2019 tags name the RCE temperatures from the cold-tank side: `RC_OU_TEM` (out of the tank) is the fluid entering
the panel and `RC_IN_TEMP` (into the tank) the fluid leaving it. They are therefore published in the same columns as the
2023–2025 probes T2 (RCE inlet) and T4 (RCE outlet), and the inlet–outlet difference is `t_circuit_2_c − t_circuit_4_c`
in every year.

## The 5-minute grid
- Intervals `[hh:00, hh:05)`, labelled by their start (`ts_local` floored to 5 minutes, local time).
- Each value is the **mean** of the readings that fall in the interval (about 5 in 2019, about 1 in 2022–2025).
- `n_samples`: source readings in the interval with at least one value for that system; `n_<group>` (e.g.
  `n_tanks`, `n_solar`): readings with at least one value of the group. 0 = gap.
- **No interpolation.** Intervals without readings are present with missing values and `n_samples = 0`.
- Every day covers the full 288 intervals for each system present that day.

## Flow noise floor
Flow readings between −0.05 and 0 L/min are electronic noise around zero and are set to 0 (`flow_clipped_n` in
`quality/daily_qc.csv`).

## Quality flags (`quality/cell_flags.csv`)
One row per flagged cell (`system`, `ts_local`, `variable`, `flag`). Flagged values are kept in the harmonized tables;
the flag says that they should not be read as measurements:

| Flag | Rule |
|---|---|
| `scada_invalid` | a WinCC record with `Validity != 1` exists for that variable in that interval and the value was nevertheless present (copied by a processed workbook) |
| `frozen` | at least 80 % of the variables present are unchanged from the previous interval for at least 12 consecutive intervals (1 h) |
| `definition_differs` | a documented period in which the channel measured something else (`flag_periods` in `docs/variable_map.yaml`) |
| `zero` | a temperature at exactly 0.0 °C for at least 1 h (probe not connected), or the solarimeter at ≤ 0 W/m² for a whole day |
| `stuck` | the same value repeated for at least 12 h (zeros of flow, wind and solar, and RH ≥ 99 % are not counted) |

When several rules apply, the first in the table is kept. `daily_qc.csv` counts each flag per day and lists in
`suspect_sensors` the variables with at least half of their values flagged that day (plus probes named in
`known_events.csv`). `validity_checked` is true when a WinCC export covers the day, i.e. when `scada_invalid` could be
evaluated. `daily_coverage.csv` gives, per variable group, the share of intervals with at least one unflagged value.

## Labels (`labels/days.csv`)
- `operated`: water circulated through the RCE (`flow_rce_l_min` > 0.05 L/min in at least one unflagged interval);
  empty when the day has no usable flow reading.
- `flow_period`: when water circulated — `day` (08:00–18:59 only), `night`, `day+night`, `none`; empty without usable
  flow. It is **not** the physical operating mode (solar heating or radiative cooling): the valve and cover states that
  would define the mode are not recorded (`known_issues.md` #13).
- `exp_type`, `exp_confidence`, `exp_source`: experiment type (1–4, `docs/experiment_types.md`) only where the
  laboratory diary describes the day, with the confidence of that reading (`high` = the diary names the type literally).
- `diary_flag`: `unreliable` when the diary declares the period unreliable, `event` for other recorded events.

## Time
- `ts_local`: Europe/Madrid local time, as recorded (naive timestamps in the sources).
- `ts_utc`: derived from `ts_local`. Local times that are ambiguous or non-existent around daylight-saving changes
  are left missing (`ts_utc_nat_n` in `quality/daily_qc.csv`).

## Systems
- `system = RCE`: the single prototype (2019–2024) and, in 2025, tags without a unit suffix (cold-tank stratification
  probes, auxiliary tank probes).
- `system = RCE-a` / `RCE-b`: tags with suffix `_RCEa` / `_RCEb` (2025). Unit A is the original prototype, automated
  in 2025 (controller); unit B was built in 2025 as the manual test bench, next to unit A. The single pyrgeometer is on
  unit A.

## What is not transformed
No calibration, no sign change of the infrared signal, no unit conversion of raw radiometer signals, no filling of
gaps, no removal of out-of-range values (they are counted in `quality/daily_qc.csv`, not altered).

## Columns not harmonized
Source columns whose meaning or scale is not documented (`SEÑ_GEN_SENSOR`, `TEMPC`, `TEMPF`, `VALORES_MEDIOS`,
`IO_BODY_TEMPERATURE`, `IO_RAW_SENSOR1_DATA`, `solar_pyr`, `PreP21`, `E_FIR, calc.`, `E_FIR, lect.`, pyrgeometer
temperatures and EMF signal, electrical meters) are not in the harmonized tables. They remain available in `raw/`;
the main ones are listed in `docs/data_dictionary.csv` with status `not_harmonized`.

## Reproducibility
The package is generated by `standardize.py` (source layer) and `build_release.py` (package) from the original files;
`validation_report.md` records the automatic checks of the build.
