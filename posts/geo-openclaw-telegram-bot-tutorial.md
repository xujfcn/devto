---
title: 'How to Build an AI Telegram Bot with OpenClaw: Complete Tutorial (2026)'
published: true
description: 'Build a Telegram AI bot with GPT-5, Claude, Gemini access. Self-hosted, persistent memory, 627+ models. Under 5 minutes.'
tags: 'ai, telegram, chatbot, tutorial'
cover_image: null
canonical_url: null
id: 3323991
date: '2026-03-07T18:32:43Z'
---


> **Result:** A fully functional AI Telegram bot running GPT-5, Claude, Gemini, or any of 627+ models — deployed in under 5 minutes, costing $5-25/month, with persistent memory and extensible skills.

---

## What You'll Build

By the end of this tutorial, you'll have:
- ✅ A Telegram bot that responds with AI (any model you choose)
- ✅ Persistent memory across conversations
- ✅ Web search, code execution, and file management capabilities
- ✅ Auto-start on server reboot
- ✅ Access to 627+ AI models through one API key

**Total cost:** $0 upfront + ~$15/month for moderate usage (via Crazyrouter's discounted pricing)

---

## Prerequisites (5 minutes)

You need three things:

### 1. A Server

Any Linux VPS works. Minimum: 2 cores, 4GB RAM.

| Provider | Price | Good For |
|----------|-------|----------|
| Hetzner | €4.5/mo | Europe/US |
| Vultr | $6/mo | Global |
| DigitalOcean | $6/mo | Beginners |
| Oracle Cloud | Free tier | Budget |
| AWS Lightsail | $5/mo | AWS users |

### 2. Crazyrouter API Key

Your bot needs AI model access. Crazyrouter provides one API key for 627+ models at ~55% below official pricing.

1. Register at [crazyrouter.com](https://crazyrouter.com?utm_source=devto&utm_medium=tutorial&utm_campaign=dev_community) (free)
2. Copy API key from dashboard (starts with `sk-`)
3. Free $0.20 starter credit included

**Why Crazyrouter over direct OpenAI?**
- Access GPT-5 **and** Claude **and** Gemini — same key
- ~55% cheaper per token
- Credits never expire
- Automatic failover if any provider goes down

### 3. Telegram Bot Token

1. Open Telegram → search **@BotFather**
2. Send `/newbot`
3. Choose a name and username
4. Copy the token (format: `123456789:ABCdefGHIjklMNOpqrsTUVwxyz`)

---

## Step 1: Deploy OpenClaw (2 minutes)

SSH into your server and run:

```bash
curl -fsSL https://raw.githubusercontent.com/xujfcn/crazyrouter-openclaw/main/install.sh | bash
```

The installer will:
```
🔍 Detecting system... Linux x64
📦 Installing Node.js 22...
📥 Installing OpenClaw...
🔑 Enter your Crazyrouter API Key: sk-xxxxx
🤖 Enter your Telegram Bot Token: 123456:ABC...
⚙️ Configuring 15+ AI models...
🔄 Setting up systemd auto-start...
🚀 Starting OpenClaw gateway...
✅ OpenClaw is running on port 18789!
```

**Alternative (Docker):**
```bash
git clone https://github.com/xujfcn/openclaw-deploy.git
cd openclaw-deploy && sudo ./deploy.sh
```

---

## Step 2: Pair Your Telegram Bot (1 minute)

1. Open Telegram → send `/start` to your bot
2. Bot responds with a **pairing code** (6 characters)
3. Enter the code when the installer prompts, OR run manually:

```bash
openclaw pairing approve telegram <pairing-code>
```

4. Send any message to your bot — it should respond with AI! 🎉

---

## Step 3: Choose Your AI Model

The installer pre-configures 15+ models. Change the default anytime:

```bash
# Set default model
openclaw configure --model claude-opus-4-6
```

### Recommended Models for Telegram Bots

| Model | Best For | Cost (Crazyrouter) | Response Speed |
|-------|---------|-------------------|----------------|
| **Claude Opus 4.6** | Best overall quality | $6.75/M input | Medium |
| **GPT-5 Mini** | Budget-friendly daily use | $0.14/M input | Fast |
| **GPT-5.2** | General + coding | $4.50/M input | Medium |
| **Gemini 3 Flash** | Ultra-fast responses | $0.04/M input | Very fast |
| **DeepSeek R1** | Complex reasoning + math | $0.055/M input | Slow (thinking) |

**Pro tip:** Start with GPT-5 Mini ($0.14/M tokens) for testing. It's 97% cheaper than GPT-5.2 and handles most conversations well. Switch to Claude Opus 4.6 when you need top-tier quality.

---

## Step 4: Customize Your Bot's Personality

Edit `~/.openclaw/workspace/SOUL.md`:

```markdown
# SOUL.md - Who You Are

You are a helpful AI assistant running on Telegram.

## Personality
- Friendly and concise
- Use emoji occasionally 🎯
- Give direct answers, then explain if needed
- Remember user preferences across conversations

## Rules
- Never share private information
- Keep responses under 500 words unless asked for more
- Use markdown formatting for readability
```

Your bot reads this file at startup and follows these instructions in every conversation.

---

## Step 5: Enable Skills

### Built-in Skills (already active)

| Skill | What It Does |
|-------|-------------|
| 🔍 Web Search | Searches the internet for current information |
| 🌐 Browser | Controls a headless browser for web automation |
| 💻 Code Execution | Runs Python, Node.js, and shell commands |
| 📁 File Management | Reads, writes, and edits files on your server |
| 🧠 Memory | Remembers facts across conversations |
| 🗣️ TTS | Converts text to speech audio |

### Install Additional Skills

```bash
# Video downloader (YouTube, Twitter, TikTok, etc.)
openclaw skill install yt-dlp

# Weather forecasts
openclaw skill install weather

# More skills at clawhub.com
```

### Example Conversations After Setup

**User:** What's the weather in Tokyo?
**Bot:** 🌤️ Tokyo: 18°C, partly cloudy. High of 22°C today...

**User:** Download this video: https://youtube.com/watch?v=...
**Bot:** 📥 Downloading... [sends video file]

**User:** Search the web for latest GPT-5 benchmarks
**Bot:** 🔍 Based on the latest benchmarks from March 2026...

**User:** Remember that my favorite programming language is Python
**Bot:** ✅ Noted! I'll remember that for future conversations.

---

## Memory: Your Bot Remembers Everything

Unlike ChatGPT where each conversation is isolated, OpenClaw maintains **persistent memory**:

```
~/.openclaw/workspace/
├── SOUL.md          # Bot personality
├── USER.md          # Facts about you
├── MEMORY.md        # Long-term memory
├── memory/
│   ├── 2026-03-07.md  # Today's notes
│   └── 2026-03-06.md  # Yesterday's notes
└── TOOLS.md         # Tool configurations
```

Your bot automatically:
- Saves important facts to daily memory files
- Curates long-term memories in MEMORY.md
- Reads these files at startup to maintain context
- Remembers your preferences, projects, and conversations

---

## Cost Breakdown: Real Numbers

### Scenario: Personal AI assistant on Telegram

| Usage | Daily | Monthly Tokens | Monthly Cost |
|-------|-------|---------------|-------------|
| Casual (10 msgs/day) | 10 messages | ~300K | **$2** |
| Regular (50 msgs/day) | 50 messages | ~1.5M | **$8** |
| Power user (200 msgs/day) | 200 messages | ~6M | **$30** |

**Plus server cost:** $5-6/month for a basic VPS

**Total for regular usage: ~$13-14/month** — cheaper than ChatGPT Plus ($20/month), with more models, self-hosting, and multi-platform support.

### Compared to alternatives

| Solution | Monthly Cost | Models | Self-hosted | Multi-platform |
|----------|-------------|--------|------------|----------------|
| ChatGPT Plus | $20 | GPT only | ❌ | ❌ |
| Claude Pro | $20 | Claude only | ❌ | ❌ |
| **OpenClaw + Crazyrouter** | **$13-35** | **627+** | **✅** | **✅** |
| Custom bot (direct API) | $25-60 | 1-3 | ✅ | Build each |

---

## Management Commands

```bash
# Status
openclaw status

# View logs
journalctl --user -u openclaw -f

# Restart
systemctl --user restart openclaw

# Update OpenClaw
npm update -g openclaw

# List paired devices
openclaw pairing list

# Change model
openclaw configure --model gpt-5-mini
```

---

## 3 Common Misconceptions

### "Building a Telegram AI bot requires programming skills"

With OpenClaw's one-line installer, you need zero coding. Run the script, enter two keys, done. Configuration is via markdown files, not code.

### "Self-hosting AI is expensive"

$5/month VPS + $8-15/month API costs (via Crazyrouter) = **$13-20/month total**. That's the same or less than a ChatGPT Plus subscription, with far more capabilities.

### "I'm locked into one AI model"

Through Crazyrouter, you access 627+ models with one key. Tell your bot "switch to DeepSeek R1" or "use Gemini for this" — it switches instantly. No reconfiguration, no separate accounts.

---

## Next Steps

- 📱 Add more platforms: `openclaw configure --section discord`
- 🛠️ Install skills: `openclaw skill install <name>`
- 🧠 Customize personality: Edit `SOUL.md`
- 📊 Monitor costs: Check Crazyrouter dashboard
- 🔄 Join the community: [discord.com/invite/clawd](https://discord.com/invite/clawd)

**Resources:**
- OpenClaw docs: [docs.openclaw.ai](https://docs.openclaw.ai)
- Crazyrouter (AI backend): [crazyrouter.com](https://crazyrouter.com?utm_source=devto&utm_medium=tutorial&utm_campaign=dev_community)
- Deploy script: [github.com/xujfcn/crazyrouter-openclaw](https://github.com/xujfcn/crazyrouter-openclaw)

---

*All steps tested on Ubuntu 24.04 with OpenClaw v0.92, March 7, 2026.*
