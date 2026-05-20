# Research And Plan Review

Project: `corrosion-backtest`

## Refined Thesis

Industrial asset-health forecasts need auditable calibration before they can drive Navy, energy, and manufacturing maintenance decisions.

The implementation is intentionally local and synthetic, but the test harness is shaped around the real operating question: can the proposed artifact create evidence a founder, CTO, or product leader would immediately recognize as useful?

## Local Evidence Basis

- Synthetic ultrasonic thickness histories.
- Deterministic corrosion-decay fixtures.
- Local forecast-vs-actual calibration reports.

## Plan Excerpt Used

## The Gap

Predictive-maintenance forecasts often land as dashboard numbers, but the operators making expensive repair decisions need to know whether prior forecasts were calibrated once later inspection data arrived. The gap is a backtesting and counterfactual surface for corrosion and wall-thickness forecasts: "if the model said replace this tank wall in 18 months, what actually happened, and how would monthly versus quarterly inspection cadence have changed the error band?"

## The Project — `corrosion-backtest`

> A shippable backtesting and counterfactual-replay toolkit for predictive-maintenance forecasts on ultrasonic thickness data — the "Stripe Sigma" for industrial decay models, demoed on real ASME B31G pipeline test datasets.

1. **What it is.** A Python/Rust hybrid library + a thin web UI that ingests time-series ultrasonic thickness measurements (the public Open Pit Corrosion Dataset + synthetic-but-realistic shipboard tank traces matched to U.S. Navy [DDG-class wastage curves](https://en.wikipedia.org/wiki/Arleigh_Burke-class_destroyer) published in NAVSEA standards), trains a small ensemble of decay models (linear, log-linear, Bayesian Weibull, conformalised gradient-boosted), and exposes a single core API: `Backtest(asset_id, as_of=t0).predict(horizon=18mo).vs_actual(t1)`. Users get: forecast vs. realised plots, calibration diagrams (does "70% chance of <0.250" actually hit 70%?), and a counterfactual slider — "if inspection had been monthly not quarterly, p95 error drops X%."

2. **Why it solves the gap.** Predictive-maintenance prioritization under uncertainty is only credible when forecasts are calibrated, and calibration is only knowable by backtesting on locked-in historical asset data. `corrosion-backtest` is a local artifact for pressure-testing the decay-model class before those forecasts drive maintenance decisions.

3. **The "wow" moment.** A reviewer loads 14 years of ultrasonic data, slides a decision-date cursor to 2018, watches the 5-year forecast appear, then slides forward to 2023 and sees the realized curve overlay the prediction. The calibration plot pins at 71% on the 70% target band, and a side panel shows the inspection-cadence counterfactual.

## Prototype Plan (the shippable demo)

- **CLI / web surface:**
  - `corrosion-backtest fit --dataset tanks-2010-2024.arrow --as-of 2018-06-01`
  - `corrosion-backtest report --horizon 18mo --out calibration.html`
  - `streamlit run apps/replay.py` for the slider UI.
- **5 representative inputs:**
  1. NIST oil tank floor corrosion benchmark dataset (public).
  2. Synthetic DDG-51 tank wall traces seeded from NAVSEA T9074 wastage limits.
  3. A power-plant boiler tube dataset (public, EPRI).
  4. A mining haul-truck wear dataset (public, from CSIRO).
  5. A *deliberately broken* dataset (sensor drift inserted at week 200) to demo that the calibration diagram flags the drift.
- **Output:** a single HTML report with forecast-vs-actual, calibration plot, counterfactual slider; plus a CLI table of decisions under monthly versus quarterly inspection cadence.
- **Metrics:** calibration error (target <5% on 70/80/90 bands), interval coverage, p95 forecast MAPE vs. naive linear baseline, latency from load to first chart (<3s on 1M points).


## Build Acceptance Criteria

- Deterministic local fixtures.
- Domain-specific metrics and failure modes.
- Passing unit tests.
- Passing CLI verifier.
- Static dashboard generated locally.
- Benchmark output under the project `outputs/` folder.
- Public-safe README: no founder emails, no private outreach text, no credentials.
