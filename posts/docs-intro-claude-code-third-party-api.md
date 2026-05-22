---
title: "How to Configure Claude Code with a Third-Party OpenAI-Compatible API"
published: true
description: "A practical guide to configuring Claude Code with a third-party gateway, including the important base URL difference between OpenAI-compatible and Anthropic-native clients."
tags: claude, ai, cli, tutorial
canonical_url: https://docs.crazyrouter.com/en/integrations/claude-code?utm_source=devto&utm_medium=article&utm_campaign=docs_intro
---

Claude Code is a terminal-first coding assistant. It can read a repository, explain code, edit files, run commands, and help with refactors from inside your development workflow.

If you want to use it with a third-party API gateway, the setup is mostly environment variables. The part that often trips people up is this:

> Claude Code is not configured like a normal OpenAI-compatible SDK.

OpenAI-compatible clients usually use a base URL like:

```txt
https://example.com/v1
```

Claude Code uses the Anthropic Messages API style and expects the base URL to be the root domain:

```txt
https://example.com
```

This article walks through the setup using Crazyrouter as the concrete example. Crazyrouter provides docs for both OpenAI-compatible clients and Anthropic-native tools, including a dedicated [Claude Code setup guide](https://docs.crazyrouter.com/en/integrations/claude-code?utm_source=devto&utm_medium=article&utm_campaign=docs_intro) and a general [documentation introduction](https://docs.crazyrouter.com/en/introduction?utm_source=devto&utm_medium=article&utm_campaign=docs_intro).

## What you are configuring

Claude Code needs three broad things:

1. the Claude Code CLI installed locally
2. an API key from your third-party provider or gateway
3. environment variables telling Claude Code where to send Anthropic API requests

For Crazyrouter, the relevant values are:

```txt
ANTHROPIC_BASE_URL=https://crazyrouter.com
ANTHROPIC_API_KEY=sk-your-token-here
```

For the China-optimized API route, use:

```txt
ANTHROPIC_BASE_URL=https://cn.crazyrouter.com
ANTHROPIC_API_KEY=sk-your-token-here
```

Notice there is no `/v1` at the end of `ANTHROPIC_BASE_URL`.

That one detail is the difference between a clean setup and confusing errors like duplicated paths.

## Prerequisites

You should have:

- Git installed
- Node.js 18 or newer
- npm available
- a Crazyrouter account and API token
- model access for the Claude model you plan to use

The full official path is documented in the [Crazyrouter Claude Code guide](https://docs.crazyrouter.com/en/integrations/claude-code?utm_source=devto&utm_medium=article&utm_campaign=docs_intro). This article focuses on the practical configuration flow.

## Step 1: verify Git and Node.js

Claude Code is a coding tool, so make sure your local development environment is ready first.

```bash
git --version
node -v
npm -v
```

You want Node.js 18+.

On macOS with Homebrew:

```bash
brew install git node
```

On Ubuntu or Debian:

```bash
sudo apt update
sudo apt install -y git nodejs npm
```

On Windows, install Git and Node.js LTS with your preferred installer or package manager, then verify in PowerShell:

```powershell
git --version
node -v
npm -v
```

## Step 2: install Claude Code

Install the Claude Code CLI globally:

```bash
npm install -g @anthropic-ai/claude-code
```

Then verify:

```bash
claude --version
which claude
```

On Windows PowerShell:

```powershell
claude --version
where.exe claude
```

If the command is not found after installation, close and reopen your terminal so your PATH refreshes.

## Step 3: create a dedicated API token

Do not reuse a production application token for a local coding assistant.

Create a separate token such as:

```txt
claude-code-local
```

If your provider supports model allowlists and budgets, start narrow. For Crazyrouter, the Claude Code documentation suggests using Claude models such as:

```txt
claude-sonnet-4-6
claude-opus-4-6
```

A dedicated token gives you:

- easier rotation
- clearer usage logs
- separate budget control
- less risk if a local environment leaks
- simpler debugging when one tool breaks

## Step 4: set temporary environment variables

Before writing anything permanently to your shell profile, test with temporary variables.

macOS or Linux:

```bash
export ANTHROPIC_BASE_URL=https://crazyrouter.com
export ANTHROPIC_API_KEY=sk-your-token-here

printf '%s\n' "$ANTHROPIC_BASE_URL"
```

Windows PowerShell:

```powershell
$env:ANTHROPIC_BASE_URL = "https://crazyrouter.com"
$env:ANTHROPIC_API_KEY = "sk-your-token-here"

Write-Output $env:ANTHROPIC_BASE_URL
```

If you are using the China-optimized API route, set the root domain:

```bash
export ANTHROPIC_BASE_URL=https://cn.crazyrouter.com
```

Again, do not add `/v1`.

## Step 5: run Claude Code in a test repository

Create or open a small repository first. Avoid testing a new coding-agent setup inside an important production repo with uncommitted work.

```bash
mkdir claude-code-test
cd claude-code-test
git init
printf '# Claude Code test\n' > README.md
git add README.md
git commit -m "Initial commit"
```

Then start Claude Code:

```bash
claude
```

A simple first prompt:

```txt
Read this repository and suggest one small improvement. Do not edit files yet.
```

This tests authentication, network routing, model access, and basic tool behavior without making changes.

## Step 6: persist the variables after validation

Once the temporary setup works, make it persistent.

For Bash:

```bash
cat >> ~/.bashrc <<'EOF'
export ANTHROPIC_BASE_URL=https://crazyrouter.com
export ANTHROPIC_API_KEY=sk-your-token-here
EOF

source ~/.bashrc
```

For Zsh:

```bash
cat >> ~/.zshrc <<'EOF'
export ANTHROPIC_BASE_URL=https://crazyrouter.com
export ANTHROPIC_API_KEY=sk-your-token-here
EOF

source ~/.zshrc
```

For Windows PowerShell user-level variables:

```powershell
[Environment]::SetEnvironmentVariable("ANTHROPIC_BASE_URL", "https://crazyrouter.com", "User")
[Environment]::SetEnvironmentVariable("ANTHROPIC_API_KEY", "sk-your-token-here", "User")
```

Close and reopen PowerShell after setting persistent variables.

For production-like workstations, consider using a password manager, local secret manager, or shell-specific encrypted environment management instead of writing raw keys to a plain-text profile.

## Step 7: understand the base URL difference

This is the most important troubleshooting section.

Crazyrouter's [API Endpoints docs](https://docs.crazyrouter.com/en/api-endpoint?utm_source=devto&utm_medium=article&utm_campaign=docs_intro) describe the difference like this:

```txt
OpenAI-compatible SDKs / clients:
https://crazyrouter.com/v1

Claude Code / Anthropic-native clients:
https://crazyrouter.com
```

Why?

Because different clients append paths differently.

An OpenAI-compatible SDK expects a base URL ending in `/v1`, then it appends paths such as:

```txt
/chat/completions
/models
/responses
```

So the final URL becomes:

```txt
https://crazyrouter.com/v1/chat/completions
```

Claude Code uses the Anthropic-native protocol. It appends the Anthropic request path itself. If you give it this incorrect value:

```txt
ANTHROPIC_BASE_URL=https://crazyrouter.com/v1
```

You can end up with duplicated or invalid paths.

Correct:

```txt
ANTHROPIC_BASE_URL=https://crazyrouter.com
```

Incorrect:

```txt
ANTHROPIC_BASE_URL=https://crazyrouter.com/v1
ANTHROPIC_BASE_URL=https://crazyrouter.com/v1/messages
```

If your logs show paths like `/v1/v1/messages` or `/v1/messages/v1/messages`, reset the base URL to the root domain.

## Step 8: keep OpenAI-compatible app config separate

You might also have a normal app that uses the OpenAI SDK through the same gateway.

That app should still use `/v1`:

```js
import OpenAI from "openai";

const client = new OpenAI({
  apiKey: process.env.CRAZYROUTER_API_KEY,
  baseURL: "https://crazyrouter.com/v1",
});

const response = await client.chat.completions.create({
  model: "gpt-5.4",
  messages: [{ role: "user", content: "Hello" }],
});

console.log(response.choices[0].message.content);
```

This is why I recommend separate environment variable names:

```bash
# For OpenAI-compatible apps
export CRAZYROUTER_API_KEY=sk-your-token-here
export OPENAI_BASE_URL=https://crazyrouter.com/v1

# For Claude Code
export ANTHROPIC_API_KEY=sk-your-claude-code-token-here
export ANTHROPIC_BASE_URL=https://crazyrouter.com
```

Keeping these separate prevents one tool's base URL convention from breaking another tool.

## Step 9: add a safety workflow for repositories

Coding agents are most useful when they can edit files, but you should still keep a safe workflow.

Before starting a session:

```bash
git status
```

If you have uncommitted work, commit or stash it first:

```bash
git add .
git commit -m "Save work before Claude Code session"
```

Or:

```bash
git stash push -m "before claude-code session"
```

After Claude Code makes changes:

```bash
git diff
git status
npm test
```

For Python projects:

```bash
pytest
```

For Go projects:

```bash
go test ./...
```

For Rust projects:

```bash
cargo test
```

Treat the agent like a fast pair programmer: helpful, but still subject to review.

## Troubleshooting checklist

If Claude Code does not work, check these in order.

### 1. Is the CLI installed?

```bash
claude --version
```

If not found, reinstall or refresh your terminal PATH.

### 2. Are the environment variables visible in the same shell?

macOS or Linux:

```bash
echo "$ANTHROPIC_BASE_URL"
test -n "$ANTHROPIC_API_KEY" && echo "API key is set"
```

PowerShell:

```powershell
Write-Output $env:ANTHROPIC_BASE_URL
if ($env:ANTHROPIC_API_KEY) { Write-Output "API key is set" }
```

Do not print the full API key in shared logs or screenshots.

### 3. Is the base URL correct?

For Claude Code:

```txt
https://crazyrouter.com
```

Not:

```txt
https://crazyrouter.com/v1
```

### 4. Does the token have model access?

Check whether the token is allowed to use the Claude model you selected. If you created a narrow token, this is easy to overlook.

### 5. Are you behind a proxy or firewall?

If your terminal cannot reach the API host, Claude Code cannot either. Test basic connectivity from the same machine and shell.

```bash
curl -I https://crazyrouter.com
```

For the China-optimized API route:

```bash
curl -I https://cn.crazyrouter.com
```

## A recommended local setup

For regular use, I like this structure:

```txt
Token name: claude-code-local
Allowed models: Claude models only
Budget: small monthly cap at first
Base URL: https://crazyrouter.com
Storage: user shell profile or local secret manager
Test repo: small repository before production use
Review: always inspect git diff before committing
```

Then keep your app token separate:

```txt
Token name: webapp-dev
Base URL: https://crazyrouter.com/v1
Used by: OpenAI-compatible SDK app
```

This separation saves a surprising amount of debugging time.

## Final thoughts

Configuring Claude Code with a third-party API gateway is not difficult, but it is easy to copy the wrong base URL from an OpenAI-compatible example.

Remember:

```txt
Claude Code uses the Anthropic-native root URL.
OpenAI-compatible SDKs usually use the /v1 URL.
```

If you are using Crazyrouter, start with the [Claude Code setup guide](https://docs.crazyrouter.com/en/integrations/claude-code?utm_source=devto&utm_medium=article&utm_campaign=docs_intro), then keep the [API Endpoints reference](https://docs.crazyrouter.com/en/api-endpoint?utm_source=devto&utm_medium=article&utm_campaign=docs_intro) nearby whenever you configure another client.

For the broader docs map, the [Crazyrouter introduction](https://docs.crazyrouter.com/en/introduction?utm_source=devto&utm_medium=article&utm_campaign=docs_intro) links to quick start, tool integrations, text models, image generation, video generation, and AI-readable documentation.
