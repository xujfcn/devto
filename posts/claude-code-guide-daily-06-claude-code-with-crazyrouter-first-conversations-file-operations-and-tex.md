---
title: Claude Code with Crazyrouter: First Conversations, File Operations, and Text Generation
published: true
description: Daily Claude Code guide rewrite covering practical setup, API routing, and AI coding workflows.
tags: ai, claude, coding, api
canonical_url: https://github.com/xujfcn/claude-code-guide?utm_source=devto&utm_medium=article&utm_campaign=claude_code_guide_daily
---

# Claude Code with Crazyrouter: First Conversations, File Operations, and Text Generation

This guide is part 6 of a practical Claude Code + Crazyrouter series. In the previous steps, the focus was installation and configuration. This article moves into daily usage: sending your first prompt, referencing files in a VS Code workspace, asking Claude Code to edit or create files, and using it for structured text generation.

If you are using Crazyrouter as the unified model gateway, keep the endpoint rule simple:

- Claude Code and Anthropic-native clients use `ANTHROPIC_BASE_URL=https://cn.crazyrouter.com`
- OpenAI-compatible SDKs, raw HTTP requests, and front-end/back-end applications use `base_url=https://cn.crazyrouter.com/v1`

The most common configuration mistake is accidentally adding `/v1` twice, which leads to paths such as `/v1/v1/...`. Keep the two cases separate.

