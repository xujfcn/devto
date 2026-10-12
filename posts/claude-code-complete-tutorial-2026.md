---
title: 'Claude Code Complete Tutorial: From Installation to Production (2026)'
published: true
description: 'Step-by-step guide to Claude Code — Anthropic''s terminal-based AI coding assistant. Installation, configuration with API gateway (check model-specific pricing), core features, CI/CD integration, and advanced tips.'
tags: 'ai, programming, claudecode, tutorial'
canonical_url: 'https://crazyrouter.com/blog/claude-code-complete-tutorial-2026'
id: 3487880
date: '2026-04-11T16:35:40Z'
---

> Reviewed 2026-10-12: use the [current catalogue and prices](https://crazyrouter.com/en/pricing). Previous universal-discount, fixed-model-count and unverified free-credit claims are retired. Prices depend on the model, billing unit, account group and request time.

## What is Claude Code?

Claude Code is Anthropic's official AI coding assistant that runs directly in your terminal. Unlike Cursor or GitHub Copilot, it understands your **entire codebase** and can edit multiple files simultaneously.

**Key capabilities:**
- 🔍 Understands full project context (not just the open file)
- ✏️ Edits multiple files in a single command
- 🖥️ Executes shell commands (tests, builds, deploys)
- 🗣️ Natural language interaction — describe what you want, AI delivers

**Best for:** Backend development, scripting, automation, CI/CD integration, code review.

---

## Installation

### Requirements
- Node.js 18.0+
- macOS, Linux, or Windows 10+
- Terminal with ANSI color support

### Install

```bash
# npm (all platforms)
npm install -g @anthropic-ai/claude-code

# Verify
claude --version
```

---

## Configuration

### Option 1: Official Anthropic API

```bash
export ANTHROPIC_API_KEY="sk-ant-api03-xxxxx"
```


### Option 2: API Gateway (Recommended — Current pricing

Why use an API gateway?
- **compatible models in the current catalogue** with one key (Claude + GPT + Gemini + DeepSeek)
- **Better international latency**
- **No credit card** required (Alipay/WeChat supported)

```bash
export ANTHROPIC_API_KEY="sk-xxxxxx"  # Your gateway key
export ANTHROPIC_BASE_URL="https://crazyrouter.com"
```


[Get your API key →](https://crazyrouter.com?utm_source=devto&utm_medium=article&utm_campaign=claude_code_tutorial)

---

## Core Usage

```bash
# Start in your project
cd /path/to/project
claude
```

### Essential Commands

| Command | Description |
|---------|-------------|
| `/add <path>` | Add files/dirs to context |
| `/clear` | Clear conversation history |
| `/model <name>` | Switch model |
| `/cost` | Show session cost |
| `/exit` | Quit |

---

## Real-World Examples

### Create a REST API

```
> Create a Flask REST API with:
> 1. GET /users - list all users
> 2. POST /users - create user with validation
> 3. SQLite database
> 4. Error handling and logging
```

Claude Code creates `app.py`, `models.py`, `requirements.txt`, `init_db.py` — all tested and runnable.

### Refactor Entire Codebase

```
> Convert all sync functions in src/ to async/await
```

Scans all Python files → identifies sync functions → converts to async → updates all call sites → adds asyncio imports.

### Security Audit

```
> Review src/auth.py for security vulnerabilities
```

**Output:**
```
🔴 Security Issues:
  - Line 45: Passwords stored unencrypted
  - Line 78: SQL injection risk (string concatenation)

🟡 Performance:
  - Line 120: N+1 query in loop
  - Line 156: Missing database index

✅ Recommendations:
  1. Use bcrypt for password hashing
  2. Switch to parameterized queries
  3. Add composite index on (user_id, created_at)
```

### Generate Tests

```
> Generate unit tests for all functions in src/api.py
```

Creates `tests/test_api.py` with happy path, edge cases, error handling, and mocked dependencies.

---

## Advanced: CI/CD Integration

```yaml
# .github/workflows/ai-review.yml
name: AI Code Review
on: [pull_request]
jobs:
  review:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
        with:
          node-version: '18'
      - run: npm install -g @anthropic-ai/claude-code
      - name: AI Review
        env:
          ANTHROPIC_API_KEY: ${{ secrets.ANTHROPIC_API_KEY }}
          ANTHROPIC_BASE_URL: "https://crazyrouter.com"
        run: |
          claude --read-only --non-interactive \
            "Review this PR for bugs, security issues, and style" \
            > review.txt
          cat review.txt >> $GITHUB_STEP_SUMMARY
```

---

## Claude Code vs Cursor vs Copilot


**Choose Claude Code if:** you do backend work, need CI/CD integration, want model flexibility, or prefer terminal workflows.

**Choose Cursor if:** you prefer visual editing and work primarily on frontend.

---

## Cost Optimization

1. **Use Sonnet 4.5 for simple tasks** — 5x cheaper than Opus 4.6
2. **Clear context** with `/clear` when switching topics
4. **Monitor spending** with `/cost` command
5. **Batch similar tasks** instead of making many small requests

---

## FAQ

**Q: Does Claude Code upload my entire codebase?**
Only files you add to context via `/add` or that Claude requests to read. Use `--read-only` for extra safety.

**Q: Which languages are supported?**
All major languages: Python, JavaScript/TypeScript, Go, Rust, Ruby, PHP, Swift, Kotlin, C/C++, SQL, and more.

**Q: Can I use non-Claude models?**
Yes! With an API gateway:
```
/model gpt-4o
/model gemini-2.5-pro
/model deepseek-v3
```

**Q: How do I handle rate limits?**
Use an API gateway with automatic fallback — if one provider is rate-limited, it routes to another model automatically.

**Q: Is my code secure?**
Code is sent to the API provider (Anthropic or your gateway). For sensitive codebases, consider self-hosting or using `--read-only` mode.

---

## Get Started

1. Install: `npm install -g @anthropic-ai/claude-code`
3. Configure: `export ANTHROPIC_BASE_URL="https://crazyrouter.com"`
4. Start coding: `claude-code`

---

