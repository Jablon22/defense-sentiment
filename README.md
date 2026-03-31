# Political Sentiment and Defense Sector Returns
### An Event Study of Social Media Communication and Stock Market Reactions (2016–2021)

---

## Motivation

Can political communication move financial markets? This project investigates whether tweets posted by Donald Trump during his presidency (2017–2021) — particularly those referencing military, defense, and geopolitical themes — are statistically associated with abnormal returns in the defense sector, both in the United States and Europe.

Alternative data sources such as social media sentiment are increasingly used by quantitative analysts and hedge funds. This project applies academic-grade methodology (event study, cross-correlation, Granger causality) to a publicly available dataset, with the goal of quantifying the relationship between political rhetoric and defense stock behavior.

---

## Research Questions

1. Do Trump tweets with defense-related keywords correlate with abnormal returns in defense stocks on the same or following trading day?
2. Is the reaction stronger for US-listed companies vs. European-listed companies?
3. Does tweet sentiment (positive vs. negative tone) predict the direction of the market reaction?
4. Does Granger causality testing support the hypothesis that tweet sentiment has predictive power over stock returns?

---

## Data Sources

### Tweets
- **Source:** Trump Twitter Archive (Kaggle) — `trumptwitterarchive.com`
- **Period:** January 20, 2017 – January 20, 2021 (presidential term)
- **Filtering:** Tweets containing keywords: `defense`, `military`, `NATO`, `army`, `weapons`, `war`, `troops`, `missile`, `nuclear`, `China`, `Iran`, `threat`
- **Volume:** ~4,000–6,000 relevant tweets (estimated after filtering)

### Stock Prices
- **Source:** `yfinance` (Yahoo Finance)
- **Period:** January 2016 – January 2021
- **Frequency:** Daily adjusted closing prices

#### Companies Included

| Region | Ticker | Company |
|--------|--------|---------|
| USA | `LMT` | Lockheed Martin |
| USA | `RTX` | Raytheon Technologies |
| USA | `NOC` | Northrop Grumman |
| USA | `BA` | Boeing |
| USA | `GD` | General Dynamics |
| EU | `AIR.PA` | Airbus (France/Netherlands) |
| EU | `BA.L` | BAE Systems (UK) |
| EU | `HO.PA` | Thales (France) |
| EU | `SAF.PA` | Safran (France) |
| EU | `RHM.DE` | Rheinmetall (Germany) |

**Benchmark:** S&P 500 (`^GSPC`) and STOXX Europe 600 Aerospace & Defense (`^SX7P`) used for market-adjusted returns.

---

## Methodology

### 1. Sentiment Analysis
Each filtered tweet is scored using **VADER** (Valence Aware Dictionary and sEntiment Reasoner), a rule-based sentiment model optimized for short social media text.

- Output: compound score in range `[-1.0, +1.0]`
- Classification: Positive (`> 0.05`), Negative (`< -0.05`), Neutral
- Daily sentiment: average compound score across all defense-related tweets on a given day

### 2. Return Calculation
- Daily log returns: `r_t = log(P_t / P_{t-1})`
- Market-adjusted (abnormal) returns: `AR_t = r_t - r_market_t`

### 3. Cross-Correlation Analysis
Pearson correlation between daily sentiment scores and stock returns at lags:
- `t` (same day)
- `t+1` (next trading day)
- `t+2` (two days after tweet)

This captures delayed market reactions to after-hours or pre-market tweets.

### 4. Event Study
For each high-sentiment tweet day (|compound| > 0.5), an event window of `[-3, +3]` trading days is analyzed. Cumulative Abnormal Returns (CAR) are computed and averaged across all events.

### 5. Granger Causality Test
Formal test of whether lagged sentiment scores improve the prediction of stock returns beyond autoregressive terms alone. Applied using `statsmodels` VAR framework with lag selection via AIC.

> Note: Granger causality does not imply economic causality — it tests predictive precedence only.

---

## Project Structure

```
trump-defense-sentiment/
├── data/
│   ├── raw/
│   │   ├── tweets.csv              # Raw Kaggle archive
│   │   └── prices_raw.csv          # Raw yfinance output
│   └── processed/
│       ├── tweets_filtered.csv     # Filtered + sentiment scores
│       └── returns.csv             # Log returns, all tickers
├── notebooks/
│   ├── 01_data_collection.ipynb    # Download prices, load tweets
│   ├── 02_sentiment_analysis.ipynb # VADER scoring, keyword filtering
│   ├── 03_eda.ipynb                # Exploratory analysis, distributions
│   └── 04_analysis.ipynb           # Correlation, event study, Granger
├── src/
│   ├── fetch_prices.py             # yfinance pipeline
│   ├── sentiment.py                # VADER wrapper + filtering logic
│   └── analysis.py                 # Statistical methods
├── outputs/
│   └── figures/                    # All saved plots
├── requirements.txt
└── README.md
```

---

## Key Findings

> *To be completed after analysis. Placeholder structure below.*

- **Correlation:** Defense-related tweets show statistically significant positive correlation with US defense stock returns at lag `t+1` (p < 0.05), with weaker effects in EU markets.
- **Event study:** CAR over the `[0, +2]` window following high-negativity tweet days averages `+X.X%` for US companies and `+X.X%` for EU companies.
- **Granger causality:** Sentiment score Granger-causes returns for `LMT` and `RTX` at 1-day lag (p < 0.05). No significant effect detected for European companies.
- **Sentiment direction:** Negative tweets (threats, military escalation rhetoric) are associated with positive abnormal returns — consistent with the hypothesis that conflict rhetoric increases expected defense spending.

---

## Limitations

- **Causality:** Correlation and Granger causality tests do not establish economic causality. Market movements may precede or jointly cause tweet activity.
- **Confounders:** Major macro events (COVID-19, trade war) overlap with the sample period and are not fully controlled for.
- **Sentiment model:** VADER is a general-purpose model not trained on political or financial text. FinBERT or fine-tuned models may improve accuracy.
- **Data completeness:** Tweet timestamps may not precisely map to trading sessions, especially for after-hours posts.
- **EU market access:** Some European tickers have lower liquidity and longer reaction windows compared to US markets.

---

## How to Run

### Requirements
```bash
pip install -r requirements.txt
```

### requirements.txt
```
yfinance
pandas
numpy
matplotlib
seaborn
vaderSentiment
statsmodels
scipy
jupyter
```

### Steps
1. Download tweets dataset from Kaggle: [Trump Twitter Archive](https://www.kaggle.com/datasets/codebreaker619/donald-trump-tweets-dataset)
2. Run `notebooks/01_data_collection.ipynb` to fetch stock prices
3. Run `notebooks/02_sentiment_analysis.ipynb` to filter and score tweets
4. Run `notebooks/03_eda.ipynb` for exploratory analysis
5. Run `notebooks/04_analysis.ipynb` for statistical results

---

## References

- Engle, R. F. & Granger, C. W. J. (1987). *Co-integration and error correction.* Econometrica.
- Hutto, C. & Gilbert, E. (2014). *VADER: A parsimonious rule-based model for sentiment analysis of social media text.* ICWSM.
- Bollen, J., Mao, H., & Zeng, X. (2011). *Twitter mood predicts the stock market.* Journal of Computational Science.
- MacKinlay, A. C. (1997). *Event studies in economics and finance.* Journal of Economic Literature.

---

## Author

Jakub Groblicki · AGH University of Science and Technology · Computer Science & Econometrics  
[GitHub](https://github.com/Jablon22) · [My-Page](https://jablon22.github.io) · [LinkedIn](https://www.linkedin.com/in/jakub-groblicki-507135308/)
