# Changelog

## 0.2.3
- **2019 RCE inlet and outlet.** The 2019 logger names the RCE temperatures from the cold-tank side: `RC_OU_TEM` (out of
  the tank) is the fluid entering the panel and `RC_IN_TEMP` (into the tank) the fluid leaving it. Up to 0.2.2 `RC_IN_TEMP`
  was published as `t_rc_inlet_c`, an inlet name for the outlet. From 0.2.3 the 2019 outlet is in `t_circuit_4_c` and the
  inlet stays in `t_circuit_2_c`, the columns of the 2023–2025 probes T4 and T2, so the inlet–outlet difference is
  `t_circuit_2_c − t_circuit_4_c` in every year. **The column `t_rc_inlet_c` no longer exists**: scripts that read it
  must read `t_circuit_4_c` and treat it as the outlet. With flow at night, the inlet is warmer than the outlet in 96 % of
  the 2019 intervals (median +0.34 K), as expected in radiative cooling.
- 2019-07-11 09:15 to 2019-07-12 11:10: a hand-edited source file repeats the inlet in the outlet column; these
  303 cells of `t_circuit_4_c` are flagged `definition_differs` (values kept).
- Descriptions of `t_circuit_2_c` and `t_circuit_4_c`: RCE inlet (T2) and RCE outlet (T4); exact probe positions not documented.
- **Units in 2025**: unit A (`RCE-a`) is the original prototype, automated in 2025 and driven by the controller; unit B
  (`RCE-b`) was built in 2025 next to it as the manual test bench. There is a single pyrgeometer, on unit A; its values
  apply to both units and the unit-B pyrgeometer tags are not a measurement (glossary, methodology, known issues #5 and #8).
- Items the project files do not state (probe positions and heights, solarimeter model and mounting plane, sign
  convention of the infrared signal, units of the 2025 electrical meters) are declared as *not documented* instead of
  *to be confirmed*.
- Site: the prototype is on the roof of the CREA building (Escola Politècnica Superior, Universitat de Lleida).
- Metadata: author affiliation written as "SEMBAC, Universitat de Lleida, Edifici CREA, C/Pere de Cabrera s/n. 25001
  Lleida (Spain)"; author name "Mselle, Boniface".
- Measured values are otherwise unchanged from 0.2.2.

## 0.2.2
- Documentation and metadata: pointers to internal project files and unpublished documents removed from the glossary,
  the sensor sheet, the experiment types, `docs/variable_map.yaml` (`source_ref`), `docs/data_dictionary.csv` and
  `datapackage.json`. Provenance is given as SCADA or logger tags, the laboratory documentation and diary, and the
  published proof of concept of the device (Energies 13 (2020) 893).
- Glossary: performance figures of a forthcoming publication removed; entries describe terms only.
- `labels/days.csv`: `campaign_evidence` and `notes` name the 2019–2020 test campaigns by mode and season (for example
  `RC summer 2019 (high flow)`) instead of table numbers of an unpublished paper.
- Pt-100 accuracy given as ±0.045 °C (was written ±0.045 %). The solarimeter is described as faulty on several
  periods, including parts of 2025 (it was described as out of service for the whole of 2025).
- `datapackage.json` carries the release version.
- Measured values, flags and the other tables are unchanged from 0.2.0.

## 0.2.1
- Acknowledgements: Generalitat de Catalunya, consolidated research group 2021 SGR 01370; full title of TED2021-131446B-I00
  (BOOST-RCE) in the funding metadata.
- Authors: Boniface Dominick Mselle added (data collection of the 2019–2020 campaigns).
- Data, flags and documentation otherwise unchanged from 0.2.0.

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
