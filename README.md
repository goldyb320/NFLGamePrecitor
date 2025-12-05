## NFL Winner Predictions (Current Week)

This project provides a self-contained Jupyter notebook that pulls near real-time NFL data from ESPN’s public scoreboard API, builds simple season-to-date team ratings, predicts winners for the current week’s games, and backtests the method on past weeks with accuracy plots.

### What’s included
- `nfl_winner_predictions.ipynb`: The main notebook.
- `predictions_output/`: Folder where CSVs with current-week predictions are saved (created on first run).

### Features
- Fetches the most current week (season year, season type, week) from ESPN.
- Builds team ratings from completed games in prior weeks (points per game minus points allowed per game).
- Uses a small home-field advantage and converts rating differences to win probabilities via a logistic curve.
- Trains a simple ML model (logistic regression) each week on prior completed games using rating differential as a feature; uses the ML probability by default (falls back to heuristic if not enough data).
- Optionally blends with market-implied probabilities from ESPN odds when available (configurable weight).
- Predicts current-week winners and saves results to CSV.
- Backtests across past weeks using only prior-week information to compute accuracy.
- Plots weekly accuracy, cumulative accuracy, and a calibration (reliability) curve; prints the Brier score.
- Adds weekly and cumulative RMSE metrics (sqrt-Brier for probabilities and RMSE for point differential) and an accuracy table.

### Setup
Requirements:
- Python 3.9+
- Jupyter (Notebook or Lab)

The notebook auto-installs these Python packages if missing:
- `requests`, `pandas`, `matplotlib`, `seaborn`, `numpy`, `scikit-learn`

On Windows (WSL) or any shell:
1) (Optional) Create a virtual environment
   - `python -m venv .venv`
   - Windows PowerShell: `.venv\\Scripts\\Activate.ps1`
   - macOS/Linux/WSL: `source .venv/bin/activate`
2) Install Jupyter if needed: `python -m pip install jupyter`
3) Launch Jupyter:
   - `jupyter notebook` (or `jupyter lab`)
4) Open `nfl_winner_predictions.ipynb` and run all cells.

### How it works
- Data source: ESPN scoreboard (no API key required)
  - Base URL: `https://site.api.espn.com/apis/site/v2/sports/football/nfl/scoreboard`
  - Parameters: `year`, `seasontype` (1=Pre, 2=Reg, 3=Post), `week`
- Ratings:
  - For a given week w, team ratings are computed from completed games prior to week w.
  - Rating metrics: points per game (PPG), points allowed per game (PAPG), and net rating = PPG − PAPG.
- Prediction pipeline:
  - Heuristic: Home-field advantage (+1.5 by default) and logistic transform of rating differential → win probability.
  - ML: Logistic regression trained weekly on prior completed games with feature `home_rating − away_rating`; used when sufficient data exists.
  - Market odds blending (optional): If ESPN odds exist, blend model probability with moneyline-implied home win probability via a configurable weight.
- Backtesting:
  - For each week 1..N, compute ratings using weeks < current, predict week, compare to actual final scores.
  - Outputs weekly accuracy, cumulative accuracy, calibration plot, Brier score, and RMSE tables/plots.

Reference for ESPN NFL endpoints (including odds): `ESPN API ENDPOINTS` — see the curated list here: https://gist.github.com/nntrn/ee26cb2a0716de0947a0a4e9a157bc1c/b99b9e0d2df72470fa622e2f76cecb0362111e9a

### Configuration knobs (in the notebook)
- `HOME_FIELD_ADVANTAGE_POINTS` (default: 1.5)
- `LOGISTIC_SCALE` (default: 7.0) — higher = flatter probability curve
- `USE_ML_MODEL` (default: True) — enable logistic regression trained on prior weeks.
- `USE_MARKET_ODDS` (default: True) — blend with moneyline-implied probability when available.
- `BLEND_WEIGHT` (default: 0.30) — weight for market blending (0.0=model-only, 1.0=market-only when available).

### Outputs
- CSV: Saved in `predictions_output/` as `nfl_predictions_w{WEEK}_{YEAR}_{TIMESTAMP}.csv`.
- Plots (displayed in the notebook):
  - Weekly accuracy
  - Cumulative accuracy
  - Calibration (reliability) scatter vs. the ideal 45° line
- Metrics:
  - Accuracy and cumulative accuracy per week
  - Brier score (lower is better calibration)
  - RMSE (probability) = sqrt(Brier); RMSE (point differential) in points

### Troubleshooting
- Empty predictions:
  - If it’s offseason or ESPN’s endpoint changes, the current-week fetch may return no events.
- Network/API errors:
  - Ensure internet connectivity; occasional transient errors can be resolved by re-running a cell.
- Time zones:
  - Kickoff times are shown as UTC in the notebook output.

### Extending the model
- Replace net-rating with Elo or incorporate priors from previous seasons.
- Add more features to the ML model (strength of schedule, recent form, injuries) and/or regularize.
- Blend in injury reports, QB changes, or richer odds history.
- Add strength-of-schedule adjustments to stabilize early-season ratings.

### Notes
- ESPN data is used for educational purposes. API structure may change without notice.


