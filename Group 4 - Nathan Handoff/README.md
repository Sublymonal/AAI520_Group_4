# Group 4: testing and evaluation handoff

This folder contains the working notebook and an HTML export with saved outputs.
The added section starts at "Nathan: integration, testing, and workflows."

The code adds integration and failure-case tests, live runs for AAPL, MSFT, and
INTC, external comparisons, data checks, and a model-backed news workflow.
It distinguishes missing news from neutral sentiment, skips failed data when
routing, and separates complete, partial, and failed collection results.

Local results, October 15, 2026:

- All 15 behavior checks passed.
- All three live tickers returned four specialist results. INTC was partial
  because its trailing P/E was unavailable.
- Eleven comparisons against Stock Analysis were within the 1% review
  tolerance. INTC P/E was unavailable in both sources. Dates, values, and
  source URLs are recorded in external_validation.csv and the notebook.
- The evaluator changed an unsupported neutral-news claim in the synthetic
  example. In the live runs it qualified sentiment when previews did not
  mention the target company. These flags still need human relevance review.

To run, install requirements.txt in a Python 3.12 environment and open the
notebook in Jupyter or Databricks. Set NEWS_API_KEY in the environment or load
it from a Databricks secret scope. The local execution used the supplied group
key without copying it into the delivered notebook. The live cells make API
calls; missing keys and API failures remain visible in the results.
RUN_INTERACTIVE enables the original input loop. RUN_EXAMPLES enables the
original extra examples. Both default to False so Run All does not wait for input.

News workflow:

The added chain cleans and deduplicates previews, asks a model to classify
relevance and topic, extracts short sourced facts, and creates a report using
all four specialist results and topic-routed news context. A model evaluator checks the report against
the evidence and requests up to two revisions. Source ids and exact-quote
checks help detect unsupported outputs. A failed evaluation remains visible.

The workflow checks use explicitly scripted replies: they verify stage passing,
routing, citation handling, revision, empty news, and rejection of invented
quotes. Invalid extractions get up to two correction attempts; unresolved
extractions remain needs_review. Scripted checks do not establish live model quality. The earlier dated stock and
external-validation outputs are preserved.

The workspace's Unity Gateway lists Llama 3.3 70B Instruct, so MODEL_NAME
defaults to system.ai.meta-llama-3-3-70b-instruct. The SDK sends chat requests
to /ai-gateway/mlflow/v1/chat/completions using notebook authentication.
The October 15 live AAPL run completed: 20 articles, 8 cleaned previews,
6 relevant stories, and observations from all four specialists. Quote checks
and report evaluation passed on the first attempt, so this run needed no
revision. AAPL_news_workflow.json preserves the captured result. RUN_LLM
defaults to True inside Databricks and False locally; local runs display
the saved result. Run News Workflow.ipynb is a smaller workspace check
with the saved public AAPL data and needs no news keys. The model pass
checks consistency with supplied evidence, not independent article accuracy.
The sentiment specialist still uses all 20 original previews.

API reference: https://docs.databricks.com/aws/en/large-language-models/llm-serving-intro

Remaining group work: add the actual GitHub URL to the notebook and complete
research planning, dynamic tool selection, and memory across runs. The news
prompt chain and model evaluator have a verified live AAPL run; synthetic
checks demonstrate revision and rejection behavior. FRED was not run
in the live stock tests. Free cash flow and 52-week range calculations still
need review. These additions do not change the group's submission roles.

The original project README follows. Its longer metric list describes planned
coverage, not a claim that each metric is implemented or validated.

---

﻿# AAI520_Group_4
AAI, 520 - NLP and GenAI\
Group 4 - Final Group Project\
Lashana Narayan, Nik, Nathan
## Investment Research Agent
\
This research agent will access external sources to extract current news and events given a stock symbol to help the end user make an informed decision on investment. To do this, the agent will take in a stock symbol and provide a report containing the following information:

