---
title: 'Customize OpenClaw: Build Your Own AI Assistant Platform from Source to Deployment'
published: true
tags:
  - openclaw
  - ai
  - customization
  - docker
series: OpenClaw Deep Dive
description: 'From config files and personality customization to custom tools, plugins, and Docker deployment — a complete guide to making OpenClaw truly yours.'
id: 3319776
date: '2026-03-07T02:33:35Z'
---

OpenClaw is an open-source AI assistant runtime framework supporting multi-model, multi-channel, multi-plugin architectures. But its real power isn't the default configuration — it's the extreme customizability. This article walks you through the entire customization process: config files, personality design, capability extension, and deployment.

Whether you're building a dedicated customer service bot, a personality-rich personal assistant, or an enterprise Agent integrated with internal toolchains, OpenClaw has you covered.

---

## Before You Start: Understanding the File Structure

OpenClaw's configuration has two layers: global config and Workspace files. Understanding both is the foundation for all customization work.

### The Config File

The global config lives at `~/.openclaw/openclaw.json`, uses JSON5 format (comments and trailing commas allowed), and undergoes strict schema validation.

```json5
{
  // Default model
  "defaultModel": "anthropic/claude-sonnet-4-20250514",

  // Gateway port
  "port": 18789,

  // Multi-Agent config
  "agents": {
    "list": [
      {
        "name": "main",
        "workspace": "~/.openclaw/workspace",
        "agentDir": "~/.openclaw/agents/main",
      },
      {
        "name": "customer-service",
        "workspace": "~/.openclaw/workspace-cs",
        "agentDir": "~/.openclaw/agents/cs",
      },
    ],
  },

  // Channel plugins
  "channels": {
    "telegram": {
      "token": "YOUR_BOT_TOKEN",
    },
  },
}
```

Each Agent gets independent workspace, agentDir, and sessions directories. You can run completely different AI assistants on the same OpenClaw instance.

> **Note:** JSON5 is forgiving, but schema validation is strict — extra fields will error out.

### Workspace Files

The workspace directory contains the files that define your AI's "soul":

- **`SOUL.md`** — Personality, tone, values
- **`IDENTITY.md`** — Identity info (name, gender, role)
- **`AGENTS.md`** — Operational instructions, behavior rules
- **`USER.md`** — User profile to help the AI understand who it's serving
- **`TOOLS.md`** — Tool notes, API keys, usage tips

These files auto-load at every session start. Most customization is just editing these Markdown files.

---

## Personality Customization: SOUL.md & IDENTITY.md

The most immediate way to customize OpenClaw is editing personality files. Same underlying model, completely different behavior.

### Crafting Your AI's Personality

Here's a SOUL.md for a technical customer service scenario:

```markdown
# SOUL.md - Technical Support Assistant

## Core Principles

**Professional but not cold.** Users facing tech issues are often anxious.
Balance accuracy with empathy.

**Solve first, explain second.** Give the solution, then briefly explain
why the issue occurred.

**Acknowledge uncertainty.** If you're not sure, say so. Don't fabricate.

## Tone

- Use formal address
- Avoid excessive exclamation marks
- Keep tech terms in English with brief explanations
- Replies under 200 words unless user requests detail

## Boundaries

- Only answer product-related technical questions
- Billing/refund issues → direct to human support
- No competitor comparisons
```

A good SOUL.md has four parts: core principles (what kind of AI), tone rules (how to speak), behavioral boundaries (what not to do), and edge case handling (what to do in gray areas). The more specific, the more consistent.

### Multi-Personality Setup

Configure multiple Agents in `agents.list`, each pointing to a different workspace:

- **main** — Personal assistant, casual tone, full permissions
- **customer-service** — Support bot, formal tone, restricted permissions
- **content-creator** — Writing assistant, strong at SEO and content

Each Agent's IDENTITY.md:

