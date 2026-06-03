---
title: 'Claude Code Dynamic Workflows, Rebuilt: A Practical Ultracode-Style Orchestration Template'
published: true
description: 'Dynamic workflows in Claude Code are trending because they turn one prompt into orchestration, subagents, and verification gates. We rebuilt the useful pattern as a reproducible local workfl'
tags: 'ai, coding, claude, agents'
cover_image: 'https://raw.githubusercontent.com/xujfcn/images/main/blog/covers/claude-opus-46-47-48-benchmark-cover.png'
canonical_url: 'https://crazyrouter.com/blog/claude-code-dynamic-workflows-ultracode-rebuilt-crazyrouter?utm_source=devto&utm_medium=article&utm_campaign=dynamic_workflows'
---

# Claude Code Dynamic Workflows, Rebuilt: A Practical Ultracode-Style Orchestration Template

Claude Code Dynamic Workflows and `ultracode` are getting attention because they change the shape of AI coding work.

Instead of asking one agent to do everything in one long conversation, the workflow pattern looks more like this:

1. detect that a task is complex;
2. write an orchestration plan;
3. split the work into scoped packets;
4. assign subagents or roles;
5. require review and verification gates;
6. keep trace evidence for what happened.

The important part is not the brand name. The important part is the orchestration pattern.

So we rebuilt the useful part locally as a reproducible template that works with an OpenAI-compatible model gateway like Crazyrouter.

