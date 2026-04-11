---
title: "How to Use Hermes Agent with Crazyrouter — 600+ Models, Lower Cost"
published: true
description: "Configure NousResearch Hermes Agent to use Crazyrouter instead of OpenRouter. Access 600+ AI models at lower cost with a single API key. One-command setup, no code changes."
tags: ai, llm, api, devtools
canonical_url: https://xufjcn.hashnode.dev/hermes-agent-crazyrouter-setup-guide
---

# How to Use Hermes Agent with Crazyrouter — 600+ Models, Lower Cost

[Hermes Agent](https://github.com/nousresearch/hermes-agent) by NousResearch has quickly become one of the most capable open-source AI agents available. With 57,000+ GitHub stars, built-in terminal access, browser automation, MCP support, and persistent memory, it's a serious tool for developers who want an agent that actually gets things done.

By default, Hermes routes all LLM calls through OpenRouter. But here's something most people don't realize: you can swap the provider to any OpenRouter-compatible gateway without touching a single line of Hermes source code.

This guide shows you how to set up Hermes Agent with [Crazyrouter](https://crazyrouter.com?utm_source=devto&utm_medium=article&utm_campaign=dev_community) — an AI API gateway that gives you access to 600+ models at prices typically 30-50% lower than going direct.

## Why Switch from OpenRouter to Crazyrouter?

Both OpenRouter and Crazyrouter use the same OpenAI-compatible API format. The difference is what you get on the other side:

| | OpenRouter | Crazyrouter |
|---|---|---|
| Models available | 300+ | 600+ |
| Pricing | Standard markup | 30-50% cheaper on most models |
| API format | OpenAI-compatible | OpenAI-compatible |
| Free tier | ✅ | ✅ |
| Video/Audio models | ❌ | ✅ (Sora, Whisper, TTS) |
| Image generation | Limited | ✅ (DALL-E, Flux, Midjourney) |

The key point: Crazyrouter is a **drop-in replacement**. Same API format, same model naming convention, different base URL.

## Quick Setup (One Command)

If you already have Hermes installed, run this:

```bash
curl -fsSL https://raw.githubusercontent.com/xujfcn/crazyrouter-hermes/main/setup-crazyrouter.sh | bash
```

The script will:
1. Ask for your Crazyrouter API key (get one free at [crazyrouter.com](https://crazyrouter.com?utm_source=devto&utm_medium=article&utm_campaign=dev_community))
2. Update `~/.hermes/.env` with your key
3. Update `~/.hermes/config.yaml` to point to Crazyrouter's endpoint

That's it. Run `hermes` and you're using Crazyrouter.

You can also pass the key as an environment variable:

```bash
CRAZYROUTER_API_KEY=sk-your-key curl -fsSL https://raw.githubusercontent.com/xujfcn/crazyrouter-hermes/main/setup-crazyrouter.sh | bash
```

## Manual Setup (Step by Step)

### Step 1: Get a Crazyrouter API Key

Sign up at [crazyrouter.com](https://crazyrouter.com?utm_source=devto&utm_medium=article&utm_campaign=dev_community) — it's free, no credit card required. Copy your API key from the dashboard.

### Step 2: Edit the Config File

Open `~/.hermes/config.yaml` and update the model section:

```yaml
model:
  provider: "openrouter"
  base_url: "https://crazyrouter.com/v1"
  default: "anthropic/claude-opus-4.6"
```

The `provider: "openrouter"` setting tells Hermes to use the OpenRouter-compatible code path. The `base_url` override redirects all requests to Crazyrouter instead.

### Step 3: Set the API Key

Edit `~/.hermes/.env`:

```bash
OPENROUTER_API_KEY=sk-your-crazyrouter-api-key
```

Hermes reads `OPENROUTER_API_KEY` when the provider is set to `openrouter`. Since Crazyrouter uses the same API format, your Crazyrouter key works here directly.

### Step 4: Verify

```bash
hermes chat "What model are you using?"
```

## How It Works Under the Hood

Hermes Agent's architecture makes this swap trivial. Here's why:

1. Hermes uses the OpenAI Python SDK internally
2. When `provider: "openrouter"` is set, it reads `OPENROUTER_API_KEY` and uses the configured `base_url`
3. Crazyrouter exposes an OpenAI-compatible API at `https://crazyrouter.com/v1`
4. All endpoints work: `/chat/completions`, `/audio/transcriptions`, `/images/generations`, etc.

No monkey-patching. No forks. Just a config change.

## Recommended Models

Once connected to Crazyrouter, you can switch models with `hermes model`:

| Model | Best for | Input cost |
|-------|----------|-----------|
| `anthropic/claude-opus-4.6` | Complex reasoning, coding | ~$15/M tokens |
| `anthropic/claude-sonnet-4-5` | Balanced speed and quality | ~$3/M tokens |
| `openai/gpt-5.2` | General purpose | ~$12/M tokens |
| `google/gemini-3-pro` | Multimodal, long context (2M) | ~$7/M tokens |
| `deepseek/deepseek-r2` | Budget reasoning | ~$0.5/M tokens |
| `google/gemini-3-flash` | Fast tasks, cheap | ~$0.1/M tokens |

## Advanced: Smart Model Routing

Hermes supports smart routing — using a cheap model for simple turns and your main model for complex ones:

```yaml
smart_model_routing:
  enabled: true
  max_simple_chars: 160
  max_simple_words: 28
  cheap_model:
    provider: openrouter
    model: google/gemini-3-flash
```

Simple responses go to Gemini Flash (~$0.10/M tokens), while complex reasoning stays on Claude Opus. This can cut your costs significantly without sacrificing quality.

## Advanced: Compression Model

Hermes compresses long conversations to stay within context limits. Route the compression model through Crazyrouter too:

```yaml
compression:
  enabled: true
  threshold: 0.50
  summary_model: "google/gemini-3-flash"
```

## Switching Back

If you ever want to switch back to OpenRouter:

```yaml
model:
  provider: "openrouter"
  base_url: "https://openrouter.ai/api/v1"
```

## Repository

The setup script and full documentation are on GitHub:

**[github.com/xujfcn/crazyrouter-hermes](https://github.com/xujfcn/crazyrouter-hermes)**

## Conclusion

Hermes Agent is one of the best open-source AI agents available today. By pointing it at Crazyrouter instead of the default provider, you get access to more models at lower cost — without changing any source code.

The setup takes about 30 seconds. One config change, one API key, and you're running Hermes with 600+ models behind it.

Get your free API key at [crazyrouter.com](https://crazyrouter.com?utm_source=devto&utm_medium=article&utm_campaign=dev_community) and try it out.

---

*Crazyrouter is an AI API gateway that provides unified access to 600+ AI models through a single API key. OpenAI-compatible format, no vendor lock-in.*
