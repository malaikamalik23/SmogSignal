# 🌫️ SmogSignal

**Explainable, uncertainty-aware 24–72 hour air-pollution early warning for Lahore, Pakistan.**

> SmogSignal predicts when air quality will become dangerous, explains **why**, shows **what changed**, and tells users **how reliable** the prediction is.

**Status:** 🚧 Hackathon project in development. Features below are marked as planned or in progress. Nothing here is claimed as complete until it is built and evaluated.

---

## Problem

People can see today's pollution, but not what tomorrow looks like or why conditions are changing. Most AQI apps are black boxes: one number, no reasoning, no sign of how trustworthy it is. Lahore also has sparse ground monitoring, so overconfident neighbourhood-level claims would be scientifically dishonest.

## Solution

SmogSignal forecasts **PM2.5** at 6, 12, 24, 48 and 72 hours ahead (converted to AQI only for display) and adds:

| Feature | Description | Status |
|---|---|---|
| Multi-horizon forecast | Gradient-boosted model, one per horizon | Planned |
| **Why?** | SHAP converted into plain-language environmental drivers | Planned |
| **What changed?** | Diff against the previous forecast with likely reasons | Planned |
| Calibrated uncertainty | Conformal prediction intervals and ensemble disagreement | Planned |
| Unusual-conditions detector | Flags inputs outside the training distribution | Planned |
| Explanation stability | Checks attribution consistency across seasons and regimes | Planned |
| Model health panel | Accuracy, drift, stability, sensor coverage | Planned |
| Lahore map | Observed vs model-estimated values, with confidence overlay | Planned |
| Replay demo mode | "Simulate New Update" using cached real data | Planned |

> SHAP shows model attribution, **not causation**. Explanations are labelled accordingly.

## Architecture

```
Ingestion → Validation → Feature engineering → Forecasting
   → Uncertainty → Explainability → Anomaly/Drift → FastAPI → Dashboard
```

## Tech Stack

Python · Pandas · NumPy · scikit-learn · LightGBM/XGBoost · SHAP · FastAPI · SQLite · Plotly · Leaflet · React/Next.js (Streamlit as fallback) · Docker

## Data Sources

| Data | Source |
|---|---|
| Air quality (PM2.5/PM10) | OpenAQ, US Embassy/Consulate Lahore monitor |
| Weather | Open-Meteo, ERA5 (Copernicus) |
| Fire activity | NASA FIRMS (VIIRS/MODIS) |
| Geography | OpenStreetMap, Pakistan admin boundaries |

See `docs/data_sources.md` for licences, resolution, update frequency and missing-data percentages. No data is fabricated. The demo runs on cached public data.

## Evaluation Plan

- Time-aware split: train (period A) → validate (period B) → test (period C)
- Baselines: persistence, seasonal climatology, linear model
- Metrics: MAE, RMSE, R², AQI-category F1, high-pollution recall
- Interval coverage and calibration; performance by horizon, season and high-pollution events
- Explanation stability, distribution shift, missing/sparse-sensor robustness

## Getting Started

*Coming soon.* Setup instructions will be added as the code lands.

## Roadmap

- [ ] M1: Data collection, cleaning, missing-data audit
- [ ] M2: Feature engineering and baselines
- [ ] M3: Final model and conformal intervals
- [ ] M4: Explanations and "What changed?"
- [ ] M5: Anomaly, drift and explanation-stability analysis
- [ ] M6: FastAPI backend and replay engine
- [ ] M7: Dashboard
- [ ] M8: Evaluation report and demo

## Disclaimer

SmogSignal provides general air-quality information and exposure-oriented guidance. It is **not medical advice**.

## Team

- Name 1: role
- Name 2: role
- Name 3: role

## License

MIT