![Dynamic workflow score and latency chart](https://raw.githubusercontent.com/xujfcn/images/main/blog/posts/claude-opus-46-47-48-score-latency.webp)

## Why dynamic workflows are different

A normal AI coding session is linear:

```text
human prompt -> agent edits files -> human checks result
```

A dynamic workflow is closer to:

```text
human goal
  -> planner packet
  -> implementer packet
  -> adversarial reviewer packet
  -> verifier packet
  -> trace log + go/no-go
```

That structure matters because complex coding tasks fail when everything is mixed together:

- planning gets confused with implementation;
- implementation gets reviewed by the same assumptions that created it;
- tests are mentioned but not actually run;
- the developer cannot tell what happened after a long agent session;
- token usage explodes without a routing policy.

## The local reproduction

We created a small tool:

```text
tools/agent_workflows/workflow_orchestrator.py
```

It creates a dynamic-workflow folder with role packets, verification gates, and trace logging.

Example:

```bash
python tools/agent_workflows/workflow_orchestrator.py \
  --title "Claude Code Dynamic Workflows ultracode reproduction" \
  --task "Reproduce the useful part of Claude Code Dynamic Workflows / ultracode: split a complex coding request into scoped packets, assign model routes, require adversarial review, and verify with evidence." \
  --out generated/dynamic_workflows_20260603/ultracode_reproduction
```

Generated output:

```text
ultracode_reproduction/
├── README.md
├── workflow.json
├── trace.jsonl
├── verify.sh
├── article_notes.md
└── packets/
    ├── 01-planner.md
    ├── 02-implementer.md
    ├── 03-adversarial-reviewer.md
    └── 04-verifier.md
```

This is not trying to clone Claude Code internals. It reproduces the workflow primitive in a way that is portable, inspectable, and publishable.

## The four-packet workflow

| Packet | Role | Suggested model route | Completion gate |
|---|---|---|---|
| Planner | Convert request into scope, risks, acceptance criteria, file-level plan | `claude-opus-4-7` | Plan lists affected files, risks, tests, rollback |
| Implementer | Apply the smallest safe patch | coding-optimized model | Patch maps to acceptance criteria |
| Adversarial reviewer | Challenge assumptions, security, edge cases, tests | alternate reviewer model, e.g. `claude-opus-4-8` | Returns approve / request changes / block |
| Verifier | Run tests or inspect evidence | fast summary model | Produces command output or direct inspection notes |

The point is role separation. The model that implements should not be the only model that reviews.

## Crazyrouter model routing setup

When model calls are needed, use one OpenAI-compatible base URL and switch only the model ID.

```python
from openai import OpenAI

client = OpenAI(
    api_key="YOUR_CRAZYROUTER_API_KEY",
    base_url="https://cn.crazyrouter.com/v1"
)

planner = client.chat.completions.create(
    model="claude-opus-4-7",
    messages=[{"role": "user", "content": "Create a scoped implementation plan with risks and tests."}],
    temperature=0
)

reviewer = client.chat.completions.create(
    model="claude-opus-4-8",
    messages=[{"role": "user", "content": "Adversarially review this plan. Return approve, request-changes, or block."}],
    temperature=0
)
```

Keep API endpoints clean. Do not add UTM parameters to `base_url`.

Correct:

```text
https://cn.crazyrouter.com/v1
```

Wrong:

```text
https://cn.crazyrouter.com/v1?utm_source=blog
```

## Why `ultracode`-style workflows can get expensive

Dynamic workflows can fan out. A single request may create many sub-tasks, and each sub-task may ask for context, write code, review code, and run verification.

That is powerful, but it needs budget control.

A good workflow should log:

| Field | Why it matters |
|---|---|
| role | planner / implementer / reviewer / verifier |
| model | model routing and cost attribution |
| latency | user experience and bottleneck analysis |
| tokens | usage and cost estimation |
| result | approve / changes / blocked / verified |
| evidence | tests, screenshots, logs, URLs |

We also created:

```text
tools/agent_workflows/agent_trace_logger.py
```

Example trace summary:

```text
By model:
- claude-opus-4-7: calls=1, avg_latency=7.46s, total_tokens=1800
- claude-opus-4-8: calls=1, avg_latency=4.59s, total_tokens=1500
```

![Dynamic workflow test matrix](https://raw.githubusercontent.com/xujfcn/images/main/blog/posts/claude-opus-46-47-48-test-matrix.webp)

## A practical routing policy

For teams using AI coding agents, start simple:

| Workflow step | Routing rule |
|---|---|
| Planning | stronger reasoning model, low temperature |
| Implementation | coding-specialized or cost-balanced model |
| Review | different model from implementer |
| Verification summary | fast/cheap model only after tests run |
| High-risk changes | require human approval before merge |

The mistake is using the most expensive model for every step. The other mistake is using the cheapest model for steps where failure is expensive.

A gateway lets you route by step.

## What we shipped

For this reproduction, we created:

- `tools/agent_workflows/workflow_orchestrator.py`
- `tools/agent_workflows/agent_packetizer.py`
- `tools/agent_workflows/agent_trace_logger.py`
- `generated/dynamic_workflows_20260603/ultracode_reproduction/`
- `growth_ops/codex_claude_search_report_20260603.md`
- `growth_ops/twitter_codex_claude_cases.md`

This turns a trending workflow idea into reusable operating infrastructure.

## FAQ

### Is this the same as Claude Code Dynamic Workflows?

No. It is a local reproduction of the useful workflow pattern: orchestration, packets, review, verification, and trace logging.

### Why not just use one agent?

One agent is fine for small tasks. For complex tasks, role separation catches more mistakes and creates better evidence.

### Why use Crazyrouter here?

Crazyrouter provides an OpenAI-compatible API gateway so each workflow step can route to a different model without rewriting client code.

### Should every task use dynamic workflows?

No. Use them for multi-file changes, migrations, security-sensitive edits, large refactors, or tasks where review evidence matters.

### What is the next improvement?

The next step is to connect the orchestrator to real model calls, collect token/latency data automatically, and compare routing policies.

## Final take

Dynamic workflows are not magic. They are structured orchestration.

The winning pattern is:

```text
plan -> implement -> adversarial review -> verify -> log evidence
```

If you combine that with model routing, you get something more useful than a single long AI coding chat: a measurable engineering workflow.

Try the API gateway here: [Crazyrouter](https://crazyrouter.com?utm_source=blog&utm_medium=article&utm_campaign=dynamic_workflows)
