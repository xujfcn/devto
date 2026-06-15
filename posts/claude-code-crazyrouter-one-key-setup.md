---
title: 'Claude Code with One API Key: How to Route Claude, GPT, Gemini, and DeepSeek Through Crazyrouter'
published: true
description: 'Configure Claude Code through Crazyrouter and use one token for Claude, GPT, Gemini, DeepSeek, and OpenAI-compatible tools.'
tags: ai, api, claude, tutorial
canonical_url: 'https://xujfcn.github.io/crazyrouter-claude-code/?utm_source=devto&utm_medium=article&utm_campaign=claude_code_crazyrouter_setup'
---

# Claude Code with One API Key: How to Route Claude, GPT, Gemini, and DeepSeek Through Crazyrouter

Claude Code is powerful when it can work with the model you actually need for the task. The problem is that modern AI development rarely depends on one provider only.

One day you want Claude Opus for complex reasoning. Another day you want GPT for tool-heavy workflows. For fast summarization, Gemini or DeepSeek may be enough. If each provider needs a separate key, endpoint, billing account, and configuration path, your local coding setup becomes fragile very quickly.

That is the problem this small project solves:

https://xujfcn.github.io/crazyrouter-claude-code/

It gives Claude Code a simple path to use Crazyrouter as the model gateway: one token, one configuration flow, and access to multiple major model families.

## Why this matters

Claude Code is not just a chat interface. It is a coding agent that reads files, proposes edits, explains errors, and helps you move through real development work. That means the model layer matters.

A good coding agent setup should be:

- easy to configure;
- easy to reproduce on a new machine;
- compatible with strong reasoning models;
- flexible enough to switch models;
- safe enough not to hardcode keys into projects;
- clear enough for a teammate to debug.

The Crazyrouter Claude Code setup page focuses on that exact workflow.

## Two paths: configure only or full install

The site splits setup into two practical paths.

If Claude Code is already installed, use the configure-only command. This updates your environment variables and points Claude Code to Crazyrouter without reinstalling the CLI.

For macOS or Linux:

```bash
curl -fsSL https://raw.githubusercontent.com/xujfcn/crazyrouter-claude-code/main/configure.sh | bash
```

For Windows PowerShell:

```powershell
irm https://raw.githubusercontent.com/xujfcn/crazyrouter-claude-code/main/windows/configure.ps1 | iex
```

If Claude Code is not installed yet, use the full install path. It installs the required dependencies and then configures Crazyrouter.

For macOS or Linux:

```bash
curl -fsSL https://raw.githubusercontent.com/xujfcn/crazyrouter-claude-code/main/setup.sh | bash
```

For Windows PowerShell:

```powershell
irm https://raw.githubusercontent.com/xujfcn/crazyrouter-claude-code/main/windows/setup.ps1 | iex
```

Before running either path, create a Crazyrouter token:

https://cn.crazyrouter.com

## What gets configured

The script writes user-level environment variables for both Anthropic-style and OpenAI-compatible tooling.

Typical values include:

```text
ANTHROPIC_BASE_URL=https://cn.crazyrouter.com
ANTHROPIC_AUTH_TOKEN=<your token>
ANTHROPIC_MODEL=claude-opus-4-8
CLAUDE_MODEL=claude-opus-4-8
OPENAI_API_KEY=<your token>
OPENAI_BASE_URL=https://cn.crazyrouter.com/v1
```

This matters because many AI tools expect either Anthropic-style variables or OpenAI-compatible variables. Setting both makes the same token useful across Claude Code and other developer tools.

## The `/v1` detail

One small detail prevents many support problems:

- Anthropic-style base URL: `https://cn.crazyrouter.com`
- OpenAI-compatible base URL: `https://cn.crazyrouter.com/v1`

The `/v1` suffix belongs to the OpenAI-compatible endpoint. If you forget it in OpenAI SDK tools, requests may fail even when the token is correct.

That is why a repeatable setup script is better than manual copy-paste.

## When to use this setup

This setup is useful if you:

- use Claude Code daily;
- want one token for multiple model families;
- switch between Claude, GPT, Gemini, and DeepSeek models;
- work across macOS, Linux, and Windows;
- want a reproducible onboarding command for teammates;
- do not want to maintain many provider keys locally.

## Final thought

AI coding agents are only as reliable as their configuration. A missing base URL, wrong token, or inconsistent shell environment can make a good model feel broken.

The Crazyrouter Claude Code setup page turns that fragile configuration step into a repeatable workflow:

https://xujfcn.github.io/crazyrouter-claude-code/

If you use Claude Code and want one key for multiple models, this is the shortest path to try.


Try the setup page: https://xujfcn.github.io/crazyrouter-claude-code/?utm_source=devto&utm_medium=article&utm_campaign=claude_code_crazyrouter_setup
