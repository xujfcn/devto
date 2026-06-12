---
title: Getting Started with Claude Code via Crazyrouter: Trae, Z Code, and Base URL Setup
published: true
description: Daily Claude Code guide rewrite covering practical setup, API routing, and AI coding workflows.
tags: ai, claude, coding, api
canonical_url: https://github.com/xujfcn/claude-code-guide?utm_source=devto&utm_medium=article&utm_campaign=claude_code_guide_daily
---

## Getting Started with Claude Code via Crazyrouter

Claude Code is useful because it lets you work with a project through natural language: ask it to explain a file, draft a refactor plan, generate tests, or edit code after approval. For many teams, the next practical question is not whether Claude Code is helpful, but how to make access predictable across local machines, IDEs, scripts, and internal tools.

This guide shows a beginner-friendly setup path for using Claude Code with Crazyrouter, with two interface options: Trae as a VS Code-like IDE and Z Code as a lightweight AI coding workspace. It also covers the most common configuration mistake: mixing the root Anthropic-style endpoint with the OpenAI-compatible `/v1` endpoint.

Project reference: [Claude Code Guide](https://github.com/xujfcn/claude-code-guide?utm_source=devto&utm_medium=article&utm_campaign=claude_code_guide_daily)

## The two endpoint rules you should remember

Crazyrouter exposes different entry points depending on the client style you are using:

- Claude Code or Anthropic-native clients use: `https://cn.crazyrouter.com`
- OpenAI-compatible SDKs, direct HTTP calls, and frontend/backend apps use: `https://cn.crazyrouter.com/v1`

Keep these endpoints unchanged in code and configuration. Do not add tracking parameters to API endpoints.

The most common failure mode is accidentally producing a duplicated path such as `/v1/v1/...`. This usually happens when an SDK already appends `/v1`, but the configured base URL also includes `/v1`, or when a Claude Code configuration receives the OpenAI-compatible endpoint instead of the root endpoint.

A minimal shell-style configuration looks like this:

```bash
# Claude Code / Anthropic-native clients
export ANTHROPIC_BASE_URL="https://cn.crazyrouter.com"
export ANTHROPIC_API_KEY="your_crazyrouter_token"

# OpenAI-compatible SDKs or custom HTTP integrations
export OPENAI_BASE_URL="https://cn.crazyrouter.com/v1"
export OPENAI_API_KEY="your_crazyrouter_token"
```

Use a dedicated API token for local development instead of sharing one token across the whole team. If you later need separate permissions for projects, environments, or model allowlists, this habit will save time.

## What is Claude Code?

Claude Code is an AI coding assistant that works inside a developer workflow. You can use it to inspect a repository, discuss design choices, generate code, write tests, summarize errors, or propose step-by-step implementation plans.

The key point is that Claude Code is not only for senior programmers. A non-specialist can still use it effectively because the interaction model is conversational. Instead of memorizing a complex command sequence, you can describe the task: “Find where the login form validates email,” “Explain this error,” or “Draft a safe plan to migrate this function.”

For developers, the benefit is deeper: Claude Code can work against project context and can help reduce the overhead of repetitive inspection, documentation, and test-writing tasks. You still need to review its output, especially before editing production code, but it can make the first pass faster and more structured.

## Option 1: Use Claude Code in Trae

Trae is a VS Code-like editor that supports a familiar extension workflow. If you already know VS Code, the UI, shortcuts, sidebar layout, and extension marketplace concepts will feel familiar.

A typical installation flow is straightforward:

1. Download the Trae installer for your operating system.
2. Choose the Windows, macOS Intel, or macOS Apple Silicon version as appropriate.
3. Install it using the normal system installer flow.
4. Launch Trae and open a project folder.
5. Open the extensions view with `Ctrl+Shift+X` on Windows/Linux or `Cmd+Shift+X` on macOS.
6. Search for Claude Code and install the extension.

After installation, you should see a Claude Code icon in the editor interface. Click it to open the chat panel. From there you can start a conversation, reference files, and ask for code-oriented help.

If the plugin asks for account or API configuration, make sure you understand which endpoint field it expects. For Claude Code or Anthropic-native configuration, the base URL should be the root endpoint:

`https://cn.crazyrouter.com`

Do not use `https://cn.crazyrouter.com/v1` for Claude Code’s Anthropic-style base URL field.

## Understanding the Claude Code interface

Most Claude Code interfaces share a few common areas.

The chat input is where you describe the task. It usually supports multi-line prompts and pasted content. Treat this input as a work order: include what you want, the relevant constraints, and how cautious the assistant should be.

The conversation history is your working log. It shows what you asked, what Claude answered, and sometimes system messages or tool-related events. When debugging a bad response, scroll up and inspect whether the model had enough context.

Slash commands are often available through `/`. They may include conversation operations, file references, configuration options, and workflow commands. File selection is often exposed through `@`, making it easier to attach specific project files instead of pasting large blocks manually.

Execution modes matter. In many Claude Code workflows, you can choose between planning, asking before edits, and automatic editing:

- Plan mode: good for design reviews, migrations, and complex tasks.
- Ask before edits: useful for normal development because you can approve changes before they are applied.
- Edit automatically: faster, but should be used carefully, especially in shared repositories.

For a new project, start with planning or ask-before-edit mode. Once you trust the workflow and have version control in place, you can decide where automatic edits are appropriate.

## Option 2: Use Z Code for a lighter AI coding workflow

Z Code is a lightweight AI collaboration tool from Crazyrouter. Its purpose is to make command-line AI coding tools easier to access through a visual desktop interface. Instead of manually managing several CLI tools, you can work from a unified UI and configure tools such as Claude Code, Codex-style assistants, or Gemini-style assistants depending on what the current version supports.

This makes Z Code a good option for developers who want to try AI coding workflows without immediately adopting a full IDE. It is also useful when you want a focused place for AI-assisted planning, generation, and review.

A typical setup looks like this:

1. Install Z Code for Windows or macOS.
2. Launch the editor.
3. Open the settings or AI tool configuration area.
4. Add Claude Code as a tool.
5. Enter your API key.
6. Save and test the connection.

Because Z Code has been described as Alpha-stage software, you should treat it as a productivity tool rather than the only source of truth for your project. Keep code in Git, commit frequently, and avoid applying large generated changes without review.

## Trae vs Z Code: which should you choose?

Choose Trae if you want a full IDE-like environment. It is a better fit when you need a familiar editor, project navigation, extensions, terminal access, and long-running development work. VS Code users will likely adapt quickly.

Choose Z Code if you want a lighter interface centered on AI coding tools. It is a better fit for quick experiments, prototype work, or users who prefer a visual wrapper around multiple AI assistants.

The practical difference is workflow depth. Trae is closer to a complete development environment. Z Code is closer to a focused AI collaboration surface. Neither choice removes the need for code review, tests, and version control.

## A simple first workflow

Once the tool is configured, do not begin with “write the whole feature.” Start with a smaller, reviewable task:

1. Open a real project folder.
2. Ask Claude Code to summarize the repository structure.
3. Ask it to identify the files related to one feature.
4. Ask for an implementation plan, not code.
5. Review the plan and correct assumptions.
6. Ask for a small patch.
7. Run tests or manually verify the result.
8. Commit the change if it is acceptable.

This pattern keeps the assistant grounded. It also makes failures easier to diagnose because each step has a clear purpose.

## Troubleshooting checklist

If the connection fails, check these items before changing multiple settings at once:

- Is the API token valid and copied without extra spaces?
- Is Claude Code using `https://cn.crazyrouter.com` rather than the `/v1` endpoint?
- Is your OpenAI-compatible SDK using `https://cn.crazyrouter.com/v1`?
- Did the final request path accidentally become `/v1/v1/...`?
- Are proxy, firewall, or network settings interfering with the request?
- Are you using separate tokens for local testing and shared environments?

Most setup problems come from endpoint mismatch or token handling. Fix those first.

## Final notes

Claude Code is easiest to adopt when you keep the setup boring: one token per project or developer, the correct endpoint for the client type, and a cautious editing workflow. Trae is a strong choice if you want a VS Code-like IDE experience. Z Code is worth trying if you want a lighter visual interface for AI coding tools.

The important rule is simple: Claude Code and Anthropic-native clients use `https://cn.crazyrouter.com`; OpenAI-compatible SDKs and direct app integrations use `https://cn.crazyrouter.com/v1`. Once that distinction is clear, the rest of the workflow becomes much easier to standardize.
