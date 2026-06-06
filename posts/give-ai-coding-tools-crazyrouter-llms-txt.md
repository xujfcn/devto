---
title: "Give Your AI Coding Tools the Right Docs: How to Use Crazyrouter llms.txt"
published: true
description: "Use Crazyrouter's llms.txt entry point to help ChatGPT, Claude, Cursor, Cline, Aider, and Codex generate correct API code."
tags: ai, api, llm, tooling
canonical_url: https://docs.crazyrouter.com/llms-guide?utm_source=devto&utm_medium=article&utm_campaign=llms_txt_docs
---

# Give Your AI Coding Tools the Right Docs: How to Use Crazyrouter `llms.txt`

AI coding tools are great at writing integration code — until they guess the wrong endpoint, invent a model name, or mix OpenAI, Claude, Gemini, image, video, and audio routes together.

That is exactly why Crazyrouter now provides an AI-readable documentation entry point:

```text
https://docs.crazyrouter.com/llms.txt
```

If you use ChatGPT, Claude, Cursor, Cline, Aider, Codex CLI, or another coding agent, give it this URL first. It tells the AI where to find the right Crazyrouter docs before it writes code.

Full guide:

https://docs.crazyrouter.com/llms-guide?utm_source=devto&utm_medium=article&utm_campaign=llms_txt_docs

## Why `llms.txt` matters

Most AI API mistakes are not syntax mistakes. They are routing mistakes:

- using the wrong base URL
- adding `/v1` twice
- calling a chat endpoint for a video model
- mixing OpenAI Responses with Chat Completions
- using Anthropic-style clients with an OpenAI-style base URL
- guessing model prices or billing modes from old memory

`llms.txt` gives AI tools a compact map of the Crazyrouter docs, including:

- quick start
- authentication
- API endpoints
- model listing
- OpenAI-compatible APIs
- Claude Messages API
- Gemini native API
- image generation
- video generation
- text-to-speech and speech-to-text
- coding tool integrations such as Cursor, Cline, Claude Code, and Codex CLI

Instead of pasting a huge documentation site into your prompt, you can start with one stable entry point.

## Recommended prompt

Copy this into your AI coding tool:

```text
Please read https://docs.crazyrouter.com/llms.txt first, then answer my question based on the official Crazyrouter documentation.

My task is: [describe your task here]

Important:
- OpenAI-compatible SDKs should use base_url: https://cn.crazyrouter.com/v1
- If model names, pricing, or billing are involved, use https://crazyrouter.com/pricing as the source of truth
- If model availability is unclear, remind me to check the Pricing page or GET /v1/models
- Give me runnable code examples
```

Example task:

```text
My task is: use Python to call an OpenAI-compatible chat model through Crazyrouter.
```

Another example:

```text
My task is: configure Codex CLI to use Crazyrouter's OpenAI Responses endpoint.
```

## Base URL rule

For OpenAI-compatible SDKs, use:

```text
https://cn.crazyrouter.com/v1
```

For raw Chat Completions calls, the full endpoint is:

```text
https://cn.crazyrouter.com/v1/chat/completions
```

Do not add tracking parameters to API endpoints. UTM parameters belong on human-facing links, not API calls.

## Confirm models before shipping

Documentation examples are not a full live model catalog. Models, pricing, and endpoint support can change.

For public model names and pricing, check:

```text
https://crazyrouter.com/pricing
```

For token-level availability, call:

```bash
curl https://cn.crazyrouter.com/v1/models \
  -H "Authorization: Bearer YOUR_API_KEY"
```

Never paste a real API key into an untrusted AI tool or public chat. Use `YOUR_API_KEY` as a placeholder when asking for code.

## When to use `llms-full.txt`

Use `llms.txt` when the AI needs a compact index.

Use this when the AI can handle longer context and you want it to read the full documentation snapshot:

```text
https://docs.crazyrouter.com/llms-full.txt
```

## Markdown pages are also available

Many Crazyrouter docs pages can be read as Markdown by adding `.md` to the URL:

```text
https://docs.crazyrouter.com/quickstart.md
https://docs.crazyrouter.com/api-endpoint.md
https://docs.crazyrouter.com/video/sora.md
```

This is useful for AI tools that read Markdown better than rendered web pages.

## Quick checklist

Before asking an AI tool to generate Crazyrouter code, tell it to:

1. read `https://docs.crazyrouter.com/llms.txt`
2. choose the correct endpoint type
3. use `https://cn.crazyrouter.com/v1` for OpenAI-compatible SDKs
4. verify model names and pricing against the Pricing page
5. use `/v1/models` when checking what your API key can actually call
6. use `YOUR_API_KEY`, never your real key, in shared prompts

Start here:

https://docs.crazyrouter.com/llms-guide?utm_source=devto&utm_medium=article&utm_campaign=llms_txt_docs
