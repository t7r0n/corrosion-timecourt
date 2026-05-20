# Corrosion Backtest Local

Industrial asset-health forecasts need auditable calibration before they can drive Navy, energy, and manufacturing maintenance decisions.

The demo hook is concrete: A decision-date slider proves whether a corrosion forecast would have held up once actual inspection data arrived.

## Use case

Offline predictive-maintenance forecast calibration for ultrasonic asset-health histories.

## Signal design

- Seeds `asset` fixtures for `corrosion-backtest` with both normal operations and faulted paths.
- Computes `calibration_error`, `interval_coverage`, `mape_lift`, and `sensor_drift_caught` from deterministic inputs so the result can be reproduced exactly.
- Stress-tests `uncalibrated_decay`, `inspection_cadence_gap`, `sensor_drift`, and `pit_aggregation_loss` as named failure classes rather than vague edge cases.
- Packages `Corrosion Backtest Local` artifacts for code review, live demo, and regression comparison.

## Demo path

```bash
uv sync --extra dev
uv run corrosion-backtest init-demo --force
uv run corrosion-backtest run-suite
uv run corrosion-backtest verify
uv run corrosion-backtest dashboard
uv run corrosion-backtest benchmark --iterations 100
uv run corrosion-backtest export-demo-pack
```

## Files worth opening

- `data/scenarios.json`
- `outputs/summary.json`
- `outputs/reports.json`
- `outputs/evidence_pack.md`
- `outputs/dashboard.html`
- `outputs/benchmark.json`
- `outputs/demo-pack.zip`

## Build checks

```bash
uv run ruff check .
uv run pytest -q
uv run corrosion-backtest run-suite
uv run corrosion-backtest verify
uv run corrosion-backtest benchmark --iterations 100
```

## Data policy

Every example in `corrosion-backtest-local` is fabricated for repeatability. Generated outputs are rebuildable artifacts, not source material.
