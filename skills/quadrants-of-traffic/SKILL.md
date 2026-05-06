---
name: quadrants-of-traffic
description: >
  Load when the user asks about traffic segmentation, mobile vs desktop, new vs
  returning visitors, why test results look inconclusive, or mentions "Quadrants
  of Traffic," "first-time visitors," or "why don't my tests make sense."
---

# quadrants-of-traffic

## What this skill enables

This skill teaches Claude to apply Dylan Ander's Quadrants of Traffic framework
as the mandatory first filter before drawing any conclusions from analytics or
test data — and to execute that segmentation natively using the Heatmap MCP's
`SharedAnalyticsFilterInput` (`new_user`, `returning_user`, `device`). After
loading this skill, Claude can segment any metric by quadrant with real MCP
calls, validate split test results at the quadrant level, and recommend
quadrant-specific implementation strategies.

Source material: *Billion Dollar Websites* Chapter 16; MCP source
`src/types.ts` (confirmed `new_user`, `returning_user` filters).

---

## The Four Quadrants

Every website visitor belongs to exactly one quadrant at any given moment.

| Quadrant | `new_user` | `device` | Traffic share | CR | Primary channel |
|---|---|---|---|---|---|
| First-Time Mobile | `true` | `"MOBILE"` | ~70%* | ~0.5%* | Paid ads, top-of-funnel SEO, affiliates |
| Returning Desktop | `false` | `"DESKTOP"` | ~4%* | ~4%+* | Email, retargeting, direct |

*Book-cited figures (Ch 16). The 8× CR gap between these two Quadrants is
the core insight.

For the other two Quadrants (First-Time Desktop, Returning Mobile), use your
own site's data as the benchmark. The book's ethos is that your own data is
the only meaningful benchmark anyway.

**The 8× CR gap is the core insight.** A sitewide analysis that blends these
four segments produces a number that is simultaneously too high (for paid
traffic benchmarking) and too low (for email/SMS benchmarking). It is almost
always inconclusive or misleading as a standalone metric.

---

## Why Mobile and Desktop Are Different Websites

Mobile and desktop run on different codebases. The nav collapses differently.
Tap targets behave differently from mouse clicks. Scroll depth patterns differ.
A change that wins on mobile can genuinely hurt on desktop, and vice versa.
This is not a framing choice — it is a data integrity requirement.

**~80% of traffic is mobile.** Optimizing desktop-first while deferring mobile
is optimizing for 20% of your audience first. Always analyze and fix mobile
before desktop.

---

## Why New and Returning Are Different Customers

Returning visitors convert at 4–8× the rate of first-time visitors. They have
already completed the trust-building process. The Gator recognizes the brand.
The Judge is quieter because prior experience has already answered the "is this
safe to buy from?" question.

First-time visitors are in evaluation mode. The Judge is active and skeptical.
Every element on the page is being assessed for the first time. A headline test
designed for brand recognition will behave completely differently depending on
whether the visitor has ever seen the brand before.

---

## Quadrant-Native API Filters

The MCP's `SharedAnalyticsFilterInput` provides first-class quadrant filtering.
**No client-side joins or post-hoc calculations are needed.**

| Quadrant | Filter JSON |
|---|---|
| First-Time Mobile | `{ "new_user": true, "device": "MOBILE" }` |
| First-Time Desktop | `{ "new_user": true, "device": "DESKTOP" }` |
| Returning Mobile | `{ "returning_user": true, "device": "MOBILE" }` |
| Returning Desktop | `{ "returning_user": true, "device": "DESKTOP" }` |

Pass these as the `filters` argument on any tool that accepts a
`SharedAnalyticsFilterInput`: `get_pageview_analytics`,
`get_session_analytics`, `get_raw_pageview_analytics`,
`get_raw_event_analytics`, `get_funnels`, `get_journey_continuation_analytics`,
`get_conversions`, `get_custom_events`.

---

## MCP Recipes

### Recipe 1 — Full quadrant baseline (the mandatory first analysis)

**Question:** What is our RPS and CR for each of the four quadrants?

Run four calls, one per quadrant. Use `get_session_analytics` for a clean
top-level summary.

