---
title: Claude Code + Crazyrouter: Practical Shortcuts and Workflow Habits for Daily Development
published: true
description: Daily Claude Code guide rewrite covering practical setup, API routing, and AI coding workflows.
tags: ai, claude, coding, api
canonical_url: https://github.com/xujfcn/claude-code-guide?utm_source=devto&utm_medium=article&utm_campaign=claude_code_guide_daily
---

# Claude Code + Crazyrouter: Practical Shortcuts and Workflow Habits for Daily Development

Keyboard shortcuts are not the most exciting part of an AI coding setup, but they are one of the easiest ways to reduce friction. When Claude Code becomes part of your daily development workflow, small delays start to matter: opening a new conversation, searching history, switching models, copying a generated command, or uploading a group of files for review.

This article focuses on two practical areas:

- Common Claude Code shortcuts and quick actions worth memorizing
- Work habits that help you use Claude Code responsibly when routed through Crazyrouter

If you are standardizing your Claude Code or Anthropic-compatible usage through Crazyrouter, the main project reference is here: [Claude Code Guide](https://github.com/xujfcn/claude-code-guide?utm_source=devto&utm_medium=article&utm_campaign=claude_code_guide_daily).

## Base URL first: avoid the `/v1/v1` mistake

Before talking about shortcuts, make sure your endpoint setup is clean. A surprising number of integration issues come from mixing the Claude/Anthropic style endpoint with the OpenAI-compatible endpoint.

Use these rules:

- Claude Code or Anthropic native clients: `ANTHROPIC_BASE_URL=https://cn.crazyrouter.com`
- OpenAI-compatible SDKs, HTTP clients, frontend/backend applications: `base_url=https://cn.crazyrouter.com/v1`

Do not append `/v1` twice. If your SDK already appends API paths internally, using `https://cn.crazyrouter.com/v1` in the wrong place can result in requests like `/v1/v1/...`.

A simple shell setup for Claude Code can look like this:

```bash
export ANTHROPIC_BASE_URL="https://cn.crazyrouter.com"
export ANTHROPIC_API_KEY="your_crazyrouter_api_token"

# For OpenAI-compatible SDKs, use this separately:
# base_url="https://cn.crazyrouter.com/v1"
```

Keep these two endpoint patterns documented in your team README. It prevents debugging sessions that have nothing to do with prompts, models, or code quality.

## Core shortcuts to remember

You do not need to memorize every shortcut on day one. Start with the ones used dozens of times per session.

### Sending and starting conversations

**Send message**

- Windows/Linux: `Ctrl + Enter`
- macOS: `Cmd + Enter`

This is the first shortcut to learn. It keeps your hands on the keyboard while you iterate on prompts, commands, and code review requests.

**New conversation**

- Windows/Linux: `Ctrl + Shift + N`
- macOS: `Cmd + Shift + N`

Use a new conversation when the task context changes. For example, do not mix a database migration review, a frontend refactor, and a release note draft in the same thread unless they are truly connected.

**Open settings**

- Windows/Linux: `Ctrl + ,`
- macOS: `Cmd + ,`

Settings are useful when you need to check model selection, permissions, appearance, or shortcut customization.

**Open help**

- Windows/Linux: `Ctrl + /`
- macOS: `Cmd + /`

This is useful for onboarding teammates. Instead of sending a long internal message, ask them to open help and learn the interface from the product itself.

## Editing shortcuts developers already know

Most editing shortcuts follow operating-system conventions:

- Copy: `Ctrl/Cmd + C`
- Paste: `Ctrl/Cmd + V`
- Cut: `Ctrl/Cmd + X`
- Select all: `Ctrl/Cmd + A`
- Undo: `Ctrl/Cmd + Z`
- Redo: `Ctrl + Y`, `Ctrl + Shift + Z`, `Cmd + Y`, or `Cmd + Shift + Z` depending on OS and app behavior

These matter because AI workflows are copy-heavy. You copy stack traces, terminal output, snippets, test failures, schemas, and generated patches. The risk is not only speed; it is also accuracy. Use select-all and copy deliberately when transferring logs or generated code, and avoid partially copied context.

## Navigation shortcuts for long sessions

AI conversations become long quickly. If you are reviewing a multi-file change or iterating on an architecture decision, navigation shortcuts save time.

Common navigation actions include:

- Scroll up: `Page Up` or `Shift + Space`
- Scroll down: `Page Down` or `Space`
- Go to top: `Home`
- Go to bottom: `End`
- Switch tab: `Ctrl/Cmd + Tab`
- Switch reverse tab direction: `Ctrl/Cmd + Shift + Tab`

For developers, the most useful habit is jumping to the bottom after reading a long answer, then immediately asking a focused follow-up. For example:

- “Show only the files that need to change.”
- “Convert this into a migration plan.”
- “List the risky assumptions.”
- “Give me the smallest safe patch.”

Shortcuts help, but good follow-up prompts are what make the session productive.

## Search shortcuts: history, files, and global context

Search is often more valuable than scrolling.

- Search conversation history: `Ctrl/Cmd + F`
- Search files: `Ctrl/Cmd + P`
- Global search: `Ctrl/Cmd + Shift + F`

Use search when you need to recover a decision from earlier in the thread: a table name, an API route, a requirement, or a rejected approach. If Claude Code is part of a larger repository workflow, file search helps you move from discussion to implementation without manually opening folders.

A useful pattern is:

1. Search for the relevant file or function.
2. Ask Claude Code to explain the current behavior.
3. Ask for a minimal change plan.
4. Apply and review the generated edits manually.

## Quick action buttons are not just for beginners

Keyboard-first users sometimes ignore UI buttons, but Claude Code quick actions are useful when precision matters.

### Chat window actions

The send button is useful when you want to review a prompt before submitting. The file upload button is better than copy-pasting long files into the chat. The code insertion button helps keep snippets formatted, which reduces interpretation errors. Text formatting tools are helpful for structured requests, especially when writing product requirements or bug reports.

### Message actions

Common message-level actions include:

- Copy a response
- Regenerate a response
- Give positive feedback
- Give negative feedback

Regeneration should not be used blindly. If the first answer was poor because your prompt was unclear, rewrite the prompt instead. If the prompt was good but the answer missed something, regenerate or ask a targeted correction question.

### Toolbar actions

Toolbar buttons usually cover:

- New conversation
- History
- Settings
- Help

For teams, the history panel is especially useful. It can help you recover prior reasoning, but you should still move final decisions into durable places: pull request descriptions, issue comments, architecture decision records, or internal documentation.

## Batch processing without losing control

Claude Code is useful for batches, but batch workflows need boundaries.

### Batch upload files

You can upload multiple files by selecting them in the file picker or dragging them into the Claude Code file area. This is useful for:

- Reviewing related source files
- Summarizing multiple logs
- Comparing configuration files
- Extracting fields from structured documents

When uploading several files, explain what each file represents. Do not assume the model will infer your business context from filenames alone.

### Batch process messages

If your interface supports selecting multiple messages, you can copy groups of messages into a document or issue. This is useful when turning a session into a work artifact. Clean it up before sharing: remove false starts, temporary ideas, and sensitive information.

### Batch execute prompt templates

For repeated tasks, use templates. Examples:

- “Review this diff for security risks, breaking changes, and missing tests.”
- “Turn this error log into likely causes and next debugging steps.”
- “Rewrite this API documentation for backend developers.”
- “Generate test cases for the following function.”

The key is to keep the template stable and vary only the input. This makes output easier to compare across tasks.

## Quick model switching

Some Claude Code setups support model selection through the toolbar or command-style input. A common pattern is:

- Use the toolbar model dropdown when you want a visible, deliberate switch.
- Use a command such as `/model [model-name]` when your environment supports it.

Examples may look like:

- `/model claude`
- `/model qwen`
- `/model wenxin`

Model availability depends on your Crazyrouter account, token permissions, and routing configuration. If your team uses multiple models, document which model is expected for coding, summarization, translation, and data transformation tasks.

## Build better AI work habits

Shortcuts improve speed. Habits improve quality.

### Treat AI as an assistant, not an owner

Claude Code can draft, explain, refactor, and summarize. It should not silently own production decisions. For important work, a human should review:

- Security implications
- Data exposure
- Licensing concerns
- Business rules
- Edge cases
- Test coverage

This is especially important when using AI to touch authentication, billing, permissions, migrations, or customer-facing workflows.

### Use AI where it fits

Good use cases include:

- Repetitive formatting
- Test generation drafts
- Code explanation
- Log analysis
- Documentation cleanup
- Brainstorming implementation options

Be careful with:

- Sensitive data
- Legal or medical judgment
- Real-time facts
- Highly ambiguous requirements
- Customer communication that requires empathy and accountability

### Iterate instead of expecting perfection

A productive Claude Code session often looks like this:

1. Provide context.
2. Ask for a plan.
3. Challenge the plan.
4. Request a minimal change.
5. Review the output.
6. Ask for tests or verification steps.
7. Apply only what you understand.

This workflow is slower than accepting the first answer, but it is safer and usually faster than debugging a bad automated change later.

## Create a prompt and workflow knowledge base

Teams should not rediscover useful prompts every week. Keep a small internal library for:

- Code review prompts
- Bug investigation prompts
- Release note templates
- Documentation rewrite prompts
- Migration planning prompts
- Test generation prompts

Include examples of both good and bad outputs. Over time, this becomes more valuable than a generic prompt list because it reflects your stack, style, and risk tolerance.

## Final checklist

If you want to make Claude Code with Crazyrouter smoother for daily development, start with this checklist:

- Configure `ANTHROPIC_BASE_URL=https://cn.crazyrouter.com` for Claude Code and Anthropic-native clients.
- Use `https://cn.crazyrouter.com/v1` only for OpenAI-compatible SDKs and HTTP clients.
- Memorize send, new conversation, settings, help, search, and tab switching shortcuts.
- Use batch uploads with clear instructions.
- Keep reusable prompt templates.
- Review AI output before applying it.
- Store final decisions outside the chat.

Shortcuts reduce mechanical friction. A clean endpoint setup prevents avoidable integration errors. Good work habits make Claude Code a reliable part of your development process instead of an unpredictable side channel.
