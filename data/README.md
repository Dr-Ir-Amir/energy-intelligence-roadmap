# Data Directory

## Overview
This folder contains all raw and processed datasets used in the Energy Intelligence Roadmap project.

## Sources

| Dataset | Source | Licence | Refresh Cadence | Format |
|---|---|---|---|---|
| Carbon Intensity (half-hourly) | [NESO Carbon Intensity API](https://api.carbonintensity.org.uk/) | CC BY 4.0 | Half-hourly (auto) | JSON → Parquet |
| Generation Mix (half-hourly) | [NESO Carbon Intensity API](https://api.carbonintensity.org.uk/) | CC BY 4.0 | Half-hourly (auto) | JSON → Parquet |
| UK Grid Demand (half-hourly) | [NESO Carbon Intensity API](https://api.carbonintensity.org.uk/) | CC BY 4.0 | Half-hourly (auto) | JSON → Parquet |
| Weather (hourly, London) | [Open-Meteo Historical API](https://open-meteo.com/) | CC BY 4.0 | On-demand | JSON → Parquet |

## Timezone Convention
All timestamps are stored in **UTC**. Any local-time conversions happen at the analysis or presentation layer, never in storage.

## File Naming
- `raw/` — data as received from APIs (before cleaning)
- `processed/` — cleaned, merged, resampled to a common half-hourly grid
- `carbon_intensity.parquet` — half-hourly carbon intensity (gCO₂/kWh)
- `generation_mix.parquet` — half-hourly generation mix (% by fuel type)
- `demand.parquet` — half-hourly national demand (MW)
- `weather.parquet` — hourly weather for London, resampled to half-hourly
- `energy_dataset.parquet` — the merged, analysis-ready dataset

## Missing Value Strategy
- **Carbon intensity / generation mix**: forward-fill up to 2 periods (1 hour), then flag as missing
- **Weather**: linear interpolation (smooth physical quantity)
- **Demand**: forward-fill up to 2 periods, then flag

## Outlier Handling
Outliers are **flagged, not deleted**. A boolean `is_outlier` column is added where relevant, using the IQR method (1.5× IQR beyond Q1/Q3). Analysts can filter as needed.

## Refresh Instructions
Run the ingestion notebook to pull the latest data:
```bash
jupyter lab notebooks/week1_energy_data_fundamentals.ipynb
```
Execute all cells — the notebook handles API calls, cleaning, and Parquet export.
