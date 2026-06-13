---
title: Using Crazyrouter as a Unified Gateway for Domestic Models in Claude Code
published: true
description: Daily Claude Code guide rewrite covering practical setup, API routing, and AI coding workflows.
tags: ai, claude, coding, api
canonical_url: https://github.com/xujfcn/claude-code-guide?utm_source=devto&utm_medium=article&utm_campaign=claude_code_guide_daily
---

# Using Crazyrouter as a Unified Gateway for Domestic Models in Claude Code

Claude Code is most useful when it becomes part of your normal development workflow: reading a repository, explaining unfamiliar code, refactoring modules, writing tests, or helping you reason through a change. If your team also wants to use domestic models, the operational question quickly becomes less about individual model vendors and more about how to manage access consistently.

A practical approach is to put Crazyrouter in front of Claude Code and other AI-enabled applications. Instead of distributing different API keys, endpoint formats, model permissions, and billing views across multiple tools, you can standardize on one gateway.

This article explains the correct Claude Code configuration, the different endpoint format required by OpenAI-compatible clients, and the most common Base URL mistakes to avoid.

Repository for the full guide: [Claude Code Guide](https://github.com/xujfcn/claude-code-guide?utm_source=devto&utm_medium=article&utm_campaign=claude_code_guide_daily)

## The key distinction: Claude Code vs OpenAI-compatible clients

There are two endpoint patterns you need to keep separate:

- Claude Code and Anthropic-native clients use: `https://cn.crazyrouter.com`
- OpenAI-compatible SDKs, HTTP calls, and application backends use: `https://cn.crazyrouter.com/v1`

This difference matters because Claude Code builds the Anthropic Messages path itself. If you configure Claude Code with a Base URL that already includes `/v1`, Claude Code may generate duplicated paths such as `/v1/v1/messages`.

For Claude Code, use the root endpoint:

`ANTHROPIC_BASE_URL=https://cn.crazyrouter.com`

For OpenAI-compatible applications, use the `/v1` endpoint:

`base_url=https://cn.crazyrouter.com/v1`

Those two values look almost identical, but mixing them up is one of the most common integration errors.

## Why route Claude Code through Crazyrouter?

Using Crazyrouter as the access layer gives you a more manageable setup, especially when more than one developer or project is involved.

The main advantages are operational:

- One API Token can be used to manage access to multiple model families.
- Logs, balances, failure reasons, and model usage can be checked from one console.
- Claude Code can have its own dedicated Token with budget limits and model allowlists.
- Domestic network access can use `https://cn.crazyrouter.com` directly.
- Model switching usually requires changing a model name or Token policy, not rewriting the application integration.

This is particularly helpful for teams. You do not want every developer to maintain a different collection of vendor keys on their laptop. You also do not want debugging to depend on asking, “Which platform did this request go through?” A unified gateway makes the request path easier to reason about.

## Configure Claude Code correctly

For Claude Code, set the Anthropic-compatible environment variables. The Base URL must be the root endpoint, without `/v1`.

```bash
# macOS / Linux
export ANTHROPIC_BASE_URL=https://cn.crazyrouter.com
export ANTHROPIC_API_KEY=YOUR_CRAZYROUTER_API_KEY
claude

# OpenAI-compatible HTTP example: use /v1 here, not in ANTHROPIC_BASE_URL
curl https://cn.crazyrouter.com/v1/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_CRAZYROUTER_API_KEY" \
  -d '{
    "model": "deepseek-v4-pro",
    "messages": [
      {"role": "user", "content": "Explain what Crazyrouter does in one sentence."}
    ]
  }'
```

On Windows PowerShell, set the same values as user-level environment variables:

`ANTHROPIC_BASE_URL` should be `https://cn.crazyrouter.com`, and `ANTHROPIC_API_KEY` should be your Crazyrouter API Token. After setting them, reopen the terminal before running `claude` so the new environment is loaded.

A good first command inside Claude Code is `/status`. It helps confirm that the tool is running and gives you a quick view of the current state before you start a longer repository task.

## Configure OpenAI-compatible applications correctly

If you are not configuring Claude Code, but instead integrating a backend service, frontend proxy, CLI script, or SDK that follows the OpenAI-compatible API shape, use the `/v1` endpoint:

`https://cn.crazyrouter.com/v1`

This applies to common `chat/completions` style calls and SDKs that ask for a `base_url` or equivalent configuration option.

The rule of thumb is simple:

- Anthropic / Claude Code environment variable: root endpoint
- OpenAI-compatible SDK or HTTP API: `/v1` endpoint

Do not try to make one value work everywhere. Configure each tool according to the protocol it expects.

## Suggested Token strategy

Avoid using one shared Token for every experiment, CLI tool, and application. Create a dedicated Crazyrouter Token for Claude Code.

That gives you several benefits:

1. You can apply a model allowlist specifically for development-agent usage.
2. You can set a separate budget for interactive coding work.
3. You can inspect Claude Code logs without mixing them with backend traffic.
4. If a laptop is replaced or a developer leaves the project, you can rotate only the relevant Token.

For teams, this is easier to audit than a single long-lived Token copied across scripts, local shells, and CI jobs.

## Choosing models for different tasks

Do not treat “domestic model access” as a requirement to manually integrate every provider. In this workflow, the Crazyrouter model list and Token permissions are the source of truth.

A practical model selection pattern looks like this:

- For code reading, repository navigation, refactoring, and complex engineering tasks, prefer Claude models or coding models that have been verified for Claude Code compatibility.
- For Chinese-language documentation, summarization, and content processing, choose a general-purpose model with strong Chinese-language quality and reasonable cost.
- For long project analysis, select a model with a longer context window and stable output behavior.
- For batch jobs, prioritize lower cost and predictable throughput, and enforce a Token budget.

Available models should be checked from the Crazyrouter console or the models endpoint: `GET https://cn.crazyrouter.com/v1/models`.

## Common errors and how to diagnose them

### 1. Duplicated `/v1` in the request path

If logs show paths such as `/v1/v1/messages` or `/v1/v1/models`, the Base URL likely includes `/v1` in a place where the client already adds it.

For Claude Code, the correct value is:

`ANTHROPIC_BASE_URL=https://cn.crazyrouter.com`

For OpenAI-compatible SDKs, the correct value is:

`base_url=https://cn.crazyrouter.com/v1`

Start by checking this before changing models or regenerating Tokens.

### 2. Token does not allow the selected model

If the API returns `model not allowed` or an HTTP 403, inspect the Token settings in the Crazyrouter console. The Token may not have permission to use the model requested by Claude Code or your application.

This is another reason to use a dedicated Claude Code Token. It makes permission issues easier to isolate.

### 3. Secret values committed to code

Examples often use placeholders such as `YOUR_CRAZYROUTER_API_KEY`. Real Tokens should not be committed to Git, pasted into issue trackers, or added to long-running AI chat context.

For local development, environment variables are usually enough. For team infrastructure, use your normal secret manager or CI secret storage.

### 4. Terminal session did not reload environment variables

After changing environment variables, reopen the terminal. This is especially important on Windows after setting user-level variables through PowerShell or system settings.

## Recommended workflow

A reliable setup sequence is:

1. Create a dedicated API Token for Claude Code in the Crazyrouter console.
2. Set `ANTHROPIC_BASE_URL=https://cn.crazyrouter.com`.
3. Set `ANTHROPIC_API_KEY` to the dedicated Token.
4. Start Claude Code and run `/status`.
5. Check the Crazyrouter console to confirm requests appear in the logs.
6. Adjust model selection or Token allowlists based on the work you plan to do.

This keeps keys, permissions, logs, and troubleshooting in one place. It also makes it easier to support both Claude models and domestic models through one consistent operational path.

## Final notes

The most important part of this integration is not the shell command. It is keeping protocol boundaries clear:

- Claude Code uses `https://cn.crazyrouter.com`.
- OpenAI-compatible clients use `https://cn.crazyrouter.com/v1`.

Once that distinction is correct, the rest of the setup becomes much easier to maintain. Use separate Tokens, check logs from the console, and treat the Crazyrouter model list as the source of truth for what your team can call.