**Arguments (First-Time Mobile):**
```json
{
  "site_id": "<id>",
  "date_range": { "start": "2026-04-01", "end": "2026-04-30" },
  "metrics": ["SESSIONS", "REVENUE", "REVENUE_PER_SESSION", "CONVERSION_RATE", "AVERAGE_ORDER_VALUE"],
  "filters": { "new_user": true, "device": "MOBILE" }
}
```
Repeat with `{ "new_user": true, "device": "DESKTOP" }`,
`{ "returning_user": true, "device": "MOBILE" }`,
`{ "returning_user": true, "device": "DESKTOP" }`.

**Output:** Parse `totals` JSON from each call. Assemble into a four-row
table: Quadrant | Sessions | RPS | CR | AOV.

**Interpretation:**
- If First-Time Mobile sessions are < 50% of total: the brand is primarily
  retargeting/email-driven — optimize for returning users.
- If First-Time Mobile CR is > 1.5%: that's above benchmark; the paid funnel
  is strong — investigate other quadrants for lift.
- If Returning Desktop RPS is < 2× First-Time Mobile RPS: the retained
  customer experience may be under-optimized.

---

### Recipe 2 — Cross-quadrant split test validation

**Question:** Our A/B test looks flat sitewide. Did it win or lose within any
quadrant?

For each quadrant, compare the test period with a matched control period (or
use the test platform's variant segmentation if it instruments via custom
events).

**Arguments (test period, First-Time Mobile only):**
```json
{
  "site_id": "<id>",
  "date_range": { "start": "<test-start>", "end": "<test-end>" },
  "metrics": ["SESSIONS", "REVENUE_PER_SESSION", "CONVERSION_RATE"],
  "filters": { "new_user": true, "device": "MOBILE" },
  "page_url": "/the-page-being-tested"
}
```

**Arguments (control period, same quadrant):**
```json
{
  "site_id": "<id>",
  "date_range": { "start": "<control-start>", "end": "<control-end>" },
  "metrics": ["SESSIONS", "REVENUE_PER_SESSION", "CONVERSION_RATE"],
  "filters": { "new_user": true, "device": "MOBILE" },
  "page_url": "/the-page-being-tested"
}
```

Compare `revenue_per_session` and `conversion_rate` between test and control
for each quadrant.

**Decision rules:**
- If one quadrant shows ≥ 10% RPS lift and another shows ≤ -5% RPS: implement
  for the winning quadrant only.
- If all quadrants show flat or negative: the test hypothesis was wrong —
  build the follow-up.
- Never call a test inconclusive without running all four quadrant cuts first.

**Ch 16 case study:** A previous agency tested Value Propositions above the
fold sitewide. Sitewide result: flat at ~68% confidence, called inconclusive.
Quadrant filter revealed +20% for all Mobile Users (both first-time and
returning). The correct decision was mobile-only implementation. The agency
missed a real win by not running the Quadrant filter.

---

### Recipe 3 — Mobile-first optimization prioritization

**Question:** Our optimization roadmap has 10 items. Which do we do for mobile first?

**Step 1 — Identify mobile sessions share:**
```json
{
  "site_id": "<id>",
  "date_range": { "start": "2026-04-01", "end": "2026-04-30" },
  "metric": "SESSIONS",
  "breakdown": "DEVICE"
}
```
`get_analytics` with `breakdown: "DEVICE"` returns session counts by
`MOBILE`, `DESKTOP`, `TABLET`. Compute mobile share = mobile_sessions /
total_sessions.

**Step 2 — Get First-Time Mobile page RPS:**
```json
{
  "site_id": "<id>",
  "date_range": { "start": "2026-04-01", "end": "2026-04-30" },
  "metrics": ["SESSIONS", "REVENUE_PER_SESSION"],
  "filters": { "new_user": true, "device": "MOBILE" },
  "sort_by": "SESSIONS",
  "sort_direction": "DESC",
  "limit": 30
}
```

**Prioritization rule:** The page with the most First-Time Mobile sessions and
the lowest RPS vs. site average is Priority 1 for mobile optimization. This
is where the volume × opportunity product is highest.

---

### Recipe 4 — Paid vs. warm channel attribution

**Question:** Is our revenue primarily driven by paid (cold) traffic or
email/SMS (warm) traffic?

