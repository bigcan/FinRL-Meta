# Specify (spec-kit) Adoption — Decision & Pilot

Decision: YES — adopt the Specify CLI from spec-kit as an acceptance/spec layer on top of existing pytest unit tests.

Why this fits
- Tests-as-docs for cross-cutting workflows (data ingestion, paper trading) improve review clarity and onboarding.
- Complements pytest: keep fast unit tests in `unit_tests/`, add readable scenario specs in `specs/`.
- Encodes data and lifecycle invariants (schemas, NaN policy, timezone, order lifecycle) without asserting stochastic RL performance.

Where to apply first (pilot scope)
- Data pipeline invariants: `DataProcessor` download/clean/features (shape, columns, NA policy, time index).
- Paper-trading happy path: mocked broker, deterministic feed; verify orders/positions/PnL invariants.
- Config wiring smoke: configs load, secrets present, pipelines boot without network.

Out of scope (for specs)
- RL performance/metrics assertions (keep as benchmarks/notebooks and invariant-based unit tests).

Pilot plan (2–3 weeks, <60s runtime)
1) Install & pin
   - Add the Specify CLI (spec-kit) as a dev dependency and pin the version (Node or Python distribution per team preference).
   - Provide `specify run` and `specify validate` scripts in package tooling.
2) Repository layout
   - Create `specs/` with domains: `specs/data/`, `specs/paper_trading/`, `specs/config/`, plus `specs/_fixtures/` and `specs/_steps/` for glue.
3) Determinism & fixtures
   - Use tiny date ranges and local/recorded HTTP (e.g., VCR cassettes) for data providers; no live network in CI.
4) Glue code
   - Thin step handlers that call repository APIs (e.g., `DataProcessor`, `trade` entrypoints); no deep logic in steps.
5) CI and pre-commit
   - Add a separate CI job to run `specify run` and publish HTML/JSON reports.
   - Add a pre-commit hook for `specify validate` (lint/schema only; fast).
6) Flakiness rules
   - Assert invariants (schemas, monotonicity, bounds), not floats/returns; fix seeds; record IO.

Success criteria
- Specs remain green across 3+ PRs with no >10% CI time increase.
- Reviewers report improved clarity on data/paper-trading behavior.
- Minimal maintenance overhead (few snapshot churns; fixtures stable).

Risks & mitigations
- Runner/tooling friction → Keep pytest as backbone; if integration blocks, use `pytest-bdd`/`pytest-describe` with same scenarios.
- Flaky/stochastic behavior → Deterministic fixtures, invariant assertions, fixed seeds, recorded IO.
- CI runtime growth → Isolate a dedicated "specs" job; cap data/time windows; fail fast.

Immediate next actions
- Choose packaging route (Node vs Python) and pin a version for the Specify CLI.
- Scaffold `specs/` tree and `_steps` glue modules.
- Draft first spec: Binance downloader invariants (1 symbol, short window) with recorded fixture.
- Add pre-commit `specify validate` and CI job for `specify run`.

Reference touchpoints in this repo
- Unit tests baseline: `unit_tests/test_binance.py`
- Configuration and entrypoints: `meta/config.py`, `main.py`, `trade.py`

Rollback plan
- If the pilot adds friction or CI cost without clarity gains, remove the `specs/` tree and CI job; retain pytest-only tests.

