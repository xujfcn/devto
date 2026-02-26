---
title: "Deploy Your Own AI Assistant in One Command: Crazyrouter + OpenClaw Self-Hosting Guide"
published: true
description: "One command to deploy a private AI gateway supporting 300+ models with Telegram Bot integration"
tags: 'ai, chatgpt, selfhosted, tutorial'
cover_image: https://raw.githubusercontent.com/xujfcn/crazyrouter-openclaw/main/screenshots/install-part2.png
canonical_url: null
---

## Why Self-Host an AI Gateway?

If you've used ChatGPT, Claude, and Gemini, you know the pain: **separate accounts, separate API keys, separate billing for each platform**.

- Claude for coding (best code quality)
- GPT for daily chat (cheap)
- Gemini for long document analysis (huge context window)
- DeepSeek R1 for reasoning tasks (best value)

That's 4 API keys, 4 invoices, 4 different API formats.

**Crazyrouter + OpenClaw solves this:**

- **Crazyrouter**: One API key for 300+ models (GPT, Claude, Gemini, DeepSeek, Kimi, etc.). OpenAI-compatible, cheaper than official pricing.
- **OpenClaw**: Open-source AI gateway that turns AI models into your personal assistant on Telegram, Discord, Slack, and more.

Combined = **your own AI infrastructure**.

## What You Get

After deployment:

1. An AI gateway running on your server (port 18789)
2. A Telegram Bot for chatting with AI anytime
3. 15+ popular models pre-configured and ready to use

![Telegram Bot chat](https://raw.githubusercontent.com/xujfcn/crazyrouter-openclaw/main/screenshots/telegram-chat.jpg)
*Send /start in Telegram to pair and start chatting with your AI assistant*

## Prerequisites

| Item | How to Get |
|------|-----------|
| Crazyrouter API Key | Register at [crazyrouter.com](https://crazyrouter.com?utm_source=devto&utm_medium=article&utm_campaign=dev_community) |
| Telegram Bot Token (optional) | Create via @BotFather on Telegram |

Server requirements:
- Linux (Ubuntu/Debian/CentOS) or macOS
- 4GB+ RAM
- Internet access

## One-Command Install

```bash
curl -fsSL https://raw.githubusercontent.com/xujfcn/crazyrouter-openclaw/main/install.sh | bash
```

Or download first:

```bash
wget https://raw.githubusercontent.com/xujfcn/crazyrouter-openclaw/main/install.sh
bash install.sh
```

Non-interactive:

```bash
CRAZYROUTER_API_KEY=sk-yourkey curl -fsSL https://raw.githubusercontent.com/xujfcn/crazyrouter-openclaw/main/install.sh | bash
```

## Installation Process

The script runs 10 automated steps with progress bars:

![Installation part 1](https://raw.githubusercontent.com/xujfcn/crazyrouter-openclaw/main/screenshots/install-part1.png)
*Steps 1-7: System detection, Node.js & OpenClaw installation, config generation*

![Installation part 2](https://raw.githubusercontent.com/xujfcn/crazyrouter-openclaw/main/screenshots/install-part2.png)
*Steps 7-10: IM plugins, systemd service, gateway startup — "Installation complete!"*

Total time: ~2-5 minutes.

## Pre-configured Models

| Vendor | Model | Strength |
|--------|-------|----------|
| Anthropic | Claude Opus 4.6 | Best overall |
| Anthropic | Claude Sonnet 4.6 | Best value |
| OpenAI | GPT-5.2 | Latest flagship |
| OpenAI | GPT-5.3 Codex | Code specialist |
| Google | Gemini 3.1 Pro | Long context |
| Google | Gemini 3 Flash | Ultra-fast |
| DeepSeek | R1 | Strong reasoning |
| DeepSeek | V3.2 | Best for Chinese |
| Others | Kimi K2.5, GLM-5, Grok 4.1, MiniMax M2.1 | Various |

**One API key for everything.**

## Connecting Telegram Bot

**Step 1:** Search @BotFather on Telegram, send `/newbot`:

![BotFather](https://raw.githubusercontent.com/xujfcn/crazyrouter-openclaw/main/screenshots/botfather.jpg)

**Step 2:** Paste token into the install script:

![Telegram setup](https://raw.githubusercontent.com/xujfcn/crazyrouter-openclaw/main/screenshots/telegram-setup.png)

**Step 3:** Start chatting!

![Bot conversation](https://raw.githubusercontent.com/xujfcn/crazyrouter-openclaw/main/screenshots/telegram-chat.jpg)

## Docker Deployment (Optional)

```bash
git clone https://github.com/xujfcn/crazyrouter-openclaw.git
cd crazyrouter-openclaw
docker build -t openclaw-bot .
docker run -d --name openclaw-bot -p 18789:18789 -v ~/.openclaw:/root/.openclaw openclaw-bot
```

## Post-Install Management

```bash
# Linux (systemd)
systemctl --user status openclaw    # Status
journalctl --user -u openclaw -f   # Live logs
systemctl --user restart openclaw  # Restart
systemctl --user stop openclaw     # Stop
```

## Stability

Built-in `crash-guard.cjs` handles Node.js 22 + undici 7.x TLS crashes:
- TLS Session null pointer protection
- Transient undici error suppression
- Stale lock file cleanup
- Memory directory backup every 5 minutes

**24/7 stable operation, zero manual intervention.**

## FAQ

**Q: Crazyrouter vs OpenRouter?**
Lower prices, supports Chinese models, fully OpenAI-compatible. No overseas credit card needed.

**Q: Raspberry Pi?**
Yes! arm64 supported. Pi 4/5 works great.

**Q: Cost?**
Per-token billing, cheaper than official APIs. See [crazyrouter.com](https://crazyrouter.com?utm_source=devto&utm_medium=article&utm_campaign=dev_community).

## Try It

```bash
curl -fsSL https://raw.githubusercontent.com/xujfcn/crazyrouter-openclaw/main/install.sh | bash
```

2 minutes to your own AI. 🚀

---

- [Crazyrouter](https://crazyrouter.com?utm_source=devto&utm_medium=article&utm_campaign=dev_community)
- [OpenClaw on GitHub](https://github.com/openclaw/openclaw)
- [Deploy repo](https://github.com/xujfcn/crazyrouter-openclaw)
- [Telegram channel](https://t.me/crazyrouter)
