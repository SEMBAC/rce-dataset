# Glossary for non-specialists

This dataset comes from a research prototype in Lleida (Spain). Column names in the original files are in
Spanish or Catalan (the language of the laboratory); each entry below gives the English term, the original
term or SCADA *tag* in parentheses, and where the information comes from. Entries marked
*[to be confirmed by the team]* are documented as far as the project files allow and are being verified.

## 1. The device
- **Radiative Collector and Emitter (RCE)** (*col·lector-emissor radiatiu*): a modified commercial flat solar thermal collector (BAXI SOL200) that works in two ways: by day it collects solar heat, by night it emits heat towards the sky and cools down. [source: draft paper A1, Materials and Methods; docs/02_rce_context.md]
- **Solar thermal collection** (*captació solar tèrmica*, mode SH): with the glass cover in place, sunlight heats the fluid circulating in the panel, which warms the hot tank. Measured 140–324 W/m² in summer, 115–220 W/m² in winter. [source: A1 Conclusions]
- **Radiative cooling** (*refredament radiatiu*, mode RC): every surface emits infrared radiation; at night, with the cover removed and the sky clear, the panel loses more heat to the sky than it receives and drops below the air temperature, so it can cool the fluid. Measured 15–25 W/m² in summer, 10–18 W/m² in winter. [source: A1 Conclusions]
- **Cover / glass** (*vidre*, *tapa*): the glazing of the collector. Kept on for heating; removed (motorised in the automatic system) for night cooling. The diary says "treiem el vidre" (we remove the glass). [source: docs/02_rce_context.md; diary]
- **Mode SH / mode RC**: the two operating modes above (solar heating by day / radiative cooling by night). [source: data_catalog/02 §2]
- **Automatic system vs manual test bench**: from 2025 two units exist, one driven by the controller and one operated by hand for comparison; the diary and SCADA tags call them A (`_RCEa`) and B (`_RCEb`). Which letter is the automatic unit is a working hypothesis of the project notes (docs/02_rce_context.md, marked pending). [to be confirmed by the team]

## 2. The installation
- **Hot tank / DHW tank** (*dipòsit calent*, *ACS*, tag `TempT6`): 150 L storage tank for domestic hot water (target about 60 °C). [source: docs/02_rce_context.md table]
- **Cold tank / CWT** (*dipòsit fred*, tag `TempT9`): 50 L storage tank charged by night cooling. [source: docs/02_rce_context.md]
- **Heat-transfer fluid (HTF)**: the liquid that carries heat between panel and tanks: water-glycol 30 % in loops 1 and 2, water in loop 3. Energy calculations must use glycol properties (about 10 % lower heat capacity than water). [source: A1; DECISION_LOG D-011]
- **Loops 1 / 2 / 3**: the hydraulic circuits (panel loop and tank loops). Exact routing is given by the P&ID drawings 22UDL03 / 25UDL01, not included in this package. [to be confirmed by the team]
- **Valves EV1 / EV2**: electrovalves that send the fluid to the cold tank (EV1, RC mode) or to the hot tank (EV2, SH mode). [source: docs/02_rce_context.md diagram]
- **Pump** (*bomba*): circulates the HTF; "flow = 0" means the pump is off or the valves are closed. [source: diary; data_catalog/03 §4]
- **Electric heater and air-source heat pump** (*resistència*, *aerotèrmia*): used in some tests to fix the starting or inlet temperature of the water. [source: data_catalog/02 §1]
- **Auxiliary tank** (tags `TempT22..T24`): circuit used to emulate a building demand (hot water draw-off). [to be confirmed by the team]

## 3. The sensors
- **Pt-100 / RTD**: platinum resistance thermometers (±0.045 %) used for all temperatures. [source: A1 Table 2 via docs/02_rce_context.md]
- **Surface probes T3_1…T3_6**: six temperature probes on the panel surface; T3_3 is at the outlet (coldest point). Exact positions on the panel: [to be confirmed by the team]
- **Circuit probes T1, T2, T4, T5, T7, T8, T10, T11**: temperatures at points of the hydraulic circuit (inlets, outlets, exchanger); the mapping of each number to a physical point follows the P&ID. [to be confirmed by the team]
- **Cold-tank stratification probes T9_1..T9_3**: three probes at different heights inside the cold tank (warmer water stays on top). [to be confirmed by the team]
- **Flowmeter** (*cabalímetre*, tag `CauQ1`; 2019: `FLOW_RC`, `FLOW_COL`): Schmidt Mess SDNC 503, ±4 %, litres per minute. [source: A1 Table 2]
- **Pressure transmitters** (tags `PresPT1`, `PresPT2`): circuit pressure in bar, typically 2–2.5 bar. [source: A1; diary]
- **Pyranometer / solarimeter** (*solarímetre*, tag `IO_SENSOR1_DATA`; 2019: `SOLARIM`): measures global solar irradiance in W/m² (variable `solar_global_w_m2`); whether it is mounted horizontally or in the panel plane is not stated in the project files. Out of service in 2025. [source: data_catalog/03; BITACORA] [to be confirmed by the team]
- **Pyrgeometer** (*pirgeòmetre*, LP PIRG 01 DeltaOhm, ±5 %): measures infrared (longwave) radiation exchanged with the sky; "net" means received minus emitted. The sign convention and the unit differ between years in the files; values are given as recorded. [source: A1 Table 2; data_catalog/01 §3]
- **Weather station** (tags `TempExt`, `HumExt`, `VelVent`; 2019: `T_EXT`, `HR_EXT`): outdoor air temperature, relative humidity and wind speed. [source: data_catalog/01 §1]

