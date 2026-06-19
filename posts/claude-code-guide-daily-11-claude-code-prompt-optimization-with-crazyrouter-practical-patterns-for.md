---
title: Claude Code Prompt Optimization with Crazyrouter: Practical Patterns for Clearer Requests
published: true
description: Daily Claude Code guide rewrite covering practical setup, API routing, and AI coding workflows.
tags: ai, claude, coding, api
canonical_url: https://github.com/xujfcn/claude-code-guide?utm_source=devto&utm_medium=article&utm_campaign=claude_code_guide_daily
---

# Claude Code Prompt Optimization with Crazyrouter: Practical Patterns for Clearer Requests

When you connect Claude Code through Crazyrouter, the integration details matter: base URLs, tokens, model routing, and logging all affect whether your workflow is stable. But after the setup works, the next bottleneck is usually not the endpoint. It is the prompt.

A vague request such as “fix this” or “write a document” forces the model to guess your intent. Claude Code may still produce something useful, but you will spend more time correcting assumptions. A clear prompt, on the other hand, gives the model enough context to act like a capable collaborator: it knows the goal, the constraints, the expected output, and the audience.

This article focuses on practical prompt optimization for developers using Claude Code with Crazyrouter. It is written for day-to-day coding, documentation, data review, and team workflows rather than one-off demos.