```markdown
# IDENTITY.md

- **Name:** Helper
- **Role:** Technical Support
- **Gender:** Neutral
- **Language:** English, technical terms preserved
- **Emoji:** 🛠️
```

When switching Agents, OpenClaw auto-loads the corresponding workspace config. Seamless personality switching.

---

## Extending Capabilities: Custom Tools & Plugins

OpenClaw's plugin architecture has four core slots: Channel, Memory, Tool, and Provider.

### Building Custom Skills

Skills are the tool extension mechanism, with three-tier loading priority:

1. **bundled** — Built-in, ships with OpenClaw
2. **managed** — User-level, in `~/.openclaw/skills/`
3. **workspace** — Project-level, in `workspace/skills/`

Higher priority overrides lower. A custom Skill's basic structure:

```
~/.openclaw/skills/my-tool/
├── SKILL.md          # Entry file (required)
├── scripts/
│   └── run.sh        # Execution script
└── templates/
    └── prompt.md     # Prompt template
```

Best practices:
- Each Skill does one thing — single responsibility
- Write clear trigger keywords in SKILL.md's `description`
- Scripts should handle errors and return meaningful messages

### Integrating Third-Party APIs

Via TOOLS.md and custom Skills, integrate any external API:

- **Search APIs**: Brave Search, Google Custom Search
- **Social Media APIs**: Twitter, Mastodon, Bluesky
- **Cloud APIs**: Cloudflare, AWS
- **Internal systems**: CRM, ticketing, monitoring

The key: document auth info and usage notes in TOOLS.md, encapsulate call logic in Skills.

---

## Deployment Options

### Docker (Recommended)

Docker is the best deployment method — isolated environment, easy migration:

```dockerfile
FROM node:22-slim

WORKDIR /app

# Install OpenClaw
RUN npm install -g openclaw

# Copy config
COPY openclaw.json /root/.openclaw/openclaw.json
COPY workspace/ /root/.openclaw/workspace/

EXPOSE 18789

CMD ["openclaw", "gateway", "start", "--foreground"]
```

Build and run:

```bash
docker build -t my-openclaw .
docker run -d \
  --name openclaw \
  -p 18789:18789 \
  -v openclaw-data:/root/.openclaw \
  my-openclaw
```

Tips:
- Mount `~/.openclaw` as a volume — don't lose data on container rebuild
- Use `docker-compose` for multi-Agent instances
- Set `restart: unless-stopped` for auto-recovery

### Cloud Server (VPS)

For remote access, deploy to a VPS and use SSH tunnels:

```bash
# Local SSH tunnel
ssh -L 18789:localhost:18789 user@your-server.com

# Or use autossh for persistence
autossh -M 0 -f -N -L 18789:localhost:18789 user@your-server.com
```

Access your remote OpenClaw at `localhost:18789` without exposing the port publicly.

### Security Hardening

OpenClaw provides multi-layer security:

- **Device pairing** — First-connect verification
- **Token auth** — All API requests need valid tokens
- **Sandbox isolation** — Commands run in sandboxed environments

**Security checklist:**
1. Change the default port
2. Enable firewall, allow only necessary inbound connections
3. Rotate tokens regularly
4. Use environment variables for sensitive credentials (not TOOLS.md in production)
5. Enable SSH key auth, disable password login

---

## Wrapping Up

Customizing OpenClaw follows four dimensions: **config → personality → tools → deployment**. From JSON5 configuration to SOUL.md personality design, from custom Skills to Docker containerization — each step has a clear path.

OpenClaw's philosophy is "convention over configuration" — most customization is just editing Markdown files, not source code. This dramatically lowers the barrier, making it accessible even to non-developers.

---

> 🚀 Need to connect multiple AI models? [Crazyrouter](https://crazyrouter.com?utm_source=devto&utm_medium=article&utm_campaign=openclaw_custom) provides a unified API gateway — one endpoint for all major models, smart routing, cost optimization, and 99.9% availability. One line in your OpenClaw config and you're connected.
