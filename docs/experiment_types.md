# Experiment types and operating modes

## The four experiment types
Definitions given by the energy group that operates the installation; examples come from the laboratory diary and
from the laboratory's analysis of the 2023–2024 night tests.

| Type | Name | Description | Expected signature in the data |
|---|---|---|---|
| **1** | No control of the initial temperature | The water to be heated/cooled starts at whatever temperature it has. | flow > 0; free drift of the tank temperature. |
| **2** | Fixed initial temperature | The tank starts at a set temperature; afterwards it evolves freely (it is not held). | tank temperature set at the start, then drifting; flow > 0. |
| **3** | Constant tank temperature | Initial temperature fixed **and** maintained during the whole test (heater / heat pump compensate). | tank temperature ≈ flat; stable inlet–outlet ΔT. |
| **4** | Stagnant water | Water left still inside the RCE; its internal temperature change is measured. | flow ≈ 0 while the test is active; surface probes drift below ambient at night. |

## The two operating modes (orthogonal to the types)
| Mode | Period | Cover | Target tank |
|---|---|---|---|
| **SH** – solar heating | day (about 08–19 h local) | glass on | hot tank / DHW (`t_hot_tank_c`) |
| **RC** – radiative cooling | night (about 19–07 h local) | glass removed, lids open | cold tank (`t_cold_tank_c`) |

The recorded data do not contain the valve or cover states, so the physical mode of a day cannot be read from the
files. `labels/days.csv` gives only `flow_period`, derived from when the flow was > 0.05 L/min: `day` (08:00–18:59
only), `night`, `day+night`, `none`; it is empty when there is no usable flow reading. A day with flow by day and by
night is not necessarily a day of both modes (the pump of a unit may run continuously).

## Examples from the laboratory diary (traceable)
| Date | Diary note (translated) | Type / mode |
|---|---|---|
| 2023-06-22 | "heating mode… see whether we reach 60 °C" | SH (DHW target) |
| 2023-07-13 | "radiative cooling… cold tank set-point changed 30→45 °C" | the laboratory analysis classifies the night test as type 1 |
| 2023-07-17 to 07-20 | "we open / remove the glass… automatic mode" | automatic SH/RC cycles |
| 2024-02-28 | "radiative cooling… pump and valves manual, initial T 42 °C" | type 2 (initial T fixed) |
| 2024-03-06 | "circulation towards the cold tank… heater… keep inlet T at 40–42 °C" | type 3 |
| 2024-04-04 | "radiative cooling tests with controlled inlet T of 40 °C" | type 3 (temperature held) |
| 2024-09-17, 09-23, 09-24, 09-25, 10-21 | "we remove the glass, we run **experiment type 1**" | type 1 (literal label) |
| 2024-10-01, 10-03 | "we close the taps and the pump, **experiment type 4**" | type 4 (literal label, flow 0) |

## 2019–2020 test campaigns (laboratory test programme)
- Radiative cooling (night): summer 2019: 1–2, 12–13, 13–14, 14–15, 15–16 August (flow 0.8–0.9 L/min); high-flow
  tests 17–22 July (1.7–1.8 L/min). Winter 2020: 17–18, 18–19, 20–21, 25–26, 27–28 February (flow 0.8 L/min).
- Solar heating (day): summer 2019: 17–21 July, 2–4 August (flow 4.1 L/min). Winter 2020: 17–24 and 28 February.

The campaigns that cover a harmonized day are listed in `campaign_evidence` of `labels/days.csv` (for example
`RC summer 2019 (high flow)`); 2020 has no harmonized data, its files are in `raw/`.

## Night tests of 2023–2024 (laboratory analysis)
The laboratory analysed and classified 23 night tests of 2023–2024 (radiative cooling only; type 1: 13, type 2: 3, type 3: 5, type 4: 2).
In `labels/days.csv` they carry `exp_source = lab_analysis` and `exp_confidence = high`, on the date the night test
starts; this classification wins over the diary reading, which is kept in `notes`.

## Honesty note
`exp_type` is set only where the diary describes the day or the laboratory analysis classifies the night test;
`exp_confidence` says how directly: `high` for the laboratory analysis and when the diary names the type literally
("experiment tipus 1"), `medium` or `low` when the type is inferred from the procedure the diary describes. The large majority of days are `unknown`. This is a fact about
the records, not a defect of the data.
