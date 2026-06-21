---
title: Using Programming Thinking with Claude Code and Crazyrouter: Decompose, Automate, Iterate
published: true
description: Daily Claude Code guide rewrite covering practical setup, API routing, and AI coding workflows.
tags: ai, claude, coding, api
canonical_url: https://github.com/xujfcn/claude-code-guide?utm_source=devto&utm_medium=article&utm_campaign=claude_code_guide_daily
---

# Using Programming Thinking with Claude Code and Crazyrouter: Decompose, Automate, Iterate

Programming thinking is not only about writing code. It is a practical way to turn vague, messy work into small, testable steps. When you combine that habit with Claude Code and a unified model gateway such as Crazyrouter, you can move from “asking AI for help” to building repeatable workflows.

This article focuses on three things:

- What programming thinking means in daily technical and operational work
- How to break complex problems into manageable tasks
- How to design AI-assisted workflows with Claude Code through Crazyrouter

If you are setting up Claude Code with Crazyrouter, the full guide is available here: [Claude Code Guide](https://github.com/xujfcn/claude-code-guide?utm_source=devto&utm_medium=article&utm_campaign=claude_code_guide_daily).

## The endpoint rule: avoid `/v1/v1` mistakes

Before discussing workflows, keep the integration boundary clear.

Claude Code and Anthropic-native clients should use the root endpoint:

`https://cn.crazyrouter.com`

OpenAI-compatible SDKs, direct HTTP calls, and frontend/backend applications should use the v1 endpoint:

`https://cn.crazyrouter.com/v1`

Do not add tracking parameters to API endpoints. Also, do not accidentally configure `https://cn.crazyrouter.com/v1` in a client that already appends `/v1`, or you may end up calling `/v1/v1/...`.

A typical Claude Code environment setup looks like this:

```bash
export ANTHROPIC_BASE_URL="https://cn.crazyrouter.com"
export ANTHROPIC_AUTH_TOKEN="your_crazyrouter_api_token"

# For OpenAI-compatible SDKs, use this instead in that SDK's base_url setting:
# base_url="https://cn.crazyrouter.com/v1"
```

Keep separate tokens for projects or teams where possible. It makes permission management, cost review, and incident handling much easier.

## What is programming thinking?

Programming thinking is a structured problem-solving approach. It usually includes four habits:

1. **Decomposition**: split a large problem into smaller pieces.
2. **Pattern recognition**: identify repeated structures or recurring tasks.
3. **Abstraction**: turn specific examples into reusable rules, templates, or functions.
4. **Algorithm design**: decide the exact order of operations and validation points.

This does not require every person on the team to become a software engineer. It simply means treating work as a process that can be inspected, improved, and partially automated.

For example, writing meeting notes can be decomposed into: collect raw notes, identify decisions, extract action items, assign owners, draft the summary, review, and send. Once you see the pattern, you can create a template. Once you have a template, Claude Code can help convert messy notes into a consistent format. The final review still belongs to a human, but the repetitive middle steps become faster and less error-prone.

## Why non-programmers benefit from it

Teams often describe work as “too complicated to automate.” In many cases, the issue is not complexity. The issue is that the workflow has never been made explicit.

Programming thinking helps because it forces you to answer concrete questions:

- What is the input?
- What output do we expect?
- What steps are always required?
- Which steps vary by case?
- What can be validated automatically?
- Where is human judgment required?

Once those questions are answered, Claude Code becomes more useful. Instead of sending a broad prompt like “write my weekly report,” you can give the model a structured task: read these project notes, classify completed work, identify blockers, draft next-week priorities, and mark uncertain items for review.

## Decomposing complex problems

A good decomposition should satisfy three conditions.

### 1. Independence

Each subtask should be as independent as possible. If every step depends on every other step, the workflow will be hard to automate and hard to debug.

For example, in a customer management system, avoid starting with “build CRM.” Break it into requirements, data model, authentication, customer records, order history, reporting, permissions, and tests.

### 2. Operability

Each subtask should be specific enough that someone can do it. “Improve customer experience” is not operable. “Collect the top 20 support tickets from last month and classify them by root cause” is operable.

Claude Code works better with operable tasks because the instructions are easier to verify.

### 3. Verifiability

Every step should have a completion signal. Examples:

- A CSV file is normalized.
- A report contains required sections.
- A pull request includes tests.
- A support article answers a specific question.
- A data analysis flags missing values.

Verifiability is what turns AI output from a one-off draft into part of a workflow.

## Three decomposition methods

### Top-down

Start with the whole problem and repeatedly split it into smaller pieces. This works well for product planning, system design, documentation, and operational redesign.

Example:

- Build customer feedback system
  - Requirements analysis
  - Architecture design
  - Backend development
  - Frontend development
  - Testing and launch
  - Monitoring and improvement

### Bottom-up

Start from existing tasks and group them into larger processes. This is useful when the team already has many manual actions but no clear workflow.

Example:

- Export support tickets
- Copy ticket summaries into a spreadsheet
- Tag ticket categories manually
- Write weekly support summary
- Send to product team

These tasks can become a repeatable “support insight pipeline.”

### Hybrid

In practice, hybrid decomposition is often best. Start top-down to define the target, then inspect actual tasks bottom-up to avoid designing a workflow that ignores reality.

## Identifying the core elements

When a problem feels unclear, use lightweight analysis methods.

### 5W1H

Ask:

- **What** is the problem?
- **Why** does it happen?
- **Who** is involved?
- **When** does it occur?
- **Where** does it occur?
- **How** can it be addressed?

For example, if sales performance declines, do not jump directly to “generate more content.” First identify whether the issue is lead quality, product positioning, sales process, pricing, onboarding, or customer retention.

### Dependency mapping

List tasks and mark dependencies. This is especially important when Claude Code is used inside engineering or documentation workflows.

Example for a product launch:

1. Market research has no dependency.
2. Product development depends on research.
3. Customer testing depends on a working product.
4. Sales training depends on positioning and messaging.
5. Launch depends on product readiness, testing, messaging, and support materials.

This prevents AI-assisted speed from creating coordination problems. Fast drafts do not help if they are produced in the wrong order.

## Designing an AI-assisted solution

A solution should specify tools, steps, risks, and review points.

Consider a weekly sales report workflow.

### Inputs

- Sales export
- CRM export
- Marketing campaign data
- Notes from sales managers

### Processing steps

1. Normalize files in a spreadsheet or script.
2. Use Claude Code to identify trends, anomalies, and open questions.
3. Generate a draft report from a fixed template.
4. Add charts from the spreadsheet or BI tool.
5. Human reviewer checks numbers, conclusions, and sensitive wording.
6. Send and archive the final report.

### Risks

- Data formats may change.
- Some fields may be missing.
- The model may overstate a conclusion.
- The report may need team-specific context.

### Controls

- Use a stable schema for exports.
- Add validation checks before prompting the model.
- Ask Claude Code to mark uncertain claims instead of guessing.
- Keep final approval with a human owner.

The goal is not to remove people from the process. The goal is to move people away from repetitive formatting and toward judgment, prioritization, and communication.

## Prompting with programming thinking

A useful prompt mirrors the decomposition. Instead of a vague request, provide role, input, steps, output format, and validation rules.

For example:

- Role: act as an operations analyst.
- Input: raw meeting notes and task updates.
- Steps: classify decisions, action items, blockers, and follow-ups.
- Output: Markdown meeting summary.
- Validation: mark missing owners as `TBD`; do not invent dates.

This style works well with Claude Code because it makes the model’s task explicit and testable.

## Practical workflow pattern

A reusable pattern looks like this:

1. **Define the outcome**: what should exist after the workflow runs?
2. **Collect inputs**: files, notes, exports, code, logs, tickets.
3. **Normalize inputs**: remove noise, align formats, check required fields.
4. **Ask Claude Code for structured transformation**: summary, classification, refactor, report, test plan.
5. **Validate output**: compare against requirements and known facts.
6. **Store the result**: commit to Git, save to a knowledge base, or send through the normal channel.
7. **Improve the template**: update prompts, schemas, and review checklists.

This is programming thinking applied to AI collaboration: small steps, clear contracts, repeatable outputs.

## Where Crazyrouter fits

Crazyrouter is useful when a team wants a unified access layer for Claude Code, Anthropic-native clients, OpenAI-compatible SDKs, and applications. The key is to standardize configuration without mixing endpoint conventions.

Use:

- `ANTHROPIC_BASE_URL=https://cn.crazyrouter.com` for Claude Code and Anthropic-native clients
- `base_url=https://cn.crazyrouter.com/v1` for OpenAI-compatible SDKs and HTTP integrations

Once the access layer is stable, teams can focus on workflow design instead of endpoint debugging.

## Closing advice

Start with one small workflow. Meeting notes, weekly reports, support ticket summaries, release notes, and documentation cleanup are good candidates. Do not automate everything at once. Make the process visible, decompose it, add Claude Code where it fits, validate the result, then iterate.

Programming thinking is valuable because it gives AI collaboration structure. With a clear workflow and correct Crazyrouter configuration, Claude Code becomes less of a chat box and more of a practical development assistant.
