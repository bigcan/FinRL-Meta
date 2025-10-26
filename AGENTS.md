# Repository Guidelines

## Project Structure & Module Organization
Core environment logic lives in `meta/`, including configs, data pipelines, and trading workflows. Reinforcement learning wrappers are in `agents/` grouped by backend (ElegantRL, RLlib, Stable-Baselines3). Reference notebooks, scripts, and demos sit under `examples/`, while production-friendly scripts (`main.py`, `train.py`, `trade.py`) are in the repository root. Tests for market data providers reside in `unit_tests/`, and visual assets appear in `figs/`. Treat `Paper Trading/` as the staging ground for deployment templates and live trading integrations.

## Build, Test, and Development Commands
Create or refresh a virtual environment, then install dependencies with `python -m pip install -r requirements.txt`. Run `pre-commit install` once, and execute `pre-commit run --all-files` before every push to enforce formatting and import order. Validate functionality via `pytest unit_tests` and optionally narrow focus with patterns such as `pytest unit_tests/test_binance.py::test_download`. Kick off end-to-end workflows through `python main.py --mode=train` or `python trade.py --config configs/paper_trading.yaml` after credentials are set in `meta/config.py`.

## Coding Style & Naming Conventions
Follow PEP 8, 4-space indentation, and type hints for new modules. Public classes use `PascalCase`, functions and variables use `snake_case`, and constants remain uppercase. Let Black drive formatting (`black .`), and keep imports sorted using the bundled `reorder-python-imports` hook. Keep module-level docstrings concise and highlight configuration expectations with inline comments where ambiguity could impede other agents.

## Testing Guidelines
Tests rely on PyTest; place new cases under `unit_tests/` with filenames matching `test_*.py`. Parametrize over exchanges, date ranges, or data sources to capture edge cases. Maintain fast turnaround (<60s) for unit-level tests, supplementing with notebook demonstrations only when strictly necessary. Record baseline metrics for algorithms in README tables or notebooks so performance regressions surface quickly.

## Commit & Pull Request Guidelines
Write commits in present tense imperative (e.g., `Add alpaca data refresher`) and include issue or PR references when relevant. Pull requests should summarize motivation, list verification steps (`pytest`, `pre-commit`, smoke runs), and attach artifacts such as benchmark comparisons or screenshots of training curves. Flag configuration changes, required credentials, or data migrations in bold within the PR body, and request review from domain owners owning the touched subsystems.

## Communication Guidelines
Always use simple, plain, easy-to-understand English when communicating with users.
