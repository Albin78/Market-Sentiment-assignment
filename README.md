# 🧾 Market Sentiment Analysis


1.  Problem Statement

Objective:
To explore the relationship between market sentiment (Fear–Greed Index) and trader performance from Hyperliquid’s historical trade data.
The goal is to uncover how emotions and sentiment regimes influence trading outcomes (PnL, volume, leverage, etc.) and whether sentiment can predict trader profitability.

2. Datasets Overview

Dataset	Description	Key Columns	Period / Size
Bitcoin Market Sentiment Dataset	Daily classification of market mood	Date, Classification (Fear, Neutral, Greed, Extreme Greed, Extreme Fear)	848 rows
Hyperliquid Trader Dataset	Individual trader executions	account, symbol, execution price, size, side, time, closedPnL, leverage, etc.

Challenge:
The sentiment dataset had daily-level granularity, while the trader dataset had many trades per day.
To analyze relationship, trade data was aggregated per day for comparability.

3. Data Preparation and Cleaning

Converted timestamps → date

Removed missing and irrelevant columns

Computed daily aggregates:


Winsorized (5%) total_pnl to reduce outlier influence

Log-transformed total_volume_usd for scale normalization

Standardized features using robust z-score (based on MAD)


4. Exploratory Data Analysis (EDA)

4.1. Distribution Insights

Histograms and boxplots showed total_pnl had heavy-tailed distribution (large outliers).

Winsorization reduced extreme tails -> improved symmetry.

Boxplot confirmed reduced outliers ->  better comparability between periods.

4.2. Trade Volume vs PnL

Hexbin plot of log(total_volume_usd) vs total_pnl revealed:

High-volume days generally had higher absolute PnL.

Most activity concentrated around low PnL values (dense violet region).

Confirms volume can amplify profit/loss magnitude.

5. Linking with Market Sentiment
5.1. Sentiment Mapping

Mapped sentiment classes to numeric scale:

Extreme Fear = -2
Fear = -1
Neutral = 0
Greed = 1
Extreme Greed = 2


Merged with trade aggregates on overlapping dates.

6. Relationship Analysis

6.1. Regression (sns.lmplot)

Lowess and regression plots of sentiment_score vs total_pnl:

Regression line near zero -> weak linear relation.

Few positive and negative deviations -> suggests nonlinear or regime-dependent relationship.

Traders may profit and losses in both extremes (contrarian behavior).

6.2. Sentiment Regime Boxplots

PnL distributions across sentiment classes:

Small boxes indicate most returns cluster near zero.

Outliers show extreme profits/losses during “Extreme Greed” and “Extreme Fear”.

Suggests higher volatility and risk-taking during emotional extremes.


6.3. Lag Correlation Analysis

Tested correlation between lagged sentiment and PnL:

Lag (days)	Corr
−5 to +5	from −0.19 to almost +0.00

Interpretation:

Negative correlation for lags −1 to −3: Trader losses tend to follow periods of greed (possible overconfidence).

Weak overall values (< 0.2) → sentiment not a strong direct driver but may have short-term behavioral effects.

6.4. Rolling Correlation (Dynamic Relationship)

14-day rolling correlation between sentiment_score and total_pnl:

Values fluctuate between −0.6 and +0.5.

Positive windows -> greed aligning with profit.

Negative windows -> greed leading to losses.

Implies time-varying, non-stationary relationship.


7. Key Findings

Sentiment and PnL are weakly correlated overall (−0.1 to 0.1 globally).

Stronger relationships appear in short bursts, visible in rolling windows.

Outlier control (winsorization) improved interpretability.

Trader performance volatility increases during emotional extremes (Fear/Greed).

Lag correlation suggests possible delayed reaction to sentiment (behavioral bias).



## Conclusion

Market sentiment influences trader behavior, but not linearly or consistently.

The relationship is context-dependent — during emotional extremes, volatility and opportunity rise.

Traders might profit from contrarian behavior (acting opposite to extreme sentiment).

For predictive modeling, sentiment can serve as a supplementary signal rather than a primary driver.



## Future Work

Test nonlinear models (Random Forest, XGBoost) to predict PnL from lagged sentiment.

Segment by account type or leverage to identify sensitive traders.

Add technical indicators (returns, volatility index) for multi-factor interaction.