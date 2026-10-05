# Architecture

Ingestion → Validation/cleaning → Feature engineering → Forecasting (LightGBM, per horizon)
→ Uncertainty (quantile + conformal) → Explainability (TreeSHAP → plain language)
→ Anomaly/drift detection → FastAPI → Dashboard

(Add diagram to `docs/` when ready.)
