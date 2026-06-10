---
title: Claude Code with Crazyrouter: Quick Start, Base URL Setup, and Daily Workflow
published: true
description: Daily Claude Code guide rewrite covering practical setup, API routing, and AI coding workflows.
tags: ai, claude, coding, api
canonical_url: https://github.com/xujfcn/claude-code-guide?utm_source=devto&utm_medium=article&utm_campaign=claude_code_guide_daily
---

# Claude Code with Crazyrouter: Quick Start, Base URL Setup, and Daily Workflow

Claude Code is most useful when it is configured as part of a repeatable development workflow rather than treated as a one-off chat tool. This guide walks through a practical setup for connecting Claude Code to Crazyrouter, then covers the day-to-day commands and habits that help keep coding sessions controlled, auditable, and easier to resume.

The most important configuration detail is the endpoint format. Claude Code and Anthropic-native clients should use:

`ANTHROPIC_BASE_URL=https://cn.crazyrouter.com`

OpenAI-compatible SDKs, direct HTTP requests, and frontend or backend applications should use:

`base_url=https://cn.crazyrouter.com/v1`

Do not mix these two forms. A common mistake is adding `/v1` to an Anthropic-style base URL and ending up with duplicated paths such as `/v1/v1/...`.

Project reference: [claude-code-guide on GitHub](https://github.com/xujfcn/claude-code-guide?utm_source=devto&utm_medium=article&utm_campaign=claude_code_guide_daily)

## Who this guide is for

This article is for developers who already use Claude Code, teams that want to route model access through Crazyrouter, or engineers preparing to standardize local AI coding workflows. The focus is not on benchmarking models or promising productivity gains. Instead, it covers installation checks, environment variables, CLI usage, session management, and safety practices that make Claude Code easier to operate in real projects.

Before you start, create a dedicated Crazyrouter API token for Claude Code. Avoid reusing a broad production token on local machines. If your team uses model allowlists or per-project permissions, create a separate token for each project or developer group.

## Prerequisites

Claude Code runs on common developer environments:

- Linux such as Ubuntu 18.04+ or CentOS 7+
- macOS 10.15+
- Windows 10+
- Stable internet connection
- At least 500 MB of available disk space

You also need Node.js and Git. Check them first:

```bash
node --version
npm --version
git --version

# Install Claude Code globally
npm install -g @anthropic-ai/claude-code

# Verify the installation
claude --version

# Configure Claude Code for Crazyrouter
export ANTHROPIC_BASE_URL=https://cn.crazyrouter.com
export ANTHROPIC_API_KEY=YOUR_CRAZYROUTER_API_KEY

# Start Claude Code
claude
```

On Windows, set the same environment variables through PowerShell user environment settings or your preferred terminal profile. After changing persistent environment variables, restart the terminal so the new values are loaded.

## Endpoint rules that prevent configuration bugs

Most connection issues come from using the wrong base URL for the client type.

Use `https://cn.crazyrouter.com` for Claude Code and Anthropic-native clients. This is the root endpoint expected by Anthropic-style tooling.

Use `https://cn.crazyrouter.com/v1` for OpenAI-compatible SDKs and custom HTTP integrations. These clients expect the `/v1` API prefix to be part of the configured base URL.

If a request fails, check the effective URL in logs before changing tokens or reinstalling tools. If you see duplicated path segments, fix the base URL first. Keeping endpoint rules written in your project documentation or `CLAUDE.md` saves time for the whole team.

## Start with the CLI help

Once Claude Code launches, run `claude --help`. It is the fastest way to discover supported flags and confirm your installed version behaves as expected.

Useful commands and session actions include:

- `/clear`: clear the current context and start fresh.
- `/compact`: summarize and compress the conversation when context is getting large.
- `/cost`: inspect current session usage, then compare it with Crazyrouter console logs.
- `/login` and `/logout`: switch accounts or refresh authentication.
- `/model`: change model if your token permissions and model allowlist permit it.
- `/status`: view the current Claude Code status.
- `/doctor`: diagnose installation and environment issues.
- `claude -c`: continue the most recent conversation.
- `claude -r`: select and restore a previous session.
- `claude -p`: run a non-interactive prompt, useful for scripts.

For regular work, learn the resume commands early. Long-running implementation tasks often span multiple terminal sessions, and recovering the right conversation is easier than recreating context from scratch.

## Use Claude Code inside your editor

Claude Code also works well from IDEs such as VS Code and Cursor. Install the official Claude Code extension from the marketplace, then open it from the editor UI or configured shortcut. The practical benefit is context: selected code, diffs, and workspace files can be passed into the conversation without repeatedly copying snippets between windows.

Editor integration is especially useful for review tasks. You can select a function, ask for a refactor plan, inspect the diff, and decide whether to apply it. Even when you trust the tool, keep Git as the source of truth. Commit before large edits, and review changes with your normal diff workflow.

## Choose the right working mode

Claude Code supports several interaction patterns. Switching modes based on task risk is more reliable than using one mode for everything.

For routine file edits, auto-edit behavior can reduce confirmation fatigue. It is helpful for generating boilerplate, renaming files, or applying a simple pattern across a small codebase. Use it with Git so unwanted changes are easy to revert.

For larger changes, use planning first. Ask Claude Code to describe the approach, affected files, migration steps, and testing strategy before it edits anything. If the plan is vague, ask it to revise the plan rather than letting it proceed. This is valuable for new features, framework migrations, and bug fixes with unclear root causes.

For high-permission work, `claude --dangerously-skip-permissions` exists, but treat it carefully. It can be useful in a disposable sandbox, container, or temporary branch where the tool needs to run commands without repeated prompts. Do not use it in directories containing secrets, SSH keys, production credentials, or unrelated repositories.

## Make `CLAUDE.md` your project memory

A good `CLAUDE.md` file acts like project-level operating instructions. Claude Code can read it and use it to keep behavior consistent across sessions.

Keep it short and practical. Useful content includes:

- Project architecture notes.
- How to run tests and linters.
- Local service ports.
- Rules for database migrations.
- Coding conventions.
- Safety rules such as requiring evidence before claiming success.

One effective workflow is:

1. Create an initial `CLAUDE.md` with project rules.
2. Work through a milestone in Claude Code.
3. Use `/compact` when context is close to full.
4. Ask Claude Code to update `CLAUDE.md` with durable decisions, not every detail.
5. Continue in a new or resumed session.

Do not let the file become a transcript. The goal is reusable memory, not a complete log.

## Control sessions before they drift

AI coding sessions can drift if you let them run without checkpoints. Use interruption and rollback deliberately.

Press `Esc` to pause when a command hangs, dependency installation takes too long, or the tool appears to be solving the wrong problem. Pressing `Esc` twice can move back to a previous conversation point, but confirm before doing this because redo behavior may not match your expectations.

If the code is wrong, ask for a rollback to the previous state. Still verify with Git. Claude Code is useful, but your repository history is the more reliable recovery mechanism.

When you see context warnings such as low remaining context before auto-compact, run `/compact` manually at a logical boundary. Ask for a short summary of completed work, remaining tasks, and changed files before continuing.

## Monitor usage and batch work carefully

Usage should be visible, especially on team tokens. The `/cost` command gives session-level visibility, while external tools such as `npx ccusage@latest` can help inspect usage locally. Compare this with Crazyrouter logs when investigating unexpected consumption.

For repetitive work, `claude -p` can process tasks from a file. Keep the tasks sequential rather than parallel unless you understand your rate limits and account policies. Add timeouts for long-running prompts and restrict tool permissions when possible. For example, allowing only edit operations can reduce accidental shell execution during documentation generation.

Batch automation is best for bounded tasks: generating documentation stubs, applying a repetitive style change, or summarizing a list of files. It is not a replacement for code review.

## Safety practices for real projects

A few habits make Claude Code safer in daily work:

- Work on a branch and commit before large changes.
- Keep secrets out of the working directory.
- Use containers or sandboxes for risky command execution.
- Ask for evidence when Claude Code says a task is complete.
- Require test output, file paths, or diff references instead of accepting a success message.
- Keep endpoint configuration visible so new team members do not confuse Anthropic and OpenAI-compatible base URLs.

A useful rule for `CLAUDE.md` is: whenever the assistant claims success, it must include the command run, the result, and the relevant file path or diff summary. This simple requirement reduces false completion reports and makes review easier.

## Final checklist

To connect Claude Code through Crazyrouter, install Node.js, Git, and Claude Code; set `ANTHROPIC_BASE_URL` to `https://cn.crazyrouter.com`; set `ANTHROPIC_API_KEY` to your Crazyrouter token; then launch `claude` from your project directory.

For OpenAI-compatible applications, remember that the base URL is different: `https://cn.crazyrouter.com/v1`.

After the first successful session, invest in workflow quality: create `CLAUDE.md`, learn `/compact`, use Git checkpoints, monitor usage, and reserve high-permission modes for controlled environments. That is what turns Claude Code from an interesting CLI into a dependable part of your development process.
