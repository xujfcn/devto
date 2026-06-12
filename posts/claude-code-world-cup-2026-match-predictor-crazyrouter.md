---
title: 'Can Claude Code Build a World Cup 2026 Match Predictor? A Real Crazyrouter API Test'
published: true
description: 'We built a reproducible World Cup 2026 match predictor demo with Claude Code-style workflow, Elo/Poisson probabilities, charts, and real Crazyrouter API calls through https://cn.crazyrouter.'
tags: ai, api, coding, tutorial
cover_image: 'https://raw.githubusercontent.com/xujfcn/images/main/blog/covers/claude-code-world-cup-2026-match-predictor-crazyrouter.webp'
canonical_url: 'https://crazyrouter.com/blog/claude-code-world-cup-2026-match-predictor-crazyrouter?utm_source=devto&utm_medium=article&utm_campaign=claude_code_worldcup_predictor'
---

# Can Claude Code Build a World Cup 2026 Match Predictor? A Real Crazyrouter API Test

Claude Code is most interesting when it builds a working project, not when it only generates snippets. For this experiment, I used a Claude Code-style workflow to build a small **World Cup 2026 match predictor**: fixture data, a transparent Elo/Poisson prediction model, charts, and real LLM-generated match previews through Crazyrouter.

This is an analytics and developer workflow demo. It is **not betting advice**.

The live API layer was tested through:

```text
Base URL: https://cn.crazyrouter.com/v1
Date: 2026-06-12 UTC
Endpoints tested:
- GET /v1/models
- POST /v1/chat/completions
```

