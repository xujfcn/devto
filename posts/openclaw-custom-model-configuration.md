---
title: 'OpenClaw Custom Model Configuration: Connect Any AI Model You Want'
published: true
tags:
  - openclaw
  - ai
  - llm
  - devops
series: OpenClaw Deep Dive
description: 'Complete guide to OpenClaw''s Provider system — configure OpenAI, Claude, Gemini, DeepSeek, Ollama local models, and API gateways with key rotation and failover.'
id: 3319854
date: '2026-03-07T02:48:09Z'
---

One of OpenClaw's biggest advantages as an open-source AI coding assistant: it doesn't lock you into any single model vendor. Through its flexible Provider system, you can freely connect to OpenAI, Anthropic, Google, DeepSeek, and other cloud models, run local models via Ollama or llama.cpp, or use an API gateway to access 627+ models with one key.

This is the complete guide to configuring custom models in OpenClaw.

---

## Understanding the Provider System

Every model in OpenClaw is referenced in `provider/model` format — like `openai/gpt-5.1-codex` or `anthropic/claude-opus-4-20250918`. The Provider determines the API endpoint, auth method, and request format.

### Built-in Providers

| Provider | Description | Typical Models |
|----------|-------------|----------------|
| `openai` | OpenAI official API | gpt-5.1, gpt-5.1-mini |
| `anthropic` | Anthropic official API | claude-opus-4, claude-sonnet-4 |
| `google` | Google AI Studio | gemini-2.5-pro, gemini-2.5-flash |
| `google-vertex` | Google Cloud Vertex AI | gemini-2.5-pro (enterprise) |
| `openai-codex` | OpenAI Codex | codex-mini-latest |
| `opencode` | OpenCode-compatible endpoints | community models |

Quick CLI check:

```bash
# List all configured models
openclaw models list

# Switch primary model
openclaw models set anthropic/claude-opus-4-20250918
```

### API Key Configuration & Rotation

OpenClaw supports multi-key rotation with this priority order:

```bash
# Priority high → low:
OPENCLAW_LIVE_OPENAI_KEY        # Highest (runtime injection)
OPENAI_API_KEYS                 # Multi-key (comma-separated)
OPENAI_API_KEY                  # Standard single key
OPENAI_API_KEY_1                # Wildcard match OPENAI_API_KEY_*
OPENAI_API_KEY_2
```

**Smart rotation:** Keys only rotate on `429 Too Many Requests` or `rate_limit` errors. Other errors (auth failure, model not found) throw immediately — no wasted rotation attempts. This ensures:

- High-concurrency pressure is automatically distributed
- Non-rate-limit errors surface fast
- Multiple teams sharing a key pool don't interfere

---

## Cloud Models: OpenAI, Claude, Gemini, DeepSeek

### OpenAI GPT-5 Series

```json5
{
  agents: {
    defaults: {
      model: {
        primary: "openai/gpt-5.1-codex",
        fallbacks: [
          "openai/gpt-5.1-mini",
          "openai/gpt-5.1"
        ]
      },
      // models also serves as allowlist
      models: [
        "openai/gpt-5.1-codex",
        "openai/gpt-5.1",
        "openai/gpt-5.1-mini"
      ]
    }
  }
}
```

### Anthropic Claude & Google Gemini

Set environment variables and specify in config:

```bash
# Anthropic Claude
export ANTHROPIC_API_KEY="sk-ant-xxxxx"

# Google AI Studio
export GOOGLE_API_KEY="AIzaSyxxxxx"

# Google Vertex AI (enterprise, needs service account)
export GOOGLE_VERTEX_PROJECT="my-project-id"
export GOOGLE_VERTEX_LOCATION="us-central1"
```

```json5
{
  agents: {
    defaults: {
      model: {
        primary: "anthropic/claude-opus-4-20250918",
        fallbacks: ["google/gemini-2.5-pro"]
      }
    }
  }
}
```

### DeepSeek V3

DeepSeek uses OpenAI-compatible format via a custom Provider:

```json5
{
  models: {
    providers: {
      deepseek: {
        baseUrl: "https://api.deepseek.com/v1",
        apiKey: "sk-xxxxx"
      }
    }
  }
}
```

Then use `deepseek/deepseek-chat` or `deepseek/deepseek-coder` as model identifiers.

---

## Local Models: Ollama & llama.cpp

For privacy-focused or offline scenarios, OpenClaw fully supports local models.

### Ollama Setup

Ollama is the simplest local option:

```bash
# Install
curl -fsSL https://ollama.com/install.sh | sh

# Pull models
ollama pull llama3.1:70b
ollama pull codellama:34b

# Start (default port 11434)
ollama serve
```

OpenClaw config:

