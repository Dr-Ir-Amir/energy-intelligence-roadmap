# Data Dictionary — Energy Intelligence Roadmap

## Overview
This document describes every field in the merged analysis-ready dataset (`data/processed/energy_dataset.parquet`). All timestamps are UTC. All data is aligned to a half-hourly grid.

---

## Core Fields

| Field | Type | Units | Source | Description |
|---|---|---|---|---|
| `timestamp` | datetime64[ns, UTC] | — | Derived | Half-hourly period start (e.g. 00:00, 00:30, 01:00 …) |
| `carbon_intensity_actual` | float64 | gCO₂/kWh | NESO Carbon Intensity API | Actual carbon intensity of the GB electricity grid |
| `carbon_intensity_forecast` | float64 | gCO₂/kWh | NESO Carbon Intensity API | NESO's own forecast of carbon intensity |
| `carbon_intensity_index` | category | — | NESO Carbon Intensity API | Categorical band: very low / low / moderate / high / very high |

## Generation Mix Fields
Each field represents the percentage contribution of a fuel type to total generation for that half-hour.

| Field | Type | Units | Source | Description |
|---|---|---|---|---|
| `biomass` | float64 | % | NESO Carbon Intensity API | Biomass generation share |
| `coal` | float64 | % | NESO Carbon Intensity API | Coal generation share |
| `gas` | float64 | % | NESO Carbon Intensity API | Natural gas (CCGT + OCGT) generation share |
| `hydro` | float64 | % | NESO Carbon Intensity API | Hydro generation share |
| `imports` | float64 | % | NESO Carbon Intensity API | Interconnector imports share |
| `nuclear` | float64 | % | NESO Carbon Intensity API | Nuclear generation share |
| `other` | float64 | % | NESO Carbon Intensity API | Other sources share |
| `solar` | float64 | % | NESO Carbon Intensity API | Solar generation share |
| `wind` | float64 | % | NESO Carbon Intensity API | Wind generation share |

## Demand Fields

| Field | Type | Units | Source | Description |
|---|---|---|---|---|
| `demand_MW` | float64 | MW | NESO Carbon Intensity API (national) | National grid demand |

## Weather Fields (London, 51.51°N 0.13°W)

| Field | Type | Units | Source | Description |
|---|---|---|---|---|
| `temperature_2m` | float64 | °C | Open-Meteo | Air temperature at 2 m above ground |
| `relative_humidity_2m` | float64 | % | Open-Meteo | Relative humidity at 2 m |
| `wind_speed_10m` | float64 | km/h | Open-Meteo | Wind speed at 10 m above ground |
| `cloud_cover` | float64 | % | Open-Meteo | Total cloud cover |
| `shortwave_radiation` | float64 | W/m² | Open-Meteo | Global horizontal irradiance (proxy for solar potential) |

## Derived / Calendar Fields

| Field | Type | Units | Source | Description |
|---|---|---|---|---|
| `hour` | int | 0–23 | Derived | Hour of day (UTC) |
| `day_of_week` | int | 0–6 | Derived | Monday=0, Sunday=6 |
| `month` | int | 1–12 | Derived | Calendar month |
| `is_weekend` | bool | — | Derived | True if Saturday or Sunday |
| `is_outlier_intensity` | bool | — | Derived | True if carbon intensity is outside 1.5×IQR |
| `is_outlier_demand` | bool | — | Derived | True if demand is outside 1.5×IQR |

## Quality Flags

| Field | Type | Description |
|---|---|---|
| `intensity_ffilled` | bool | True if carbon intensity was forward-filled (original was NaN) |
| `demand_ffilled` | bool | True if demand was forward-filled |
| `weather_interpolated` | bool | True if weather value was interpolated from hourly to half-hourly |

---

## Notes
- The generation mix percentages should sum to ~100% per period. Small rounding discrepancies are expected.
- Weather data is sourced hourly and linearly interpolated to half-hourly to match the energy data grid.
- All Parquet files use snappy compression.
