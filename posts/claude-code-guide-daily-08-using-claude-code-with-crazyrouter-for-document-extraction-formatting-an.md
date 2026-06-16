---
title: Using Claude Code with Crazyrouter for Document Extraction, Formatting, and Batch Processing
published: true
description: Daily Claude Code guide rewrite covering practical setup, API routing, and AI coding workflows.
tags: ai, claude, coding, api
canonical_url: https://github.com/xujfcn/claude-code-guide?utm_source=devto&utm_medium=article&utm_campaign=claude_code_guide_daily
---

# Using Claude Code with Crazyrouter for Document Extraction, Formatting, and Batch Processing

Document-heavy work is one of the most practical places to use Claude Code. Long reports, meeting notes, contracts, invoices, design specs, course material, and internal policies all contain useful information, but developers and operators often lose time copying, summarizing, reformatting, and validating them by hand.

This article shows how to use Claude Code with Crazyrouter as a unified access layer for document management workflows. The focus is not on replacing review or approval processes. Instead, the goal is to make extraction, formatting, and batch document operations more repeatable.

Main repository for the series: [claude-code-guide](https://github.com/xujfcn/claude-code-guide?utm_source=devto&utm_medium=article&utm_campaign=claude_code_guide_daily)

## Base URL rules when using Crazyrouter

Before building document workflows, get the endpoint configuration right. Most integration issues come from mixing root endpoints and OpenAI-compatible `/v1` endpoints.

Use these rules:

- Claude Code and Anthropic-native clients use: `https://cn.crazyrouter.com`
- OpenAI-compatible SDKs, HTTP clients, backend services, and frontend apps use: `https://cn.crazyrouter.com/v1`

Do not append `/v1` twice. If your SDK already adds API paths internally and you configure the wrong base URL, you can accidentally produce requests such as `/v1/v1/...`.

A minimal environment setup for Claude Code looks like this:

```bash
export ANTHROPIC_BASE_URL="https://cn.crazyrouter.com"
export ANTHROPIC_API_KEY="your_crazyrouter_api_token"

# For OpenAI-compatible SDKs, configure the client base URL separately:
# base_url="https://cn.crazyrouter.com/v1"
```

For team use, create a dedicated API token per project or team. That makes access control, auditing, and future rotation easier.

## 1. Extracting key information from long documents

The most common document task is extraction: turning a long file into a short, structured result. Claude Code can help with summaries, decisions, dates, tables, entities, action items, risks, or any other field you define.

The important part is to make the extraction target explicit. A vague prompt such as “summarize this document” can be useful for casual reading, but it is not ideal for operational workflows. A better prompt tells Claude Code what to extract, how to group it, and what format to return.

For example:

> Read this meeting note and extract only confirmed decisions. Return a Markdown table with columns: Area, Decision, Owner, Due Date, Evidence Quote. If an owner or due date is missing, write `Not specified`.

This prompt has several useful properties:

- It defines the document type.
- It limits the task to confirmed decisions.
- It defines the output format.
- It asks for evidence quotes, which makes review easier.
- It avoids inventing missing fields.

### Summary extraction

For reports, research notes, or planning documents, ask for a summary at the right level of detail. A 200-word executive summary is different from a technical summary for implementation planning.

Useful summary formats include:

- one-paragraph executive summary
- bullet-point key findings
- background, method, result, conclusion
- decision log
- risk register
- open questions
- implementation checklist

For example:

> Generate a detailed summary of this report. Use sections: Background, Method, Findings, Recommendations, Risks, and Open Questions. Keep each section under five bullet points.

This is much easier to reuse than a free-form paragraph.

### Extracting specific fields

Contracts, policies, invoices, and proposals often require field-level extraction. You might need all dates, payment terms, renewal clauses, parties, scope items, penalties, or responsible teams.

A practical prompt:

> Extract all date-related information from this contract. Return a table with columns: Date, Event, Related Clause, Obligation, Notes. Do not infer dates that are not explicitly present.

For sensitive or high-impact documents, include a review instruction:

> Mark uncertain items as `Needs human review` and include the source sentence.

This keeps the workflow safe. Claude Code can accelerate reading, but legal, financial, or compliance decisions still need human verification.

## 2. Extracting and analyzing tables

Many documents contain tables embedded in Word, PDF, HTML, or exported reports. Claude Code can help extract these tables into Markdown, CSV-like structures, or JSON-like records.

For developer workflows, table extraction should preserve structure first and analysis second. If you ask for analysis too early, you may miss extraction errors.

A good two-step workflow is:

1. Extract all tables exactly as they appear.
2. Analyze the cleaned table data.

Prompt for step one:

> Extract every table from this document. Preserve column names and row order. If a cell is blank, write `EMPTY`. Return each table separately with a short title.

Prompt for step two:

> Using the extracted sales table, identify the highest month, lowest month, month-over-month changes, and any rows that look inconsistent. Do not calculate totals unless all required cells are present.

This workflow is useful for sales reports, HR lists, project status documents, invoices, and operations dashboards.

## 3. Turning source documents into reusable outlines

Claude Code is especially helpful when several documents need to become one structured document. For example, a teacher might upload course notes and ask for a 12-week syllabus. A design lead might combine design guidelines into a design system document. A product manager might turn user interviews into a requirements outline.

The trick is to ask Claude Code to separate source extraction from synthesis:

> First extract the key topics from each document. Then merge duplicate topics. Finally create a teaching outline with weekly goals, required reading, exercises, and assessment ideas.

This prevents the model from jumping straight to a polished result before it has mapped the source material.

A reusable outline format might include:

- title
- audience
- prerequisites
- duration
- modules
- learning goals
- required materials
- exercises
- assessment criteria
- follow-up tasks

For internal documentation, you can adapt this pattern into onboarding plans, support playbooks, architecture notes, or release checklists.

## 4. Formatting and converting documents

Document processing is not only about content. Teams often need consistent formatting: fonts, headings, numbering, spacing, tables, image captions, and file types.

Typical formatting tasks include:

- normalize fonts and sizes across multiple files
- standardize heading levels
- convert inconsistent numbered lists into a defined hierarchy
- convert plain text into Markdown
- transform structured notes into a document template
- detect broken image references or missing captions

When asking Claude Code to help with formatting, provide a formatting standard. Do not assume it knows your internal style guide.

Example:

> Reformat this document using the following standard: H1 for the document title, H2 for major sections, H3 for subsections, numbered lists only for procedures, bullet lists for non-sequential items, and Markdown tables for structured data.

For plain text to Markdown conversion, ask Claude Code to infer hierarchy conservatively:

> Convert this text file to Markdown. Preserve original wording. Infer headings only when the line clearly represents a section title. Do not rewrite paragraphs.

That last sentence matters. Conversion and rewriting are different tasks.

## 5. Batch document processing

Batch operations save time but also increase risk. A mistake repeated across 200 files is harder to fix than a mistake in one file. Treat batch document workflows like code migrations: test, review, then apply widely.

Useful batch tasks include:

- rename files based on date, title, invoice number, or first heading
- replace outdated product names across multiple documents
- add copyright or confidentiality notices
- normalize heading styles
- generate one report per department from a spreadsheet
- create templated emails or notices from a list
- extract invoice fields and create a summary report

A safe batch workflow:

1. Back up the original files.
2. Run the task on one or two representative files.
3. Inspect the output manually.
4. Ask Claude Code to produce a processing log.
5. Run the remaining files in batches.
6. Randomly sample results after completion.

For file renaming, define a collision strategy:

> Rename files using `YYYY-MM-DD_title_###.ext`. If two files produce the same title and date, increment the sequence number. Remove characters not allowed in file names. Return a before/after mapping table before applying changes.

For text replacement, ask for a report:

> Replace `Product A` with `New Product X` across these files. Before modifying, list all files containing the term and the number of matches in each file. After modifying, provide a final replacement count.

This makes the result auditable.

## 6. Practical prompt templates

Here are compact templates you can adapt.

### Decision extraction

> Extract confirmed decisions from this document. Return a table with Area, Decision, Owner, Due Date, and Evidence. Do not include discussion items unless a decision was made.

### Contract date extraction

> Extract all dates from this contract. Return Date, Event, Clause, Obligation, and Review Notes. If the meaning of a date is unclear, mark it as Needs Review.

### Table extraction

> Extract all tables exactly as they appear. Preserve columns, row order, and empty cells. Return each table in Markdown and include the page or section where it was found if available.

### Formatting cleanup

> Clean up formatting without changing meaning. Standardize headings, numbering, spacing, and tables. Return a summary of changes and list any sections that may need manual review.

### Batch report generation

> Using this spreadsheet and report template, generate one report per department. Each report should include department summary, key metrics, issues, recommendations, and next-month plan. Return a file list and note missing data.

## 7. Validation matters

Claude Code is useful for document acceleration, but document outputs should still be checked. This is especially important for contracts, invoices, compliance records, academic material, and customer-facing documents.

A practical validation checklist:

- Are all required fields present?
- Are missing fields marked instead of invented?
- Are numbers and dates copied correctly?
- Are source quotes included for critical claims?
- Are table rows and columns aligned?
- Was the original meaning preserved during formatting?
- Were batch changes logged?

For high-risk workflows, ask Claude Code to create a review report rather than silently changing files.

## Conclusion

Claude Code with Crazyrouter can make document work more structured: extract key information, summarize long files, convert plain text to Markdown, normalize formatting, rename files, and generate batches of reports or notices.

The best results come from precise instructions: define the extraction target, specify the output format, require evidence for important fields, test batch operations on a small sample, and keep humans in the loop for final review.

If you are building a team workflow, start with the endpoint setup, then create prompt templates for your most common document tasks. The series repository is here: [claude-code-guide](https://github.com/xujfcn/claude-code-guide?utm_source=devto&utm_medium=article&utm_campaign=claude_code_guide_daily).
