---
name: heatmap-metrics-literacy
description: >
  Load when the user asks about RPS, RPC, AOV, LPS, conversion rate, scroll
  depth, or phrases like "what metrics should I track," "is my CR good,"
  "how do I compute RPC," or "what's the difference between RPS and RPC."
---

# heatmap-metrics-literacy

## What this skill enables

This skill gives Claude precise, book-accurate definitions for every metric in
the Heatmap methodology, the exact API field that surfaces each one, the formula
for computing derived metrics (RPC, RPS from raw data), when to use each metric
vs. its alternatives, and four MCP recipes for pulling and interpreting metric
data from a live site. After loading this skill, Claude can compute, interpret,
and explain every number a Heatmap customer will encounter.

Source material: *Billion Dollar Websites* Chapters 2–3; MCP source
`heatmap-mcp` repository (commit 275e777).

---

## Core Metrics Framework

### Revenue Per Session (RPS)

**Book definition:** The revenue generated per website session. North Star
metric for ecommerce. Combines Conversion Rate and AOV into one number that
accounts for both the site's ability to convert and the value of those
conversions.

**Formula:** `RPS = Total Revenue ÷ Sessions`

**API field:** `REVENUE_PER_SESSION` / `revenue_per_session`

**Available in:** `get_pageview_analytics` (as a metric in `table_rows`),
`get_raw_pageview_analytics` (as `revenue_per_session` on each row),
`get_session_analytics` (as part of `totals` JSON when included in `metrics`).

**Important terminology note:** The book uses "visitors" as the denominator.
In the Heatmap API, the unit is a **session**, defined as one distinct
`visitId`. One visitor can generate multiple sessions. For practical purposes
treat sessions ≈ visitors within a short analysis window.

**When to use:**
- Comparing any two pages to each other (which LP earns more per visitor?)
- Comparing two elements of the same type (which product tile has the highest RPS?)
- As the primary metric for split test evaluation — it is the tiebreaker when
  CR and AOV point in opposite directions
- Benchmarking a page's performance over time

**When NOT to use:**
- Cross-element comparisons of different types (nav item vs. CTA button) — use RPC
- Sitewide blended reporting without segmentation — always segment by Quadrant first

---

### Revenue Per Click (RPC)

**Book definition:** The revenue potency of a single click event on a specific
element. How much revenue is generated each time that element is clicked.

**Formula:** `RPC = Revenue attributed to sessions that clicked the element ÷ Number of clicks on the element`

**API field:** Derived — no native field. Computed from `get_raw_event_analytics`
rows as `row.revenue / row.value`. (`value` = total clicks; `revenue` = revenue
from sessions that included a click on this selector.)

**When to use:**
- Comparing dissimilar elements (search bar vs. nav link vs. FAQ entry vs. CTA)
- Identifying Conversion Killers: high `value` (clicks) + near-zero `revenue`
- Ranking FAQ items, nav items, or CTA buttons by revenue potency
- Identifying elements worth promoting above the fold

**When NOT to use:**
- Elements with < 50 clicks — RPC is noise below this threshold; use RPS instead
- Same-type element comparisons where RPS is available and more stable

---

### Average Order Value (AOV)

**Book definition:** The average size of each purchase transaction.

**Formula:** `AOV = Total Revenue ÷ Number of Orders`

**API fields:** `AVERAGE_ORDER_VALUE` / `average_order_value` / `aov` (varies
by tool).

**Available in:** `get_raw_pageview_analytics` rows (`average_order_value`),
`get_raw_event_analytics` rows (`aov`), `get_conversions` rows (`aov`),
`get_session_analytics` totals (when `AVERAGE_ORDER_VALUE` included in metrics).

**Constraint:** AOV must always be evaluated alongside RPS. A higher AOV that
suppresses Conversion Rate enough to lower RPS is a net loss, not a win. AOV
alone is directionally useful but never the decision metric.

---

### Conversion Rate (CR)

**Book definition:** The fraction of sessions that result in a purchase (or
defined conversion event). The book explicitly labels global CR a "vanity
metric."

**Formula:** `CR = Orders ÷ Sessions`

**API field:** `CONVERSION_RATE` / `conversion_rate`

**CRITICAL:** The API returns this as a **decimal ratio** (e.g., `0.025`).
Always multiply by 100 before displaying as a percentage (`0.025 → 2.5%`).

**Available in:** `get_raw_pageview_analytics` rows, `get_session_analytics`
totals.

**Legitimate uses (per the book):**
1. Step-by-step funnel analysis to find leaks (use `get_funnels` `completed_rate`)
2. Measuring "stickiness" of a Landing Page — does cold traffic scroll or bounce?
3. Businesses optimizing for new customer volume over first-order profitability

**Do not use** as the primary optimization target for anyone doing revenue CRO.
Use RPS instead — it already contains CR.

---

### Lead Per Session (LPS)

**Book definition:** The Lead-Gen equivalent of RPS. Fraction of sessions that
generate a qualified lead (booked call, demo request, free trial signup, form
submission).

**Formula:** `LPS = Leads ÷ Sessions`

