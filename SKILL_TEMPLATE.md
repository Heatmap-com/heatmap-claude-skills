---
name: skill-name-here
description: >
  Load when the user asks about [primary topic], [secondary topic], or phrases
  like "[trigger phrase 1]," "[trigger phrase 2]," or "[trigger phrase 3]."
  Target: 25–40 words. Cover the main load triggers without repeating the title.
---

# skill-name-here

## What this skill enables

One paragraph. Answer: what can Claude do *after* loading this skill that it
could not do before? Be specific about the output (recipes, frameworks,
decisions) not just the topic coverage.

State the source material: book chapter(s) and MCP commit reference if
recipes are included.

---

## Core Framework

### Section Heading

The main theory, model, or framework. Use sub-headings for sub-concepts.

Write for Claude as the reader — opinionated, concrete, in the same voice as
the methodology. Avoid hedging.

---

## MCP Recipes

For each recipe, use this uniform structure:

### Recipe N — Short title

**Question:** The user question this recipe answers.

**MCP tool:** `tool_name`

**Arguments:**
```json
{
  "site_id": "<id>",
  "date_range": { "start": "<last-28-days-start>", "end": "<last-28-days-end>" }
  // ...other args
}
```

**Output shape:** What the response looks like. Which fields to parse. Note if
any fields are raw JSON strings that require `JSON.parse()`.

**Interpretation:** How to read the output. What numbers mean what.

**Decision rule:** The threshold or condition that triggers action.

---

## Decision Rules

Numbered list. Each rule is a single, actionable statement Claude follows when
this skill is loaded. No hedging. No "it depends" without a follow-up branch.

---

## Gotchas

**Gotcha N — Title.**
One paragraph. The mistake, why it happens, and how to avoid it.

---

## Cross-references

- `skill-name` — one-line reason why
- `skill-name` — one-line reason why

---

> **eBook Team flag:** Note any content that is inferred, extrapolated, or
> unconfirmed from the book. Be specific about what needs Dylan's sign-off and
> what the resolution options are. Remove this block when all flags are cleared.
