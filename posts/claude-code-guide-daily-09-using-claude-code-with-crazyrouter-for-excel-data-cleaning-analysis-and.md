---
title: Using Claude Code with Crazyrouter for Excel Data Cleaning, Analysis, and Reporting
published: true
description: Daily Claude Code guide rewrite covering practical setup, API routing, and AI coding workflows.
tags: ai, claude, coding, api
canonical_url: https://github.com/xujfcn/claude-code-guide?utm_source=devto&utm_medium=article&utm_campaign=claude_code_guide_daily
---

# Using Claude Code with Crazyrouter for Excel Data Cleaning, Analysis, and Reporting

Data work is rarely glamorous. A typical spreadsheet arrives with duplicated rows, inconsistent dates, mixed currency formats, blank cells, hidden empty columns, and business questions that are not fully specified. Claude Code can help turn that mess into a repeatable workflow: inspect the file, clean it, validate the result, summarize the data, and draft a report.

This article focuses on a practical setup: using Claude Code through Crazyrouter, then applying it to Excel-style data processing and analysis. The goal is not to replace your spreadsheet tool or BI stack. The goal is to use an AI coding assistant as a reliable operator that can write scripts, explain transformations, check assumptions, and produce a reviewable output.

Repository for the full guide: [claude-code-guide](https://github.com/xujfcn/claude-code-guide?utm_source=devto&utm_medium=article&utm_campaign=claude_code_guide_daily)

## 1. Use the correct Crazyrouter endpoint

Before working on data tasks, make sure the endpoint is configured correctly. Crazyrouter has two common integration patterns:

- Claude Code / Anthropic-native clients use: `https://cn.crazyrouter.com`
- OpenAI-compatible SDKs, HTTP calls, and frontend/backend apps use: `https://cn.crazyrouter.com/v1`

The difference matters. If an SDK already appends `/v1`, and you also configure a base URL ending in `/v1`, you may accidentally call paths like `/v1/v1/...`. That is one of the most common integration mistakes.

A minimal shell configuration for Claude Code looks like this:

```bash
# Claude Code / Anthropic-native usage
export ANTHROPIC_BASE_URL="https://cn.crazyrouter.com"
export ANTHROPIC_API_KEY="your_crazyrouter_token"

# OpenAI-compatible SDKs should use this instead:
# base_url="https://cn.crazyrouter.com/v1"
```

Keep tokens scoped by project or team where possible. For spreadsheet work, this is especially useful because finance, sales, and customer data often require stricter operational boundaries.

## 2. Treat Excel processing as a pipeline, not a chat trick

A good data-cleaning workflow should be repeatable. Instead of asking “clean this file” and accepting a black-box result, ask Claude Code to build a small processing pipeline with explicit steps.

A useful structure is:

1. Inspect workbook structure: sheets, columns, row counts, data types.
2. Create a backup or never overwrite the original file.
3. Normalize headers and column names.
4. Remove duplicate rows using clear keys.
5. Fill or flag missing values based on business rules.
6. Normalize dates, numbers, currency, and text.
7. Filter records for the analysis target.
8. Produce an audit summary.
9. Save cleaned data and a transformation log.

This approach turns AI assistance into something a teammate can review. It also prevents subtle data errors, such as treating Excel serial dates as currency values or converting IDs into numbers and losing leading zeros.

## 3. Cleaning Excel data with Claude Code

Start by making the business rules explicit. For example:

> Inspect this workbook and produce a cleaning plan before changing anything. I need duplicate rows removed, blank cells handled, dates normalized to YYYY-MM-DD, amount columns formatted as decimals, and a summary of every transformation.

Claude Code can then generate a script, often with Python libraries such as `pandas` and `openpyxl`, or it can guide you through spreadsheet formulas depending on your environment.

Common cleaning operations include:

### Remove duplicates

Do not simply say “delete duplicates” unless every column must match. In many business datasets, duplicates should be detected by a stable key such as order ID, invoice number, customer ID plus purchase date, or product SKU plus transaction ID.

A better prompt:

> Remove duplicate rows using `order_id` as the unique key. If two rows have the same `order_id`, keep the latest row based on `updated_at`. Output the removed rows to a separate sheet named `duplicates_removed`.

This preserves evidence and makes review easier.

### Fill missing values

Filling every blank cell with “N/A” or “None” is sometimes useful for reporting, but it can damage analysis. A missing region, a missing customer name, and a missing sales amount are different problems.

Use different rules by column type:

- Text fields: trim whitespace; optionally fill blanks with `Unknown`.
- Numeric fields: keep blanks as null unless the business rule says zero is valid.
- Date fields: do not guess; flag invalid or missing dates.
- Category fields: map variants such as `BJ`, `Beijing`, and `北京` only if the mapping is approved.

### Remove empty rows and columns

This is a safe operation when rows and columns are truly empty. Ask Claude Code to report what it removed:

> Delete rows and columns that are completely empty. Do not delete rows with partial data. Report the sheet name, row indexes, and column names removed.

### Normalize dates

Excel date handling is a frequent source of errors. Dates can appear as strings, serial numbers, localized formats, or mixed separators. Ask for detection and validation instead of blind conversion.

> Normalize the `sale_date` column to `YYYY-MM-DD`. Treat invalid dates as null and write them to an `invalid_dates` sheet for review.

### Normalize amounts

Amounts often arrive as `1200`, `1,200`, `¥1,200.00`, `1200.0`, or text values with spaces. Claude Code can generate code to strip symbols, remove thousands separators, parse decimals, and preserve the final numeric type.

Important: formatting for display and numeric storage are different. Store amounts as numbers; apply formatting only in the final Excel output or report.

## 4. Filtering data for analysis

Once the dataset is clean, filtering becomes more reliable. Typical requests include:

- Sales greater than a threshold.
- Transactions within a date range.
- Records owned by a specific salesperson.
- Customers in selected regions.
- Orders with missing invoice numbers.
- Outliers that require review.

Make filters explicit and ask for counts before and after filtering:

> Filter sales records from 2024-01-01 to 2024-01-31 where `amount` is greater than 50000. Return the filtered workbook and a summary showing original row count, filtered row count, excluded row count, and filter conditions.

This keeps the output auditable.

## 5. Basic statistics and business summaries

Claude Code is useful for turning cleaned data into first-pass summaries. It can compute totals, averages, medians, grouped totals, frequency tables, and percentage distributions. However, you should avoid asking it to “find insights” without specifying the business question.

Better analysis prompts include:

- “Calculate monthly sales by product and region.”
- “Rank customers by annual revenue and split them into configurable value tiers.”
- “Show the top products by revenue and unit count.”
- “Compare this month with the previous month if both periods exist in the file.”
- “Detect unusually high or low transactions and explain the method used.”

For statistical analysis, always ask Claude Code to state the method. For example, outlier detection could use an interquartile range rule, z-score, or a business-defined threshold. Correlation analysis should include caveats: correlation does not prove causation, and small datasets may not support strong conclusions.

## 6. Trend analysis without overclaiming

Spreadsheet reports often need time-series summaries: monthly sales, quarter-over-quarter changes, year-over-year comparisons, seasonality, and simple forecasts. Claude Code can help produce these, but it should not invent precision.

A practical prompt:

> Analyze sales trends by month. If the dataset does not contain enough historical periods for a reliable trend or forecast, say so. Calculate month-over-month changes only where both months are present. Do not fill missing months unless you clearly mark them as missing.

This avoids a common failure mode: polished analysis based on incomplete data.

For domestic teams integrating multiple models through Crazyrouter, the same workflow can be reused across projects while keeping the endpoint configuration consistent. Claude Code handles the local analysis workflow; Crazyrouter standardizes model access.

## 7. Charts and reports

Claude Code can generate chart code, spreadsheet charts, Markdown reports, or slide outlines. For developer-friendly workflows, ask it to produce artifacts rather than only text:

- `cleaned_sales.xlsx`
- `analysis_summary.md`
- `charts/ monthly_sales.png`
- `transformation_log.json`
- `invalid_rows.xlsx`

For charts, specify the intended audience. A finance review may need tables and exact numbers. A sales meeting may need a trend line and a short explanation. An executive summary may need only three metrics, two risks, and next actions.

Useful chart requests:

- Bar chart: product or region comparison.
- Line chart: time trend.
- Pie or donut chart: percentage distribution, only when category count is small.
- Histogram: transaction amount distribution.
- Scatter plot: relationship between marketing spend and revenue.

Ask Claude Code to generate the chart and the code used to create it. That way, you can rerun the report next month.

## 8. A reusable monthly sales report prompt

Here is a practical prompt you can adapt:

> You are helping prepare a monthly sales report. First inspect the workbook and list sheets, columns, row counts, and suspected data quality issues. Then create a cleaning plan. After I approve it, generate a Python script that: backs up the original file, removes duplicate orders by `order_id`, normalizes `sale_date` to `YYYY-MM-DD`, parses `amount` as numeric, flags invalid rows, calculates sales by product, region, and salesperson, creates charts for monthly trend and product comparison, and writes a Markdown report with assumptions, methods, and next-step recommendations.

The approval step is important. It keeps Claude Code from silently applying assumptions that may not match your business rules.

## 9. Validation checklist

Before using the output in a business report, run through this checklist:

- Did the original file remain unchanged?
- Are row counts before and after cleaning documented?
- Are removed duplicates saved somewhere?
- Are missing and invalid values reported separately?
- Are dates parsed correctly, including Excel serial dates?
- Are currency and amount fields stored as numbers?
- Are formulas and derived metrics documented?
- Are charts based on the cleaned dataset, not the raw one?
- Are recommendations supported by the data?
- Can the workflow be rerun next month?

## Conclusion

Claude Code is strongest in data processing when you use it as a workflow builder: inspect, plan, script, validate, analyze, and report. Crazyrouter keeps the model access layer consistent, but the quality of the result still depends on clear rules and reviewable artifacts.

Use `https://cn.crazyrouter.com` for Claude Code and Anthropic-native clients. Use `https://cn.crazyrouter.com/v1` for OpenAI-compatible SDKs and HTTP integrations. Keep those endpoints clean, avoid accidental `/v1/v1` paths, and make every data transformation auditable.