Project reference: [Claude Code Guide on GitHub](https://github.com/xujfcn/claude-code-guide?utm_source=devto&utm_medium=article&utm_campaign=claude_code_guide_daily)

## 1. Start your first Claude Code conversation

Once Claude Code is installed and configured, the first workflow is simple: type a request, send it, read the response, and continue with follow-up questions.

In the Claude Code interface, the input box is usually at the bottom of the chat panel. This is where you describe what you want Claude Code to do. A good first request can be very small:

- “Explain what this project does.”
- “Summarize the current README.”
- “Help me find where authentication is implemented.”
- “Review this function and suggest improvements.”

For normal chat usage:

- Press `Enter` to send the message.
- Press `Shift + Enter` to insert a new line.
- Use `Ctrl + A` on Windows/Linux or `Cmd + A` on macOS to select all text.
- Paste longer context directly into the input when needed.

Claude Code responses may include plain text, bullet lists, code blocks, tables, and proposed file edits. For longer answers, scroll through the chat history and verify the details before applying changes.

## 2. Use conversation history intentionally

Claude Code can use the current conversation as context. This is useful when you are doing iterative development:

1. Ask it to inspect a file.
2. Ask it to explain the current behavior.
3. Ask for a safer refactor plan.
4. Ask it to apply only the first step.
5. Review the diff.
6. Continue.

This is usually better than asking for a large rewrite in one prompt. Smaller steps reduce ambiguity and make review easier.

For example:

```text
@src/services/UserService.ts Please explain what this service does first. Do not edit files yet.

Now propose a refactor plan that keeps the public API unchanged.

Apply only step 1 of the plan and show me the changed files.
```

That pattern gives you control. Claude Code gets enough context, but you still decide when edits should happen.

## 3. Reference files with `@`

One of the most useful Claude Code features is direct access to files in your VS Code workspace. You do not need to upload files manually. Claude Code can read and operate on files that are already part of the current workspace.

The most accurate way to reference a file is the `@` symbol. Type `@` in the input box and select a file from the list. You can usually filter by typing part of the file name.

Examples:

- `@package.json Explain the scripts and dependencies.`
- `@src/components/Header.tsx Review this component for accessibility issues.`
- `@README.md Update the installation section for the current setup.`
- `@config.yaml Check whether this configuration is valid.`

You can also reference multiple files:

- `@src/api/user.ts @src/api/order.ts Compare their error handling patterns.`
- `@Button.tsx @Button.test.tsx Add missing test cases for disabled state.`

When a repository is large, be specific. Referencing the exact file is better than saying “check the project” unless you want a broad search.

## 4. Use paths when you already know the location

If you already know the file path, you can mention it directly:

- “Please inspect `src/utils/formatDate.ts` and simplify the date formatting logic.”
- “Find the login handler under `src/routes` and explain how validation works.”
- “Update `docs/setup.md` to include the Crazyrouter endpoint configuration.”

Use `/` as the path separator in prompts, even on Windows. It keeps examples portable and avoids escaping issues.

## 5. Common file operations

Claude Code can help with several file-level tasks. The important part is to state the expected outcome and any constraints.

### Read and explain

Use this when entering an unfamiliar codebase:

- `@src/main.ts Explain the application startup flow.`
- `@vite.config.ts Explain each configuration option.`
- `@schema.prisma Summarize the data model and relationships.`

### Edit existing files

Be explicit about what should change:

- `@app.js Replace console.log calls with logger.info, but do not change error handling.`
- `@PaymentService.ts Add input validation before creating a payment.`
- `@Header.tsx Improve keyboard navigation without changing the visual layout.`

After edits, always review the diff. Claude Code can accelerate the work, but you are still responsible for correctness, security, and style.

### Create new files

You can ask Claude Code to create files with a specific path and purpose:

- “Create `src/components/Button.tsx` with a typed React button component.”
- “Create `src/lib/crazyrouterClient.ts` that exports a configured client.”
- “Create `docs/troubleshooting.md` for common setup issues.”

When creating files, include framework, language, style, and export requirements if they matter.

### Rename or delete files

For destructive operations, be extra careful. Ask Claude Code to explain the impact first:

- “Find references to `old-name.ts` before renaming it.”
- “List what imports will break if `legacyClient.ts` is removed.”
- “Delete `test-old.js` only if it is not referenced anywhere.”

This reduces the chance of removing something that is still used.

## 6. Batch operations and project-wide searches

Claude Code is useful for repetitive cleanup across a directory, but large changes should be staged.

Examples:

- “Search the project for all `TODO` comments and group them by file.”
- “Find all usages of `calculateTotal` and explain the call sites.”
- “In `src/api`, add request timeout handling to each client file, one file at a time.”
- “Find all React components that use `useState` and identify which ones may need `useReducer`.”

For broad edits, ask for a plan before applying changes:

1. Search and list affected files.
2. Explain the intended change.
3. Apply changes to one representative file.
4. Review the diff.
5. Continue with the remaining files.

This workflow works well for migrations, logging updates, type annotations, lint fixes, and documentation cleanup.

## 7. File types Claude Code can handle well

Claude Code is strongest with code and text files. Typical examples include:

- Code: `.js`, `.ts`, `.jsx`, `.tsx`, `.py`, `.go`, `.rs`, `.java`, `.php`, `.c`, `.cpp`
- Config: `.json`, `.yaml`, `.yml`, `.toml`, `.ini`, `.xml`
- Docs and text: `.md`, `.txt`, `.csv`
- Styles: `.css`, `.scss`, `.sass`, `.less`

For code files, ask for reviews, bug fixes, refactors, tests, comments, and migration steps. For config files, ask it to validate structure, explain options, or update values. For Markdown, ask for clearer instructions, better headings, or missing troubleshooting notes.

## 8. Practical text generation inside Claude Code

Claude Code is not only for code. It can also help generate and revise text related to engineering work:

- README sections
- release notes
- pull request descriptions
- onboarding docs
- API usage examples
- meeting summaries
- task checklists
- incident notes

For better output, include audience, format, length, and tone.

Weak prompt:

- “Write documentation.”

Better prompt:

- “Update `README.md` with a 5-step setup guide for a backend developer. Include prerequisites, environment variables, how to run tests, and common endpoint mistakes. Keep the tone concise.”

You can also ask for transformations:

- “Rewrite this paragraph in a more formal tone.”
- “Shorten this release note to 120 words.”
- “Turn this meeting note into action items with owners and due dates.”
- “Summarize this issue thread and list unresolved questions.”

## 9. Crazyrouter endpoint checklist

When connecting Claude Code through Crazyrouter, keep this checklist nearby:

- Use a dedicated API token for the project or team.
- For Claude Code or Anthropic-native clients, set `ANTHROPIC_BASE_URL` to `https://cn.crazyrouter.com`.
- For OpenAI-compatible SDKs and HTTP clients, use `https://cn.crazyrouter.com/v1` as the base URL.
- Do not append `/v1` to both the base URL and the request path.
- If calls fail, inspect logs first, then verify the endpoint and token.
- Keep secrets out of source control.

Example environment shape:

```bash
# Claude Code / Anthropic-native clients
export ANTHROPIC_BASE_URL="https://cn.crazyrouter.com"
export ANTHROPIC_API_KEY="your_api_token_here"

# OpenAI-compatible SDKs should use:
# base_url="https://cn.crazyrouter.com/v1"
```

## 10. A safe beginner workflow

If you are just starting with Claude Code, use this repeatable loop:

1. Open the project in VS Code.
2. Ask Claude Code to explain one file, not the whole repository.
3. Reference files with `@` whenever possible.
4. Ask for a plan before edits.
5. Apply small changes.
6. Review the diff.
7. Run tests or local checks.
8. Continue with the next change.

Claude Code becomes more useful when your prompts are specific and your workflow is review-driven. Crazyrouter helps centralize model access, but the development habits stay the same: clear context, small steps, readable diffs, and careful validation.
