---
title: "OpenClaw Skill Development Guide: Teach Your AI Assistant Any New Ability"
published: true
tags: ["openclaw", "ai", "skills", "extensibility"]
series: "OpenClaw Deep Dive"
description: "Learn how OpenClaw Skills work — the three-tier loading system, SKILL.md anatomy, and 5 practical examples from weather queries to automated reporting."
---

Ever wonder why some AI assistants can only chat, while others check weather, download videos, manage emails, and auto-generate reports? The secret is **OpenClaw Skills** — an extension mechanism that lets AI assistants learn new abilities like unlocking skill points in an RPG.

This guide takes you from understanding the architecture to building your first Skill, with 5 real-world examples showing what's possible.

---

## How OpenClaw Skills Work

The core philosophy: **Skills are instruction sets, not code libraries.** They tell the AI "what to do when encountering a certain type of problem" — they don't provide executable programs directly.

### Three-Tier Loading System

OpenClaw manages Skills through a priority-based loading hierarchy:

| Tier | Location | Priority | Notes |
|------|----------|----------|-------|
| **Bundled** | Ships with OpenClaw | Lowest | Built-in: `weather`, `tmux`, `healthcheck` |
| **Managed/Local** | `~/.openclaw/skills/` | Medium | User-installed via ClawHub or manually |
| **Workspace** | `<workspace>/skills/` | Highest | Project-level, follows the workspace |

**Conflict resolution:** When multiple tiers have a same-named Skill, the higher priority version wins. Drop a custom `weather` Skill in your Workspace, and it overrides the built-in version.

This means you can override or extend any Skill without touching system files.

### How Skills Get Loaded

When responding to a user, the Agent automatically:

1. Scans the `available_skills` list for all registered Skills
2. Matches the most relevant Skill based on its `description` field
3. Reads the matched `SKILL.md` for detailed instructions
4. Follows those instructions using available Tools

### Skills vs. Tools: What's the Difference?

| Dimension | Skill | Tool |
|-----------|-------|------|
| **Nature** | Instruction set — tells AI "how" | Capability — gives AI "the means" |
| **Form** | Markdown docs + optional scripts | System-level functions (exec, read, write) |
| **Loading** | On-demand, loaded when matched | Always available |
| **Custom** | Users create freely | Platform-provided |

Think of it this way: Tools are the hammer and screwdriver in your hand. Skills are the manual that says "screw first, then nail." The AI needs Tools for basic ability and Skills to know how to combine them.

### Config Gating

Skills support gating via configuration and environment variables. Certain Skills only activate when specific conditions are met — like an API key being set or a feature flag enabled. Great for enterprise access control.

---

## Building Your First Skill

### Directory Structure

A standard Skill looks like this:

```
my-skill/
├── SKILL.md          # Entry file (required)
├── scripts/          # Script files (optional)
│   ├── run.sh
│   └── helper.py
├── templates/        # Templates (optional)
│   └── output.md
└── assets/           # Static resources (optional)
    └── config.json
```

**Core rule:** `SKILL.md` is the only entry point. The Agent reads only this file to understand how to use your Skill. Everything else is referenced via relative paths from SKILL.md.

### Writing an Effective SKILL.md

```markdown
# My Awesome Skill

## Description
One-line explanation of what this Skill does and when to trigger it.

## When to Use
- When the user mentions XXX
- When the user requests YYY
- NOT for ZZZ scenarios

## Prerequisites
- Environment variable: MY_API_KEY
- Dependency: some-tool

## Instructions

### Step 1: Get Input
Extract key parameters from the user's message...

### Step 2: Execute
Run the script: `./scripts/run.sh <params>`

### Step 3: Return Results
Format output and return to user...

## Error Handling
- API timeout: retry once
- Missing params: ask user to provide

## Examples
User: "Check the weather in Tokyo"
→ Execute weather skill, param location=Tokyo
```

**Best practices:**

1. **Precise description** — The Agent uses it for matching. Vague = missed or false triggers
2. **Specific instructions** — Write it like a manual for a smart colleague who lacks context
3. **Relative paths** — Reference scripts/assets relative to SKILL.md
4. **Error handling required** — Tell the Agent how to degrade gracefully

### Scripts and Resources

Scripts typically execute via the `exec` Tool:

