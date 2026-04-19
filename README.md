# Political Sentiment and Defense Sector Returns
### An Event Study of Social Media Communication and Stock Market Reactions (2017–2021)

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
- **Source:** Trump Twitter Archive (Kaggle)
- **Period:** January 20, 2017 – January 20, 2021 (presidential term)
- **Total tweets in dataset:** 56,571
- **After keyword filtering:** 3,417 defense-related tweets across 1,062 unique days
- **Keywords used:** `defense`, `military`, `NATO`, `army`, `weapons`, `war`, `troops`, `missile`, `nuclear`, `China`, `Iran`, `threat`, `bomb`, `attack`, `soldiers`, `Pentagon`, `armed forces`

### Stock Prices
- **Source:** Yahoo Finance via `yfinance`
- **Period:** January 2017 – January 2021
- **Frequency:** Daily adjusted closing prices

#### Companies Included

| Region | Ticker | Company |
|--------|--------|---------|
| USA | `LMT` | Lockheed Martin |
| USA | `RTX` | Raytheon Technologies |
| USA | `NOC` | Northrop Grumman |
| USA | `BA` | Boeing |
| USA | `GD` | General Dynamics |
| EU | `AIR.PA` | Airbus (Netherlands/France) |
| EU | `BA.L` | BAE Systems (UK) |
| EU | `HO.PA` | Thales (France) |
| EU | `SAF.PA` | Safran (France) |
| EU | `RHM.DE` | Rheinmetall (Germany) |

---

## Methodology

### 1. Sentiment Analysis
Each filtered tweet is scored using **VADER** (Valence Aware Dictionary and sEntiment Reasoner), a rule-based sentiment model optimized for short social media text.

- Output: compound score in range `[-1.0, +1.0]`
- Classification: Positive (`> 0.05`), Negative (`< -0.05`), Neutral
- Daily sentiment: average compound score across all defense-related tweets on a given day
- Mean daily sentiment score: **+0.19** (slightly positive overall)

### 2. Return Calculation
- Daily percentage returns: `r_t = (P_t - P_{t-1}) / P_{t-1}`
- Missing values due to market holiday differences handled via forward fill

### 3. Cross-Correlation Analysis
Pearson correlation between daily sentiment scores and stock returns at lags t, t+1, t+2. Captures delayed market reactions to after-hours or pre-market tweets.

### 4. Event Study
High-sentiment days defined as `|sentiment| > 0.5`. Cumulative Abnormal Returns (CAR) computed over event window `[-3, +3]` trading days and averaged across all events separately for positive and negative tweet days.

### 5. Granger Causality Test
Formal test of whether lagged sentiment scores improve prediction of stock returns beyond autoregressive terms alone. Applied using `statsmodels` with lags 1–5. Significance threshold: p < 0.05.

> Note: Granger causality does not imply economic causality — it tests predictive precedence only.

---

## Key Findings

### Sentiment Distribution
- **1,062** trading days contained at least one defense-related tweet
- Average daily sentiment: **+0.19** (range: -0.98 to +0.98)
- Trump's defense-related communication was slightly positive on average, with high variance

### Correlation Analysis
- Sentiment scores show **small negative correlations** with same-day and next-day returns across most tickers (range: -0.01 to -0.09)
- This suggests that **more negative tweets are associated with slightly higher defense stock returns** — consistent with the hypothesis that conflict rhetoric increases expected defense spending
- US stocks are highly correlated with each other (LMT–NOC: 0.83, GD–RTX: 0.74), moving as a sector
- EU–US cross-market correlations are weaker (LMT–RHM.DE: 0.18), suggesting different regional dynamics

### Event Study
- **Negative tweet days consistently produce higher CAR** than positive tweet days for US defense stocks (LMT, RTX, NOC, BA) in the [0, +3] window
- The pattern is economically interpretable: geopolitical threats and military rhetoric signal increased defense procurement
- EU stocks show mixed and less consistent reactions, with RHM.DE displaying an opposite pattern to US peers

