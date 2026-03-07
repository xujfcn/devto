---
title: "OpenClaw Tutorial: Build Your Own Private AI Assistant from Scratch"
published: true
tags: ["openclaw", "ai", "tutorial", "chatbot"]
series: "OpenClaw Deep Dive"
description: "A complete step-by-step guide to installing OpenClaw, configuring AI models, connecting chat platforms like Telegram, and sending your first message."
---

Ever wanted your own AI assistant that lives on your server, responds on Telegram, and isn't just another ChatGPT wrapper? Not the web-based kind — a real, always-on, private assistant running on your own VPS, Raspberry Pi, or even your laptop.

That's exactly what OpenClaw does. It's an open-source AI assistant framework that you can deploy anywhere and chat with through Telegram, WhatsApp, Discord, and more. It supports 627+ AI models, has flexible configuration, and is surprisingly easy to set up.

Let's jump right in.

---

## Step 1: Installation & Environment Setup (Node.js 22+, One-Line Install)

### System Requirements

Before we start, make sure your environment checks these boxes:

- **OS**: Linux (Ubuntu 22.04+ recommended), macOS, or WSL2
- **Node.js**: 22.0 or higher (hard requirement)
- **RAM**: 512MB minimum, 1GB+ recommended
- **Network**: Internet access (for AI model API calls)

If you don't have Node.js 22+ yet, grab it with nvm:

```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.3/install.sh | bash
source ~/.bashrc
nvm install 22
node -v  # Should output v22.x.x
```

> **Why Node.js 22+?** OpenClaw uses native WebSocket support and improved ESM modules from Node.js 22. Older versions will just crash. Don't ask how I know.

### One-Line Install

Once your environment is ready, installing OpenClaw takes exactly one command:

```bash
curl -fsSL https://openclaw.ai/install.sh | bash
```

This script automatically:

1. Detects your system environment and Node.js version
2. Installs OpenClaw globally via npm
3. Creates the default config directory `~/.openclaw/`
4. Generates initial configuration files

After installation, run the onboarding wizard:

```bash
openclaw onboard
```

This interactive guide walks you through basic configuration — setting your workspace directory and choosing a default model. If you prefer manual config (like me), skip it and edit the config file directly.

---

## Step 2: Configure Your First AI Model (API Keys, 627+ Models via Crazyrouter)

### Understanding the Config File

All of OpenClaw's configuration lives in one file:

```
~/.openclaw/openclaw.json
```

Despite the `.json` extension, it actually supports **JSON5** — meaning you can use comments, trailing commas, and single quotes. Developer paradise.

Here's the minimum viable config:

```json5
{
  // Minimal OpenClaw config
  agents: {
    defaults: {
      // Workspace directory for personality files
      workspace: "~/.openclaw/workspace",
      
      // AI model configuration
      model: {
        primary: "openai/gpt-4o",  // Format: provider/model
      },
    },
  },
}
```

### Workspace Files Explained

Your workspace directory contains key files that define your AI assistant's "soul":

- **`SOUL.md`** — Core personality and behavior guidelines
- **`AGENTS.md`** — Workflow rules and session conventions
- **`USER.md`** — Information about you (the user)
- **`TOOLS.md`** — Tool usage notes and API keys
- **`IDENTITY.md`** — Identity info (name, personality traits, etc.)

Edit these freely to customize your assistant. Write "be concise, no fluff" in `SOUL.md` and your assistant becomes straight to the point.

### One API Key for 627+ Models via Crazyrouter

OpenClaw has built-in support for multiple AI providers: `openai`, `anthropic`, `google`, `openrouter`, `deepseek`. You can plug in each provider's API key directly.

But if you don't want to juggle a dozen API keys, there's a simpler approach — use **Crazyrouter** as a unified gateway. One API key gets you access to 627+ models including GPT-4o, Claude 4, Gemini 2.5, DeepSeek R1, and more:

```json5
{
  agents: {
    defaults: {
      workspace: "~/.openclaw/workspace",
      model: {
        // Use Claude via Crazyrouter
        primary: "openai/anthropic/claude-sonnet-4-20250514",
      },
      providers: {
        openai: {
          apiKey: "sk-your-crazyrouter-key",
          baseURL: "https://crazyrouter.com/v1",
        },
      },
    },
  },
}
```

### Multi-Model Fallback Strategy

