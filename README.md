# Stock Market Analysis and Portfolio Management Web App

A Flask web application for Indian (NIFTY) stock screening and visualisation, ML-based portfolio allocation by age and risk profile, a SIP/compound-interest calculator and an admin-managed blog, with user registration and login.

## Features
- **Auth**: register/login with bcrypt-hashed passwords (SQLite via Flask-SQLAlchemy), password-strength regex; session-based; an `Admin` user can publish blog posts with image uploads.
- **Screener** (`/screener`): table of NIFTY stocks with technical indicators (MACD histogram, ADX, ATR, EMA-200, RSI, Stochastic and slopes) computed with the `ta` library; searching a company redirects to Google Finance.
- **Visualization** (`/visualization`): matplotlib plots of top/bottom performers over ~1 month, 1 year and 5 years, rendered as base64 images.
- **Data refresh**: an APScheduler job reruns `run()` hourly: downloads OHLC for the symbols in `nifty_data.csv` via yfinance, computes indicators, pickles the data and rebuilds the result CSVs.
- **Portfolio allocation** (`/portfolio`): a scikit-learn `RandomForestRegressor` trained on `data.csv` (13 rows of age x investor type to asset-class percentages: equity, mutual funds, debt funds, gold bonds, govt bonds, PPF, FDs) predicts an allocation and an estimated blended return from hard-coded average returns.
- **SIP calculator** (`/compound-interest`): monthly-compounding projection with chart data.
- **Blog** (`/blog`, `/admin`).

## Tech stack
Flask, Flask-SQLAlchemy (SQLite), bcrypt, pandas, NumPy, scikit-learn, yfinance, ta, matplotlib/seaborn/plotly, APScheduler; Jinja templates, CSS/JS. Notebooks `jupternode.ipynb` (portfolio data exploration) and `demo.ipynb` (ticker/data scratch work).

## Structure
```
app.py                 # all routes, models, scheduler
utils/                 # data_utils.py, ml_utils.py
templates/  static/    # pages, images, uploads
data/                  # generated CSVs and pickles (~50 MB)
nifty_data.csv  data.csv  portfolio.pkl
instance/users_post.db # SQLite user/blog DB
```

## Run
```bash
python -m venv venv && source venv/bin/activate
pip install -r requirements.txt
python app.py
```
`db.create_all()` runs at startup, so `flask db` migration commands in the old README are not required (no Flask-Migrate in requirements). Generated data files already exist under `data/`; on first run the hourly job will refresh them (requires internet for yfinance). Note: `app.py` imports `matplotlib`, `ta` and `yfinance`, all listed in requirements.txt.

## Limitations
- Hard-coded Flask `secret_key` placeholder and `debug` style settings; not production-ready.
- Hourly job downloads ~200 tickers sequentially; no caching strategy, error handling or tests.
- Portfolio model is trained on a tiny hand-made table; returns are static assumptions. Not financial advice.
- Old README mentions DEPLOYMENT.md, LICENSE and a placeholder clone URL that do not exist.
