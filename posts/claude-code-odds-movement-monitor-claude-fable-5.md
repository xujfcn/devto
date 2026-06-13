---
title: 'Build a World Cup Odds Movement Monitor with Claude Code and claude-fable-5'
published: true
description: 'A second Claude Code project in the World Cup analytics series: build an odds movement monitor, compute implied probability shifts, and use claude-fable-5 through Crazyrouter to generate val'
tags: ai, api, coding, tutorial
cover_image: 'https://raw.githubusercontent.com/xujfcn/images/main/blog/covers/claude-code-odds-monitor-claude-fable-5-cover.png'
canonical_url: 'https://crazyrouter.com/blog/claude-code-odds-movement-monitor-claude-fable-5?utm_source=devto&utm_medium=article&utm_campaign=claude_code_odds_monitor_fable'
---

# Build a World Cup Odds Movement Monitor with Claude Code and claude-fable-5

In the first article of this Claude Code project series, I built a small World Cup 2026 match predictor: Python calculated transparent Elo/Poisson probabilities, and Crazyrouter routed multiple LLMs to generate structured match previews.

The second project follows the same principle, but focuses on a different sports analytics workflow:

> Can Claude Code build an odds movement monitor, then use `claude-fable-5` to summarize market shifts as validated JSON?

This is still an engineering demo. It is **not betting advice**, and it does not recommend any wager. The purpose is to show how an AI coding agent can build a reproducible monitoring pipeline around numeric signals, alerts, LLM analysis, and validation.

The API layer was tested through Crazyrouter:

```text
Base URL: https://cn.crazyrouter.com/v1
Model tested: claude-fable-5
Task: summarize odds movement alerts as valid JSON
```

---

## Why odds movement is a good Claude Code project

Odds movement sounds like a betting topic, but from an engineering perspective it is really a time-series monitoring problem.

You have:

- incoming numeric data;
- opening and latest values;
- derived metrics;
- thresholds;
- anomaly alerts;
- human-readable summaries;
- strict disclaimers and compliance boundaries.

That makes it a useful project for testing an AI coding agent.

The agent should not say “bet on this team.” Instead, it should build a monitoring system that answers safer engineering questions:

- Did the implied probability change significantly?
- Which market moved the most?
- Is the movement mirrored by the opposite market?
- Should we check data quality or news context?
- Can an LLM summarize the alerts without giving betting advice?

That is a much better use of Claude Code than asking a model to guess match outcomes.

---

## Project architecture

The demo pipeline is intentionally small and reproducible:

```text
sample odds CSV
  ↓
Python parser
  ↓
decimal odds → implied probability
  ↓
movement calculation
  ↓
alert threshold
  ↓
claude-fable-5 analysis through Crazyrouter
  ↓
JSON validation
  ↓
article/chart assets
```

The generated project lives here:

```text
generated/claude_code_odds_monitor_fable_20260613/
├── build_odds_monitor.py
├── sample_odds_movements.csv
├── odds_monitor_output.json
├── odds_movement_chart.svg
├── crazyrouter_raw_claude-fable-5.json
├── claude_fable_5_test_result.json
└── claude_fable_5_final_test_result.json
```

The key idea is simple:

```text
Python detects the movement.
claude-fable-5 explains the alert.
JSON validation decides whether the explanation is usable.
```

---

## Sample odds data

For a reproducible demo, I used synthetic sample data instead of relying on a paid sportsbook feed.

The CSV contains four early World Cup match examples:

```csv
match,market,opening_decimal,latest_decimal,timestamp
USA vs Paraguay,USA win,2.20,2.08,2026-06-12T08:00:00Z
USA vs Paraguay,Draw,3.55,3.65,2026-06-12T08:00:00Z
USA vs Paraguay,Paraguay win,3.10,3.25,2026-06-12T08:00:00Z
Brazil vs Morocco,Brazil win,1.78,1.72,2026-06-13T08:00:00Z
Qatar vs Canada,Canada win,2.08,1.92,2026-06-13T08:00:00Z
Germany vs Curaçao,Germany win,1.20,1.16,2026-06-14T08:00:00Z
```

A production version would replace this with a real odds API, multiple bookmakers, timestamped snapshots, and data-quality checks.