- Company overview
- Recent price performance
- Relevant market and economic context
- Recent news themes and sentiment
- Earnings and financial highlights
- Material risks and uncertainties
- Evidence-backed research summary
- Source list and data timestamps

Useful information to make an informed investment decision includes the following. We should select the most important among these for initial testing and deployment and can further develop to include all of these at a later time.

## 1. FUNDAMENTAL ANALYSIS DATA (For our Fundamental Analysis Agent)

Financial Health Metrics

- Valuation Ratios: P/E, P/B, P/S, EV/EBITDA, PEG ratio
- Profitability: ROE, ROA, ROI, profit margins (gross, operating, net)
- Liquidity: Current ratio, quick ratio, cash ratio
- Leverage: Debt-to-equity, debt-to-assets, interest coverage ratio
- Efficiency: Asset turnover, inventory turnover, receivables turnover Financial Statements
- Income Statement: Revenue growth, earnings growth, operating income trends
- Balance Sheet: Assets, liabilities, shareholder equity trends
- Cash Flow: Operating, investing, financing cash flows
- Key Metrics: Free cash flow, dividend yield, payout ratio Company Fundamentals
- Market capitalization, float, insider ownership
- Business model and competitive advantages
- Management quality and governance
- Industry position and market share

## 2. TECHNICAL ANALYSIS DATA (For your Technical Analysis Agent)

**Price Action & Volume** Historical Prices: Open, high, low, close (OHLC) data Volume: Trading volume patterns and volume-weighted average price (VWAP) Price Trends: Support/resistance levels, trend lines Technical Indicators Momentum: RSI, MACD, Stochastic oscillator Moving Averages: SMA, EMA (20, 50, 200-day) Volatility: Bollinger Bands, Average True Range (ATR) Volume Indicators: On-balance volume, volume profile Chart Patterns Candlestick patterns, head & shoulders, triangles, flags Breakout patterns and consolidation zones Fibonacci retracements and extensions

## 3. NEWS & SENTIMENT DATA (For your News Sentiment Agent)

News Analysis

- Headlines & Content: Recent news articles, press releases
- Sentiment Scores: Positive, negative, neutral classification
- Event Detection: Earnings announcements, mergers, regulatory changes
- Social Media: Twitter sentiment, Reddit discussions (if available) Market-Moving Events Earnings reports and guidance updates
- FDA approvals (for healthcare stocks)
- Regulatory changes and legal issues
- Management changes and strategic announcements

## 4. RISK ASSESSMENT DATA (For your Risk Assessment Agent)

Risk Metrics

- Volatility: Historical volatility, implied volatility
- Beta: Stock's correlation with market movements
- Value at Risk (VaR): Potential losses over time periods
- Sharpe Ratio: Risk-adjusted returns Market Risk Factors
- Sector performance and correlation
- Geographic exposure and currency risk
- Liquidity risk (bid-ask spreads, trading volume)
- Credit risk and default probability

## 5. ECONOMIC CONTEXT DATA (Using FRED API for macro environment)

Macroeconomic Indicators

- Interest Rates: Federal funds rate, 10-year treasury yield
- Inflation: CPI, PPI trends and expectations
- GDP Growth: Economic growth rates and forecasts
- Employment: Unemployment rate, job growth data Market Environment
- Market Indices: S&P 500, sector-specific indices
- Currency: USD strength/weakness (for international exposure)
- Commodity Prices: Oil, gold, relevant sector commodities
- Economic Sentiment: Consumer confidence, business sentiment

## 6. COMPETITIVE & INDUSTRY DATA (MED)

Industry Analysis

- Industry growth rates and trends
- Competitive positioning and market share
- Regulatory environment and changes
- Seasonal patterns and cyclicality

Peer Comparison

- Relative valuation vs. competitors
- Performance benchmarking
- Industry-specific metrics (e.g., same-store sales for retail)
Industry-specific metrics (e.g., same-store sales for retail)
