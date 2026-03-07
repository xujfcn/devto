---
title: "OpenClaw Setup Guide 2026: Deploy Your AI Assistant with Crazyrouter in 5 Minutes"
published: true
description: "Deploy a self-hosted AI assistant on Telegram, Discord, or WhatsApp with 627+ models. One command, 5 minutes, $13/month."
tags: ai, telegram, opensource, tutorial
canonical_url: 
---


> **Key Takeaway:** OpenClaw is an open-source AI assistant framework that connects to Telegram, Discord, WhatsApp, and 20+ platforms. The fastest setup uses Crazyrouter as the AI backend — one API key gives your bot access to 627+ models (GPT-5, Claude, Gemini, DeepSeek) at 55% below official pricing. Total setup time: under 5 minutes.

---

## What Is OpenClaw?

OpenClaw is an **open-source personal AI assistant framework** that turns any messaging platform into an AI-powered interface. Think of it as "ChatGPT, but self-hosted, on your own terms."

**Core capabilities:**
- **20+ messaging platforms** — Telegram, Discord, WhatsApp, Slack, Signal, LINE, MS Teams, IRC, and more
- **Any AI model** — GPT-5, Claude Opus 4.6, Gemini 3, DeepSeek R1, Llama 4, etc.
- **Skills system** — Extensible plugins for web search, code execution, file management, browser control
- **Persistent memory** — Your assistant remembers conversations across sessions
- **Multi-model routing** — Switch models per conversation or per task
- **Self-hosted** — Your data stays on your server

### Why Crazyrouter + OpenClaw?

OpenClaw needs an AI model API to function. You have three options:

| Backend | Models | Cost | Setup Complexity |
|---------|--------|------|-----------------|
| Direct OpenAI | OpenAI only | $10/M tokens (GPT-5.2) | Moderate |
| Direct Anthropic | Claude only | $15/M tokens (Opus 4.6) | Moderate |
| **Crazyrouter** | **627+ models** | **$4.50/M tokens** (55% off) | **Easiest** |

Crazyrouter is the recommended backend because:
1. **One API key** → access all models (no switching providers)
2. **55% cheaper** → same models, lower bill
3. **OpenAI-compatible** → zero extra configuration in OpenClaw
4. **Credits never expire** → no wasted balance
5. **7 global edge nodes** → low latency worldwide

---

## Quick Start: 3 Methods

### Method 1: One-Line Install (Fastest)

```bash
curl -fsSL https://raw.githubusercontent.com/xujfcn/crazyrouter-openclaw/main/install.sh | bash
```

The script automatically:
1. Detects your system (Linux/macOS, x64/arm64)
2. Installs Node.js 22+ if needed
3. Installs OpenClaw
4. Asks for your Crazyrouter API key
5. Pre-configures 15+ popular AI models
6. Sets up systemd/launchd auto-start
7. Guides you through Telegram bot pairing