---

## Converting odds into implied probability

Decimal odds can be converted into implied probability with a very small function:

```python
def implied_prob(decimal_odds):
    return 1.0 / decimal_odds
```

Then we compare opening and latest implied probability:

```python
open_p = implied_prob(opening)
latest_p = implied_prob(latest)
delta_pp = (latest_p - open_p) * 100
```

If the absolute movement exceeds a threshold, the script creates an alert:

```python
if abs(delta_pp) >= 2.5:
    alerts.append({
        "match": match,
        "market": market,
        "direction": movement_direction,
        "delta_pp": round(delta_pp, 2),
        "note": "Large implied-probability movement; review news, lineup, liquidity, and data quality."
    })
```

This makes the system transparent. The alert is not based on vibes or a black-box LLM. It is based on a deterministic calculation.

---

## Example alert output

The script generated four significant movement alerts:

```json
[
  {
    "match": "USA vs Paraguay",
    "market": "USA win",
    "direction": "shortened",
    "delta_pp": 2.62
  },
  {
    "match": "Qatar vs Canada",
    "market": "Qatar win",
    "direction": "drifted",
    "delta_pp": -3.18
  },
  {
    "match": "Qatar vs Canada",
    "market": "Canada win",
    "direction": "shortened",
    "delta_pp": 4.01
  },
  {
    "match": "Germany vs Curaçao",
    "market": "Germany win",
    "direction": "shortened",
    "delta_pp": 2.87
  }
]
```

The biggest movement was Canada win in Qatar vs Canada, where implied probability increased by 4.01 percentage points.

Again: this is not a betting signal. It is a monitoring signal that says, “Something changed enough that a system should record it and maybe ask for more context.”