```bash
#!/bin/bash
# scripts/fetch_data.sh

API_KEY="${MY_API_KEY:?Error: MY_API_KEY not set}"
QUERY="$1"

if [ -z "$QUERY" ]; then
  echo "Usage: fetch_data.sh <query>"
  exit 1
fi

curl -s "https://api.example.com/data?q=${QUERY}" \
  -H "Authorization: Bearer ${API_KEY}" | jq '.'
```

Reference in SKILL.md:

```markdown
## Instructions
Run the data fetch script:
```bash
bash ./scripts/fetch_data.sh "<user query>"
```
```

---

## 5 Practical Skill Examples

### 1. Weather Query

The classic built-in Skill:

```markdown
# Weather Skill

## Description
Get current weather and forecasts. Triggers on mentions of weather, temperature, forecast.

## Instructions
1. Request wttr.in: `curl -s "wttr.in/{location}?format=j1"`
2. Parse JSON for current temp, feels-like, conditions
3. If forecast needed, show next 3 days
```

No API key needed — uses the free wttr.in service. Perfect starter project.

### 2. Video Downloader

Combining yt-dlp with OpenClaw:

```markdown
# Video Download Skill

## Description
Download videos from YouTube, Twitter, etc. Triggers on video URLs.

## Instructions
1. Identify the source platform
2. Run: `yt-dlp -f "best[filesize<50M]" -o "output.mp4" <URL>`
3. If file > 50MB, compress with ffmpeg
4. Send file to user
```

Platform-specific limits matter: Telegram caps at 50MB (2GB for Premium), Discord at 25MB (500MB for Nitro).

### 3. Database Query (with Security)

```markdown
# Database Query Skill

## Description
Query business database. Triggers on data query, report data mentions.

## Instructions
1. Convert natural language to SQL
2. Execute: `psql -h $DB_HOST -U $DB_USER -d $DB_NAME -c "<SQL>"`
3. Format results as table

## Security
- SELECT only — no DROP, DELETE, UPDATE, ALTER
- Sensitive fields auto-masked
```

### 4. Email Management

```markdown
# Email Ops Skill

## Description
Manage email sending and contacts. Triggers on email, newsletter, campaign mentions.

## Instructions
1. Determine operation type (send/template/contact management)
2. Must generate HTML preview and get confirmation before sending
3. Use Resend API for execution
```

### 5. Automated Reports

```markdown
# Daily Report Skill

## Description
Generate daily growth reports. Triggers on daily report, ops plan, traffic analysis.

## Instructions
1. Call GA4 API for traffic data
2. Call Search Console API for SEO data
3. Compile into Markdown report
4. Can pair with cron for daily auto-generation
```

**Pro tip:** Combine with Cron to auto-generate and push to Telegram every morning at 9 AM — fully automated operations.

---

## Publishing & Sharing Skills

### Testing Checklist

Before publishing:

1. **Trigger test** — Does the description match correctly?
2. **Happy path** — Does the main functionality work?
3. **Error handling** — Missing params, API timeout, permission issues?
4. **Multi-tier test** — Load in both Workspace and Managed directories, verify priority behavior

**Debug tips:**
- Add a `## Debug` section in SKILL.md for verbose logging in debug mode
- Use the built-in `skill-creator` Skill to generate and validate structure
- Check `available_skills` list to confirm registration

### Publishing to ClawHub

[ClawHub](https://clawhub.com) is OpenClaw's official skill marketplace:

1. **Prepare** — Ensure proper structure, complete SKILL.md
2. **README** — Add usage docs and examples
3. **Submit** — Via CLI tool or web interface
4. **Review** — Community review, then available for installation

**Naming convention:** lowercase with hyphens (e.g., `weather-pro`). Must include clear description and at least 2 usage examples.

---

## Summary

OpenClaw Skills solve AI capability extension elegantly — no code plugins, no model retraining. Just write clear instructions in Markdown, and your AI learns a new skill.

The three-tier loading system ensures flexibility. SKILL.md conventions lower the development barrier. ClawHub makes sharing as easy as installing an app.

If you're building AI-powered products or workflows, start with a simple OpenClaw Skill. You'll find that teaching AI new tricks is easier than you think.

---

> 🚀 Want to quickly integrate various AI model APIs? [Crazyrouter](https://crazyrouter.com?utm_source=devto&utm_medium=article&utm_campaign=openclaw_skill) provides a unified AI API gateway — one key for OpenAI, Claude, Gemini, and more. The ideal companion for OpenClaw developers.
