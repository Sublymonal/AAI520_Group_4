# Multi-Agent Investment Research Agent
**AAI-520 NLP & GenAI · Final Group Project · Group 4**
Lashana Narayan · Nik · Nathan

An autonomous investment research agent. Given a stock symbol, it plans its research, gathers live market, news and economic data, analyzes them with five specialist agents, checks and improves the quality of its own report, and learns from previous runs.

> Research and education only. Not investment advice.

---

## Files

- [Working notebook](https://github.com/Sublymonal/AAI520_Group_4/blob/main/Group%204%20-%20Investment%20Researchers%20-%20Final.ipynb): project code and saved outputs.
- [HTML export](https://github.com/Sublymonal/AAI520_Group_4/blob/main/Group%204%20-%20Investment%20Researchers%20-%20Final.html): readable notebook with results.
- [External comparisons](external_validation.csv): values, dates, and source URLs.

  
## What it does

Enter a ticker (e.g. `INTC`) and the agent produces a 14-section research report covering:

- Company profile and fundamentals (valuation, profitability, leverage, growth)
- Price performance and technical signals (moving averages, RSI, trend)
- News themes and sentiment
- Macro assessment: inflation, labor market, rates, yield curve, growth, whether the backdrop is a headwind or tailwind for stocks, and the sector tilt
- Material risks, caveats and reconciled conflicting signals
- Agent confidence and a quality score, with full source and timestamp list

## How it meets the project requirements

| Requirement | Implementation |
|---|---|
| **Plans** research steps | `ResearchPlanner` builds an ordered, explained plan from the company profile, sector and past-run memory |
| **Uses tools dynamically** | `ToolRegistry` wraps Yahoo Finance, NewsAPI and FRED as named tools, with logging, fallbacks and learned reliability |
| **Self-reflects** | `SelfReflection` scores each agent's confidence and flags missing data, small samples and conflicting signals |
| **Learns across runs** | `AgentMemory` (persistent JSON) stores lessons; the planner applies them on the next run, including fixes learned on other stocks |
| **Prompt chaining** | `NewsProcessingChain`: Ingest → Preprocess → Classify → Extract → Summarize |
| **Routing** | `MacroAwareCoordinator` routes data to the Fundamental, Technical, Sentiment, Risk and Economic agents; `ContentRouter` routes news stories to earnings, market and news-event analyzers |
| **Evaluator–Optimizer** | `AnalysisEvaluator` scores each report out of 100 on 5 criteria; `AnalysisOptimizer` re-runs tools or rewrites weak sections until the report passes |

## Architecture

Data collection     Yahoo Finance · NewsAPI · FRED

│
Specialist agents   Fundamental · Technical · Sentiment · Risk · Economic

│
Autonomous agent    plan → tools → prompt chain → routing → self-reflection
→ evaluate ⇄ optimize → report → memory

│
Output              report · 11 charts · evaluation (tests, metrics, accuracy)


## Key results (final run, 2026-10-19)

- **Reliability:** 100% tool success; every run under the 30-second target (median 0.9 s)
- **Quality:** first drafts averaged 88.5/100 and final reports 96.9/100 (+8.4 points from the optimizer); 100% of runs passed the 85-point threshold
- **Learning:** a repeat run on the same stock started at 98.2 instead of 84.9 and needed 1 iteration instead of 2; a fix learned on earlier stocks let NVDA pass on its first iteration
- **Validation:** 35/35 agent validation tests passed
- **Sentiment accuracy:** TextBlob reached 50% on labeled financial headlines (baseline 35%). It misses financial negatives, so it is the main candidate for improvement.

## Repository contents

| File | Description |
|---|---|
| `Group 4 - Investment Researchers - Final.ipynb` | Complete notebook with code, explanations and saved outputs |
| `README.md` | This file |

## Running the notebook

**Requirements:** a Databricks workspace (or Jupyter with Python 3.10+), plus free API keys from [FRED](https://fred.stlouisfed.org/docs/api/api_key.html) and [NewsAPI](https://newsapi.org/register). Yahoo Finance needs no key.

1. **Import** the notebook into Databricks and attach it to a cluster.
2. **Provide the API keys without putting them in the notebook.** Either:
   - add them as cluster environment variables (**Compute → cluster → Edit → Advanced options → Environment variables**):
     ```
     FRED_API_KEY=your_fred_key
     NEWS_API_KEY=your_newsapi_key
     ```
   - or store them in Databricks secrets and load them in the API key cell.
3. **Run all cells.** The first cell installs the libraries (`yfinance`, `textblob`, `pandas-datareader`, `pandas-ta`, `requests`, `numpy`, `scipy`, `matplotlib`).
4. **Enter a stock symbol** when prompted in Section 3.8. Invalid symbols get suggestions (e.g. `INTEL` → `INTC`).

A full run takes a few minutes and uses about 15–20 NewsAPI requests; the free tier allows 100 per day.

## Notebook outline

1. Setup
2. Data Collection Layer
3. Specialist Agents & Coordinator
4. Autonomous Agent Functions
5. Workflow Patterns
6. The Autonomous Investment Research Agent
7. Visualizations
8. Demonstration
9. System Evaluation
10. Key Findings
11. Requirements Checklist & Limitations

## Limitations and next steps

- Sentiment uses TextBlob; a finance-tuned model such as FinBERT would be more accurate.
- News themes are classified with keyword rules; the summarize step already builds an LLM prompt, so an LLM can be added through the `llm_summarizer` parameter.
- The free NewsAPI tier limits history (about 30 days) and request volume; Yahoo Finance data is unofficial.

## Team contributions

- **Lashana Narayan:** data collection platform and API integrations; coordinator and report synthesis; autonomous agent functions (memory, tools, planner, self-reflection); Economic Agent; workflow patterns; integrated agent; visualizations; demonstration; system evaluation; documentation
- **Nik:** Fundamental, Technical, Sentiment and Risk specialist agents
- **Nathan:** Evaluation, testing

*Parts of the code and documentation were developed with assistance from an AI coding assistant. The team designed, reviewed, ran and validated the work.*