![World Cup odds movement monitor sample chart](https://raw.githubusercontent.com/xujfcn/images/main/blog/posts/claude-code-odds-monitor-claude-fable-5-chart.png)

---

## Calling claude-fable-5 through Crazyrouter

The next step was to ask `claude-fable-5` to summarize the alerts as structured JSON.

The API configuration follows the OpenAI-compatible format:

```python
from openai import OpenAI

client = OpenAI(
    api_key="YOUR_CRAZYROUTER_API_KEY",
    base_url="https://cn.crazyrouter.com/v1",
)
```

The request used:

```json
{
  "model": "claude-fable-5",
  "messages": [
    {
      "role": "user",
      "content": "Return raw JSON only. Analyze these odds movement alerts for an engineering demo..."
    }
  ],
  "max_tokens": 1200
}
```

The instruction explicitly said:

```text
No betting advice.
Output raw JSON only.
```

---

## claude-fable-5 test result

The final successful test result:

| Model | HTTP | Latency | Prompt tokens | Completion tokens | Total tokens | Valid JSON |
|---|---:|---:|---:|---:|---:|---|
| `claude-fable-5` | 200 | 12.14s | 833 | 599 | 1432 | True |

The model returned valid JSON without needing post-cleanup:

```json
{
  "summary": "4 significant odds movements detected across 3 matches. The Qatar vs Canada match shows the largest activity, with Canada win shortening by 4.01pp while Qatar win drifted by -3.18pp, indicating a consistent directional shift within the same market pair. USA win and Germany win both shortened moderately (2.62pp and 2.87pp respectively).",
  "alerts_count": 4,
  "top_movement": {
    "match": "Qatar vs Canada",
    "market": "Canada win",
    "direction": "shortened",
    "delta_pp": 4.01
  },
  "checks": [
    "Verify lineup announcements and injury news for all flagged matches, especially Qatar vs Canada",
    "Confirm market liquidity at the time of movement to rule out thin-market noise",
    "Validate data feed integrity and timestamp consistency across sources",
    "Cross-check movements against multiple bookmakers to confirm market-wide vs single-source shifts",
    "Check whether Qatar/Canada movements are mirrored (overround-consistent) or indicate a data anomaly"
  ],
  "recommendations": [
    "Treat the paired Qatar vs Canada movements as a single correlated event in downstream analytics",
    "Add automated correlation detection for opposing markets in the same match",
    "Log raw odds snapshots around alert timestamps for audit and replay",
    "Set per-market thresholds since a 4pp move may be normal in volatile markets but anomalous in stable ones",
    "Flag movements lacking corresponding news events for manual data-quality review"
  ],
  "disclaimer": "This analysis is for engineering and data-quality demonstration purposes only. It does not constitute betting advice, predictions, or recommendations to wager. Odds movements reflect market activity and data signals, not outcome probabilities endorsed by this system."
}
```

That output is useful because it does not turn the alert into a betting recommendation. It turns the alert into an engineering checklist.

---

## A real integration lesson: payload shape matters

There was one important failure during testing.

My first request to `claude-fable-5` returned:

```text
HTTP 400
Invalid request
```

The cause was not the model name. The model was available in `/v1/models`.

The issue was request compatibility. A slightly different payload shape worked later.

I also tested a longer JSON-output prompt with a lower token cap. That returned HTTP 200, but the content was wrapped in a Markdown code fence and sometimes reached the output limit.

The final working pattern was:

- use `model: claude-fable-5`;
- keep the input compact;
- use `max_tokens` rather than extra unsupported parameters;
- explicitly request raw JSON;
- validate the returned JSON anyway.

This is exactly why “model quality” is not only about intelligence.

For production systems, quality includes:

- payload compatibility;
- schema adherence;
- output length behavior;
- latency;
- retry and fallback behavior;
- cost per accepted output.

---

## Why this is a good Crazyrouter use case

An odds monitor is a good example of why an API gateway helps.

The workflow has several model-dependent tasks:

1. summarize large movement alerts;
2. identify possible data-quality checks;
3. explain correlated market movements;
4. generate an analyst-friendly report;
5. avoid prohibited or unsafe advice.

Different models may behave differently on these requirements.

With Crazyrouter, the integration stays simple:

```text
one API key
one OpenAI-compatible base URL
multiple model routes
same validation layer
```

That makes it easier to compare `claude-fable-5` with other models later, without rewriting the application.

---

## What Claude Code should build here

A useful Claude Code workflow should not just produce a script. It should build the operational pieces around the script:

1. `sample_odds_movements.csv` for reproducible input;
2. `odds_monitor_output.json` for deterministic alert output;
3. `odds_movement_chart.svg` for visualization;
4. `crazyrouter_raw_claude-fable-5.json` for raw API audit;
5. `claude_fable_5_final_test_result.json` for validation evidence;
6. a publishable technical article.

That is the pattern I care about in this series.

Claude Code should not be treated as a magic answer machine. It should be treated as a project executor that produces files, tests, logs, and artifacts.

---

## Limitations

This demo intentionally uses synthetic sample data.

A production odds monitor would need:

- real sportsbook or odds API integrations;
- multiple bookmakers;
- overround normalization;
- liquidity estimates;
- timestamp alignment;
- lineup and injury news;
- alert deduplication;
- historical calibration;
- compliance review.

The article is about building the monitoring workflow, not about making betting predictions.

---

## Next improvements

A stronger version could add:

1. scheduled odds snapshots;
2. multiple data providers;
3. overround-adjusted implied probabilities;
4. correlated movement detection within each match;
5. news and lineup correlation;
6. model comparison between `claude-fable-5`, GPT, Gemini, and Qwen routes;
7. cost per valid JSON report;
8. a small web dashboard.

The most important metric would not be token cost alone.

It would be:

```text
cost per valid alert report
```

That includes failed requests, retries, malformed JSON, and time spent debugging payload issues.

---

## Final verdict

Yes, Claude Code can build an odds movement monitor, but the right framing matters.

The best architecture is:

```text
Python calculates implied probability movement.
Claude Code builds the pipeline and artifacts.
claude-fable-5 summarizes alerts through Crazyrouter.
JSON validation decides whether the output is usable.
```

This project is a good second entry in the Claude Code project series because it moves from match prediction into monitoring: a more realistic pattern for analytics dashboards, financial dashboards, API observability, and operational alerts.

If you want to test `claude-fable-5` or other models through one OpenAI-compatible API layer, try Crazyrouter:

https://crazyrouter.com?utm_source=crazyrouter_blog&utm_medium=article&utm_campaign=claude_code_odds_monitor_fable
