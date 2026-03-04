---
title: 'How to Skip Permission Prompts in Claude Code: The --dangerously-skip-permissions Flag Explained'
published: true
description: 'Tired of clicking ''Allow'' every 10 seconds in Claude Code? Here''s how to use --dangerously-skip-permissions safely, plus smarter alternatives.'
tags: 'ai, productivity, cli, tutorial'
canonical_url: null
cover_image: null
id: 3309214
date: '2026-03-04T16:31:43Z'
---

If you've used Claude Code (Anthropic's CLI for Claude), you know the pain: every file edit, every shell command, every tool call triggers a permission prompt.

```
Claude wants to edit src/index.ts
Allow? (y/n)
```

For a 10-file refactor, that's 30+ prompts. It kills your flow.

The `--dangerously-skip-permissions` flag exists for exactly this reason. But the name is scary on purpose. Let's break down what it actually does, when to use it, and how to stay safe.

---

## What Does --dangerously-skip-permissions Do?

By default, Claude Code asks for your approval before:
- Editing or creating files
- Running shell commands
- Accessing external tools (web search, APIs, etc.)

The `--dangerously-skip-permissions` flag **auto-approves all of these**. Claude will read, write, execute, and modify without asking.

```bash
claude --dangerously-skip-permissions
```

That's it. No more prompts.

---

## Why It's Called "Dangerously"

Anthropic named it this way to make you think twice. Without permission prompts, Claude can:

- **Overwrite files** without confirmation
- **Run shell commands** including destructive ones (`rm -rf`, `git push --force`)
- **Install packages** you didn't ask for
- **Modify system configs** if it has access

In a production environment or a repo with uncommitted changes, this could be catastrophic.

---

## When It's Actually Safe to Use

Here's the thing: for most development workflows, the risk is low if you follow basic precautions.

**Safe scenarios:**
- ✅ Working in a git repo with clean commits (you can always `git reset`)
- ✅ Greenfield projects (nothing to break)
- ✅ Disposable environments (Docker containers, VMs, CI/CD)
- ✅ Code review tasks (Claude is reading, not writing)
- ✅ Automated pipelines where you review output afterward

**Dangerous scenarios:**
- ❌ Production servers
- ❌ Repos with uncommitted work
- ❌ Directories with sensitive files (SSH keys, env files, credentials)
- ❌ Running as root without sandboxing

---

## The Smart Way: Use It with Safeguards

### 1. Always commit before starting

```bash
git add -A && git commit -m "checkpoint before claude session"
claude --dangerously-skip-permissions
```

Now if anything goes wrong: `git reset --hard HEAD`

### 2. Use in a specific directory

```bash
cd ~/projects/my-safe-project
claude --dangerously-skip-permissions
```

Don't run it from your home directory or root.

### 3. Combine with a task description

```bash
claude --dangerously-skip-permissions -p "Refactor the auth module to use JWT tokens. Don't touch the database layer."
```

Clear instructions reduce the chance of Claude going off-script.

### 4. Use Docker for full isolation

```bash
docker run -it -v $(pwd):/workspace -w /workspace \
  node:22 bash -c "npm i -g @anthropic-ai/claude-code && claude --dangerously-skip-permissions"
```

Even if Claude runs `rm -rf /`, it only affects the container.

---

## Alternative: Permission Allow Lists

If `--dangerously-skip-permissions` feels too aggressive, Claude Code also supports **allow lists** in your project settings. Create a `.claude/settings.json`:

```json
{
  "permissions": {
    "allow": [
      "Edit(src/**)",
      "Edit(tests/**)",
      "Bash(npm test)",
      "Bash(npm run build)"
    ],
    "deny": [
      "Edit(.env*)",
      "Edit(*.key)",
      "Bash(rm *)",
      "Bash(git push*)"
    ]
  }
}
```

This gives Claude freedom to edit source code and run tests, but blocks it from touching secrets or pushing to git. Best of both worlds.

---

## Using It in Automated Pipelines

The flag really shines in CI/CD and automation. If you're using Claude Code for automated code review, refactoring, or test generation:

```bash
# In a GitHub Action or CI pipeline
claude --dangerously-skip-permissions -p "Review the PR diff and suggest improvements" --output-format json
```

In automated contexts, there's no human to click "Allow," so the flag is essentially required.

---

## Pro Tips

### Pair with an API gateway for cost control

When Claude runs autonomously, it can burn through tokens fast. Using an API gateway with budget limits prevents surprise bills:

```bash
# Use a gateway with spending caps
export ANTHROPIC_BASE_URL="https://crazyrouter.com/v1"
export ANTHROPIC_API_KEY="your-gateway-key"
claude --dangerously-skip-permissions
```

Most gateways let you set daily/monthly spending limits, so even if Claude goes wild, your wallet is safe.

### Check what Claude did afterward

```bash
# After a session, review all changes
git diff
git diff --stat

# Or use git log if Claude made commits
git log --oneline -10
```

### Set up a pre-commit hook as a safety net

```bash
# .git/hooks/pre-commit
#!/bin/bash
# Prevent accidental commits of sensitive files
if git diff --cached --name-only | grep -E '\.(env|key|pem|secret)'; then
  echo "⚠️  Sensitive file detected in commit. Aborting."
  exit 1
fi
```

---

## TL;DR

| Approach | Safety | Convenience | Best For |
|----------|--------|-------------|----------|
| Default (prompts) | ⭐⭐⭐⭐⭐ | ⭐⭐ | Production, sensitive repos |
| Allow lists | ⭐⭐⭐⭐ | ⭐⭐⭐⭐ | Daily development |
| `--dangerously-skip-permissions` | ⭐⭐ | ⭐⭐⭐⭐⭐ | Greenfield, CI/CD, Docker |
| Skip + git checkpoint | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | **Recommended for most devs** |

**My recommendation:** `git commit` → `--dangerously-skip-permissions` → review changes → commit or reset. Fast, safe, reversible.

---

*How do you handle Claude Code permissions? Share your workflow in the comments.*