For the full guide repository, see: [claude-code-guide](https://github.com/xujfcn/claude-code-guide?utm_source=devto&utm_medium=article&utm_campaign=claude_code_guide_daily).

## Endpoint reminder before prompting

Keep the endpoint rules simple:

- Claude Code and Anthropic-native clients use `ANTHROPIC_BASE_URL=https://cn.crazyrouter.com`
- OpenAI-compatible SDKs, HTTP calls, backend services, and frontend apps use `base_url=https://cn.crazyrouter.com/v1`

Do not append `/v1` twice. A common failure mode is accidentally producing paths like `/v1/v1/messages`. If Claude Code is using the Anthropic-compatible path, keep the base URL at `https://cn.crazyrouter.com`. If your code uses an OpenAI-compatible client, use `https://cn.crazyrouter.com/v1`.

Once that is correct, prompt quality becomes the highest-leverage improvement.

## The core rule: remove guessing

A good Claude Code prompt reduces ambiguity. It tells the model:

1. What you want done
2. What input it should use
3. What constraints it must follow
4. What output format you expect
5. What context matters
6. What should not be changed

For development work, this is especially important because the model may otherwise over-edit files, introduce unnecessary abstractions, or answer at the wrong level of detail.

Compare these two prompts:

Bad:

“Improve this API.”

Better:

“Review the Express API route in `routes/orders.ts`. Identify security, validation, and error-handling issues. Do not rewrite the whole file yet. Return a Markdown table with columns: issue, severity, file/line reference, suggested fix.”

The second prompt gives scope, task type, constraints, and output format. It also prevents Claude Code from jumping straight into implementation before you have reviewed the plan.

## Be specific about the task type

Developers often ask Claude Code for help with broad verbs: build, fix, improve, analyze, clean up, document. These verbs are useful, but they need a target.

Instead of:

“Fix the login bug.”

Try:

“Investigate why users are redirected back to `/login` after successful OAuth callback. Start by reading `auth/callback.ts`, `middleware.ts`, and session cookie handling. Explain the likely cause before editing files.”

Instead of:

“Refactor this module.”

Try:

“Refactor `billingService.ts` to reduce duplicated invoice validation logic. Keep public function names unchanged. Do not change database schema or API response shape. After changes, summarize what was moved and why.”

Instead of:

“Write tests.”

Try:

“Add unit tests for `calculateDiscount()` covering percentage discounts, fixed discounts, zero values, expired coupons, and invalid coupon types. Use the existing test style in `billing.test.ts`.”

A precise prompt does not need to be long. It needs to make the important decisions explicit.

## Provide enough context, but not everything

Claude Code can inspect project files, but you should still explain the business context when it affects the implementation.

For example, if you ask for pagination, the technical implementation depends on the product requirement:

- Is stable ordering required?
- Is cursor pagination preferred over offset pagination?
- Should deleted records appear?
- Is the response consumed by a mobile app?
- Are there compatibility constraints for existing clients?

A better request might be:

“Add cursor pagination to the transaction list endpoint. This endpoint is used by a mobile app, so keep the existing JSON fields and add optional `nextCursor`. Sort by `createdAt` descending, then `id` descending for stable ordering. Do not remove offset pagination yet.”

That tells Claude Code not only what to implement, but also why certain choices matter.

## Specify output format

If you want a plan, ask for a plan. If you want a patch, ask for a patch. If you want a table, ask for a table.

Useful output formats include:

- Markdown checklist
- Risk table
- Step-by-step migration plan
- Unified diff
- JSON object
- Test matrix
- Release note
- Pull request description

For example:

```markdown
Task: Review the authentication middleware before I merge it.

Context:
- Framework: Next.js
- Files to inspect: middleware.ts, lib/session.ts, app/api/auth/*
- Main concern: users should not access /dashboard without a valid session

Output format:
| Area | Finding | Risk | Suggested fix |
|------|---------|------|---------------|

Rules:
1. Do not edit files yet.
2. Point out assumptions separately.
3. If you need more information, list questions at the end.
```

This template is effective because it separates task, context, format, and rules. It also prevents the model from modifying files before the review is complete.

## Use examples to define style

Examples are one of the fastest ways to improve output quality. If you want documentation in a specific tone, paste a short sample. If you want a commit message format, show one. If you want an API response shape, provide a minimal example.

For documentation, instead of saying:

“Make it developer friendly.”

Say:

“Use this style: short paragraphs, direct explanations, command examples, and a troubleshooting section. Avoid marketing language. Assume the reader is a backend developer integrating the API for the first time.”

For code generation, instead of saying:

“Follow our style.”

Say:

“Follow the style in `userService.ts`: small pure helper functions, early returns, typed errors, no default exports.”

This reduces the need for multiple correction rounds.

## Break complex work into stages

Claude Code is more reliable when complex work is split into inspect, plan, implement, verify, and document stages.

A useful flow:

1. Ask it to inspect relevant files and summarize the current behavior.
2. Ask it to propose a minimal implementation plan.
3. Review the plan.
4. Ask it to implement only the approved changes.
5. Ask it to run or suggest tests.
6. Ask it to write a concise summary for your pull request.

This approach is especially useful for migrations, auth changes, payment flows, and data model changes.

Example staged request:

“First, inspect the current invoice creation flow. Do not edit files. Return: current flow, files involved, risks, and a proposed plan. After I approve the plan, we will implement it.”

This avoids a common problem: the model makes a plausible but risky change before you understand the impact.

## Give specific feedback during iteration

Iteration is normal. The important part is to give actionable feedback.

Weak feedback:

“This is not good.”

Better feedback:

“The implementation is too broad. Keep the existing API response unchanged, remove the new abstraction layer, and only add validation for missing `customerId` and invalid `amount`. Then update the existing tests rather than adding a new test file.”

For writing tasks:

“The introduction is too formal. Rewrite it for experienced developers. Keep the code example, remove the product claims, and add a troubleshooting paragraph about incorrect base URLs.”

For data analysis:

“The summary is useful, but I need the output grouped by region and product. Add a table with totals and percentages. Do not infer causes unless the data supports them.”

Good feedback tells Claude Code what to keep, what to remove, and what to change.

## Add constraints explicitly

Constraints are not a burden. They are part of the specification.

Common constraints for developer prompts:

- Do not change public APIs
- Do not modify database schema
- Keep backward compatibility
- Use existing libraries only
- Avoid adding dependencies
- Do not edit generated files
- Keep the solution minimal
- Prefer readability over cleverness
- Return questions before making assumptions
- Include tests for changed behavior

For Crazyrouter-related work, include endpoint constraints when relevant:

“Use `ANTHROPIC_BASE_URL=https://cn.crazyrouter.com` for Claude Code examples. Use `base_url=https://cn.crazyrouter.com/v1` only for OpenAI-compatible SDK examples. Do not add tracking parameters to API endpoints.”

That final instruction matters when you are generating documentation or sample code.

## Build a reusable prompt library

Teams should not rewrite their best prompts every day. Create a small internal prompt library for repeated tasks:

- Code review prompt
- Bug investigation prompt
- Test generation prompt
- API documentation prompt
- Release note prompt
- Incident summary prompt
- Data cleanup prompt
- Migration planning prompt

Store prompts near your engineering docs or in the repository. Keep them short, versioned, and easy to copy. When a prompt consistently produces useful results, promote it into the shared library. When it causes confusion, revise it.

A practical prompt library entry should include:

- When to use it
- The prompt template
- Required inputs
- Optional inputs
- Expected output
- Known limitations

This turns prompt writing from personal habit into team infrastructure.

## A practical checklist

Before sending a prompt to Claude Code, ask:

- Did I name the files, modules, or feature area?
- Did I explain the desired outcome?
- Did I state what must not change?
- Did I specify whether I want analysis or implementation?
- Did I define the output format?
- Did I include relevant constraints?
- Did I ask for assumptions or questions when needed?

If the answer is no, refine the prompt before running a large task.

## Closing thoughts

Prompt optimization is not about finding magic words. It is about writing better task specifications. Claude Code works best when you treat it like a capable developer who still needs context, constraints, examples, and review.

With Crazyrouter handling unified model access, you can standardize the integration layer and then focus on improving the quality of requests your team sends. Clear prompts reduce rework, make reviews easier, and help Claude Code produce outputs that fit your actual workflow.