**Prerequisites:**
- Crazyrouter API key → Register free at [crazyrouter.com](https://crazyrouter.com?utm_source=devto&utm_medium=tutorial&utm_campaign=dev_community)
- Telegram Bot token → Create via @BotFather on Telegram

### Method 2: Docker Deploy

```bash
git clone https://github.com/xujfcn/openclaw-deploy.git
cd openclaw-deploy
chmod +x deploy.sh
sudo ./deploy.sh
```

Choose Docker when prompted. The script builds and runs the container.

### Method 3: Manual Install

```bash
# Install Node.js 22+
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.3/install.sh | bash
source ~/.bashrc
nvm install 22

# Install OpenClaw
npm install -g openclaw

# Configure
openclaw configure
```

During `openclaw configure`, enter:
- **AI Provider**: OpenAI-compatible
- **Base URL**: `https://crazyrouter.com/v1`
- **API Key**: Your Crazyrouter key (starts with `sk-`)
- **Default Model**: `claude-opus-4-6` (or any model you prefer)

---

## Connecting to Messaging Platforms

### Telegram (Most Popular)

```bash
openclaw configure --section telegram
```

Enter your Bot token from @BotFather. Then:

1. Open Telegram → send `/start` to your bot
2. Bot replies with a **pairing code**
3. Enter the code in terminal, or run: `openclaw pairing approve telegram <code>`
4. Done — start chatting!

### Discord

```bash
openclaw configure --section discord
```

Enter your Discord bot token. Add the bot to your server via OAuth2 URL.

### WhatsApp

```bash
openclaw configure --section whatsapp
```

Scan the QR code with WhatsApp to pair.

### All Supported Platforms

| Platform | Setup Command | Auth Method |
|----------|--------------|-------------|
| Telegram | `--section telegram` | Bot token |
| Discord | `--section discord` | Bot token |
| WhatsApp | `--section whatsapp` | QR code |
| Slack | `--section slack` | OAuth |
| Signal | `--section signal` | Phone number |
| LINE | `--section line` | Channel token |
| MS Teams | `--section msteams` | App registration |
| IRC | `--section irc` | Server/channel |
| WeChat Work | `--section wechatwork` | Corp ID |
| DingTalk | `--section dingtalk` | App key |
| Feishu | `--section feishu` | App ID |
| Matrix | `--section matrix` | Homeserver URL |
| Google Chat | `--section googlechat` | Service account |
| iMessage | `--section imessage` | macOS only |

---

## Choosing Your AI Model

With Crazyrouter as your backend, you can switch models anytime:

### Best Models by Use Case

| Use Case | Recommended Model | Cost via Crazyrouter | Why |
|----------|------------------|---------------------|-----|
| General assistant | Claude Opus 4.6 | $6.75/M input | Best overall quality |
| Budget assistant | GPT-5 Mini | $0.14/M input | 97% cheaper, still great |
| Coding help | GPT-5.3 Codex | $3.38/M input | Code-optimized |
| Research | Gemini 3 Pro | $1.58/M input | 2M context window |
| Math/Logic | DeepSeek R1 | $0.055/M input | Best reasoning, cheapest |
| Fast responses | Gemini 3 Flash | $0.04/M input | Ultra-low latency |
| Creative writing | Claude Opus 4.6 | $6.75/M input | Most nuanced language |
| Chinese content | DeepSeek V3.2 | $0.012/M input | Native Chinese model |

### How to Switch Models

In your OpenClaw config (`openclaw.json`):

```json
{
  "providers": {
    "crazyrouter": {
      "type": "openai",
      "baseUrl": "https://crazyrouter.com/v1",
      "apiKey": "sk-your-key",
      "models": {
        "claude-opus-4-6": {},
        "gpt-5.2": {},
        "gemini-3-pro": {},
        "deepseek-r1": {},
        "gpt-5-mini": {}
      }
    }
  },
  "defaultModel": "claude-opus-4-6"
}
```

Switch default model at any time:
```bash
openclaw configure --model gpt-5.2
```

Or switch per-conversation by telling your bot: "Switch to GPT-5 Mini" or "Use DeepSeek for this conversation."

---

## OpenClaw Skills (Plugins)

Skills extend your assistant's capabilities:

| Skill | What It Does | Built-in? |
|-------|-------------|-----------|
| Web Search | Search the internet via Brave API | ✅ |
| Browser | Control a headless browser | ✅ |
| Code Execution | Run Python/Node/Shell code | ✅ |
| File Management | Read, write, edit files | ✅ |
| Video Download | Download from YouTube, Twitter, etc. | Install via ClawHub |
| Weather | Get forecasts via wttr.in | Install via ClawHub |
| TTS | Text-to-speech conversion | ✅ |
| Memory | Persistent cross-session memory | ✅ |
| Canvas | Present web UIs to users | ✅ |

### Installing Skills from ClawHub

```bash
openclaw skill install <skill-name>
```

Browse available skills at [clawhub.com](https://clawhub.com).

### Creating Custom Skills

Create a `SKILL.md` file in `~/.openclaw/skills/my-skill/`:

```markdown
# My Custom Skill

Trigger: When user asks about [topic]

## Steps
1. Search knowledge base
2. Format response
3. Return answer
```

---

## Memory and Context

OpenClaw has a unique memory system:

- **MEMORY.md** — Long-term memory (curated facts, preferences)
- **memory/YYYY-MM-DD.md** — Daily logs (raw conversation notes)
- **SOUL.md** — Your bot's personality and behavior rules
- **USER.md** — Information about who it's helping

Your assistant reads these files at startup, maintaining context across sessions. Unlike ChatGPT where conversations are isolated, OpenClaw **remembers everything** you tell it.

---

## Cost Estimation

How much does running OpenClaw cost?

| Usage Level | Daily Messages | Monthly Token Usage | Monthly Cost (Crazyrouter) |
|------------|---------------|--------------------|-----------------------------|
| Light | 10-20 | ~500K tokens | **$2-5** |
| Moderate | 50-100 | ~3M tokens | **$15-25** |
| Heavy | 200+ | ~10M tokens | **$45-70** |
| Power user | 500+ | ~30M tokens | **$135-200** |

**Compared to ChatGPT Plus ($20/month):** For moderate usage, OpenClaw + Crazyrouter costs about the same — but you get access to **627 models** instead of just GPT, full self-hosting control, and multi-platform integration.

**Compared to direct API:** Using Crazyrouter saves ~55% versus calling OpenAI/Anthropic directly. A heavy user saves approximately **$55-85/month**.

---

## System Requirements

| Component | Minimum | Recommended |
|-----------|---------|-------------|
| CPU | 1 core | 2+ cores |
| RAM | 2 GB | 4 GB |
| Storage | 1 GB | 10 GB (for skills, memory) |
| OS | Linux, macOS | Ubuntu 22+ or macOS 13+ |
| Node.js | 18+ | 22+ |
| Network | Any internet | Low-latency to AI providers |

OpenClaw runs on everything from a **$5/month VPS** to a Raspberry Pi to a gaming PC.

---

## OpenClaw vs Alternatives

| Feature | OpenClaw | Rasa | Botpress | Custom Build |
|---------|----------|------|----------|-------------|
| Open source | ✅ | ✅ | Partial | N/A |
| Self-hosted | ✅ | ✅ | ✅ | ✅ |
| LLM-native | ✅ | ❌ (rule-based) | Partial | Varies |
| 20+ platforms | ✅ | ❌ (custom) | Limited | Build each |
| Persistent memory | ✅ | ❌ | Limited | Build it |
| Skills/plugins | ✅ | ✅ | ✅ | Build each |
| Setup time | 5 min | Hours | 30 min | Days-weeks |
| AI model choice | 627+ via gateway | Limited | Limited | Any |
| Monthly cost | $5-70 (API only) | Free-$$$ | Free-$500+ | API costs + dev time |

**Bottom line:** OpenClaw is the fastest way to deploy a self-hosted AI assistant with multi-platform support. Combined with Crazyrouter for model access, total setup is under 5 minutes and ongoing costs are minimal.

---

## Troubleshooting

### Bot doesn't respond

```bash
# Check if OpenClaw is running
openclaw status

# Check logs
journalctl --user -u openclaw -f

# Verify pairing
openclaw pairing list
```

### API errors (401/429)

- **401**: Check your Crazyrouter API key in the config
- **429**: Rate limited — wait 30s or switch to a different model
- **500**: Upstream provider issue — Crazyrouter's failover should handle this automatically

### Model not found

Ensure model name matches exactly. Check available models:
```bash
curl https://crazyrouter.com/v1/models \
  -H "Authorization: Bearer sk-your-key" | python3 -m json.tool | head -50
```

---

## 3 Common Misconceptions

### "Self-hosting AI is complicated"

With the one-line installer, OpenClaw + Crazyrouter deploys in under 5 minutes. No Docker knowledge needed, no infrastructure to manage (beyond a basic VPS).

### "I need separate API accounts for each model"

Through Crazyrouter, one API key accesses 627+ models. Switch between GPT-5, Claude, Gemini, and DeepSeek with a single config change — no separate accounts or billing.

### "Self-hosted means expensive"

A $5/month VPS + $15-25/month in API costs (via Crazyrouter's discounted pricing) gives you a more capable assistant than a $20/month ChatGPT Plus subscription — with full control, multi-platform access, and persistent memory.

---

## Getting Started Now

1. **Get your API key** → [crazyrouter.com](https://crazyrouter.com?utm_source=devto&utm_medium=tutorial&utm_campaign=dev_community) (free, $0.20 starter credit)
2. **Create Telegram bot** → @BotFather on Telegram
3. **Run the installer:**
   ```bash
   curl -fsSL https://raw.githubusercontent.com/xujfcn/crazyrouter-openclaw/main/install.sh | bash
   ```
4. **Start chatting** → Send `/start` to your bot

**Resources:**
- OpenClaw docs: [docs.openclaw.ai](https://docs.openclaw.ai)
- OpenClaw GitHub: [github.com/openclaw/openclaw](https://github.com/openclaw/openclaw)
- Crazyrouter API docs: [crazyrouter.apifox.cn](https://crazyrouter.apifox.cn)
- One-click deploy: [github.com/xujfcn/crazyrouter-openclaw](https://github.com/xujfcn/crazyrouter-openclaw)
- Skills marketplace: [clawhub.com](https://clawhub.com)

---

*Tested and verified on Ubuntu 24.04, macOS 15, March 7, 2026. Model count from live Crazyrouter API.*
