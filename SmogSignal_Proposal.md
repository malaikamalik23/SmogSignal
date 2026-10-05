# SmogSignal: Explainable, Uncertainty-Aware Air-Pollution Early Warning for Lahore

**Track:** AI in Urban Tech
**Keywords:** Air Quality Forecasting, Explainable AI, Uncertainty Estimation, Early Warning System, Distribution Shift Detection

---

## 1. Project Overview & Problem Statement

Lahore regularly ranks among the most polluted cities in the world, with severe smog episodes every winter. Existing apps (AQI dashboards) show only **current** pollution. Residents, schools, and city planners lack three things:

1. **Advance warning**: what air quality will look like 24-72 hours from now.
2. **Understanding**: *why* pollution is expected to rise (weak winds, temperature inversion, upwind crop/waste fires).
3. **Trust**: how reliable a prediction is, especially during unusual weather or when sensors are missing.

Black-box forecasts that give a single number with no context are easy to ignore and easy to over-trust. Lahore also has sparse ground monitoring, so pretending to offer perfect neighbourhood-level truth would be scientifically dishonest.

## 2. Proposed Solution

**SmogSignal** is a web-based early-warning system that forecasts PM2.5 for Lahore at 6, 12, 24, 48, and 72 hours ahead and converts it to AQI only for display. Its promise:

> Predict when air quality will become dangerous, explain **why**, show **what changed** since the last forecast, and tell users **how reliable** the prediction is.

PM2.5 (not AQI) is the modelling target because it is the physical quantity; AQI is a non-linear, piecewise transformation of it and would add artificial discontinuities to the learning problem.

## 3. Objectives & Expected Outcomes

| Objective | Expected outcome |
|---|---|
| Accurate multi-horizon forecasts | Gradient-boosted model beating persistence and seasonal baselines on time-aware test data |
| Plain-language explanations | Every forecast shows top drivers translated from SHAP into environmental reasoning |
| "What changed?" | Each new forecast is diffed against the previous one with the likely reasons |
| Honest uncertainty | Calibrated prediction intervals (conformal prediction) with measured coverage |
| Self-awareness | Anomaly/distribution-shift detector that lowers the reliability score |
| Trustworthy AI evaluation | Explanation-stability and drift metrics reported alongside accuracy |
| Demoable product | Replayable demo that never depends on a live API |

## 4. Target Users

- **Residents and parents** of Lahore planning commutes, school runs, and outdoor activity.
- **Schools and event organisers** deciding on outdoor activities.
- **Journalists, NGOs, and researchers** who need transparent, auditable forecasts.
- **City/environment agencies** (secondary) looking for early-warning decision support.

## 5. Key Features / Deliverables

**Must have**
- 24-72 h PM2.5 forecasts at 5 horizons, with AQI conversion for display
- Hero panel: "Smog risk tomorrow: HIGH, expected peak PM2.5, peak time, confidence"
- **Why?** panel: SHAP values mapped to plain-language drivers (weak wind, atmospheric stability, upwind fires, humidity)
- **What changed?** panel: forecast diff plus a timeline of how forecasts evolve as data arrives
- **Confidence / uncertainty:** conformal prediction intervals and ensemble disagreement, with reasons when confidence is low
- **Unusual conditions detector:** flags inputs outside the training distribution and lowers reliability
- Interactive Lahore map separating **observed** (station) from **model-estimated** values, with a confidence overlay
- General exposure guidance by AQI category (clearly labelled *not medical advice*)
- Scientific evaluation report

**Should have**
- NASA FIRMS fire-hotspot layer and upwind-fire feature
- Model Health panel (accuracy, data drift, explanation stability, sensor coverage)
- Area selection (e.g. Gulberg) with a 24 h summary
- Historical replay mode with a "Simulate New Update" button

**Nice to have (only if time remains)**
- Alerts (SMS/WhatsApp), multi-city support, personalised recommendations

## 6. Technical Approach (Summary)

**Pipeline:** Ingestion → Validation/cleaning → Feature engineering → Forecasting → Uncertainty → Explainability → Anomaly/drift detection → FastAPI → Dashboard

- **Forecasting:** LightGBM/XGBoost, direct multi-horizon strategy (one model per horizon). Features: lagged PM2.5, rolling stats, wind speed and direction (u/v components), temperature, humidity, pressure and its tendency, boundary-layer height and stability proxies, precipitation, hour/day/season encodings, fire counts upwind within a bearing-aware radius.
- **Baselines:** (1) persistence, (2) seasonal/hourly climatology, (3) Ridge/linear model; final model is the tuned gradient-boosting ensemble.
- **Uncertainty:** quantile models plus split-conformal calibration; ensemble spread as a secondary signal. Reported as calibrated intervals and measured empirical coverage. Confidence labels are derived from interval width and coverage, never invented.
- **Explainability:** TreeSHAP, grouped into meteorology, fires, persistence, and calendar, then mapped to templated plain-language statements. The UI explicitly separates *model importance* from *environmental interpretation*; we make no causal claims.
- **Anomaly / drift:** Isolation Forest and Mahalanobis distance on key inputs versus the training distribution; PSI/KS tests for data drift over rolling windows.
- **Explanation stability:** compare SHAP attribution vectors across seasons, high/low pollution periods, and train vs test windows (rank correlation, cosine similarity, top-k overlap), including cases where predictions are similar but attributions differ.
- **Spatial layer:** station observations interpolated with weather and geographic covariates, displayed only within a supported radius of data, with uncertainty rising with distance from sensors. Observed and estimated values are visually distinct.

