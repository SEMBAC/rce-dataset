# Glossary for non-specialists

This dataset comes from a research prototype in Lleida (Spain). Column names in the original files are in
Spanish or Catalan (the language of the laboratory); each entry below gives the English term and the original
term or SCADA *tag* in parentheses. The device and its first proof of concept are described in S. Vall, M. Medrano,
C. Solé, A. Castell, Energies 13 (2020) 893 (https://doi.org/10.3390/en13040893); the other entries rely on the
laboratory documentation and diary. Entries marked *[not documented]* are described as far as the
project files allow; the missing detail is not stated in them.

## 1. The device
- **Radiative Collector and Emitter (RCE)** (*col·lector-emissor radiatiu*): a modified commercial flat solar thermal collector (BAXI SOL200) that works in two ways: by day it collects solar heat, by night it emits heat towards the sky and cools down.
- **Solar thermal collection** (*captació solar tèrmica*, mode SH): with the glass cover in place, sunlight heats the fluid circulating in the panel, which warms the hot tank.
- **Radiative cooling** (*refredament radiatiu*, mode RC): every surface emits infrared radiation; at night, with the cover removed and the sky clear, the panel loses more heat to the sky than it receives and drops below the air temperature, so it can cool the fluid.
- **Cover / glass** (*vidre*, *tapa*): the glazing of the collector. Kept on for heating; removed (motorised in the automatic system) for night cooling. The diary says "treiem el vidre" (we remove the glass).
- **Mode SH / mode RC**: the two operating modes above (solar heating by day / radiative cooling by night).
- **Automatic system vs manual test bench**: from 2025 two units stand side by side: unit A (`_RCEa`) is the original prototype, automated in 2025 and driven by the controller; unit B (`_RCEb`) was built in 2025 as the manual test bench. Until 2024 the original prototype was the only unit.

## 2. The installation
- **Hot tank / DHW tank** (*dipòsit calent*, *ACS*, tag `TempT6`): 150 L storage tank for domestic hot water (target about 60 °C).
- **Cold tank / CWT** (*dipòsit fred*, tag `TempT9`): 50 L storage tank charged by night cooling.
- **Heat-transfer fluid (HTF)**: the liquid that carries heat between panel and tanks: water-glycol 30 % in loops 1 and 2, water in loop 3. Energy calculations must use glycol properties (about 10 % lower heat capacity than water).
- **Loops 1 / 2 / 3**: the hydraulic circuits (panel loop and tank loops). Exact routing is given by the electrical and piping drawings 22UDL03 / 25UDL01, not included in this package. [not documented]
- **Valves EV1 / EV2**: electrovalves that send the fluid to the cold tank (EV1, RC mode) or to the hot tank (EV2, SH mode).
- **Pump** (*bomba*): circulates the HTF; "flow = 0" means the pump is off or the valves are closed.
- **Electric heater and air-source heat pump** (*resistència*, *aerotèrmia*): used in some tests to fix the starting or inlet temperature of the water.
- **Auxiliary tank** (tags `TempT22..T24`): circuit used to emulate a building demand (hot water draw-off). [not documented]

## 3. The sensors
- **Pt-100 / RTD**: platinum resistance thermometers used for all temperatures; ±0.045 °C as stated for this probe type in the 2020 proof of concept. Calibrations after 2020 are not documented.
- **Surface probes T3_1…T3_6**: six temperature probes on the panel surface; T3_3 is at the outlet (coldest point). Exact positions on the panel: [not documented]
- **Circuit probes T1, T2, T4, T5, T7, T8, T10, T11**: temperatures at points of the hydraulic circuit (inlets, outlets, exchanger); T2 and T4 are the inlet and outlet of the RCE panel; the mapping of each other number to a physical point follows the piping drawings. [not documented]
- **Cold-tank stratification probes T9_1..T9_3**: three probes at different heights inside the cold tank (warmer water stays on top); probe heights: [not documented]
- **Flowmeter** (*cabalímetre*, tag `CauQ1`; 2019: `FLOW_RC`, `FLOW_COL`): Schmidt Mess SDNC 503, ±4 %, litres per minute. In 2019 the panel had two loops that alternated, each with its own
  flowmeter: `FLOW_COL` by day (panel to hot tank) and `FLOW_RC` by night (panel to cold tank); from 2022 a single
  flowmeter measures the panel flow in both modes.
- **Pressure transmitters** (tags `PresPT1`, `PresPT2`): circuit pressure in bar, typically 2–2.5 bar according to the diary.
- **Pyranometer / solarimeter** (*solarímetre*, tag `IO_SENSOR1_DATA`; 2019: `SOLARIM`): Kipp & Zonen SMP6-A, installed in 2017 and calibrated in 2020, mounted horizontally: it measures global horizontal irradiance in W/m² (variable `solar_global_w_m2`). Faulty on several periods, including parts of 2025 (`quality/known_issues.md` #4).
- **Pyrgeometer** (*pirgeòmetre*, LP PIRG 01 DeltaOhm, ±5 %): measures infrared (longwave) radiation exchanged with the sky; "net" means received minus emitted. There is a single pyrgeometer, on unit A; the two units stand side by side, so its values apply to both, and the unit-B pyrgeometer tags (`_RCEb`) are not a measurement. `ir_net_w_m2` is the net value: downwelling atmospheric longwave minus the emission of the sensor body, so negative values mean a net loss to the sky (radiative cooling potential); `ir_down_w_m2` (2024–2025) is the downwelling atmospheric longwave itself, typically 200–450 W/m², and `t_pyrgeometer_c` the temperature of the sensor body. In 2022–2024 only a raw signal of undocumented scale was exported (`ir_raw_signal`).
- **Weather station** (tags `TempExt`, `HumExt`, `VelVent`; 2019: `T_EXT`, `HR_EXT`): outdoor air temperature, relative humidity and wind speed.

## 4. How the data were recorded
- **PLC** (programmable logic controller): the industrial computer that drives pumps, valves and cover.
- **SCADA / WinCC**: the Siemens supervision software that logs sensor values and exports them to files.
- **Tag**: the name of a signal in the SCADA (e.g. `TempT6_RCEa`). The suffix `_RCEa` / `_RCEb` identifies unit A / unit B (2025); unsuffixed tags in 2025 exports do not state the unit. [not documented]
- **Validity**: WinCC quality flag attached to each reading (1 = valid). Values taken from the WinCC exports keep only Validity = 1; values copied by the processed monthly workbooks are kept and flagged `scada_invalid` when the SCADA had marked them invalid (see `methodology.md`).
- **$RT_OFF$**: marker written by WinCC when logging stops; those rows are removed.
- **Sampling**: the WinCC SCADA logs every tag cyclically every 300 s; the 2019 logger recorded every minute. The harmonized layer averages readings into 5-minute intervals.
- **Cell flag**: a mark in `quality/cell_flags.csv` saying that a value is present but should not be read as a measurement (invalid in the SCADA, frozen recording, disconnected probe at 0 °C, stuck sensor, channel measuring something else).
- **Flow period**: when water circulated through the panel on a day (day, night, both, none); it is not the operating mode.
- **Local time**: timestamps are Europe/Madrid local time as recorded by the SCADA; a UTC column is derived from them.
- **Sentinel value 3276.7** (2019): value written by the data logger for a channel without a sensor; removed. The logger also writes `1e-30` when it has no reading; removed as well.

## 5. The experiments
- **Experiment type 1 – no control of the initial temperature**: the water starts at whatever temperature it has and drifts freely.
- **Experiment type 2 – fixed initial temperature**: the tank starts at a set temperature and then evolves freely.
- **Experiment type 3 – constant tank temperature**: the initial temperature is fixed and maintained during the whole test (heater or heat pump compensate).
- **Experiment type 4 – stagnant water**: pump off, valves closed; the water inside the panel is left still and its temperature change is measured ("tanquem aixetes i bomba", diary 2024-10-01).
- **Operated day** (*dia útil*): a day with flow > 0 at some moment; days without flow are "idle" (`operated` in `labels/days.csv`).
- **Campaign**: a set of test days; the 2019–2020 campaigns (dates and flows) are listed in `experiment_types.md` and, per day, in `campaign_evidence` of `labels/days.csv`.

## 6. Terms used in the analyses
- **COP (coefficient of performance)**: useful thermal energy divided by the energy spent to obtain it (pumps, auxiliary equipment).
- **Thermal efficiency**: useful heat gained divided by the solar (or radiative) energy available.
- **ΔT** (delta T): a temperature difference, e.g. inlet minus outlet, or tank minus ambient.
- **Daily energy**: V·ρ·Cp·ΔT of a tank (volume × density × heat capacity × temperature change), computed with glycol properties.
- **Persistence (forecasting)**: the trivial forecast "the temperature will not change"; the usual reference that a prediction model must beat.
- **Forecast horizon**: how far ahead a prediction is made.
- **Provenance `real` / `model` / `ai`**: whether a value was measured, simulated or predicted. Everything in this dataset is `real`.

## 7. Abbreviations
- **ACS / DHW**: *agua caliente sanitaria* / domestic hot water.
- **CWT**: cold water tank.
- **HTF**: heat-transfer fluid.
- **SH / RC**: solar heating / radiative cooling.
- **RTD**: resistance temperature detector (the Pt-100 probes).
- **GHI / GTI**: global horizontal / global tilted irradiance; the solarimeter here is mounted horizontally and measures GHI (see `solar_global_w_m2`).
- **IR**: infrared (longwave) radiation.
- **TMY**: typical meteorological year (a synthetic year used in building simulation; not part of this dataset).