### Granger Causality
- **No ticker passed the p < 0.05 significance threshold** across any lag (1–5 days)
- **RHM.DE** showed the strongest signal at lag 1 (p = 0.071) — closest to significance
- **HO.PA** (Thales) showed moderate signal at lag 1 (p = 0.154)
- Conclusion: tweet sentiment does not Granger-cause defense stock returns in a statistically significant way over this sample period

### Interpretation
The null result in Granger causality is consistent with the efficient market hypothesis — if the effect were strong and predictable, it would be arbitraged away. The event study nonetheless reveals a directional pattern: negative political rhetoric around defense themes is associated with small positive abnormal returns in US defense stocks, which is economically meaningful even if not statistically exploitable.

---

## Visualizations

| Figure | Description |
|--------|-------------|
| `sentiment_over_time.png` | Daily sentiment score 2017–2021 with tweet volume |
| `sentiment_distribution.png` | Histogram and pie chart of sentiment classification |
| `correlation_heatmap.png` | Pearson correlation matrix: sentiment vs. all tickers |
| `scatter_sentiment_returns.png` | Scatter plots with trend lines: sentiment (t-1) vs. returns (t) |
| `granger_pvalues.png` | Heatmap of Granger causality p-values across tickers and lags |
| `event_study.png` | CAR around positive and negative high-sentiment tweet days |

---

## Project Structure

```
defense-sentiment/
├── data/
│   ├── raw/
│   │   └── tweets.csv              # Raw Kaggle archive
│   └── processed/
│       ├── tweets_filtered.csv     # Filtered + sentiment scores
│       ├── returns.csv             # Daily returns, all tickers
│       ├── daily_sentiment.csv     # Aggregated daily sentiment
│       └── merged_final.csv        # Final merged dataset
├── notebooks/
│   ├── 01_data_collection.ipynb    # Download prices, load tweets
│   ├── 02_sentiment_analysis.ipynb # VADER scoring, keyword filtering
│   ├── 03_eda.ipynb                # Exploratory analysis, correlations
│   └── 04_analysis.ipynb           # Granger causality, event study
├── outputs/
│   └── figures/                    # All saved plots
├── requirements.txt
└── README.md
```

---

## How to Run

```bash
pip install -r requirements.txt
```

1. Download tweets dataset from Kaggle: [Trump Twitter Archive](https://www.kaggle.com/datasets/codebreaker619/donald-trump-tweets-dataset)
2. Place file in `data/raw/tweets.csv`
3. Run notebooks in order: `01` → `02` → `03` → `04`

---

## Limitations

- **Causality:** Correlation and Granger tests do not establish economic causality. Market movements may be driven by the same underlying events that trigger tweets.
- **Confounders:** Major macro events (COVID-19 in 2020, US-China trade war) overlap with the sample and are not controlled for.
- **Sentiment model:** VADER is a general-purpose model not trained on political or financial text. FinBERT may improve scoring accuracy.
- **Keyword filtering:** Some false positives remain in the filtered dataset (tweets matched keywords in non-defense context).
- **EU market hours:** Tweet timestamps (US Eastern Time) do not always map cleanly to European trading sessions, potentially misaligning event windows.

---

## References

- Hutto, C. & Gilbert, E. (2014). *VADER: A parsimonious rule-based model for sentiment analysis of social media text.* ICWSM.
- Bollen, J., Mao, H., & Zeng, X. (2011). *Twitter mood predicts the stock market.* Journal of Computational Science.
- MacKinlay, A. C. (1997). *Event studies in economics and finance.* Journal of Economic Literature.
- Engle, R. F. & Granger, C. W. J. (1987). *Co-integration and error correction.* Econometrica.

---

## Author

**Jakub Groblicki**  
AGH University of Science and Technology · Computer Science & Econometrics  
[GitHub](https://github.com/Jablon22) · [LinkedIn](#)