You can also configure fallback models for automatic switching when the primary model is unavailable:

```json5
{
  agents: {
    defaults: {
      model: {
        primary: "openai/anthropic/claude-sonnet-4-20250514",
        fallback: "openai/gpt-4o",        // Fallback when primary is down
        fast: "openai/gpt-4o-mini",        // Cheap model for simple tasks
      },
    },
  },
}
```

Your assistant automatically picks the best model based on task complexity and availability.

---

## Step 3: Connect a Chat Platform (Telegram, WhatsApp, Discord)

Model configured — now let's get your AI assistant online. OpenClaw supports Telegram, WhatsApp, Discord, and other major platforms.

### Telegram Bot Setup (Recommended)

Telegram is OpenClaw's recommended platform — simplest setup, most complete feature set.

**Create a Telegram Bot:**

1. Find `@BotFather` in Telegram
2. Send `/newbot`, follow the prompts
3. Grab your Bot Token (looks like `123456:ABC-DEF...`)

**Add it to your config:**

```json5
{
  agents: {
    defaults: {
      workspace: "~/.openclaw/workspace",
      model: {
        primary: "openai/anthropic/claude-sonnet-4-20250514",
      },
      providers: {
        openai: {
          apiKey: "sk-your-crazyrouter-key",
          baseURL: "https://crazyrouter.com/v1",
        },
      },
    },
  },
  channels: {
    telegram: {
      token: "your-bot-token",
      allowFrom: ["your-telegram-username"],  // Whitelist
    },
  },
}
```

> **Security Warning:** Always set `allowFrom`. Without it, anyone can use your bot (and your API credits). Learned that the hard way.

### WhatsApp & Discord

**WhatsApp** uses the WhatsApp Business API:

```json5
{
  channels: {
    whatsapp: {
      allowFrom: ["+1234567890"],
    },
  },
}
```

**Discord** requires a bot from the Discord Developer Portal:

```json5
{
  channels: {
    discord: {
      token: "your-discord-bot-token",
      allowFrom: ["your-discord-user-id"],
    },
  },
}
```

The pattern is the same for every platform: get the token, add it to the config, set up your whitelist.

---

## Step 4: Send Your First Message

### Start the Gateway

With everything configured, fire up the OpenClaw Gateway:

```bash
openclaw gateway start
```

The gateway starts on port **18789** by default. Check the status:

```bash
openclaw gateway status
```

If you see `running`, you're good to go.

### The Control UI

OpenClaw ships with a web management interface:

```bash
openclaw dashboard
```

Open `http://localhost:18789` in your browser for a clean dashboard with:

- **Chat interface** — Talk to your AI directly in the browser
- **Config management** — Visual config editor
- **Log viewer** — Real-time conversation and error logs
- **Model switcher** — One-click model switching

### Send That First Message

Open Telegram, find your bot, and send:

```
Hello! Who are you?
```

If everything's configured correctly, your AI assistant replies within seconds. Congratulations — your private AI assistant is live! 🎉

**Troubleshooting checklist if there's no reply:**

1. `openclaw gateway status` — is the service running?
2. Is the Bot Token correct in the config?
3. Is your username in `allowFrom`?
4. Check the terminal output of `openclaw gateway`

### CLI Quick Reference

Commands you'll use constantly:

```bash
# Service management
openclaw gateway start      # Start
openclaw gateway stop       # Stop
openclaw gateway restart    # Restart
openclaw gateway status     # Check status

# Setup & management
openclaw onboard            # Interactive setup wizard
openclaw dashboard          # Open web UI
```

---

## Wrapping Up

That's the complete tutorial: install → configure → connect → chat. Three real steps: install OpenClaw, fill in your config, start the service.

For AI model access, if you don't want to manage multiple API keys, check out [Crazyrouter](https://crazyrouter.com?utm_source=devto&utm_medium=tutorial&utm_campaign=openclaw_tutorial). One key for 627+ models — OpenAI, Anthropic, Google, DeepSeek, all the majors. Transparent pricing, pay-as-you-go, free tier available. For solo developers and small teams, it eliminates the hassle of managing multiple accounts — the perfect companion for OpenClaw.

Questions? Open an issue on the [OpenClaw GitHub](https://github.com/nicepkg/openclaw) or join the community discussion.

Happy hacking! 🚀