**API field:** Computed via `get_conversions` with `conversion_name: "<your
lead event name>"`, or via `get_custom_events` if leads are instrumented as
custom events. Divide the returned conversion count by sessions from
`get_session_analytics`.

**When to use:** Any time the business does not collect revenue directly on the
website (SaaS, service business, B2B). Replace every reference to RPS with LPS
throughout the methodology. All RPS-based decision rules apply equally to LPS.

---

### Sessions

**API field:** `SESSIONS` / `sessions`

**Definition:** Count of distinct `visitId` values in the date range. One user
may generate multiple sessions. Sessions is the denominator in RPS and LPS.

**Available in:** Most tools — `get_session_analytics`, `get_pageview_analytics`,
`get_raw_pageview_analytics`, etc.

---

### Pageviews

**API field:** `PAGE_VIEWS` / `pageviews`

**Definition:** Total page-load events. Higher than Sessions because one session
can include multiple page loads. Pageviews is the denominator in per-page
engagement analysis but **not** in RPS (which uses sessions).

---

### Scroll Depth

**API field:** `scroll_depth`

**Definition:** Fraction of the page scrolled on average across sessions. Returned
as a ratio (0.0–1.0). Available in `get_raw_pageview_analytics` rows.

**Interpretation thresholds (from the book):**
- `< 0.30`: Hero section failure — visitors aren't engaging past the fold
- `0.30–0.60`: Mid-page content may be losing them; check Bucket RPS
- `> 0.60`: Visitors are reaching the buy box area; diagnose buy box and social proof

---

### Time on Page

**API field:** `time_on_page`

**Definition:** Average seconds spent on the page. Available in
`get_raw_pageview_analytics` rows. Directional only — high time-on-page can mean
engaged reading or confused navigation.

---

## The F*ck-Conversion-Rate Stance

Global Conversion Rate is affected by seasonality, traffic quality, ad spend,
stock availability, and dozens of external factors. Two sites at 1.2% CR and
7.4% CR cannot be compared without knowing AOV, traffic source, and product
category. The number is effectively meaningless in isolation.

The only exception where CR as a primary metric makes sense: step-by-step funnel
diagnosis. Use `get_funnels` `completed_rate` per step to find the biggest leak
in the purchase journey — that is a valid CR application because you are
comparing the same audience to itself across a defined sequence.

---

## MCP Recipes

### Recipe 1 — Pull the site's North Star metrics for a date range

**Question:** What is our RPS, CR, AOV, and revenue for last month, sitewide?

**MCP tool:** `get_session_analytics`

```json
{
  "site_id": "<id>",
  "date_range": { "start": "2026-04-01", "end": "2026-04-30" },
  "metrics": [
    "SESSIONS", "PAGEVIEWS", "REVENUE",
    "REVENUE_PER_SESSION", "AVERAGE_ORDER_VALUE", "CONVERSION_RATE"
  ],
  "group_by": ["DATE"],
  "sort_by": "DATE",
  "sort_direction": "ASC"
}
```

**Output:** Parse `totals` JSON for sitewide aggregates. Parse `series_rows`
JSON for day-by-day trends.

**Interpretation:** RPS trend over time is your single health-check number.
If RPS is flat or declining while Sessions are growing, your new traffic is
lower quality than your old traffic — investigate traffic source and Quadrant
composition.

---

### Recipe 2 — Page-level RPS ranking to find optimization priorities

**Question:** Which pages have the highest RPS? Which have high traffic but low RPS (leaks)?

**MCP tool:** `get_pageview_analytics`

```json
{
  "site_id": "<id>",
  "date_range": { "start": "2026-04-01", "end": "2026-04-30" },
  "metrics": ["SESSIONS", "REVENUE", "REVENUE_PER_SESSION", "CONVERSION_RATE"],
  "sort_by": "REVENUE_PER_SESSION",
  "sort_direction": "DESC",
  "limit": 50
}
```

**Output:** Parse `table_rows` JSON. Sort is server-side; top rows are highest RPS.

**Interpretation:**
- Top 3 by RPS: your best-converting pages — study what they do right
- High sessions + RPS below site average: leaking pages — prioritize for optimization
- Low sessions + high RPS: underexposed pages — test promoting them in navigation

---

### Recipe 3 — Element-level RPC computation (Conversion Killer detection)

**Question:** Which elements on our homepage are Conversion Killers?

**MCP tool:** `get_raw_event_analytics`

```json
{
  "site_id": "<id>",
  "date_range": { "start": "2026-04-01", "end": "2026-04-30" },
  "page_url": "/",
  "order_by": "REVENUE",
  "sort_direction": "ASC",
  "limit": 100
}
```

**Output:** `rows[]` with `selector`, `value` (clicks), `revenue`, `aov`.

**Compute RPC:** For each row: `rpc = row.revenue / row.value`.

**Thresholds:**
- `value > 50` AND `revenue < 1.00`: Conversion Killer candidate. Investigate with screen recordings before removing.
- `rpc > 3× page average rpc`: High-potency element worth promoting upward.
- `value < 10`: Too few clicks for RPC to be reliable; note but don't act.

