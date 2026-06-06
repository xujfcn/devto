---
title: "Stop AI Agents from Guessing API Routes: Crazyrouter llms.txt Endpoint Guide"
published: true
description: "How Crazyrouter llms.txt helps AI coding agents choose the correct endpoint for OpenAI, Responses, Claude, Gemini, images, videos, and audio."
tags: ai, api, agents, docs
canonical_url: https://docs.crazyrouter.com/llms-guide?utm_source=devto&utm_medium=article&utm_campaign=llms_txt_docs
---

# Stop AI Agents from Guessing API Routes: Crazyrouter `llms.txt` Endpoint Guide

When an AI agent writes API code, the most expensive bug is often not a typo. It is an endpoint mismatch.

A model might support Chat Completions, Responses, Claude Messages, Gemini native calls, image generation, video generation, or audio APIs. If the AI guesses the route from memory, the generated code may look reasonable while still being wrong.

Crazyrouter's `llms.txt` helps prevent that.

```text
https://docs.crazyrouter.com/llms.txt
```

The user-facing guide is here:

https://docs.crazyrouter.com/llms-guide?utm_source=devto&utm_medium=article&utm_campaign=llms_txt_docs

## The core idea

Before generating code, tell the AI to read the docs entry point and match the model's endpoint type to the correct documentation page.

Use this prompt:

```text
Read https://docs.crazyrouter.com/llms.txt first.
Use Crazyrouter's official docs to choose the correct endpoint.
Do not invent model names, prices, billing modes, or routes from memory.
Generate code only after matching the model to the correct endpoint type.
```

## Common endpoint mappings

Here are the important mappings AI tools should not guess:

| Use case | Crazyrouter endpoint |
|---|---|
| OpenAI Chat Completions | `POST /v1/chat/completions` |
| OpenAI Responses | `POST /v1/responses` |
| Anthropic Messages | `POST /v1/messages` |
| Gemini native | `POST /v1beta/models/{model}:generateContent` |
| Image generation | `POST /v1/images/generations` |
| Image editing | `POST /v1/images/edits` |
| OpenAI-style video | `POST /v1/video/generations` or `POST /v1/videos` |
| Unified video | `POST /v1/video/create` and `GET /v1/video/query` |
| Text-to-speech | `POST /v1/audio/speech` |
| Speech-to-text | `POST /v1/audio/transcriptions` |

## Base URL details

For OpenAI-compatible SDKs:

```text
https://cn.crazyrouter.com/v1
```

For raw HTTP Chat Completions:

```text
https://cn.crazyrouter.com/v1/chat/completions
```

For Anthropic-native clients, use the root domain expected by the client rather than manually appending `/v1` in the wrong place.

This is the kind of detail that `llms.txt` is designed to surface before the AI writes code.

## Pricing and model availability

Do not ask an AI model to rely on memory for model names or prices.

Use:

```text
https://crazyrouter.com/pricing
```

For machine-readable pricing data:

```text
https://crazyrouter.com/api/pricing?lang=en
```

For the exact models available to a specific API key:

```bash
curl https://cn.crazyrouter.com/v1/models \
  -H "Authorization: Bearer YOUR_API_KEY"
```

Use `YOUR_API_KEY` in shared prompts. Do not paste real keys into public chats or untrusted tools.

## Example: better agent instruction

Bad prompt:

```text
Write code to call a Crazyrouter model.
```

Better prompt:

```text
Read https://docs.crazyrouter.com/llms.txt first.
My task is to call [MODEL_NAME] from Node.js.
Check which endpoint type applies, then write runnable code.
Use YOUR_API_KEY as the placeholder.
If the model or endpoint is uncertain, tell me to verify it on https://crazyrouter.com/pricing or GET /v1/models.
```

## Why this matters for agent workflows

AI coding agents are increasingly asked to configure entire projects:

- Cursor project settings
- Cline providers
- Claude Code API setup
- Codex CLI Responses API setup
- OpenAI-compatible SDK examples
- video and image generation scripts
- multimodal demos

A small routing error can waste a lot of time. `llms.txt` gives the agent a map before it starts editing files.

Start with the full guide:

https://docs.crazyrouter.com/llms-guide?utm_source=devto&utm_medium=article&utm_campaign=llms_txt_docs
