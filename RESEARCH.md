# Research And Plan Review

Company: Gecko Robotics
Project: `cantilever-backtest`

## Refined Thesis

Industrial asset-health forecasts need auditable calibration before they can drive Navy, energy, and manufacturing maintenance decisions.

The implementation is intentionally local and synthetic, but the test harness is shaped around the real operating question: can the proposed artifact create evidence a founder, CTO, or product leader would immediately recognize as useful?

## Fresh Sources Checked

- https://www.geckorobotics.com/sustainment
- https://techcrunch.com/2026/03/17/gecko-robotics-lands-the-largest-u-s-navy-robotics-deal-yet/
- https://breakingdefense.com/2026/03/navy-taps-gecko-robotics-for-ship-scaling-robots-to-identify-repairs-in-pacific-fleet/

## Plan Excerpt Used

## The Gap

Cantilever is described everywhere as "the operating platform for the built world," and its [own marketing page](https://www.geckorobotics.com/cantilever) lists modules — AIR, Aerial Scans, Advanced Manufacturing, Sustainment — but **no public way for Navy depot engineers, energy customers, or partners like [Trident](https://www.geckorobotics.com/news/gecko-robotics-and-trident-to-accelerate-u-s-navy-production-with-ai-and-robotics) to programmatically query, simulate, or *backtest decisions* against Cantilever's data layers**. The corrosion-rate / remaining-life numbers that drive a multi-million-dollar repair go/no-go land in PDF-style dashboards. There is no public SDK, no developer docs surface, no `cantilever.geckorobotics.com/api/v1`, no replay tool — and the [Robot Report piece on Fulcrum](https://www.therobotreport.com/fulcrum-provides-inspection-data-pipeline-for-cantilever-analysis-explains-gecko-robotics/) shows ingest is plumbed but the *outbound* developer story is missing. The gap: **defense and energy customers want to ask "if Cantilever said `replace tank wall 3B in 18 months` in 2024, what was the actual decay vs. predicted?" — a backtesting / counterfactual surface for predictive-maintenance forecasts.** Without it, Cantilever is a black box at exactly the moment the Navy's [80% readiness target](https://www.techbuzz.ai/articles/gecko-robotics-lands-navy-ai-deal-to-hit-80-fleet-readiness) requires auditable, defensible decisions.

## The Project — `cantilever-backtest`

> A shippable backtesting and counterfactual-replay toolkit for predictive-maintenance forecasts on ultrasonic thickness data — the "Stripe Sigma" for industrial decay models, demoed on real ASME B31G pipeline test datasets.

1. **What it is.** A Python/Rust hybrid library + a thin web UI that ingests time-series ultrasonic thickness measurements (the public Open Pit Corrosion Dataset + synthetic-but-realistic shipboard tank traces matched to U.S. Navy [DDG-class wastage curves](https://en.wikipedia.org/wiki/Arleigh_Burke-class_destroyer) published in NAVSEA standards), trains a small ensemble of decay models (linear, log-linear, Bayesian Weibull, conformalised gradient-boosted), and exposes a single core API: `Backtest(asset_id, as_of=t0).predict(horizon=18mo).vs_actual(t1)`. Users get: forecast vs. realised plots, calibration diagrams (does "70% chance of <0.250" actually hit 70%?), and a counterfactual slider — "if inspection had been monthly not quarterly, p95 error drops X%."

2. **Why it solves the gap.** Cantilever's promise to the Navy is *prioritisation under uncertainty*. That promise is only credible if forecasts are calibrated, and calibration is only knowable by backtesting on locked-in historical asset data. Today there is no public artefact letting a Navy depot CO, a [Trident partnership](https://www.geckorobotics.com/news/gecko-robotics-and-trident-to-accelerate-u-s-navy-production-with-ai-and-robotics) joint engineer, or a Founders-Fund-due-diligence analyst pressure-test the decay-model class behind Cantilever. `cantilever-backtest` *is* that artefact — built against public corrosion datasets so it can ship Day 1, then trivially pointable at private Cantilever exports.

3. **The "wow" moment.** A 90s Loom shot: load 14 years of ultrasonic data from a public oil-tank dataset, slide a "decision date" cursor to 2018, watch the 5-year forecast appear; slide forward to 2023, the realised curve overlays; the calibration plot pins at 71% on the 70% target band. A side panel labelled *"savings if Cantilever-style inspection cadence had been used"* shows the counterfactual gap. **The reviewer sees a credible audit story Gecko could brand and ship to a Navy program office on Monday.**

## Prototype Plan (the shippable demo)

- **CLI / web surface:**
  - `cantilever-backtest fit --dataset tanks-2010-2024.arrow --as-of 2018-06-01`
  - `cantilever-backtest report --horizon 18mo --out calibration.html`
  - `streamlit run apps/replay.py` for the slider UI.
- **5 representative inputs:**
  1. NIST oil tank floor corrosion benchmark dataset (public).
  2. Synthetic DDG-51 tank wall traces seeded from NAVSEA T9074 wastage limits.
  3. A power-plant boiler tube dataset (public, EPRI).
  4. A mining haul-truck wear dataset (public, from CSIRO).
  5. A *deliberately broken* dataset (sensor drift inserted at week 200) to demo that the calibration diagram flags the drift.
- **Output:** a single HTML report with forecast-vs-actual, calibration plot, counterfactual slider; plus a CLI table of "decisions you would have made if you'd used Cantilever-style monthly cadence."
- **Metrics:** calibration error (target <5% on 70/80/90 bands), interval coverage, p95 forecast MAPE vs. naive linear baseline, latency from load to first chart (<3s on 1M points).


## Build Acceptance Criteria

- Deterministic local fixtures.
- Domain-specific metrics and failure modes.
- Passing unit tests.
- Passing CLI verifier.
- Static dashboard generated locally.
- Benchmark output under the project `outputs/` folder.
- Public-safe README: no founder emails, no private outreach text, no credentials.
