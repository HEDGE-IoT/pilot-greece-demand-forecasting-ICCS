# Demand Forecasting Notebooks

Generated from `service-1.py`, focusing only on demand forecasting.

Files:

- `demand_forecasting_aggregated_pipeline.ipynb`: aggregated hourly/quarterly demand forecasting, direct and recursive models, optional weather-enhanced extension.
- `demand_forecasting_individual_pipeline.ipynb`: individual household/device 15-minute recursive demand forecasting.

Key safety changes:

- Secrets are read from environment variables.
- Target and feature scalers are separated clearly.
- Recursive forecasts recompute lag/rolling/calendar features at each predicted step.
- Analytics and PV production forecasting are excluded.

Environment variables used by the notebooks:

- `EHS_DB_HOST`
- `EHS_DB_PORT`
- `EHS_DB_USER`
- `EHS_DB_PASSWORD`
- `EHS_DB_NAME`
- `FORECAST_ARTIFACT_DIR`