# AAI-520 Group 4: Investment Research System

Lashana Narayan, Nicholson Alforque, and Nathan Butcher

The system collects stock and news data, runs fundamental, technical, sentiment,
and risk analysis, and combines the results in a research report. Nathan's
integration, workflow, and testing updates are included in this version.

## Files

- [Working notebook](https://github.com/Sublymonal/AAI520_Group_4/blob/main/Group%204%20-%20Investment%20Researchers%20-%20Final.ipynb): project code and saved outputs.
- [HTML export]([Group%204%20-%20Nathan%20Testing.html](https://github.com/Sublymonal/AAI520_Group_4/blob/main/Group%204%20-%20Investment%20Researchers%20-%20Final.html): readable notebook with results.
- [External comparisons](external_validation.csv): values, dates, and source URLs.
- [Recorded model run](AAPL_news_workflow.json): the actual Databricks AAPL result.
- `investment_data/`: saved AAPL, MSFT, and INTC collection results.
- `requirements.txt`: package versions used for the checks.

## Integration and workflow changes

- Added behavior and failure-case tests, collection checks, and external comparisons.
- Distinguished missing news from neutral sentiment and skipped failed data during routing.
- Added the news chain: ingest, preprocess, classify, extract, and summarize.
- Routed news topics to the matching specialist context and included all four
  specialists in the combined report.
- Added model evaluation and up to two report revisions. Quotes must match their
  source previews and contain no more than 12 words. Invalid extractions get up
  to two correction attempts; unresolved checks remain `needs_review`.

## Findings from October 15, 2026

- All 15 behavior checks passed. Additional scripted checks covered chaining,
  routing, revision, quote correction, missing analysis, and unresolved feedback.
- Live collection for AAPL, MSFT, and INTC returned four specialist results each.
  INTC's collection was partial because trailing P/E was unavailable.
- Eleven available comparisons against Stock Analysis were within the 1% review
  tolerance. INTC P/E was unavailable in both sources.
- A live AAPL model run in Databricks completed with 20 collected articles,
  8 cleaned previews, and 6 relevant stories. Quote checks and report evaluation
  passed on the first attempt. This run needed no revision; the scripted tests
  separately demonstrated revision and rejection behavior.

## Running the notebook

Use an existing Jupyter environment or import the notebook into Databricks.
The local checks used Python 3.12. Install dependencies with
`pip install -r requirements.txt` and run from this folder so the saved files
can be found. Live news collection needs `NEWS_API_KEY` in the environment.

The model workflow uses Databricks notebook authentication and Unity Gateway's
`system.ai.meta-llama-3-3-70b-instruct` model. `RUN_LLM` defaults to true inside
Databricks and false locally. Local runs display the recorded model result;
Databricks runs request a new result. `RUN_INTERACTIVE` and `RUN_EXAMPLES`
default to false.

## Limitations and remaining work

The model was checked live for AAPL only. Its evaluation checks consistency with
the supplied evidence, not independent article accuracy. News previews are
incomplete, and the sentiment specialist still uses all 20 original previews.

Research planning, dynamic tool selection, and memory across runs remain
outstanding in this version. FRED was not tested; free cash flow and 52-week
range calculations need review. Add the actual team GitHub URL to the notebook
and regenerate the HTML before final submission.
