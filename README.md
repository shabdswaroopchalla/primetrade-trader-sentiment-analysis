# Trader Performance vs Market Sentiment (Hyperliquid + Bitcoin Fear/Greed Index)

## Overview
This project analyzes how **Bitcoin market sentiment** (Fear/Greed Index) relates to **trader behavior and performance** on **Hyperliquid**.

The objective is to identify whether sentiment regimes (Fear vs Greed) influence:
- trader profitability
- win rate
- trading frequency
- position sizing
- directional bias (long vs short)
- behavior of different trader segments

This analysis was completed as part of the **Data Science / Analytics Intern – Round-0 Assignment** for **Primetrade.ai**.

---

## Objective
Answer the following questions:

1. **Does trader performance differ between Fear vs Greed days?**
   - Daily PnL
   - Win rate
   - Drawdown / volatility proxy

2. **Do traders change behavior based on sentiment?**
   - Number of trades
   - Average trade size
   - Long/short bias
   - Exposure proxy (used as an approximate leverage indicator)

3. **Which trader segments are most sensitive to sentiment?**
   - Frequent vs infrequent traders
   - Large-size vs small-size traders
   - Consistent winners vs others

4. **What actionable strategy rules can be proposed from the findings?**

---

## Dataset Description

### 1) Bitcoin Market Sentiment (Fear/Greed Index)
Columns used:
- `date`
- `classification`
- `value`

### 2) Historical Trader Data (Hyperliquid)
Relevant columns used:
- `Account`
- `Execution Price`
- `Size USD`
- `Side`
- `Timestamp IST`
- `Start Position`
- `Closed PnL`
- `Fee`

---

## Important Data Notes
### 1. Daily Alignment Correction
For the trade dataset, **`Timestamp IST`** was used for daily alignment instead of `Timestamp`.

**Why?**  
The raw `Timestamp` field appeared compressed / unsuitable for reliable daily grouping, while `Timestamp IST` preserved the actual trade date-time and produced correct daily granularity.

This was an important data-quality correction in the analysis.

### 2. Leverage Limitation
The dataset **does not contain an explicit leverage column**.

To partially address the assignment requirement around leverage behavior, I created a **rough exposure proxy** using trade notional relative to starting position notional.  
This proxy is included only as a **supplementary metric** and is **not used as a primary conclusion driver**.

---

## Methodology

### Step 1 — Data Cleaning & Audit
- Loaded both datasets
- Standardized column names
- Checked:
  - number of rows / columns
  - missing values
  - duplicates
- Converted date/time fields to proper datetime format

### Step 2 — Daily Merge
- Converted both datasets to **daily granularity**
- Merged Hyperliquid trades with Fear/Greed sentiment labels on `date`

### Step 3 — Feature Engineering
Trade-level features:
- `is_win`
- `is_loss`
- `notional_usd`
- `is_long`
- `is_short`
- `pnl_to_notional`
- `negative_pnl`
- optional `leverage_proxy`

### Step 4 — Account-Day Aggregation
Built **daily trader-level metrics**:
- `daily_pnl`
- `avg_trade_pnl`
- `trade_count`
- `win_rate`
- `avg_trade_size`
- `total_volume`
- `long_ratio`
- `short_ratio`
- `pnl_std` (used as a drawdown / volatility proxy)
- `pnl_per_trade`

### Step 5 — Segmentation
Created trader segments:
- **Frequent Traders** vs **Infrequent Traders**
- **Large Size Traders** vs **Small Size Traders**
- **Consistent Winners** vs **Others**

---

## Key Metrics Analyzed
- **Performance Metrics**
  - Mean / median daily PnL
  - Win rate
  - PnL per trade
  - Within-day PnL volatility (`pnl_std`) as a drawdown proxy

- **Behavior Metrics**
  - Trade count per account-day
  - Average trade size
  - Total traded volume
  - Long bias / short bias
  - Exposure proxy (approximate leverage proxy)

---

## Key Findings (Summary)
> **Note:** Exact values are visible in the notebook outputs and charts.

### 1) Performance differs by sentiment regime
Trader profitability is not identical across sentiment regimes.  
To avoid misleading conclusions from outliers, both **mean and median daily PnL** were compared.

Because crypto trading PnL is typically **fat-tailed**, median-based comparisons are especially important.

### 2) Trader behavior changes with market sentiment
Across Fear / Greed regimes, traders exhibit measurable changes in:
- activity level (trade frequency)
- average trade size
- directional bias (BUY ratio / long ratio)

This suggests that sentiment acts as a **behavioral regime filter**, not just a market descriptor.

### 3) Sentiment impacts trader segments differently
The strongest insight comes from segmentation:
- more active traders
- larger-size traders
- historically consistent traders

These groups often show different sensitivity to Fear vs Greed regimes, which implies that **one-size-fits-all trading rules are suboptimal**.

---

## Actionable Strategy Recommendations

### Rule 1 — Reduce risk during Fear regimes
During **Fear** regimes, reduce exposure (position size / leverage proxy) for:
- high-activity traders
- large-size traders

**Rationale:** downside dispersion and PnL volatility tend to be higher in fearful conditions.

---

### Rule 2 — Scale activity selectively during Greed regimes
During **Greed** regimes, allow increased activity **only for historically consistent winners**.

**Rationale:** traders with weaker historical consistency may overtrade during optimistic conditions and take disproportionate risk.

---

### Rule 3 — Use sentiment as a regime filter, not a direct signal
Fear/Greed should be used to **adjust trading behavior** (size, frequency, aggressiveness), rather than as a simplistic “buy on greed / sell on fear” standalone rule.

---

## Project Structure

```bash
primetrade-trader-sentiment-analysis/
│
├── trader_sentiment_analysis.ipynb
├── README.md
├── requirements.txt
├── fear_greed_index.csv
└── outputs/
    ├── chart_1_pnl_by_sentiment.png
    ├── chart_2_winrate_by_sentiment.png
    ├── chart_3_trade_count_by_sentiment.png
    ├── chart_4_trade_size_by_sentiment.png
    ├── chart_5_long_bias_by_sentiment.png
    └── chart_6_segment_analysis.png
