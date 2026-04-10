---
title: "Claude Code Complete Tutorial: From Installation to Production (2026)"
published: true
description: "Step-by-step guide to Claude Code — Anthropic's terminal-based AI coding assistant. Installation, configuration with API gateway (45% cheaper), core features, CI/CD integration, and advanced tips."
tags: ai, programming, claudecode, tutorial
canonical_url: https://crazyrouter.com/blog/claude-code-complete-tutorial-2026
---

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
claude-code --version
```

---

## Configuration

### Option 1: Official Anthropic API

```bash
export ANTHROPIC_API_KEY="sk-ant-api03-xxxxx"
```

**Pricing:** Claude Opus 4.6 — $15/M input, $75/M output tokens.

### Option 2: API Gateway (Recommended — 45% Cheaper)

Why use an API gateway?
- **45% cheaper** than official pricing
- **600+ models** with one key (Claude + GPT + Gemini + DeepSeek)
- **Better international latency**
- **No credit card** required (Alipay/WeChat supported)

```bash
export ANTHROPIC_API_KEY="sk-xxxxxx"  # Your gateway key
export ANTHROPIC_BASE_URL="https://crazyrouter.com/v1"
```

| Method | Input Price | Output Price | Savings |
|--------|-----------|-------------|---------|
| Official API | $15/M | $75/M | — |
| API Gateway | $8.25/M | $41.25/M | **45%** |

[Get your API key →](https://crazyrouter.com?utm_source=devto&utm_medium=article&utm_campaign=claude_code_tutorial)

---

## Core Usage

```bash
# Start in your project
cd /path/to/project
claude-code
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
          ANTHROPIC_BASE_URL: "https://crazyrouter.com/v1"
        run: |
          claude-code --read-only --non-interactive \
            "Review this PR for bugs, security issues, and style" \
            > review.txt
          cat review.txt >> $GITHUB_STEP_SUMMARY
```

---

## Claude Code vs Cursor vs Copilot

| Feature | Claude Code | Cursor | Copilot |
|---------|-------------|--------|---------|
| Interface | Terminal | GUI (VS Code) | IDE Plugin |
| Full codebase context | ✅ | ⚠️ Limited | ❌ |
| Multi-file editing | ✅ | ✅ | ❌ |
| Custom API endpoint | ✅ | ❌ | ❌ |
| CI/CD integration | ✅ | ❌ | ❌ |
| Price | Pay-per-token | $20/mo | $10/mo |
| Model flexibility | ✅ Any model | Claude only | GPT only |

**Choose Claude Code if:** you do backend work, need CI/CD integration, want model flexibility, or prefer terminal workflows.

**Choose Cursor if:** you prefer visual editing and work primarily on frontend.

---

## Cost Optimization

1. **Use Sonnet 4.5 for simple tasks** — 5x cheaper than Opus 4.6
2. **Clear context** with `/clear` when switching topics
3. **Use an API gateway** — save 45% on every API call
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
2. Get an API key: [crazyrouter.com](https://crazyrouter.com?utm_source=devto&utm_medium=article&utm_campaign=claude_code_tutorial) (45% cheaper, 600+ models)
3. Configure: `export ANTHROPIC_BASE_URL="https://crazyrouter.com/v1"`
4. Start coding: `claude-code`

---

*Try Crazyrouter — one API key for 600+ AI models, 45% cheaper than going direct. [crazyrouter.com](https://crazyrouter.com?utm_source=devto&utm_medium=article&utm_campaign=claude_code_tutorial)*
