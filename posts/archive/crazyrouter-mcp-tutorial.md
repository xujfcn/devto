---
title: How to Access 627+ AI Models from Claude Desktop with One MCP Server
published: true
tags:
  - mcp
  - ai
  - api
  - tutorial
cover_image: null
canonical_url: 'https://crazyrouter.com/blog?utm_source=devto&utm_medium=article&utm_campaign=dev_community'
id: 3326443
date: '2026-03-08T10:19:19Z'
---

If you've been juggling multiple AI API keys — one for OpenAI, another for Anthropic, one for Google — you know the pain. What if your Claude Desktop (or Cursor, or VS Code) could access **627+ models** from all major providers through a single MCP server?

That's exactly what I built.

## The Problem

Most developers using MCP have separate servers for each AI provider:
- OpenAI MCP → GPT-5, DALL-E 3
- Anthropic MCP → Claude models
- Google MCP → Gemini models

Each requires its own API key, separate billing, different rate limits. It adds up fast.

## The Solution: One MCP Server, All Models

[Crazyrouter MCP Server](https://github.com/xujfcn/crazyrouter-mcp?utm_source=devto&utm_medium=article&utm_campaign=dev_community) connects to [Crazyrouter](https://crazyrouter.com?utm_source=devto&utm_medium=article&utm_campaign=dev_community), a unified AI API gateway. One API key gives you:

- 💬 **Chat**: GPT-5, Claude Opus 4.6, Gemini 3 Pro, DeepSeek R1, Llama 4, Qwen3, Grok 4
- 🎨 **Images**: DALL-E 3, Midjourney, Flux Pro, Stable Diffusion, Imagen 4.0
- 🎬 **Video**: Sora 2, Kling V2, Veo 3, Seedance, Pika
- 🎵 **Music**: Suno, TTS/STT engines

## Quick Setup (3 minutes)

### Step 1: Get API Key

Sign up at [crazyrouter.com](https://crazyrouter.com?utm_source=devto&utm_medium=article&utm_campaign=dev_community) and grab your API key.

### Step 2: Install

```bash
git clone https://github.com/xujfcn/crazyrouter-mcp.git
cd crazyrouter-mcp
npm install && npm run build
```

### Step 3: Configure Claude Desktop

Edit your Claude Desktop config:

**macOS**: `~/Library/Application Support/Claude/claude_desktop_config.json`
**Windows**: `%APPDATA%\Claude\claude_desktop_config.json`

```json
{
  "mcpServers": {
    "crazyrouter": {
      "command": "node",
      "args": ["/path/to/crazyrouter-mcp/dist/index.js"],
      "env": {
        "CRAZYROUTER_API_KEY": "sk-your-key-here"
      }
    }
  }
}
```

### Step 4: Use It

Restart Claude Desktop. Now you can say:

> "Use the chat tool to ask GPT-5 to explain quantum computing"

> "Generate an image of a sunset over mountains with DALL-E 3"

> "List all available video generation models"

## Available MCP Tools

| Tool | What it does |
|------|-------------|
| `chat` | Send messages to any of 627+ AI models |
| `list_models` | Browse models by category (chat/image/video/audio/music) |
| `generate_image` | Create images with DALL-E 3, Midjourney, Flux, etc. |
| `generate_video` | Generate videos with Sora 2, Kling V2, Veo 3, etc. |

## Real Test Results

I tested each tool against the live API:

```
✅ chat (gpt-4o-mini) → "Hey there, how's it going?" — 22 tokens, instant
✅ list_models → 539 models confirmed via /v1/models API
✅ generate_image (DALL-E 3) → Generated image URL returned in ~8s
✅ generate_video (kling-v2-1) → API accepted, model queue processing
```

## Also Works With

The same server works with **Cursor**, **VS Code Copilot**, **Windsurf**, and **OpenClaw**. Just add the same config to their respective MCP settings files.

## Why Not Just Use OpenAI's MCP Server?

| Feature | OpenAI MCP | Crazyrouter MCP |
|---------|-----------|----------------|
| Models | OpenAI only | 627+ from all providers |
| Image gen | DALL-E only | DALL-E + Midjourney + Flux + SD + Imagen |
| Video gen | ❌ | Sora + Kling + Veo + more |
| Music gen | ❌ | Suno, TTS |
| API keys needed | 1 per provider | 1 total |
| Pricing | Official rates | Below official rates |

---

**GitHub**: [xujfcn/crazyrouter-mcp](https://github.com/xujfcn/crazyrouter-mcp?utm_source=devto&utm_medium=article&utm_campaign=dev_community)

**Crazyrouter**: [crazyrouter.com](https://crazyrouter.com?utm_source=devto&utm_medium=article&utm_campaign=dev_community)
