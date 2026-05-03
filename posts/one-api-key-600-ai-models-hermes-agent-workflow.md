---
title: 'One API Key for 600+ AI Models: How I Simplified My AI Workflow with an Open-Source Agent'
published: true
description: I was juggling 5 API keys across different AI providers. Here is how I consolidated everything into one key using Hermes Agent and an API gateway.
tags: 'ai, opensource, productivity, tutorial'
cover_image: ''
canonical_url: 'https://crazyrouter.com/blog/hermes-agent-one-api-key-workflow?utm_source=devto&utm_medium=article&utm_campaign=hermes-workflow'
id: 3604940
date: '2026-05-03T17:39:08Z'
---

I use AI models every day — Claude for coding, GPT for writing, DeepSeek for reasoning, Gemini for quick lookups. Each one has its strengths, and I want access to all of them.

But here's the problem: every provider means a separate account, a separate API key, separate billing. Managing five different API keys across different projects gets old fast.

I recently found a setup that solved this for me: an open-source AI agent paired with an API gateway. One key, one config, access to everything. Here's how it works.

## Hermes Agent: An Open-Source AI Agent That Actually Works

[Hermes Agent](https://github.com/NousResearch/hermes-agent) is built by Nous Research. It's a terminal-based AI agent, but don't let that undersell it — this thing is genuinely useful for daily work.

What makes it stand out:

- **Solid terminal UX.** Multi-line editing, autocomplete, streaming responses. It feels like a proper tool, not a toy.
- **Multi-platform messaging.** Beyond the CLI, it connects to Telegram, Discord, Slack, and WhatsApp. Run a gateway process on a VPS and chat with your agent from your phone.
- **It learns from you.** Repeated tasks get distilled into reusable "skills" that it applies automatically next time.
- **Runs anywhere.** No GPU needed. A $5/month VPS handles it fine.

Install with one command:

```bash
curl -fsSL https://raw.githubusercontent.com/NousResearch/hermes-agent/main/scripts/install.sh | bash
```

Works on Linux, macOS, WSL2, and even Android Termux.

## The Problem: One Key Per Provider

Hermes Agent supports multiple model providers out of the box. But the default setup is the traditional "one provider, one API key" approach.

In practice, that means:

- Claude → register at Anthropic, get a key
- GPT → register at OpenAI, get a key
- Gemini → register at Google, get a key
- DeepSeek, Llama, Mistral → repeat for each

For a solo developer, managing 4-5 keys is annoying but doable. For a team, it becomes a real headache — everyone needs their own keys, and there's no unified billing.

## The Solution: An API Gateway as a Single Entry Point

Hermes Agent has a great design choice: it works with any OpenAI-compatible API endpoint.

This means you can put an API gateway in front of all the providers and only maintain one base URL and one API key:

```
Hermes Agent
     ↓  single key
  [API Gateway]
   ↓    ↓    ↓    ↓
 GPT  Claude Gemini DeepSeek
```

I'm using [Crazyrouter](https://crazyrouter.com?utm_source=devto&utm_medium=article&utm_campaign=hermes-agent) for this. It's an OpenAI-compatible gateway that aggregates 627+ models with pay-as-you-go pricing, no monthly fees.

## Setup: Under One Minute

There's a ready-made config script on GitHub:

```bash
git clone https://github.com/xujfcn/crazyrouter-hermes.git
cd crazyrouter-hermes
./setup.sh
```

On Windows, use `.\setup.ps1` or `setup.bat`.

The script handles everything: detect environment → set base URL → enter key → pick default model → test connection.

Manual setup is just as simple:

```bash
hermes config set providers.crazyrouter.base_url https://crazyrouter.com/v1
hermes config set providers.crazyrouter.api_key YOUR_KEY
hermes model   # pick a model
```

Get your API key at [crazyrouter.com](https://crazyrouter.com/register?utm_source=devto&utm_medium=article&utm_campaign=hermes-agent) — new accounts come with free credits.

## What It Looks Like in Practice

Switching models is now a one-liner:

```bash
# Coding with Claude
hermes model claude-sonnet-4-20250514
> refactor this function from callbacks to async/await

# Translation with GPT
hermes model gpt-4o
> translate this technical doc to Japanese

# Reasoning with DeepSeek
hermes model deepseek-r1
> analyze the time complexity of this algorithm

# Quick answers with Gemini Flash
hermes model gemini-2.5-flash
> how do I check which process is using port 8080 on Linux
```

Different tasks, different models, zero config changes.

Combined with Hermes Agent's other features:

- **Telegram remote access**: run `hermes gateway` and use it from your phone
- **Scheduled tasks**: say "summarize GitHub trending every day at 9am" and it creates a cron job
- **Parallel tasks**: subagent support lets you dispatch work to multiple models simultaneously

## A Few Things to Watch Out For

1. The base URL must end with `/v1` — without it you'll get a 404
2. Watch for trailing spaces when copying the API key
3. Run `hermes doctor` on first use to check your environment
4. The setup script won't overwrite existing config — it only adds the new provider

## Wrapping Up

More AI models is a good thing. But the management overhead grows with each new provider. For developers who regularly switch between models, the "open-source agent + API gateway" combo cuts out a lot of friction:

- One key for all models
- Switch with a single command
- Unified billing
- New models available instantly, no config changes needed

The whole setup takes under five minutes. Give it a try.

---

**Links:**
- Hermes Agent: [github.com/NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent)
- Setup script: [github.com/xujfcn/crazyrouter-hermes](https://github.com/xujfcn/crazyrouter-hermes)
- API Gateway: [crazyrouter.com](https://crazyrouter.com?utm_source=devto&utm_medium=article&utm_campaign=hermes-agent)