## 7. Data Sources

All sources are public. Licence terms must be re-verified before final submission.

| Data | Source | Notes |
|---|---|---|
| Air quality (PM2.5, PM10) | OpenAQ; US Embassy/Consulate Lahore monitor; community sensors (e.g. PurpleAir) if licence permits | Sparse coverage; gaps and sensor quality will be documented, with missing % measured |
| Weather (forecast and reanalysis) | Open-Meteo API; ERA5 (Copernicus) for training history | Hourly, ~0.1-0.25° resolution |
| Fire activity | NASA FIRMS (VIIRS / MODIS active fires) | Daily/near-real-time hotspots |
| Geography | OpenStreetMap, Pakistan administrative boundaries | Boundaries, coordinates, land-use context |

For each dataset the repository documents source, licence, resolution, update frequency, and measured missing-data percentage. **No data is fabricated.** Demo mode uses cached real historical data.

## 8. Implementation Plan & Timeline

| Phase | Work | Output |
|---|---|---|
| M1 | Data collection and caching, cleaning, missing-data audit | Clean hourly dataset and data card |
| M2 | Feature engineering, baselines, time-aware split (train A / validate B / test C) | Baseline metrics |
| M3 | Final model, multi-horizon training, conformal intervals | Forecasts with calibrated intervals |
| M4 | SHAP pipeline, plain-language explanation engine, forecast-diff ("What changed?") | Explainable forecasts |
| M5 | Anomaly detector, drift monitoring, explanation-stability analysis | Model Health panel data |
| M6 | FastAPI backend, SQLite storage, replay engine | Working API |
| M7 | Dashboard (hero, why, what changed, 72 h chart, map, reliability) | Polished UI |
| M8 | Evaluation report, demo rehearsal, pitch, fallback checks | Submission-ready |

**Scope rule:** if behind schedule, cut in this order: notifications → multi-city → personalised guidance → spatial interpolation (fall back to station-only map). Never cut forecasting, explanation, uncertainty, "what changed", or evaluation.

## 9. Resources / Requirements

- **Team:** 3 students (ML, backend, frontend/UX, data/evaluation)
- **Software:** Python, Pandas, NumPy, scikit-learn, LightGBM/XGBoost, SHAP, MAPIE or custom conformal code, FastAPI, SQLite, Plotly, Leaflet, React/Next.js (or Streamlit as fallback), Docker
- **Compute:** a laptop or free-tier cloud; no GPU needed
- **Data access:** free API keys (NASA FIRMS, OpenAQ)

## 10. Evaluation Methodology

- **Time-aware validation:** train on period A, validate on later period B, test on future period C; no random splits. Additional seasonal hold-out (e.g. train on other seasons, test on winter).
- **Metrics:** MAE, RMSE, R² (where appropriate), AQI-category accuracy and F1, high-pollution-event recall.
- **Breakdowns:** by horizon, by season, and during high-pollution events.
- **Uncertainty:** prediction-interval coverage versus nominal level, interval width, calibration plots.
- **Robustness:** performance under simulated missing and sparse sensors; behaviour under distribution shift.
- **Explanation stability:** attribution similarity across seasons, periods, pollution regimes, and normal vs unusual weather.
- **Comparison:** persistence vs linear vs final model.

## 11. Potential Challenges or Risks

| Risk | Mitigation |
|---|---|
| Sparse or noisy sensor data in Lahore | Document gaps, QC filtering, show uncertainty, restrict spatial claims to supported areas |
| Short usable history, limiting seasonal generalisation | Use multi-year reanalysis for weather; report seasonal results honestly, including failures |
| Live API outages during demo | Cached-data replay mode as the default; live is optional |
| SHAP misread as causation | Wording rules and explicit "model attribution, not proven cause" labelling |
| Weak 48-72 h skill | Report honestly; widen intervals and lower confidence at long horizons |
| Time pressure | Strict MVP-first plan above |

## 12. Hackathon Demo Flow (Additional Details)

1. Open the dashboard: "Smog risk tomorrow: HIGH", confidence, peak time.
2. Show the **Why?** panel with plain-language drivers.
3. Press **Simulate New Update**: new weather and fire data arrive.
4. The forecast changes, "What changed?" shows the delta and reasons, confidence updates, and the map refreshes.
5. Trigger an **unusual conditions** case: the anomaly banner appears and reliability drops.
6. Close on the Model Health and evaluation panel: accuracy, drift, explanation stability, interval coverage.

**Pitch:** People can see today's pollution but not tomorrow's, or why it is changing. SmogSignal gives a 24-72 hour early warning that explains *why*, shows *what changed*, and says *how reliable it is*, designed to be transparent and aware of its own limitations.

*General air-quality guidance shown in the app is not medical advice.*
