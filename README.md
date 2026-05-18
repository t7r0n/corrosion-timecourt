# Cantilever Backtest Local

Offline predictive-maintenance forecast calibration for ultrasonic asset-health histories.

This is a local-first, synthetic-data prototype inspired by a company-specific project plan for **Gecko Robotics**. It is built to demonstrate the engineering shape of `cantilever-backtest` without private data, credentials, external APIs, or hosted services.

## Why it matters

Industrial asset-health forecasts need auditable calibration before they can drive Navy, energy, and manufacturing maintenance decisions.

## What it does

- Generates deterministic synthetic `asset` scenarios.
- Scores each scenario against domain-specific quality gates.
- Produces evidence-backed findings for realistic failure modes.
- Writes a static dashboard, JSON reports, benchmark output, and a portable demo pack.
- Exposes a JSONL tool loop for local agent integration.

## Metrics

- `calibration_error`
- `interval_coverage`
- `mape_lift`
- `sensor_drift_caught`

## Failure modes

- `uncalibrated_decay`
- `inspection_cadence_gap`
- `sensor_drift`
- `pit_aggregation_loss`

## Quickstart

```bash
uv sync --extra dev
uv run cantilever-backtest init-demo --force
uv run cantilever-backtest run-suite
uv run cantilever-backtest verify
uv run cantilever-backtest dashboard
uv run cantilever-backtest benchmark --iterations 100
uv run cantilever-backtest export-demo-pack
```

## Expected outputs

- `data/scenarios.json`
- `outputs/summary.json`
- `outputs/reports.json`
- `outputs/evidence_pack.md`
- `outputs/dashboard.html`
- `outputs/benchmark.json`
- `outputs/demo-pack.zip`

## Validation

```bash
uv run ruff check .
uv run pytest -q
uv run cantilever-backtest run-suite
uv run cantilever-backtest verify
uv run cantilever-backtest benchmark --iterations 100
```

## Demo hook

A decision-date slider proves whether a corrosion forecast would have held up once actual inspection data arrived.
