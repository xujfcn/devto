---
title: Claude Code with Crazyrouter: Practical Setup and Workflow Patterns
published: true
description: Daily Claude Code guide rewrite covering practical setup, API routing, and AI coding workflows.
tags: ai, claude, coding, api
canonical_url: https://github.com/xujfcn/claude-code-guide?utm_source=devto&utm_medium=article&utm_campaign=claude_code_guide_daily
---

# Claude Code with Crazyrouter: Practical Setup and Workflow Patterns

Claude Code is intentionally low-level: it gives developers a command-line, agentic coding interface without forcing a particular workflow. That flexibility is useful, but it also means teams need a clean setup, clear conventions, and a few guardrails before they rely on it for day-to-day engineering work.

This guide focuses on connecting Claude Code to Crazyrouter and then shaping a practical workflow around it: project memory, permissions, GitHub operations, MCP tools, headless automation, and multi-session development.

Repository reference: [Claude Code Guide](https://github.com/xujfcn/claude-code-guide?utm_source=devto&utm_medium=article&utm_campaign=claude_code_guide_daily)

## 1. Use the correct Crazyrouter base URL

The most common integration mistake is mixing the native Anthropic-style endpoint with the OpenAI-compatible endpoint.

Use this rule:

- Claude Code and Anthropic-native clients: `ANTHROPIC_BASE_URL=https://cn.crazyrouter.com`
- OpenAI-compatible SDKs, HTTP clients, frontend/backend apps using OpenAI-style paths: `base_url=https://cn.crazyrouter.com/v1`

Do not add `/v1` twice. If an SDK already appends `/v1/messages`, and you configure the base as `https://cn.crazyrouter.com/v1`, that is correct for OpenAI-compatible clients. But if you configure Claude Code with `https://cn.crazyrouter.com/v1`, you may end up with malformed routes such as `/v1/v1/...` depending on the client behavior.

A minimal shell setup for Claude Code looks like this:

```bash
# Claude Code / Anthropic-native client setup
export ANTHROPIC_BASE_URL="https://cn.crazyrouter.com"
export ANTHROPIC_AUTH_TOKEN="your_crazyrouter_api_token"

# Optional: verify your Claude Code installation
claude --version

# Start an interactive session
claude
```

For team usage, create a dedicated API token for Claude Code rather than reusing a personal token across scripts, local terminals, CI jobs, and experiments. Separate tokens make it easier to rotate credentials, apply model allowlists, and debug usage by project.

## 2. Add project memory with `CLAUDE.md`

Claude Code automatically reads a special file named `CLAUDE.md` when starting a conversation. Treat it as a compact operating manual for your repository.

Useful content includes:

- Build, test, lint, and type-check commands
- Important directories and entry points
- Coding style rules
- Testing expectations
- Branch naming and PR conventions
- Local environment notes
- Known pitfalls or non-obvious behavior

A good `CLAUDE.md` is short, specific, and maintained like source code. For example:

- `npm run typecheck` must pass before a PR is opened.
- Prefer targeted tests before running the full test suite.
- Use ES modules, not CommonJS.
- Do not edit generated files directly.
- API handlers live under `src/server/routes`.

You can place `CLAUDE.md` in several locations:

- Repository root: the most common team-shared option
- Parent directories: useful for monorepos
- Subdirectories: useful when different packages have different rules
- `~/.claude/CLAUDE.md`: personal defaults across all projects

Do not turn this file into a long wiki dump. Because it becomes part of the prompt context, every unnecessary paragraph competes with task-relevant code. Start small and iterate. If Claude repeatedly misses a convention, add one precise instruction and observe whether behavior improves.

## 3. Manage permissions intentionally

Claude Code asks for permission before actions that may modify your system: editing files, running many shell commands, using MCP tools, and so on. This conservative default is sensible for real codebases.

There are four common ways to manage permissions:

1. Choose “always allow” during a session when prompted.
2. Use `/permissions` inside Claude Code to add or remove allowed tools.
3. Edit `.claude/settings.json` for project-level settings or `~/.claude.json` for user-level settings.
4. Start a session with `--allowedTools` for task-specific automation.

For example, a team may allow file editing but still require approval for networked commands or destructive shell operations. Another team may allow `Bash(git commit:*)` because commits are easy to inspect and revert, but require approval for pushing branches.

Avoid starting with maximum permissions. Add permissions based on repeated friction, not convenience alone.

## 4. Install GitHub CLI for repository work

Claude Code works well with the GitHub CLI (`gh`). With it, Claude can create issues, inspect pull requests, read review comments, open PRs, and summarize repository history using familiar commands.

Typical setup:

- Install `gh`
- Run `gh auth login`
- Verify with `gh --version`
- Mention in `CLAUDE.md` that GitHub-related tasks should use `gh`

This is especially useful for workflows such as:

- “Create a PR for the current branch.”
- “Read the comments on this PR and fix the simple ones.”
- “Open an issue describing this bug.”
- “Check which commit introduced this behavior.”

Claude can use GitHub APIs or MCP servers too, but `gh` is often the simplest path because it matches how many developers already operate locally.

## 5. Add tools through shell commands and MCP

Claude Code inherits your shell environment. If you have internal scripts, CLIs, or helper functions, Claude can use them, but only if it knows they exist.

Document custom tools in `CLAUDE.md` with:

- Tool name
- Purpose
- Example usage
- Whether `--help` is reliable
- Safety notes

For richer integrations, use MCP servers. Claude Code can connect to MCP servers through project config, global config, or a checked-in `.mcp.json` file. A checked-in configuration is useful when the whole team should have access to the same tools, such as browser automation, error monitoring, or internal documentation search.

Helpful MCP commands include:

- `claude mcp list`
- `claude mcp get <server>`
- `claude mcp remove <server>`
- `/mcp` inside Claude Code to inspect status or authenticate
- `claude --mcp-debug` when troubleshooting server configuration

For cloud services that require login, Claude Code supports OAuth-style authentication flows through `/mcp` where supported by the server.

## 6. Use custom slash commands for repeatable tasks

If your team has recurring workflows, store prompt templates under `.claude/commands`. These become available from the slash command menu.

Good candidates include:

- Investigate a production error
- Fix a GitHub issue
- Prepare a migration checklist
- Review a pull request locally
- Analyze logs and suggest next actions

A slash command can accept `$ARGUMENTS`, which lets you pass an issue number, file path, or incident ID into a structured workflow. This turns “tribal knowledge” into reusable team automation without building a full internal tool.

## 7. Prefer explore-plan-code-submit

A reliable general-purpose workflow is:

1. Ask Claude to read relevant files and explain what it finds. Tell it not to write code yet.
2. Ask for a plan. For complex tasks, use words like “think”, “think hard”, or “ultrathink” to encourage deeper reasoning.
3. Review the plan and correct assumptions.
4. Ask Claude to implement the solution.
5. Run tests, linting, and type checks.
6. Ask Claude to commit and open a PR if appropriate.

The first two steps are easy to skip, but they matter. Without exploration and planning, an agentic coding tool may jump into the first plausible implementation. That can be fine for tiny changes, but it is risky for architectural work, migrations, or unfamiliar code.

## 8. Use TDD when behavior is clear

Claude Code performs well when success is measurable. If you can describe expected inputs and outputs, use a test-first loop:

1. Ask Claude to write tests only.
2. Run the tests and confirm they fail.
3. Commit the failing tests.
4. Ask Claude to implement the feature without changing the tests.
5. Iterate until tests pass.
6. Commit the implementation.

This pattern reduces ambiguity. You can also use a second Claude session to review whether the implementation overfits the tests.

## 9. Use visual feedback for UI work

For UI changes, give Claude screenshots, mockups, or image file paths. If you have Puppeteer or another browser automation MCP server, Claude can implement a change, capture a screenshot, compare it with the target, and iterate.

The first result may be acceptable, but UI work often improves after several short cycles. Make the feedback concrete: spacing, contrast, alignment, responsive behavior, and visual hierarchy.

## 10. Keep context clean

Long sessions accumulate irrelevant context. Use `/clear` between tasks to reset the conversation window. For larger work, ask Claude to create a Markdown checklist or scratchpad file and update it as it progresses.

This is useful for:

- Large lint cleanups
- Framework migrations
- Multi-step refactors
- Incident analysis
- Release preparation

A checklist keeps the work auditable and reduces the chance that Claude loses track of what has already been fixed.

## 11. Use headless mode for automation

Claude Code supports non-interactive use with `-p`. This is useful in scripts, CI jobs, pre-commit checks, and data-processing pipelines.

Common patterns:

- Pipe logs into Claude and ask for a structured summary.
- Ask Claude to review a diff for subjective issues, such as misleading names or stale comments.
- Fan out many small migration tasks from a script.
- Use `--output-format json` or `--output-format stream-json` when another tool needs to parse the result.

Headless sessions do not persist context between runs, so include the required task context every time.

Be careful with permissions in automation. If you use `--dangerously-skip-permissions`, isolate the process in a container, avoid unnecessary network access, and treat the environment as disposable.

## 12. Run multiple Claude sessions for independent tasks

For bigger projects, multiple sessions can be more effective than one long conversation.

Examples:

- One Claude writes code; another reviews it.
- One Claude writes tests; another writes the implementation.
- Separate sessions work on unrelated features in separate Git worktrees.

Git worktrees are particularly practical:

1. `git worktree add ../project-feature-a feature-a`
2. `cd ../project-feature-a && claude`
3. Repeat for independent branches.
4. Remove completed worktrees with `git worktree remove ../project-feature-a`

This keeps file changes isolated while sharing the same Git history.

## Final checklist

When integrating Claude Code with Crazyrouter, start with these basics:

- Claude Code uses `https://cn.crazyrouter.com`, not the `/v1` endpoint.
- OpenAI-compatible clients use `https://cn.crazyrouter.com/v1`.
- Create a dedicated Crazyrouter API token per project or team.
- Add a concise `CLAUDE.md` to the repository.
- Install and authenticate `gh` if your team uses GitHub.
- Add permissions gradually.
- Use planning, tests, screenshots, and checklists to make success observable.
- Use `/clear` to keep context focused.

Claude Code is most useful when treated as a programmable development partner rather than a magic terminal. Crazyrouter gives you a unified access layer; your engineering practice determines how reliable the workflow becomes.