![Claude Code World Cup predictor architecture with Crazyrouter API](https://raw.githubusercontent.com/xujfcn/images/main/blog/covers/claude-code-world-cup-2026-match-predictor-crazyrouter.webp)

## Why this is a good Claude Code project

A sports prediction dashboard is a useful coding-agent demo because it forces the agent to do more than write UI code:

1. collect and normalize messy event data;
2. define a transparent model;
3. generate reproducible outputs;
4. create charts;
5. call an LLM API for structured explanations;
6. handle model failure modes like malformed JSON.

That makes it a better article than a generic “Claude Code project ideas” list. The project produces real files, real numbers and real API traces.

## Public data signals used

For this demo, I used public fixture information available on World Cup schedule pages and focused on early tournament matches around June 11-18, 2026. The fetched pages confirmed the tournament format: 48 teams, 104 matches, 16 stadiums, group-stage fixtures, and early fixtures such as Mexico vs South Africa, South Korea vs Czechia, USA vs Paraguay and Brazil vs Morocco.

The local demo data is intentionally small and reproducible:

- `fixtures.json` — nine early fixtures;
- `team_ratings_seed.json` — seed Elo-style ratings for demo purposes;
- `predictions.json` — model output for each match;
- `crazyrouter_test_results.json` — raw LLM API test summary.

The model is deliberately simple. A production system should replace the seed ratings with live Elo/FIFA ranking data, injury data, lineups, odds, travel, rest days and result updates.

## Prediction method: transparent beats mystical

The predictor combines three simple ideas:

- **Elo-style rating difference** for relative team strength;
- **host boost** for host-nation matches in relevant venues;
- **Poisson expected goals** for scoreline probabilities.

The core idea looks like this:

```python
def expected_goals(rating_for, rating_against, host_boost=0):
    diff = (rating_for + host_boost) - rating_against
    return max(0.45, min(2.65, 1.28 + diff / 520))
```

Then the script enumerates likely scorelines, estimates home/draw/away probabilities, and saves a prediction object for each fixture.

## Sample predictions

Here are the first seven predictions from the demo run:

| Date | Match | Group | xG | Home / Draw / Away | Pick |
|---|---|---|---:|---:|---|
| 2026-06-11 | Mexico vs South Africa | A | 1.68-0.98 | 55.8% / 24.2% / 19.9% | Mexico |
| 2026-06-11 | South Korea vs Czechia | A | 1.35-1.21 | 40.1% / 26.6% / 33.3% | South Korea |
| 2026-06-12 | USA vs Paraguay | D | 1.53-1.14 | 48.2% / 25.5% / 26.3% | USA |
| 2026-06-13 | Brazil vs Morocco | C | 1.64-0.92 | 54.9% / 24.7% / 20.4% | Brazil |
| 2026-06-13 | Qatar vs Canada | B | 1.1-1.46 | 27.8% / 26.1% / 46.1% | Canada |
| 2026-06-14 | Germany vs Curaçao | E | 2.08-0.48 | 75.1% / 17.7% / 7.2% | Germany |
| 2026-06-14 | Netherlands vs Japan | F | 1.53-1.03 | 49.5% / 25.7% / 24.8% | Netherlands |

![World Cup 2026 match prediction probability snapshot](https://raw.githubusercontent.com/xujfcn/images/main/blog/posts/claude-code-world-cup-2026-match-predictor-crazyrouter-01.webp)

Notice the USA vs Paraguay example: the model gives the USA a slight edge, but not a dominant one. The home win probability is 48.2%, while draw plus Paraguay win is 51.8%. That is exactly the kind of uncertainty a good demo should preserve.

## Crazyrouter real API test

After generating model probabilities, the script asked four model routes to turn the USA vs Paraguay model output into a structured JSON match preview.

Task:

```text
Generate structured match preview JSON for USA vs Paraguay using model output.
Do not give betting advice.
Return valid compact JSON only.
```

The Crazyrouter model-list endpoint also worked:

```text
GET /v1/models
HTTP status: 200
Latency: 502 ms
Models returned: 262
```

The chat-completion test results:

| Model | HTTP | Latency | Prompt tokens | Completion tokens | Total tokens | Valid JSON |
|---|---:|---:|---:|---:|---:|---|
| `gpt-4o-mini` | 200 | 4.53s | 305 | 292 | 597 | True |
| `gpt-5.5` | 200 | 6.28s | 600 | 278 | 878 | True |
| `gemini-2.5-flash` | 200 | 11.33s | 327 | 356 | 683 | False |
| `qwen-plus` | 200 | 8.13s | 325 | 177 | 502 | True |

![Crazyrouter API test latency and token usage for World Cup predictor](https://raw.githubusercontent.com/xujfcn/images/main/blog/posts/claude-code-world-cup-2026-match-predictor-crazyrouter-02.webp)

## What the model outputs showed

Three routes returned valid JSON:

- `gpt-4o-mini`
- `gpt-5.5`
- `qwen-plus`

One route returned HTTP 200 but failed JSON validation:

- `gemini-2.5-flash`

That failure is not a problem for the article; it is the article. In production AI workflows, **HTTP 200 is not enough**. A coding agent should build validators, retries and fallback routes.

A valid output example from `gpt-4o-mini`:

```json
{
  "match": {
    "date": "2026-06-12",
    "group": "D",
    "home": "USA",
    "away": "Paraguay",
    "venue": "Los Angeles Stadium"
  },
  "predicted_edge": "USA",
  "probability_summary": {
    "home_win": 0.482,
    "draw": 0.255,
    "away_win": 0.263
  },
  "key_factors": {
    "home_rating": 1785,
    "away_rating": 1710,
    "home_host_boost": 55,
    "away_host_boost": 0,
    "xg_home": 1.53,
    "xg_away": 1.14
  },
  "uncertainty": {
    "top_scores": [
      {"score": "1-1", "probability": 0.121},
      {"score": "1-0", "probability": 0.107},
      {"score": "2-1", "probability": 0.093},
      {"score": "2-0", "probability": 0.081},
      {"score": "0-1", "probability": 0.079}
    ]
  },
  "disclaimer": "Analytics demo only; not betting advice."
}
```

## What Claude Code should build around the LLM call

A robust Claude Code workflow should not simply call an LLM and trust the text. It should build an execution pipeline:

1. read fixture and rating data;
2. calculate deterministic probabilities;
3. send compact model output to Crazyrouter;
4. require JSON schema;
5. validate JSON;
6. retry or fallback when invalid;
7. save raw responses for audit;
8. render dashboard and article assets.

This is the practical reason to use an AI API gateway. You can route the same task through multiple models while keeping one API base URL and one client shape.

## Minimal reproduction structure

The demo folder contains:

```text
generated/worldcup_predictor_claude_code_20260612/
├── build_worldcup_predictor.py
├── fixtures.json
├── team_ratings_seed.json
├── predictions.json
├── crazyrouter_test_results.json
├── crazyrouter_raw_gpt-4o-mini.json
├── crazyrouter_raw_gpt-5.5.json
├── crazyrouter_raw_gemini-2.5-flash.json
├── crazyrouter_raw_qwen-plus.json
├── image_urls.json
└── claude-code-world-cup-2026-match-predictor-crazyrouter.md
```

The key API client configuration is standard OpenAI-compatible code:

```python
from openai import OpenAI

client = OpenAI(
    api_key="YOUR_CRAZYROUTER_API_KEY",
    base_url="https://cn.crazyrouter.com/v1",
)

response = client.chat.completions.create(
    model="gpt-4o-mini",
    messages=[
        {"role": "system", "content": "Return only valid JSON. No betting advice."},
        {"role": "user", "content": prompt},
    ],
    temperature=0.2,
    max_tokens=360,
)
```

Do not add UTM parameters to API base URLs. UTM belongs on human-facing links only.

## Engineering lessons

### 1. Claude Code is good at project scaffolding

This is a strong Claude Code project because the deliverable is not one function. It is a pipeline with data files, modeling code, API calls, raw logs and visual assets.

### 2. Simple models are better for explainability

For an article/demo, a transparent Elo/Poisson model is better than a black-box “AI says Brazil wins” answer. The LLM should explain the model, not replace the model.

### 3. JSON validation is mandatory

The Gemini route returned HTTP 200 but invalid JSON in this constrained test. That is a real production lesson: validate the output and fallback automatically.

### 4. A gateway makes experiments easier

The same request shape worked across GPT, Gemini and Qwen-style routes. That makes model comparison and fallback easier than wiring every provider directly.

## Limitations

This demo intentionally keeps the model small. It does not include:

- live injury reports;
- confirmed lineups;
- rest days and travel fatigue;
- bookmaker odds;
- live xG;
- player-level form;
- calibrated historical backtesting.

So the predictions should be read as a coding and data workflow demonstration, not as betting recommendations.

## Next improvements

A stronger version could add:

1. automatic fixture updates from a World Cup API;
2. real Elo/FIFA ranking ingestion;
3. Monte Carlo group-stage simulation;
4. odds movement monitoring;
5. automatic fallback when JSON validation fails;
6. a static web dashboard;
7. daily World Cup prediction articles generated from the same pipeline.

## Final verdict

Yes: Claude Code can build a credible World Cup 2026 match predictor demo, as long as the project is framed correctly.

The right architecture is:

```text
Claude Code builds the pipeline.
Deterministic code calculates probabilities.
Crazyrouter provides multi-model explanations and structured output.
Validators decide whether the LLM output is usable.
```

In this run, Crazyrouter returned 262 models from `/v1/models`, and three of four tested model routes produced valid JSON match previews. The failed JSON route is useful evidence too: real AI projects need validation and fallback, not just prompts.

If you want to build Claude Code projects that compare multiple AI models through one API layer, start with [Crazyrouter](https://crazyrouter.com?utm_source=crazyrouter_blog&utm_medium=article&utm_campaign=claude_code_worldcup_predictor).