```json
// get_session_analytics with traffic_source grouping
{
  "site_id": "<id>",
  "date_range": { "start": "2026-04-01", "end": "2026-04-30" },
  "metrics": ["SESSIONS", "REVENUE", "REVENUE_PER_SESSION"],
  "group_by": ["DATE"],
  "filters": { "new_user": true }
}
```

Then repeat with `{ "returning_user": true }`.

Compare total `revenue` from the two calls. The ratio tells you what fraction
of revenue is acquisition-driven vs. retention-driven. This determines whether
to prioritize First-Time Mobile optimizations (acquisition-heavy) or Returning
Mobile/Desktop optimizations (retention-heavy).

**Rule of thumb:** If returning users drive > 60% of revenue, the brand's paid
funnel is likely underoptimized — there is more growth available from improving
first-time conversion than from retaining existing customers.

---

## Decision Rules

1. **Never report a sitewide metric without running Quadrant filters.** Sitewide is the last number you look at, not the first.

2. **Before calling any test inconclusive:** Run all four Quadrant filters. A sitewide flat result with a quadrant-level win is a targeted implementation opportunity, not a failed test.

3. **When a test wins on mobile and loses on desktop:** Implement for mobile only. Do not implement sitewide. The mobile win is real and the desktop hurt is real.

4. **When a test shows a slight overall win:** Check whether it is hurting Returning Desktop before implementing sitewide. A small aggregate gain that damages your best-converting quadrant is a net revenue loss.

5. **Mobile analysis always before desktop.** ~80% of traffic is mobile. The revenue lift from a mobile fix is ~4× the lift from the same desktop fix by sheer volume.

6. **Establish your own quadrant baselines.** The benchmark table above is a starting point only. Measure your own site's Quadrant metrics and use those as your reference.

7. **Tablet traffic is not a fifth quadrant.** Too small for most sites to be analytically reliable. Exclude from quadrant analysis or classify by viewport behavior.

---

## Gotchas

**Gotcha 1 — Calling a test inconclusive without Quadrant filters.**
This is the single most expensive mistake in A/B testing. A sitewide flat
result at 68% confidence can have a 94% confident mobile win hidden inside it.
Never call a test done until all four quadrant cuts are run.

**Gotcha 2 — Implementing a sitewide winner that hurts a key quadrant.**
A test that improves First-Time Mobile CR by 15% but hurts Returning Desktop
revenue by 30% is a net negative if your Returning Desktop users convert at 4×.
Always cross-check every quadrant before shipping any result.

**Gotcha 3 — Treating First-Time Mobile as low-value because it has a low CR.**
Low CR on cold traffic is not a website failure — it is the expected behavior
for visitors who have never seen the brand before. The opportunity is to improve
the trust-building speed on that first visit, not to dismiss the quadrant as
"hard to convert."

**Gotcha 4 — Using tablet as a quadrant.**
Tablet traffic is too small and too variable in viewport behavior to be
analytically reliable for most brands. Do not treat it as a separate quadrant.

**Gotcha 5 — Comparing Quadrant metrics without matching date ranges.**
Seasonal effects hit quadrants differently. Paid traffic (First-Time Mobile)
surges during sale periods. Email traffic (Returning Desktop) is steadier.
Always use matched periods for comparisons.

---

## Cross-references

- `heatmap-data-model` — the MCP filter syntax (`new_user`, `returning_user`, `device`) used in every recipe above
- `split-test-interpretation` — Quadrant filtering is mandatory for every test result; test validation recipes build on Recipe 2
- `heatmap-metrics-literacy` — all metrics (RPS, CR, AOV) must be evaluated per-Quadrant
- `revenue-heatmap-playbooks` — element optimizations run on the Quadrant that dominates that page's traffic

---

> **eBook Team flag:** Hard numbers from the book: First-Time Mobile "nearly
> 70% of traffic" and "~0.5% CR"; Returning Desktop "~4% of traffic" and
> "~4%+ CR"; the 8× CR gap; the 82% new-user share; the Ch 16 Value-Props
> case study (+20% Mobile). Derived benchmark cells for First-Time Desktop and
> Returning Mobile have been removed — the table now shows only book-cited rows.