## 4. How the data were recorded
- **PLC** (programmable logic controller): the industrial computer that drives pumps, valves and cover. [source: docs/03_code_architecture.md]
- **SCADA / WinCC**: the Siemens supervision software that logs sensor values and exports them to files. [source: data_catalog/01]
- **Tag**: the name of a signal in the SCADA (e.g. `TempT6_RCEa`). The suffix `_RCEa` / `_RCEb` identifies unit A / unit B (2025); unsuffixed tags in 2025 exports do not state the unit. [source: data_catalog/01 §1] [to be confirmed by the team]
- **Validity**: WinCC quality flag attached to each reading (1 = valid). Values taken from the WinCC exports keep only Validity = 1; values copied by the processed monthly workbooks are kept and flagged `scada_invalid` when the SCADA had marked them invalid. [source: methodology.md]
- **$RT_OFF$**: marker written by WinCC when logging stops; those rows are removed. [source: data_catalog/03 §1]
- **Sampling**: the WinCC SCADA logs every tag cyclically every 300 s; the 2019 logger recorded every minute. The harmonized layer averages readings into 5-minute intervals. [source: methodology.md]
- **Cell flag**: a mark in `quality/cell_flags.csv` saying that a value is present but should not be read as a measurement (invalid in the SCADA, frozen recording, disconnected probe at 0 °C, stuck sensor, channel measuring something else). [source: methodology.md]
- **Flow period**: when water circulated through the panel on a day (day, night, both, none); it is not the operating mode. [source: methodology.md]
- **Local time**: timestamps are Europe/Madrid local time as recorded by the SCADA; a UTC column is derived from them. [source: methodology.md]
- **Sentinel value 3276.7** (2019): value written by the data logger for a channel without a sensor; removed. The logger also writes `1e-30` when it has no reading; removed as well. [source: methodology.md]

## 5. The experiments
- **Experiment type 1 – no control of the initial temperature**: the water starts at whatever temperature it has and drifts freely. [source: Marc Medrano, email B2; data_catalog/02 §1]
- **Experiment type 2 – fixed inlet temperature**: the temperature of the water entering the RCE is held constant. [source: email B2; data_catalog/02 §1]
- **Experiment type 3 – constant tank temperature**: the tank is kept at a set temperature while the RCE works (heater or heat pump compensate). [source: email B2; data_catalog/02 §1]
- **Experiment type 4 – stagnant water**: pump off, valves closed; the water inside the panel is left still and its temperature change is measured ("tanquem aixetes i bomba"). [source: email B2; diary 2024-10-01]
- **Operated day** (*dia útil*): a day with flow > 0 at some moment; days without flow are "idle". [source: data/README; labels/days.csv]
- **Campaign**: a set of test days; the 2019–2020 campaigns (dates and flows) are tabulated in the energy paper draft. [source: data_catalog/02 §4]

## 6. Terms used in the analyses
- **COP (coefficient of performance)**: useful thermal energy divided by the energy spent to obtain it; the controller uses a COP threshold to decide whether operating is worthwhile. [source: docs/03_code_architecture.md]
- **Thermal efficiency**: useful heat gained divided by the solar (or radiative) energy available; 40–46 % for heating and 22–28 % for cooling in summer. [source: A1 Conclusions]
- **ΔT** (delta T): a temperature difference, e.g. inlet minus outlet, or tank minus ambient. [source: A1]
- **Daily energy**: V·ρ·Cp·ΔT of a tank (volume × density × heat capacity × temperature change), computed with glycol properties. [source: data/etl/metrics.py]
- **Persistence (forecasting)**: the trivial forecast "the temperature will not change"; the reference that every prediction model must beat. [source: reports/paper_ml]
- **Forecast horizon**: how far ahead a prediction is made (15 minutes to 2 hours in the project's models). [source: reports/paper_ml]
- **Provenance `real` / `model` / `ai`**: whether a value was measured, simulated or predicted. Everything in this dataset is `real`. [source: data_catalog/04]

## 7. Abbreviations
- **ACS / DHW**: *agua caliente sanitaria* / domestic hot water. [source: docs/02_rce_context.md]
- **CWT**: cold water tank. [source: docs/02_rce_context.md]
- **HTF**: heat-transfer fluid. [source: A1]
- **SH / RC**: solar heating / radiative cooling. [source: data_catalog/02]
- **RTD**: resistance temperature detector (the Pt-100 probes). [source: A1]
- **GHI / GTI**: global horizontal / global tilted irradiance; which one the solarimeter measures here is pending (see `solar_global_w_m2`). [to be confirmed by the team]
- **IR**: infrared (longwave) radiation. [source: A1]
- **TMY**: typical meteorological year (used only for simulation, not in this dataset). [source: reports/paper_ml]