```json5
{
  models: {
    providers: {
      ollama: {
        baseUrl: "http://localhost:11434/v1",
        apiKey: "ollama"  // Ollama doesn't need a real key
      }
    }
  },
  memorySearch: {
    provider: "ollama"
  }
}
```

**Performance tip:** 70B models need ~40GB VRAM (A100). 7B-13B models run smoothly on consumer GPUs (RTX 4090, 24GB).

### llama.cpp Direct Connection

For lower-level control:

```bash
./llama-server -m models/codellama-34b-instruct.Q5_K_M.gguf \
  --host 0.0.0.0 --port 8080 \
  -c 8192 -ngl 99
```

```json5
{
  models: {
    providers: {
      "llama-cpp": {
        baseUrl: "http://localhost:8080/v1",
        apiKey: "none"
      }
    }
  }
}
```

### Local vs. Cloud: Quick Comparison

| Dimension | Local | Cloud |
|-----------|-------|-------|
| Latency | Hardware-dependent, 50-200ms/token | Network + inference, 20-80ms/token |
| Privacy | Fully local, zero leak risk | Data passes through third-party servers |
| Cost | One-time hardware investment | Per-token billing, higher long-term |
| Quality | Limited by local compute (7B-70B) | Access to strongest models |
| Best for | Sensitive code, offline, high-frequency | Complex reasoning, highest quality |

**Recommended:** hybrid strategy. Local for daily coding (low cost), cloud for complex architecture decisions (best quality).

---

## API Gateway: Unified Management

When you need multiple Providers, multiple keys, and automatic failover, an API gateway is the optimal solution.

### Crazyrouter: 627+ Models with One Key

[Crazyrouter](https://crazyrouter.com?utm_source=devto&utm_medium=article&utm_campaign=openclaw_models) is an API gateway built for AI developers. One key accesses OpenAI, Anthropic, Google, DeepSeek, Mistral, and 627+ more:

```json5
{
  models: {
    providers: {
      crazyrouter: {
        baseUrl: "https://crazyrouter.com/v1",
        apiKey: "sk-your-crazyrouter-key"
      }
    }
  },
  agents: {
    defaults: {
      model: {
        primary: "crazyrouter/claude-opus-4-20250918",
        fallbacks: [
          "crazyrouter/gpt-5.1-codex",
          "crazyrouter/gemini-2.5-pro"
        ]
      }
    }
  }
}
```

Or even simpler — override built-in Provider endpoints:

```bash
export OPENAI_API_KEY="sk-your-crazyrouter-key"
export OPENAI_BASE_URL="https://crazyrouter.com/v1"

export ANTHROPIC_API_KEY="sk-your-crazyrouter-key"
export ANTHROPIC_BASE_URL="https://crazyrouter.com/v1"
```

### Failover & Load Balancing

Combine `fallbacks` for automatic failover:

```json5
{
  agents: {
    defaults: {
      model: {
        primary: "crazyrouter/claude-opus-4-20250918",
        fallbacks: [
          "crazyrouter/gpt-5.1",
          "crazyrouter/gemini-2.5-pro",
          "crazyrouter/deepseek-chat"
        ]
      }
    }
  }
}
```

When the primary returns 429 or goes down, OpenClaw automatically tries the next model. Combined with key rotation, you get near 100% availability.

**Three-layer fault tolerance architecture:**

1. Multiple keys per model auto-rotate (handles per-key rate limits)
2. Multiple models auto-fallback (handles model-level failures)
3. Gateway-level smart routing (handles provider-level outages)

### Cost Optimization

API gateways also optimize costs:

- Unified billing — no separate top-ups per Provider
- Pay-as-you-go, no minimum spend
- ~45% cost reduction vs. direct Provider connections
- Built-in usage monitoring and budget alerts

For teams: shared key pool (no distributing individual Provider keys), per-project/member usage tracking, and daily/monthly budget caps.

---

## Choosing Your Setup

| Scenario | Recommended |
|----------|-------------|
| Solo dev, single Provider | Direct env vars |
| Multi-model switching | Fallbacks + multiple Providers |
| Privacy/offline | Ollama local deployment |
| Team/enterprise | API gateway for unified management |

No matter which approach, OpenClaw's flexible architecture has you covered. For maximum models with minimum config, try [Crazyrouter](https://crazyrouter.com?utm_source=devto&utm_medium=article&utm_campaign=openclaw_models) — one key, one endpoint, 627+ models, ready to go.

```bash
# 30-second setup
export OPENAI_BASE_URL="https://crazyrouter.com/v1"
export OPENAI_API_KEY="sk-your-crazyrouter-key"
openclaw models set crazyrouter/claude-opus-4-20250918
```

> 👉 [Sign up for Crazyrouter — free tier available](https://crazyrouter.com?utm_source=devto&utm_medium=article&utm_campaign=openclaw_models)
