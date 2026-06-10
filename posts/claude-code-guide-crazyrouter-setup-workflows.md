---
title: Claude Code Guide: Practical Setup, API Routing, and AI Coding Workflows
published: true
description: A practical Claude Code guide repo covering setup, Crazyrouter routing, Base URL rules, troubleshooting, and AI coding workflows.
tags: ai, claude, coding, api
canonical_url: https://github.com/xujfcn/claude-code-guide?utm_source=devto&utm_medium=article&utm_campaign=claude_code_guide
---

Claude Code is becoming one of the most useful AI coding tools for developers, but onboarding is still messy.

Some users need the basic install command. Some need to configure a third-party API endpoint. Some run into the classic Base URL problem: should the endpoint include `/v1`, or should the client append it automatically? Others want workflow examples: PRD to code, Figma to prototype, Mermaid diagrams, project planning, and reusable prompts.

That is why we created **Claude Code Guide**:

https://github.com/xujfcn/claude-code-guide?utm_source=devto&utm_medium=article&utm_campaign=claude_code_guide

It is a 36+ article Chinese tutorial repository for Claude Code users, with a practical focus on setup, API routing, and real AI coding workflows.

## What the guide covers

- Claude Code installation and basic usage
- Correct Crazyrouter integration for Claude Code
- The difference between Anthropic-style root endpoints and OpenAI-compatible `/v1` endpoints
- Common Base URL mistakes, including `/v1/v1/messages`
- Prompting and workflow patterns
- Project implementation from PRD to code
- Figma and prototype workflows
- Mermaid architecture diagrams
- Product manager and developer collaboration workflows

## The most important setup rule

For Claude Code and Anthropic-native clients, use the root endpoint:

```bash
export ANTHROPIC_BASE_URL=https://cn.crazyrouter.com
export ANTHROPIC_API_KEY=YOUR_CRAZYROUTER_API_KEY
claude
```

For OpenAI-compatible SDKs, use the `/v1` endpoint:

```text
https://cn.crazyrouter.com/v1
```

This distinction matters because different clients append paths differently. A wrong Base URL can create duplicated paths such as `/v1/v1/messages`.

## Why this repo is useful

Most AI tool documentation explains one feature at a time. This guide is organized as a learning path:

1. Quick setup
2. API and endpoint configuration
3. Basic Claude Code operations
4. Engineering workflows
5. Product and design workflows
6. Real project examples

The goal is not only to explain Claude Code, but to reduce integration friction for developers who want one practical reference.

## Repository

GitHub:

https://github.com/xujfcn/claude-code-guide?utm_source=devto&utm_medium=article&utm_campaign=claude_code_guide

If you are using Claude Code with custom API routing, model gateways, or multi-model AI coding workflows, this guide is meant to be a starting point.
