---
title: Using Claude Code with Crazyrouter for Text Processing and Content Workflows
published: true
description: Daily Claude Code guide rewrite covering practical setup, API routing, and AI coding workflows.
tags: ai, claude, coding, api
canonical_url: https://github.com/xujfcn/claude-code-guide?utm_source=devto&utm_medium=article&utm_campaign=claude_code_guide_daily
---

# Using Claude Code with Crazyrouter for Text Processing and Content Workflows

Claude Code is often discussed as a coding assistant, but many teams also use it as a practical text workstation: drafting product copy, rewriting documentation, summarizing meeting notes, preparing campaign variants, and generating creative briefs. When your team wants to route Claude Code and model calls through a unified gateway, Crazyrouter provides a clean way to manage access while keeping endpoint configuration predictable.

This article focuses on the day-to-day text processing and content creation workflows you can run from Claude Code after connecting it to Crazyrouter. It is written for developers, product managers, technical writers, marketers, and operations teams who want reusable prompts instead of one-off chats.

Full guide repository: [claude-code-guide](https://github.com/xujfcn/claude-code-guide?utm_source=devto&utm_medium=article&utm_campaign=claude_code_guide_daily)

## 1. Configure the correct Crazyrouter endpoint

Before building text workflows, make sure your Base URL is correct. The most common mistake is mixing the root endpoint and the OpenAI-compatible `/v1` endpoint.

Use these rules:

- Claude Code / Anthropic-native clients: `ANTHROPIC_BASE_URL=https://cn.crazyrouter.com`
- OpenAI-compatible SDKs, HTTP clients, backend services, and frontend apps: `base_url=https://cn.crazyrouter.com/v1`

Do not add `/v1` twice. If your SDK already appends `/v1`, using `https://cn.crazyrouter.com/v1` in the wrong place can produce paths such as `/v1/v1/...`, which usually causes confusing request failures.

A minimal shell setup for Claude Code looks like this:

```bash
export ANTHROPIC_BASE_URL="https://cn.crazyrouter.com"
export ANTHROPIC_AUTH_TOKEN="your_crazyrouter_api_token"

# Optional: keep project-specific prompt assets together
mkdir -p .ai/prompts .ai/outputs
```

For OpenAI-compatible clients, use the endpoint exactly as documented:

- `https://cn.crazyrouter.com/v1`

For Anthropic-native Claude Code usage, use the root endpoint exactly:

- `https://cn.crazyrouter.com`

## 2. Treat content generation as a workflow, not a single prompt

The source tutorial shows many examples: product descriptions, ads, social media posts, text polishing, summaries, format conversion, brainstorming, and short stories. The practical lesson is not that one prompt can do everything. The better pattern is to split content work into repeatable stages:

1. Gather inputs
2. Define audience and goal
3. Generate structured drafts
4. Ask for variants
5. Review claims and facts
6. Adapt to channel format
7. Save reusable prompt templates

This approach is especially useful in a developer environment because you can keep prompts in version control, review changes, and reuse them across product launches or documentation cycles.

## 3. Product description generation

Product copy improves significantly when you provide structured context. Instead of asking “write a product description,” give Claude Code the same information a human copywriter would need.

Useful input fields:

- Product name
- Product category
- Main features
- Target audience
- Differentiators
- Tone
- Required length
- Claims to avoid
- Call to action

Example prompt:

> Write a product description for a smart noise-cancelling headset. Target users are young office workers. Key features: active noise cancellation, long battery life, comfortable fit, and strong audio quality. Tone: modern, clear, and slightly premium. Avoid unsupported medical or performance claims. Output: headline, short intro, feature bullets, and CTA.

This style of prompt gives Claude Code room to write while keeping the result usable. It also makes review easier because the output sections match the expected format.

For food, wellness, electronics, or finance-related copy, be careful with claims. For example, “organic,” “certified,” “safe,” “pesticide-free,” or “clinically proven” should only be used when you have supporting evidence. A safer prompt can include: “Use cautious language and mark any claims that require verification.”

## 4. Ad copy generation with variants

Claude Code is useful for generating campaign options, but the first draft should rarely be the final draft. Ask for multiple versions by angle.

For example, for a smart water bottle:

- Problem angle: busy professionals forget to drink water
- Data angle: daily hydration tracking
- Convenience angle: temperature display and reminders
- Gift angle: useful office accessory
- Minimalist angle: short copy for posters or banners

A practical prompt:

> Generate 5 ad copy variants for a smart water bottle. Features: hydration reminders, drinking volume tracking, temperature display. Audience: office workers. For each variant include: campaign headline, 40-word body copy, CTA, and suggested visual direction. Avoid health guarantees.

For promotion campaigns, include constraints:

- Event name
- Discount rules
- Start and end dates
- Eligible products
- Exclusions
- Store location or link
- Compliance wording

This prevents attractive but inaccurate copy. If a coffee shop anniversary campaign is “buy one get one free for three days,” the prompt should include whether the free drink must be equal or lower value, whether takeaway is allowed, and whether it can be combined with member discounts.

## 5. Social content generation by platform

Different channels require different output styles. A polished product description does not automatically work as a social post.

For a personal feed, ask for warmth, restraint, and a natural voice. For a recommendation-style post, ask for structure: hook, personal context, product details, pros and cons, price range, buying tips, and hashtags.

A good platform-specific instruction might be:

> Convert this product description into three social posts: one professional LinkedIn-style post, one short casual personal post, and one recommendation-style lifestyle post. Keep all claims consistent with the original source. Do not add new product specifications.

This is where Claude Code can be more convenient than a normal chat tool: you can store the original source material in a file, generate platform-specific outputs into separate files, and review the diff.

## 6. Editing, rewriting, and tone conversion

Text optimization is one of the most reliable use cases. Typical requests include:

- Make this more formal
- Make this easier for non-technical users
- Shorten while preserving meaning
- Expand with examples
- Convert into bullets
- Convert into a table
- Turn rough notes into meeting minutes
- Rewrite for a product help center

The key is to define what “better” means. “Polish this” is vague. “Rewrite for a first-time user, keep under 180 words, preserve all technical constraints, and use numbered steps” is much better.

For technical documentation, audience conversion is especially useful. A sentence like “The system uses a distributed architecture with horizontal scaling and load balancing” may be accurate but difficult for non-specialists. Claude Code can convert it into user-facing language: the service is designed to remain available when traffic grows or when one server has issues. The technical version can still remain in internal documentation.

## 7. Summaries and format conversion

Claude Code is also effective for restructuring text. Common conversions include:

- Paragraph to list
- Notes to meeting minutes
- Requirements to user stories
- Plain text to Markdown
- Product data to table
- Long document to executive summary

A useful meeting-minutes prompt:

> Convert the following rough meeting notes into professional meeting minutes. Include: meeting title, date, attendees, decisions, risks, action items, owners, and next meeting. Preserve uncertainty where the notes are unclear. Do not invent missing names or dates.

That last sentence matters. Without it, a model may fill gaps too confidently. For business documents, preserving uncertainty is often better than producing a polished but inaccurate result.

## 8. Brainstorming and creative work

Creative generation works best when you separate idea generation from selection. Ask for quantity first, then evaluate.

Example:

> Brainstorm 12 campaign ideas for a coffee brand. For each idea include: concept, target audience, participation method, required assets, risk, and why it might work. Do not write final copy yet.

Then follow up:

> Select the 3 strongest ideas for a small local team with limited budget. Explain trade-offs and suggest a 2-week execution plan.

The same pattern works for product ideas, video scripts, blog outlines, community events, and story concepts. First generate options. Then apply constraints. Then produce execution-ready assets.

## 9. Save prompt templates in your repo

If your team repeatedly creates similar content, do not rely on memory. Store prompt templates next to your project assets:

- `.ai/prompts/product-description.md`
- `.ai/prompts/meeting-minutes.md`
- `.ai/prompts/social-rewrite.md`
- `.ai/prompts/campaign-brainstorm.md`

Each template should include:

- Purpose
- Required inputs
- Output format
- Tone rules
- Claim restrictions
- Review checklist

This turns Claude Code from a casual writing assistant into a lightweight content operations tool.

## 10. Review checklist before publishing

Before publishing AI-assisted text, check:

- Are product claims supported?
- Are prices, dates, and availability correct?
- Did the model invent features?
- Is the tone appropriate for the platform?
- Are sensitive or regulated claims reviewed by a human?
- Are links and endpoints correct?
- Is the final output consistent with brand terminology?

For Crazyrouter specifically, keep endpoint configuration clean:

- Claude Code: `https://cn.crazyrouter.com`
- OpenAI-compatible usage: `https://cn.crazyrouter.com/v1`

## Final thoughts

Claude Code can handle much more than code. When connected through Crazyrouter and used with structured prompts, it becomes a useful environment for product copy, ad variants, social posts, summaries, meeting minutes, documentation rewrites, and creative planning.

The practical advantage is repeatability. Put your prompt templates in your repo, define clear input fields, review outputs carefully, and keep endpoint configuration simple. That is enough to turn everyday text work into a maintainable workflow.

Repository: [https://github.com/xujfcn/claude-code-guide](https://github.com/xujfcn/claude-code-guide?utm_source=devto&utm_medium=article&utm_campaign=claude_code_guide_daily)
