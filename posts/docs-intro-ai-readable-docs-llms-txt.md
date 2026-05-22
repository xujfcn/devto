---
title: 'AI-Readable Docs for Coding Agents: How to Use llms.txt in Real Projects'
published: true
description: A practical tutorial for using llms.txt and llms-full.txt to help coding agents find the right documentation before they edit code.
tags: 'ai, docs, agents, tutorial'
canonical_url: 'https://docs.crazyrouter.com/en/introduction?utm_source=devto&utm_medium=article&utm_campaign=docs_intro'
id: 3725345
date: '2026-05-22T11:53:35Z'
---

AI coding agents are only as useful as the context they can find.

If an agent has the wrong API endpoint, an outdated setup guide, or a random blog post from two years ago, it can confidently generate broken code. That is why more documentation sites are adding AI-readable entry points such as `llms.txt` and `llms-full.txt`.

This article explains how to use those files in real projects, using Crazyrouter's docs as a practical example.

Crazyrouter's documentation introduction links directly to AI-readable docs and says the complete documentation index is available at:

```txt
https://docs.crazyrouter.com/llms.txt
```

You can start from the [Crazyrouter docs introduction](https://docs.crazyrouter.com/en/introduction?utm_source=devto&utm_medium=article&utm_campaign=docs_intro), or point your agent to the raw index when it needs machine-readable navigation.

## What is llms.txt?

`llms.txt` is a plain-text or Markdown-style file designed to help AI tools discover the most important documentation pages for a site.

Think of it as a curated map for language models.

A normal sitemap may include every page. A docs sidebar may be optimized for humans. An `llms.txt` file is usually shorter and more task-oriented:

```md
# Project Docs

## Start Here

- [Quick Start](https://example.com/quickstart.md): Make the first API request.
- [Authentication](https://example.com/authentication.md): Create and use API keys.
- [API Endpoints](https://example.com/api-endpoints.md): Choose the correct base URL.

## Integrations

- [Tool A](https://example.com/integrations/tool-a.md): Configure Tool A.
- [Tool B](https://example.com/integrations/tool-b.md): Configure Tool B.
```

The goal is simple: when an AI coding tool asks, "Where are the docs for this task?", `llms.txt` gives it a high-signal answer.

## llms.txt vs llms-full.txt

Many projects use two related files:

```txt
llms.txt       a concise index of important docs
llms-full.txt  a larger single-file documentation context
```

Use `llms.txt` when the agent needs to discover the right page.

Use `llms-full.txt` when the agent can ingest a larger context file and you want it to work from the full documentation set.

Crazyrouter exposes both:

- [llms.txt](https://docs.crazyrouter.com/llms.txt?utm_source=devto&utm_medium=article&utm_campaign=docs_intro)
- [llms-full.txt](https://docs.crazyrouter.com/llms-full.txt?utm_source=devto&utm_medium=article&utm_campaign=docs_intro)

The human-friendly docs entry is here: [Crazyrouter Docs](https://docs.crazyrouter.com/en/introduction?utm_source=devto&utm_medium=article&utm_campaign=docs_intro).

## Why this matters for coding agents

Coding agents often work in loops:

1. inspect the repo
2. infer the task
3. search or read docs
4. edit code
5. run tests
6. fix errors

The weak step is usually documentation retrieval.

Without a good docs entry point, an agent might:

- use the wrong SDK version
- invent configuration keys
- confuse OpenAI-compatible and Anthropic-native base URLs
- call deprecated endpoints
- miss tool-specific setup details
- overfit to examples from unrelated providers

With `llms.txt`, you can give the agent an explicit first stop.

For example, Crazyrouter's `llms.txt` points agents to important pages for:

- Quick Start
- API Endpoints
- Authentication
- Error Handling
- Claude Code
- Codex CLI
- Cursor
- OpenAI Chat Completions
- Claude Messages
- Gemini Native
- image generation
- video generation
- audio

That is exactly the kind of map a coding agent needs before changing integration code.

## A practical workflow for real projects

Here is a simple workflow you can add to your repository.

Create a file such as:

```txt
.agent-docs.md
```

Add the official docs entry points your coding agents should use:

```md
# Agent documentation entry points

Before editing integration code, read the relevant official docs.

## Crazyrouter

Start here:
https://docs.crazyrouter.com/en/introduction?utm_source=devto&utm_medium=article&utm_campaign=docs_intro

Machine-readable index:
https://docs.crazyrouter.com/llms.txt?utm_source=devto&utm_medium=article&utm_campaign=docs_intro

Full docs context:
https://docs.crazyrouter.com/llms-full.txt?utm_source=devto&utm_medium=article&utm_campaign=docs_intro

Important pages:
- API endpoints: https://docs.crazyrouter.com/en/api-endpoint?utm_source=devto&utm_medium=article&utm_campaign=docs_intro
- Quick start: https://docs.crazyrouter.com/en/quickstart?utm_source=devto&utm_medium=article&utm_campaign=docs_intro
- Claude Code: https://docs.crazyrouter.com/en/integrations/claude-code?utm_source=devto&utm_medium=article&utm_campaign=docs_intro
```

Then tell your agent:

```txt
Before editing API integration code, read .agent-docs.md and follow the official docs links. Do not rely on memory for endpoint paths or base URLs.
```

This small file can prevent a lot of hallucinated configuration.

## Example: using llms.txt from a script

You can also build lightweight tooling around `llms.txt`.

Here is a simple Node.js script that fetches a docs index and prints links matching a keyword.

```js
const indexUrl = "https://docs.crazyrouter.com/llms.txt";
const keyword = process.argv[2]?.toLowerCase() ?? "claude";

const response = await fetch(indexUrl);

if (!response.ok) {
  throw new Error(`Failed to fetch ${indexUrl}: ${response.status}`);
}

const text = await response.text();
const matches = text
  .split("\n")
  .filter((line) => line.toLowerCase().includes(keyword));

console.log(matches.join("\n"));
```

Run it:

```bash
node find-docs.js claude
node find-docs.js video
node find-docs.js authentication
```

This is intentionally simple. In a real internal tool, you might parse Markdown links, cache the result, and expose it to your agent runner.

## Example: convert llms.txt into an agent prompt

Sometimes the easiest integration is just to include a docs index in an agent prompt.

```js
async function buildDocsContext() {
  const res = await fetch("https://docs.crazyrouter.com/llms.txt");
  if (!res.ok) throw new Error("Could not fetch docs index");

  const llmsTxt = await res.text();

  return `
You are working on an app that uses Crazyrouter.
Use the official docs map below before editing API integration code.
Do not invent endpoint paths, model names, or environment variables.

${llmsTxt}
`;
}
```

Then pass that context to your agent before a coding task.

If your tool supports URL reading directly, you may not need to paste the content. Just give it the URL and a clear instruction:

```txt
Read https://docs.crazyrouter.com/llms.txt first. Then choose the relevant docs page for this task. Only after that, inspect and edit the repository.
```

## Example: protecting against base URL mistakes

A real example from AI API integrations is the difference between OpenAI-compatible clients and Anthropic-native clients.

From Crazyrouter's [API Endpoints docs](https://docs.crazyrouter.com/en/api-endpoint?utm_source=devto&utm_medium=article&utm_campaign=docs_intro):

```txt
OpenAI-compatible SDKs / clients:
https://crazyrouter.com/v1

Claude Code / Anthropic-native clients:
https://crazyrouter.com
```

If an agent only sees an OpenAI SDK example, it may incorrectly configure Claude Code with:

```bash
export ANTHROPIC_BASE_URL=https://crazyrouter.com/v1
```

But Claude Code should use:

```bash
export ANTHROPIC_BASE_URL=https://crazyrouter.com
```

A docs-aware agent can find the [Claude Code integration guide](https://docs.crazyrouter.com/en/integrations/claude-code?utm_source=devto&utm_medium=article&utm_campaign=docs_intro) from `llms.txt` before making that mistake.

## Add docs checks to pull requests

For teams, make docs retrieval part of the PR process.

In your PR template:

```md
## AI integration changes

If this PR changes AI provider, model, endpoint, or agent-tool configuration:

- [ ] I checked the official docs entry point
- [ ] I verified the correct base URL
- [ ] I verified the request path
- [ ] I verified required environment variables
- [ ] I tested with a non-production token

Docs used:
-
```

This is useful whether the code was written by a human, an AI agent, or both.

## Cache docs, but do not freeze them forever

For reliability, you may want to cache `llms.txt` in your agent infrastructure.

A reasonable approach:

```txt
Fetch llms.txt at task start
Cache for 1-24 hours
Record the docs URL and fetch timestamp in logs
Use live docs for high-risk integration changes
```

Avoid copying a docs snapshot into your repo and forgetting about it for a year. API gateways and model platforms change quickly.

If you need deterministic builds or audits, store:

- the docs URL
- the fetch timestamp
- the relevant excerpts used by the agent
- the final code diff
- the test results

That gives you traceability without forcing every future task to use stale documentation.

## A minimal docs-aware agent policy

You can paste this into your internal agent instructions:

```txt
When changing code that calls external AI APIs:

1. Read the repository's .agent-docs.md file if present.
2. If the provider exposes llms.txt, read it before searching elsewhere.
3. Prefer official docs over memory or third-party posts.
4. Verify base URL, endpoint path, authentication header, and model name.
5. Do not print secret values in logs.
6. Run the smallest meaningful test before reporting success.
```

This is not complicated, but it changes agent behavior in a very practical way.

## What good llms.txt entries look like

If you maintain docs, your `llms.txt` should be short enough to scan but complete enough to route tasks.

Good entries usually include:

- page title
- canonical URL
- one-line description
- task-oriented grouping
- links to integration guides
- links to authentication and endpoint references
- links to error handling
- links to full context if available

For example:

```md
## Core APIs

- [OpenAI Chat Completions](https://docs.example.com/chat/openai/completions.md): POST /v1/chat/completions.
- [Claude Messages](https://docs.example.com/chat/anthropic/messages.md): Anthropic-native messages.
- [List Models](https://docs.example.com/chat/openai/models.md): GET /v1/models.
```

This gives an agent both navigation and intent.

## Common mistakes

### Mistake 1: only giving the agent a homepage

A homepage may be designed for marketing or human navigation. Give the agent the docs index or `llms.txt` when available.

### Mistake 2: pasting old examples into prompts

If you paste an old code snippet, the agent may trust it more than the current docs. Include the official docs URL and ask it to verify.

### Mistake 3: ignoring tool-specific docs

A generic OpenAI-compatible setup guide may not apply to every tool. Claude Code, for example, uses Anthropic-native configuration and should use the root base URL.

### Mistake 4: not logging docs provenance

If an agent changes integration code, record which docs it used. This makes reviews easier.

## Final thoughts

`llms.txt` is not magic. It does not make an agent correct by itself.

But it gives the agent a better first move.

Instead of guessing, searching randomly, or relying on stale memory, the agent can start from an official, task-oriented map of the docs. That is especially valuable for API gateways, model providers, and tool integrations where a single base URL or endpoint path can make the difference between working code and a frustrating afternoon.

For a concrete example, explore the [Crazyrouter docs introduction](https://docs.crazyrouter.com/en/introduction?utm_source=devto&utm_medium=article&utm_campaign=docs_intro), the machine-readable [llms.txt](https://docs.crazyrouter.com/llms.txt?utm_source=devto&utm_medium=article&utm_campaign=docs_intro), and the full-context [llms-full.txt](https://docs.crazyrouter.com/llms-full.txt?utm_source=devto&utm_medium=article&utm_campaign=docs_intro). If your coding agent supports URL reading, make those links part of its standard workflow before it edits integration code.
