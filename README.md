## NFL Winner Predictions (Current Week)

This project provides a self-contained Jupyter notebook that pulls near real-time NFL data from ESPN’s public scoreboard API, builds simple season-to-date team ratings, predicts winners for the current week’s games, and backtests the method on past weeks with accuracy plots.

### What’s included
- `nfl_winner_predictions.ipynb`: The main notebook.
- `predictions_output/`: Folder where CSVs with current-week predictions are saved (created on first run).

### Features
- Fetches the most current week (season year, season type, week) from ESPN.
- Builds team ratings from completed games in prior weeks (points per game minus points allowed per game).
- Adds a small home-field advantage and converts rating differences to win probabilities via a logistic function.
- Predicts current-week winners and saves results to CSV.
- Backtests across past weeks using only prior-week information to compute accuracy.
- Plots weekly accuracy, cumulative accuracy, and a calibration (reliability) curve, and prints the Brier score.

### Setup
Requirements:
- Python 3.9+
- Jupyter (Notebook or Lab)

The notebook auto-installs these Python packages if missing:
- `requests`, `pandas`, `matplotlib`, `seaborn`, `numpy`

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
- Prediction:
  - Home-field advantage: +1.5 points for the home team (modifiable in the notebook).
  - Logistic transform maps rating differential to win probability.
- Backtesting:
  - For each week 1..N, compute ratings using weeks < current, predict week, compare to actual final scores.
  - Outputs weekly accuracy, cumulative accuracy, calibration plot, and Brier score.

### Configuration knobs (in the notebook)
- `HOME_FIELD_ADVANTAGE_POINTS` (default: 1.5)
- `LOGISTIC_SCALE` (default: 7.0) — higher = flatter probability curve

### Outputs
- CSV: Saved in `predictions_output/` as `nfl_predictions_w{WEEK}_{YEAR}_{TIMESTAMP}.csv`.
- Plots (displayed in the notebook):
  - Weekly accuracy
  - Cumulative accuracy
  - Calibration (reliability) scatter vs. the ideal 45° line
- Metrics:
  - Brier score (lower is better calibration)

### Troubleshooting
- Empty predictions:
  - If it’s offseason or ESPN’s endpoint changes, the current-week fetch may return no events.
- Network/API errors:
  - Ensure internet connectivity; occasional transient errors can be resolved by re-running a cell.
- Time zones:
  - Kickoff times are shown as UTC in the notebook output.

### Extending the model
- Replace net-rating with Elo or incorporate priors from previous seasons.
- Blend in injury reports, QB changes, or market odds.
- Add strength-of-schedule adjustments to stabilize early-season ratings.

### Notes
- ESPN data is used for educational purposes. API structure may change without notice.