---

### Recipe 4 — Scroll depth and time-on-page to diagnose page engagement

**Question:** At what point are visitors leaving our landing page?

**MCP tool:** `get_raw_pageview_analytics`

```json
{
  "site_id": "<id>",
  "date_range": { "start": "2026-04-01", "end": "2026-04-30" },
  "page_url": "/landing-page-slug",
  "event_types": ["SCROLL"],
  "include_revenue": true,
  "order_by": "SERVER_DATE",
  "sort_direction": "ASC"
}
```

**Output rows:** `page`, `sessions`, `scroll_depth` (ratio), `time_on_page`
(seconds), `revenue_per_session`, `conversion_rate`.

**Interpretation:**
- `scroll_depth < 0.30`: Hero section failure — fix headline and hero image first
- `scroll_depth 0.30–0.55`: Visitors reaching mid-page but not buy box — diagnose middle content buckets
- `scroll_depth > 0.55` but low `conversion_rate`: Buy box or social proof is failing

---

## Decision Rules

1. **Tiebreaker is always RPS.** When CR goes up and AOV goes down (or vice versa), RPS decides whether to implement.
2. **Segment before reporting.** Never report sitewide RPS without noting whether it's blended across Quadrants. Always specify which Quadrant or traffic source you're measuring.
3. **RPC threshold is 50 clicks.** Below 50 clicks, RPC is noise. Report it with a caveat or use RPS instead.
4. **Conversion Rate from the API is a ratio.** `0.025 × 100 = 2.5%`. Always convert before showing to a user.
5. **LPS replaces RPS for non-ecommerce.** All methodology applies identically; only the numerator changes.
6. **AOV alone never justifies a decision.** It must be evaluated in the context of RPS change.

---

## Gotchas

**Gotcha 1 — Sitewide blended RPS is almost meaningless.**
It mixes cold paid traffic (low converters) with warm email/SMS traffic (high
converters). The blended number is too high for paid traffic benchmarking and
too low for retained customer benchmarking. Always segment.

**Gotcha 2 — The API `conversion_rate` is a decimal, not a percentage.**
`0.025` means 2.5%. This trips up every new user. Multiply by 100 before
displaying.

**Gotcha 3 — High time-on-page is not always good.**
A user spending 8 minutes on a product page could be deeply engaged — or
confused about pricing. Cross-check with `conversion_rate`: high time + low CR
= confusion, not engagement.

**Gotcha 4 — RPC with < 50 clicks is noise.**
Two clicks and one purchase gives RPC = 50% of AOV. That's not a real signal.
Hold RPC calls until elements have volume.

**Gotcha 5 — RPV (Revenue Per View) is not a book-defined metric.**
The formula `RPV = RPS × Scroll%` is an inferred derived metric. The book does
not name or formalize it. See Experimental sidebar below.

---

## Experimental: Revenue Per View (RPV)

> **v0.1 status: included here as a flagged placeholder pending Dylan's sign-off.
> Do not present RPV as a canonical metric.** Full RPV methodology is deferred
> to v0.2 — see eBook Team flag below.

**Concept:** A visibility-adjusted metric that surfaces high-revenue elements
that are buried below the fold.

**Formula (inferred):** `RPV = RPS × scroll_depth`

**Possible use case:** An element with $0.008 RPS might look negligible, but
if it is only seen by 4% of visitors (scroll depth 0.04 past its position), its
per-view revenue may be higher than elements above it.

**Current status:** Not available as a native field. Would require joining
`get_raw_pageview_analytics` scroll depth with `get_raw_event_analytics` revenue
by selector and estimating visibility from scroll depth percentiles.

If a user explicitly asks about visibility-weighted revenue, present RPV as a
research concept under active development — not a production metric.

> **eBook Team flag (RPV — v0.2 gate):** This metric is inferred from the
> book's philosophy but not explicitly named or formalized. Before v0.2,
> confirm with Dylan: (1) Is RPV a named metric in the Heatmap.com UI?
> (2) Is the formula `RPS × scroll_depth` the intended definition?
> (3) Should Claude surface it proactively or only on request?
> Resolution will determine whether this section is promoted to canonical,
> kept as experimental, or removed entirely.

---

## Cross-references

- `heatmap-data-model` — the MCP tools and exact field names referenced here
- `quadrants-of-traffic` — all metrics should be segmented by Quadrant before reporting
- `revenue-heatmap-playbooks` — applies RPS and RPC to specific element types
- **RPV (v0.2):** Revenue Per View is included as an Experimental sidebar above, pending Dylan's sign-off. It is not cross-referenced from other skills until v0.2 confirms its status.
- `split-test-interpretation` — RPS as the primary test evaluation metric
- `aov-strategies` — AOV tactics must always be validated through RPS impact

---

> **eBook Team flag:** RPV is moved to an Experimental sidebar and flagged as
> not book-defined. The `conversion_rate`-as-decimal behavior is confirmed from
> the MCP source. All other metrics above are book-accurate.
