# RCE experimental dataset — version 0.2.3



Measurements from the **Radiative Collector and Emitter (RCE)** prototype of the University of Lleida (Spain): a
building-integrated flat panel that collects solar heat by day and provides radiative cooling by night, with a hot
(150 L) and a cold (50 L) storage tank and an adaptive cover. Experimental campaigns from 2019 to 2025.

Everything in this dataset was **measured** (provenance `real`). No simulated or predicted values are included.

## What is inside
| Folder | Content |
|---|---|
| `harmonized/` | One table per year (`rce_YYYY.parquet` + `.csv`): 5-minute grid, canonical English variable names, local and UTC time, `system` (unit), `n_samples` and per-group counts `n_<group>`. |
| `labels/days.csv` | One row per day and unit: `operated`, `flow_period` (when water circulated; not the physical mode), experiment type with its confidence and diary source, `diary_flag` (days the laboratory declared unreliable), notes. |
| `quality/` | `cell_flags.csv` / `.parquet` (values present but not to be read as measurements, one row per flagged cell), `daily_coverage.csv` (usable share of each variable group per day), `daily_qc.csv` (gaps, flags, removed codes, out-of-range counts, suspect sensors), `known_events.csv` (laboratory diary), `lineage.csv` (original files behind each day), `known_issues.md`. |
| `raw/` | Original files behind the harmonized values (SCADA/logger exports and the processed monthly workbooks; analysis workbooks, documents and legacy `.xls` excluded), deduplicated by SHA-256, with `index.csv` (file names, not folder paths; original and published hashes; `kind`; `used_in_harmonized`), `excluded.csv` and `checksums.sha256`. Excel files are published without personal or machine metadata; cell values are untouched (see `docs/methodology.md`). |
| `docs/` | `estructura_carpetas.md` (folder structure, in Spanish), `glossary.md` (for non-specialists), `sensors_and_site.md`, `data_dictionary.csv`, `variable_map.yaml`, `methodology.md`, `experiment_types.md`. |
| `datapackage.json` | Machine-readable description (Frictionless Data). |

## Estructura de carpetas
```text
rce-dataset/
├── harmonized/        Los datos: una tabla por año (rce_AAAA.parquet │ .csv), un valor cada 5 minutos
├── labels/            Qué pasó cada día: days.csv (funcionamiento, circulación del agua, tipo de ensayo)
├── quality/           Cuánto fiarse: celdas marcadas, cobertura y control diario, incidencias, trazabilidad
├── raw/               De dónde sale todo: índice, exclusiones y huellas SHA-256 (originales en Zenodo)
├── docs/              Cómo entenderlo: glosario, diccionario, sensores, metodología, tipos de ensayo
├── datapackage.json   Descripción legible por máquina (Frictionless Data)
├── CITATION.cff       Cómo citar el dataset
├── CHANGELOG.md       Cambios de cada versión
└── LICENSE            CC BY 4.0
```
Guía completa en castellano, con cada fichero, sus columnas, la nomenclatura y cómo se cruzan las tablas:
[`docs/estructura_carpetas.md`](docs/estructura_carpetas.md).

## Years and days (harmonized layer)
| Year | Days | Rows (5-min) |
|---|---|---|
| 2019 | 37 | 10656 |
| 2022 | 94 | 27072 |
| 2023 | 258 | 74304 |
| 2024 | 181 | 52128 |
| 2025 | 196 | 108000 |

Total: 766 days. Original files: 220 (1.16 GB).

Days per month in the harmonized layer (months not listed have no data; see `quality/known_issues.md` #11):
| Year | Month | Days |
|---|---|---|
| 2019 | 07 | 21 |
| 2019 | 08 | 16 |
| 2022 | 09 | 18 |
| 2022 | 10 | 31 |
| 2022 | 11 | 30 |
| 2022 | 12 | 15 |
| 2023 | 01 | 4 |
| 2023 | 03 | 31 |
| 2023 | 04 | 30 |
| 2023 | 05 | 31 |
| 2023 | 06 | 30 |
| 2023 | 07 | 29 |
| 2023 | 08 | 16 |
| 2023 | 09 | 15 |
| 2023 | 10 | 30 |
| 2023 | 11 | 30 |
| 2023 | 12 | 12 |
| 2024 | 01 | 16 |
| 2024 | 02 | 27 |
| 2024 | 03 | 18 |
| 2024 | 04 | 17 |
| 2024 | 07 | 17 |
| 2024 | 08 | 1 |
| 2024 | 09 | 12 |
| 2024 | 10 | 31 |
| 2024 | 11 | 25 |
| 2024 | 12 | 17 |
| 2025 | 01 | 24 |
| 2025 | 02 | 24 |
| 2025 | 03 | 12 |
| 2025 | 04 | 7 |
| 2025 | 06 | 1 |
| 2025 | 07 | 2 |
| 2025 | 08 | 18 |
| 2025 | 09 | 30 |
| 2025 | 10 | 31 |
| 2025 | 11 | 30 |
| 2025 | 12 | 17 |

## Quick start
```python
import pandas as pd
df = pd.read_parquet("harmonized/rce_2024.parquet")
day = df[(df["date"] == "2024-04-02") & (df["system"] == "RCE")]
day.plot(x="ts_local", y=["t_hot_tank_c", "t_cold_tank_c", "t_ambient_c"])
```
Variable meanings and units: `docs/data_dictionary.csv`. New to the device? Start with `docs/glossary.md`.

## Things to know before using the data
- **Use the flags.** Some values are present but are not measurements (readings the SCADA marked invalid, frozen recordings, probes at 0 °C, stuck sensors): drop the cells listed in `quality/cell_flags.csv` before any analysis, and the days with `diary_flag = unreliable` in `labels/days.csv`.
- Gaps are explicit: rows with `n_samples = 0` have missing values; nothing was interpolated.
- The infrared radiation sign/scale differs by period; see `quality/known_issues.md` #5.
- `flow_period` says when water circulated, not whether the panel was heating or cooling (valve and cover states were not recorded).
- The heat-transfer fluid is water-glycol 30 %, not water.
- Experiment types are assigned only where the laboratory diary or the energy paper gives evidence.
- Timestamps: `ts_local` is Europe/Madrid local time as recorded; `ts_utc` is derived.

## Citation
See `CITATION.cff`. DOI: [https://doi.org/10.5281/zenodo.23458309](https://doi.org/10.5281/zenodo.23458309).

## Authors
Vitor da Silva, Albert Castell, Roger Vila, Boniface Mselle, Cristian Solé, Josep Lluís Lérida, Marc Medrano,
Fernando Guirado, Ingrid Martorell — SEMBAC, Universitat de Lleida, Edifici CREA, C/Pere de Cabrera s/n. 25001 Lleida (Spain).

## Licence
[Creative Commons Attribution 4.0 International (CC BY 4.0)](https://creativecommons.org/licenses/by/4.0/). See `LICENSE`.

## Funding
This work has been granted by the Ministerio de Ciencia, Innovación y Universidades (MICIU) AEI/10.13039/501100011033 under contract PID2023-146193OB-I00. This work is also part of the grant TED2021-131446B-I00, funded by MCIN/AEI/10.13039/501100011033 and by the European Union NextGenerationEU/PRTR. The authors would like to thank the Generalitat de Catalunya for the project awarded to their research group (2021 SGR 01370).

## Contact
Vitor da Silva — vitor.dasilva@udl.cat
