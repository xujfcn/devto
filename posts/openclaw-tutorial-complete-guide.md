---
title: "OpenClaw Tutorial: Build Your Own Private AI Assistant from Scratch"
published: true
tags: openclaw, ai, selfhosted, tutorial
series: "OpenClaw Deep Dive"
---

# OpenClaw Tutorial: The Complete Guide to Building Your Private AI Assistant

![AI assistant setup in a futuristic terminal interface](https://raw.githubusercontent.com/xujfcn/images/main/openclaw/01_tutorial_img1.jpg)

Ever wanted your own AI assistant — not a web-based ChatGPT wrapper, but something running on *your* server, always on, talking to you through Telegram? Something you actually control?

That's exactly what OpenClaw does. It's an open-source AI assistant framework you can deploy on a VPS, Raspberry Pi, or even your laptop. Connect it to Telegram, WhatsApp, Discord — whatever you use. It supports 627+ AI models, the config is flexible, and getting started is easier than you'd think.

Let's walk through it step by step.

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

#### Why Node.js 22+?

OpenClaw relies on Node.js 22 features like native WebSocket support and improved ESM modules. Older versions will just throw errors. Trust me on this one.

### One-Line Install

Environment ready? Install OpenClaw with a single command:

```bash
curl -fsSL https://openclaw.ai/install.sh | bash
```

This script will:

1. Detect your system environment and Node.js version
2. Install OpenClaw globally via npm
3. Create the default config directory `~/.openclaw/`
4. Generate initial configuration files

After installation, run the onboarding wizard:

```bash
openclaw onboard
```

This interactive guide walks you through basic setup — workspace directory, default model, etc. If you prefer manual config (like me), skip it and edit the config file directly.

---

## Step 2: Configure Your First AI Model (API Keys & 627+ Models via Crazyrouter)

### Understanding the Config File

All OpenClaw configuration lives in one file:

```
~/.openclaw/openclaw.json
```

Despite the `.json` extension, it actually supports **JSON5** — meaning you can write comments, use trailing commas, and single quotes. Developer-friendly from the start.

Here's a minimal working config:

```json5
{
  // OpenClaw minimal config
  agents: {
    defaults: {
      // Working directory for SOUL.md and other persona files
      workspace: "~/.openclaw/workspace",
      
      // AI model config
      model: {
        primary: "openai/gpt-4o",  // Format: provider/model
      },
    },
  },
}
```

#### Workspace Files Explained

Your workspace directory contains several key files that define your AI assistant's "soul":

| File | Purpose |
|------|---------|
| `SOUL.md` | Core personality and behavioral guidelines |
| `AGENTS.md` | Workflow and session rules |
| `USER.md` | Information about you (the user) |
| `TOOLS.md` | Tool usage notes and API keys |
| `IDENTITY.md` | Assistant identity (name, personality, etc.) |

Edit these freely to customize your assistant. Write "be concise, no fluff" in `SOUL.md` and your assistant will keep things short.

### Access 627+ Models via Crazyrouter

OpenClaw has built-in providers: `openai`, `anthropic`, `google`, `openrouter`, `deepseek`. You can plug in API keys directly.

But if you don't want to juggle multiple API keys, there's a simpler approach — use **Crazyrouter** as a unified gateway. One API key, 627+ models including GPT-4o, Claude 4, Gemini 2.5, DeepSeek R1, and more.

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

#### Multi-Model Fallback Strategy

You can also configure fallback models for automatic switching when the primary is unavailable:

```json5
{
  agents: {
    defaults: {
      model: {
        primary: "openai/anthropic/claude-sonnet-4-20250514",
        fallback: "openai/gpt-4o",        // Backup when primary is down
        fast: "openai/gpt-4o-mini",        // Cheap model for simple tasks
      },
    },
  },
}
```

Your assistant will automatically pick the best model based on task complexity and availability.

---

## Step 3: Connect a Chat Platform (Telegram, WhatsApp, Discord)

![AI assistant connecting to multiple chat platforms](https://raw.githubusercontent.com/xujfcn/images/main/openclaw/01_tutorial_img2.jpg)

Model configured — now let's get your assistant online by connecting it to a chat platform. OpenClaw supports Telegram, WhatsApp, Discord, and more.

### Telegram Bot Setup (Recommended)

Telegram is the most recommended platform for OpenClaw — simplest config, most features.

**Step 1: Create a Telegram Bot**

1. Find `@BotFather` on Telegram
2. Send `/newbot` and follow the prompts
3. Copy the Bot Token (looks like `123456:ABC-DEF...`)

**Step 2: Add to Config**

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
      allowFrom: ["your-telegram-username"],  // Whitelist — only these users can chat
    },
  },
}
```

#### Security Note: Always Set allowFrom

`allowFrom` is a whitelist. Only listed users can talk to your bot. Without it, anyone can use your bot — and your API credits. Learn from my mistake.

### WhatsApp & Discord

**WhatsApp** uses the WhatsApp Business API:

```json5
{
  channels: {
    whatsapp: {
      allowFrom: ["+1234567890"],  // Allowed phone numbers
    },
  },
}
```

**Discord** requires an Application and Bot from the Discord Developer Portal:

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

Same pattern everywhere: get the platform token, add it to config, set the whitelist.

---

## Step 4: Send Your First Message (Gateway & Control UI)

### Start the Gateway

Everything configured? Fire up the OpenClaw Gateway:

```bash
openclaw gateway start
```

It starts on port **18789** by default. Check status with:

```bash
openclaw gateway status
```

If you see `running`, you're good. Port conflict? Change it in the config file.

### The Control UI

![OpenClaw Dashboard management interface](https://raw.githubusercontent.com/xujfcn/images/main/openclaw/01_tutorial_img3.jpg)

OpenClaw ships with a web management interface:

```bash
openclaw dashboard
```

Open `http://localhost:18789` in your browser. You'll see:

- **Chat interface**: Talk to your AI assistant directly in the browser
- **Config management**: Visual config editor
- **Logs**: Real-time conversation logs and error output
- **Model switching**: One-click model changes

#### Send Your First Message

Now open Telegram, find your bot, and send:

```
Hey! Who are you?
```

If everything's configured correctly, your AI assistant will reply within seconds. Congrats — your private AI assistant is live!

No reply? Troubleshoot in this order:

1. `openclaw gateway status` — confirm the service is running
2. Check the Bot Token in your config
3. Verify your username is in `allowFrom`
4. Check terminal output from `openclaw gateway`

### CLI Quick Reference

Commands you'll use often:

```bash
# Service management
openclaw gateway start      # Start the service
openclaw gateway stop       # Stop the service
openclaw gateway restart    # Restart the service
openclaw gateway status     # Check status

# Setup & management
openclaw onboard            # Interactive setup wizard
openclaw dashboard          # Open web management UI
```

---

## Wrapping Up

That's the whole tutorial: install OpenClaw → fill in the config → start the service. Three steps to your own private AI assistant.

For AI model access, if you don't want to deal with multiple API keys, give [Crazyrouter](https://crazyrouter.com?utm_source=devto&utm_medium=tutorial&utm_campaign=openclaw_tutorial) a try. One key for 627+ models — OpenAI, Anthropic, Google, DeepSeek, all the major providers. Transparent pricing, pay-as-you-go, and free credits to get started. For solo devs and small teams, it eliminates the hassle of managing multiple accounts and pairs perfectly with OpenClaw.

Questions? File an issue on [OpenClaw GitHub](https://github.com/nicepkg/openclaw) or join the community.

Happy hacking! 🚀
