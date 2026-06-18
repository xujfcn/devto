---
title: Claude Code + Crazyrouter: Building a Personal Workflow with Reusable Prompt Templates
published: true
description: Daily Claude Code guide rewrite covering practical setup, API routing, and AI coding workflows.
tags: ai, claude, coding, api
canonical_url: https://github.com/xujfcn/claude-code-guide?utm_source=devto&utm_medium=article&utm_campaign=claude_code_guide_daily
---

# Claude Code + Crazyrouter: Building a Personal Workflow with Reusable Prompt Templates

Claude Code is most useful when it becomes part of your daily workflow rather than a one-off chat window. After you connect it to Crazyrouter, the next step is to reduce repeated work: prompts you rewrite every week, documents that always follow the same structure, reports that need the same sections, or operational messages that differ only by date, audience, and context.

This article focuses on a practical pattern: building a small personal template library for Claude Code, then using it to simplify recurring tasks. The examples are deliberately simple, because the goal is not to create a complicated automation platform. The goal is to make your common work easier to repeat, review, and share.

If you are following the full guide, the repository is here: [Claude Code Guide](https://github.com/xujfcn/claude-code-guide?utm_source=devto&utm_medium=article&utm_campaign=claude_code_guide_daily).

## Use the correct Crazyrouter endpoint first

Before discussing workflow design, make sure your endpoint configuration is clean. A large number of integration problems come from mixing the root endpoint and the OpenAI-compatible `/v1` endpoint.

Use this rule:

- Claude Code and Anthropic-native clients use `ANTHROPIC_BASE_URL=https://cn.crazyrouter.com`
- OpenAI-compatible SDKs, HTTP clients, backend services, and frontend applications use `base_url=https://cn.crazyrouter.com/v1`

Do not add `/v1` twice. If your SDK already appends `/v1`, and you also configure `https://cn.crazyrouter.com/v1`, you may accidentally produce paths like `/v1/v1/...`.

A minimal shell setup for Claude Code can look like this:

```bash
export ANTHROPIC_BASE_URL="https://cn.crazyrouter.com"
export ANTHROPIC_AUTH_TOKEN="YOUR_CRAZYROUTER_API_TOKEN"

# For OpenAI-compatible SDKs, use this separately:
# base_url="https://cn.crazyrouter.com/v1"
```

Keep these two endpoint styles separate in your notes, project README, and team onboarding docs. It saves debugging time later.

## Why templates matter in Claude Code

Many people start with prompts like “write a product description” or “summarize this meeting.” That works, but it is hard to keep consistent. Every time you write the prompt from memory, you change the requirements slightly. One output asks for action items, another forgets them. One summary includes owners and due dates, another does not.

Templates solve this by making the shape of the request explicit. A good template does three things:

1. It defines the input fields you need to provide.
2. It defines the output structure you expect.
3. It defines quality constraints, such as tone, length, audience, and review notes.

For developers, this is similar to extracting a function. If you repeat the same logic, parameterize it. Prompt templates are parameterized work instructions.

## Start with a small template library

Do not try to template everything on day one. Start with four or five recurring tasks that already have a predictable structure.

Useful starter templates include:

- Product description
- Release note
- Pull request review summary
- Meeting notes
- Weekly status update
- Customer support reply
- Internal announcement
- Learning notes

For each template, define placeholders in square brackets or another obvious format. For example, a product description template might ask for `[product name]`, `[target audience]`, `[key features]`, `[tone]`, and `[call to action]`.

A developer-oriented release note template could include:

- Version or date
- Added features
- Fixed bugs
- Breaking changes
- Migration notes
- Known issues
- Upgrade instructions

The important part is not the exact format. The important part is that the same task uses the same structure every time.

## Example: a meeting notes template

Meeting notes are a good first workflow because the structure is usually stable and the review process is obvious. You can ask Claude Code to create a reusable template with sections such as:

- Meeting name
- Date and time
- Participants
- Agenda
- Discussion points
- Decisions
- Action items
- Owner and deadline
- Open questions
- Next meeting

When you paste rough notes or a transcript into Claude Code, ask it to normalize the content into that structure. The generated result should not be sent automatically. Review it, confirm names and decisions, then share it.

This human-in-the-loop step matters. AI is useful for structuring, extracting, and drafting, but meeting records often become official references. Owners, deadlines, and decisions should be checked before distribution.

## Example: a learning notes template

A learning notes template is useful for engineers who read documentation, research libraries, or compare frameworks. Instead of saving scattered notes, ask Claude Code to produce a consistent document:

- Topic
- Source links
- Goal of reading
- Key concepts
- Examples
- Things to try locally
- Questions still unresolved
- Follow-up tasks

This format turns passive reading into something actionable. If you later need to explain the topic to a teammate, your notes already contain definitions, examples, and open questions.

## Simplify repeated tasks without over-automating

The source material for many AI workflow tutorials often jumps directly to automation. That can be useful, but it can also create fragile processes. A better path is:

1. Identify the repeated task.
2. Create a template.
3. Use the template manually for a while.
4. Review the output quality.
5. Automate only the stable parts.

For example, a weekly report workflow might start like this:

- Export completed tasks from your project management tool.
- Collect important incidents, blockers, and decisions.
- Paste the raw material into Claude Code.
- Ask it to generate a report using your weekly report template.
- Review the result.
- Add context that is not visible in the raw data.
- Send or archive the report.

Only after this process is stable should you consider scripting data collection or connecting it to internal systems.

## How to choose what to optimize

Not every repeated task deserves automation. Evaluate candidates using a few simple questions:

- Does the task happen often?
- Is the output format predictable?
- Is the input easy to collect?
- Is the cost of a wrong output low or reviewable?
- Would a template improve consistency even without full automation?

Good candidates usually include status reports, release notes, meeting summaries, support replies, content drafts, and data cleanup instructions. Poor candidates include highly sensitive decisions, ambiguous negotiations, or tasks where the missing context is more important than the visible text.

A useful rule: if you cannot describe the expected output clearly, do not automate it yet. First improve the process, then ask Claude Code to assist.

## Build templates as shared team assets

Once a template works for you, move it out of private chat history. Store it somewhere durable:

- A `prompts/` directory in your repository
- A team wiki
- A shared documentation site
- A versioned internal cookbook

Versioning matters. Prompt templates change like code. You may update terminology, add a required section, remove an ambiguous instruction, or adapt the tone for a new audience. Keeping templates in Git gives you history, review, and rollback.

For teams using Crazyrouter as a unified model access layer, this also makes onboarding easier. New teammates do not need to guess which endpoint to use or which prompt format is preferred. The repository can include both configuration notes and workflow templates.

## Keep prompts specific, but not brittle

A strong template is specific about the goal and flexible about the input. Avoid prompts that assume perfect data. Real work is messy: transcripts may be incomplete, CSV files may contain inconsistent labels, and project updates may include shorthand.

Add instructions such as:

- If information is missing, list it under “Open questions.”
- Do not invent dates, owners, or metrics.
- Preserve technical terms and product names.
- Separate facts from assumptions.
- Highlight items that need human confirmation.

These instructions make the output safer to review. They also reduce the risk of polished but unreliable documents.

## A practical template maintenance routine

Treat your template library like a lightweight internal tool. A simple routine is enough:

- Review frequently used templates every few weeks.
- Remove templates nobody uses.
- Add examples for templates that produce inconsistent results.
- Keep endpoint configuration near the workflow docs.
- Record known failure modes and review requirements.

For example, if your meeting notes template often misses follow-up owners, add an explicit rule: “Every action item must include an owner. If no owner is present, mark it as `Owner needed`.”

If your release notes template mixes internal implementation details with public-facing notes, split it into two templates: internal changelog and customer-facing release note.

## Final thoughts

Claude Code connected through Crazyrouter becomes more valuable when you stop treating prompts as disposable text. Reusable templates give your work structure. They reduce repeated typing, improve consistency, and make AI-assisted output easier to review.

Start with one task you already repeat this week. Turn it into a template. Use it three or four times. Adjust the fields and review rules. Then save it where you and your team can find it again.

The endpoint setup is simple but important: Claude Code uses `https://cn.crazyrouter.com`, while OpenAI-compatible clients use `https://cn.crazyrouter.com/v1`. Keep that distinction clear, and build your workflow library on top of a stable integration.
