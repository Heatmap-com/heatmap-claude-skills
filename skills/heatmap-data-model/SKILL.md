---
name: heatmap-data-model
description: >
  Load when the user asks how to query Heatmap data, which MCP tool to use,
  how to authenticate, or says "pull my RPS data," "get my funnel," "use the
  MCP," or "how do I get element-level revenue." Execution layer for all skills.
---

# heatmap-data-model

## What this skill enables

This skill gives Claude the complete, ground-truth map of the Heatmap MCP and
GraphQL API surface so it can translate any analytical question — from the book
or from the user — into a concrete tool call, interpret the response, and
deliver a decision. Without this skill, Claude can discuss methodology but
cannot execute it. With it, every other skill in this set becomes runnable
against real data.

Source of truth: `heatmap-mcp` repository (branch `main`,
commit `275e777`, 2026-05-04). The MCP server wraps the GraphQL API at
`POST /graphql/v1` with an `X-Access-Key` header.

---

## Authentication and session bootstrap

Every interaction with the Heatmap data surface requires an access key. The
key is passed as `X-Access-Key: <key>` on all GraphQL requests. When the
Heatmap MCP is loaded in the client, this is handled by the MCP transport
automatically — the user authenticates once and the key is injected. When
calling GraphQL directly (no MCP client), the user passes the key explicitly.

**Always bootstrap in this order:**

1. Call `viewer_access` — returns `account_id`, `allowed_sites[]`,
   `allowed_endpoints[]`, `site_scope`. Never skip this; it tells you which
   `site_id` values are valid for this key and which endpoints are permitted.
2. Call `list_sites` — returns `id`, `name`, `domain`, `timezone`, `status`
   for each site the key can see. Use the `id` field as `site_id` in all
   subsequent calls.
3. Call `get_site_pages` with the chosen `site_id` — returns `id`,
   `page_name`, `screenshot_url` for every tracked page. The `id` values here
   are the `sequence_page_ids` for journey continuation queries.

---

## The Universal Filter: SharedAnalyticsFilterInput

This is the single composable filter that threads through almost every tool.
Public documentation underdocuments it. The full field set, from
`src/types.ts`:

```
SharedAnalyticsFilterInput {
  new_user?:                boolean   // true = first-time visitors only
  returning_user?:          boolean   // true = returning visitors only
  purchase?:                boolean   // true = sessions that purchased
  non_purchase?:            boolean   // true = sessions that did NOT purchase
  average_order_value_min?: number    // e.g. 100.00
  average_order_value_max?: number    // e.g. 499.99
  device?:                  "MOBILE" | "DESKTOP" | "TABLET"
}
```

**Critical implication for Quadrants of Traffic:** The book's four quadrants
(First-Time Mobile, First-Time Desktop, Returning Mobile, Returning Desktop)
map directly to composable `SharedAnalyticsFilterInput` combinations:

| Quadrant              | Filter                                    |
|-----------------------|-------------------------------------------|
| First-Time Mobile     | `{ new_user: true,  device: "MOBILE" }`   |
| First-Time Desktop    | `{ new_user: true,  device: "DESKTOP" }`  |
| Returning Mobile      | `{ returning_user: true, device: "MOBILE" }` |
| Returning Desktop     | `{ returning_user: true, device: "DESKTOP" }` |

You do NOT need post-hoc client-side joins. These are first-class server-side
filters available on every tool that accepts a `filters` argument.

---

## The 13 MCP Tools

### Discovery tools

**`viewer_access`** — no required inputs. Call first, always.

Output:
```json
{
  "account_id": "42",
  "allowed_sites": ["101", "102"],
  "allowed_endpoints": ["sitePages", "analytics", "sessionAnalytics", ...],
  "site_scope": "..."
}
```

**`list_sites`** — no required inputs.

Output: array of `{ id, name, domain, timezone, status }`.

**`get_site_pages`** — required: `site_id`. Optional: `url_filter` with
`{ operator: "CONTAINS" | "EQUALS" | "STARTS_WITH" | ..., value: "string" }`.

Output: array of `{ id, page_name, screenshot_url }`.

---

### Aggregate analytics tools

**`get_analytics`**

Required: `site_id`, `date_range`, `metric`.
Optional: `breakdown`, `filters`, `limit`.

`metric` values: `PAGE_VIEWS`, `SESSIONS`, `UNIQUE_VISITORS`.
`breakdown` values: `DATE`, `URL`, `COUNTRY`, `DEVICE`, `REFERRER`.

When `breakdown` is omitted: only `summary.total` is populated; `rows` is empty.
When `breakdown` is set: `rows` contains `[{ dimension, value }]` pairs.

Output:
```json
{
  "rows": [{ "dimension": "2026-04-01", "value": 12450 }],
  "summary": { "total": 87300 }
}
```

**`get_session_analytics`**

Required: `site_id`, `date_range`.
Optional: `filters`, `group_by` (default `["DATE"]`), `metrics` (default
`["SESSIONS","PAGEVIEWS","REVENUE","REVENUE_PER_SESSION","AVERAGE_ORDER_VALUE",
"CONVERSION_RATE"]`), `sort_by` (default `SESSIONS`), `sort_direction`
(default `DESC`), `page_url`, `traffic_source`, `browser`, `limit`.

Output:
```json
{
  "series_rows": "<JSON string>",
  "totals": "<JSON string>",
  "summary": { "total": 87300 }
}
```
`series_rows` and `totals` are raw JSON strings — parse them before use.

**`get_pageview_analytics`**

Required: `site_id`, `date_range`.
Optional: `filters`, `group_by` (default `["PAGE"]`), `metrics` (default
`["SESSIONS","PAGEVIEWS","REVENUE","REVENUE_PER_SESSION"]`), `sort_by`
(default `PAGEVIEWS`), `sort_direction` (default `DESC`), `conversion_name`,
`page_url`, `traffic_source`, `browser`, `limit`.

Output:
```json
{
  "table_rows": "<JSON string — array of page rows>",
  "summary": { "total": 1420 }
}
```
`table_rows` is a JSON string; parse it. Each row has the requested `metrics`
fields keyed by the metric name. This is the primary source for
**page-level RPS** (`REVENUE_PER_SESSION`).

---

### Element-level analytics tools

**`get_raw_pageview_analytics`**

Required: `site_id`, `date_range`.
Optional: `filters`, `conversion_name`, `page_url`, `event_type` (single
string), `event_types` (array: `["CLICK", "SCROLL"]`), `include_revenue`
(boolean), `order_by` (enum: `SERVER_DATE`, `PAGE`, `SESSIONS`, `PAGEVIEWS`,
`REVENUE`, `REVENUE_PER_SESSION`, `AVERAGE_ORDER_VALUE`, `CONVERSION_RATE`),
`sort_direction`, `limit`.

Output rows:
```json
{
  "page": "/products/widget",
  "server_date": "2026-04-01",
  "sessions": 4800,
  "pageviews": 9200,
  "revenue": 18240.00,
  "revenue_per_session": 3.80,
  "average_order_value": 84.00,
  "conversion_rate": 0.045,
  "purchases": 216,
  "scroll_depth": 0.62,
  "time_on_page": 132.4
}
```

**`get_raw_event_analytics`**

Required: `site_id`, `date_range`.
Optional: `filters`, `conversion_name`, `page_url`, `selector` (CSS selector
string), `include_page` (default `true`), `order_by` (enum: `PAGE`,
`SELECTOR`, `OFFSET_X`, `OFFSET_Y`, `VALUE`, `ORDERS`, `REVENUE`, `AOV`),
`sort_direction`, `limit`.

Output rows:
```json
{
  "page": "/products/widget",
  "selector": "a.nav-item[data-id='reviews']",
  "offset_x": 142,
  "offset_y": 28,
  "value": 312,
  "orders": 4,
  "revenue": 0.80,
  "aov": 0.20
}
```

`value` = total click events on this selector. `revenue` = revenue attributed
to sessions that clicked this selector. `aov` = average order value for those
sessions. This is the primary source for **element-level RPC** — compute
`revenue / value` to get revenue per click.

**`get_heatmap`**

Required: `site_id`, `date_range`.
Optional: `url`, `event_type` (`CLICK` or `SCROLL`), `selector`, `limit`.

Output rows:
```json
{
  "url": "/products/widget",
  "selector": "button.add-to-cart",
  "event_type": "CLICK",
  "events": 2841
}
```

Use for raw click-count heatmaps. Does not return revenue; pair with
`get_raw_event_analytics` when revenue attribution is needed.

---

### Funnel and journey tools

**`get_funnels`**

Required: `site_id`, `date_range`, `steps[]`.
Optional: `filters`, `breakdown` (only `"NONE"` supported), `limit`.

Each step in `steps[]` must be:
```json
{ "type": "PAGE_VIEW", "page": { "operator": "CONTAINS", "value": "/checkout" } }
```
or
```json
{ "type": "EVENT", "event": "CHECKOUT_STARTED" }
```

Event names: `CART_VIEWED`, `CHECKOUT_STARTED`, `ADD_TO_CART`,
`HOMEPAGE_VIEWED`, `PRODUCT_VIEWED`, `CHECKOUT_COMPLETED`,
`COLLECTION_VIEWED`.

Output rows (one per step):
```json
{
  "step_order": 2,
  "step": "CHECKOUT_STARTED",
  "sessions": 8430,
  "completed_users": 8430,
  "completed_rate": 0.68,
  "abandoned_users": 3960,
  "abandoned_rate": 0.32,
  "revenue": 42150.00,
  "revenue_per_completed_user": 5.00
}
```

**`get_journey_continuation_analytics`**

Required: `site_id`, `date_range`, `sequence_page_ids[]`.
Optional: `filters`, `anchor_must_be_entry` (boolean), `exclude_next_page_ids[]`,
`require_active_pages`, `include_continuation`, `include_visit_ids`, `limit`.

`sequence_page_ids` are the page `id` values from `get_site_pages`. Pass
`["/pdp-id", "/cart-id"]` to ask "after users visit PDP then Cart, where do
they go next?"

Output rows:
```json
{
  "site_id": "101",
  "visit_id": null,
  "matched_step_page_ids": ["8234", "8240"],
  "next_page_id": "8251",
  "next_time": "2026-04-01T14:32:00Z",
  "next_page_name": "Checkout",
  "next_page_screenshot_url": "https://..."
}
```

---

### Conversion and event tools

**`get_conversions`**

Required: `site_id`, `date_range`.
Optional: `breakdown` (`DATE`, `CONVERSION_NAME`, `PAGE`, `COUNTRY`, `DEVICE`),
`conversion_name`, `filters`, `limit`.

Omit `breakdown` for summary total only.

Output rows:
```json
{
  "dimension": "Purchase",
  "conversions": 312,
  "orders": 312,
  "revenue": 26208.00,
  "aov": 84.00
}
```

**`get_custom_events`**

Required: `site_id`, `date_range`.
Optional: `breakdown` (`DATE`, `EVENT_NAME`, `EVENT_CATEGORY`, `COUNTRY`,
`DEVICE`), `event_name`, `event_category`, `filters`, `limit`.

Output rows:
```json
{
  "dimension": "popup_shown",
  "events": 5420
}
```

Use to measure popup impressions, variant exposures, and any custom-instrumented
events.

---

## Metrics dictionary: book ↔ API

| Book term             | API field(s)               | Location                              | Notes |
|-----------------------|---------------------------|---------------------------------------|-------|
| RPS (Revenue Per Session) | `REVENUE_PER_SESSION` / `revenue_per_session` | `get_pageview_analytics` rows, `get_raw_pageview_analytics` rows, `get_session_analytics` totals | Book says "visitors"; API unit is a session = one `visitId` |
| RPC (Revenue Per Click)   | Derived: `revenue / value` | `get_raw_event_analytics` rows        | Compute client-side; no pre-aggregated field |
| AOV (Avg Order Value)     | `AVERAGE_ORDER_VALUE` / `average_order_value` / `aov` | multiple tools     | Returned as a dollar amount |
| CR (Conversion Rate)      | `CONVERSION_RATE` / `conversion_rate` | `get_raw_pageview_analytics` rows, `get_session_analytics` | Returned as **ratio** (0.025 = 2.5%). Multiply by 100 to display as % |
| LPS (Leads Per Session)   | `get_conversions` with `conversion_name: "lead"` / custom event | Depends on what the brand instruments | Not a native field; must be computed from named conversions |
| Sessions                  | `SESSIONS` / `sessions`   | most tools                            | `count(distinct visitId)` |
| Pageviews                 | `PAGE_VIEWS` / `pageviews` | most tools                           | Total page-load events |
| Revenue                   | `REVENUE` / `revenue`     | most tools                            | Gross revenue attributed to sessions |
| Scroll Depth              | `scroll_depth`            | `get_raw_pageview_analytics` rows     | Ratio (0.0–1.0); fraction of page scrolled on average |
| Time on Page              | `time_on_page`            | `get_raw_pageview_analytics` rows     | Seconds |

---

## Canonical Recipes

> **Date-window note:** Date ranges in the recipes below (e.g. `2026-04-01`
> to `2026-04-30`) are illustrative. Always default to the user's specified
> window, or to the last 28 days if no window is given. **Never use the literal
> example dates in live calls** — the resulting queries will return empty or
> stale data.

### Recipe 1 — Page-by-page RPS ranking (the "prioritization sweep")

**Question:** Which pages drive the most revenue per session? Where should we
focus optimization effort?

**MCP tool:** `get_pageview_analytics`

**Arguments:**
```json
{
  "site_id": "<from list_sites>",
  "date_range": { "start": "2026-04-01", "end": "2026-04-30" },
  "metrics": ["SESSIONS", "REVENUE", "REVENUE_PER_SESSION", "AVERAGE_ORDER_VALUE"],
  "sort_by": "REVENUE_PER_SESSION",
  "sort_direction": "DESC",
  "limit": 50
}
```

**Output shape:** Parse `table_rows` JSON. Each row has the four requested
metric fields keyed by metric name, plus a page identifier.

**Interpretation:** The page at rank 1 by RPS is your highest-return
optimization target. Pages with high pageviews but low RPS are leaking
revenue — they get traffic but don't convert it. Pages with low pageviews but
high RPS are underexposed high-intent pages worth promoting.

**Decision rule:** Any page with RPS > 2× site average is a priority candidate
for heatmap drilling. Any page with SESSIONS > 500 and RPS < 0.5× site average
is a conversion leak to investigate immediately.

---

### Recipe 2 — Element-level RPC audit (nav, CTA, FAQ ranking)

**Question:** Which nav items, CTA buttons, or FAQ entries drive the most
revenue per click? Which are Conversion Killers?

**MCP tool:** `get_raw_event_analytics`

**Arguments:**
```json
{
  "site_id": "<id>",
  "date_range": { "start": "2026-04-01", "end": "2026-04-30" },
  "page_url": "/",
  "order_by": "REVENUE",
  "sort_direction": "DESC",
  "limit": 100
}
```

**Output shape:** `rows[]` each with `selector`, `value` (clicks), `revenue`,
`aov`. Compute RPC = `revenue / value` for each row.

**Interpretation:**
- Selector with high `value` (clicks) and near-zero `revenue` = Conversion
  Killer. Threshold: 20+ clicks, RPC < $0.05.
- Sort remaining selectors by RPC descending — those are the elements most
  worth reinforcing (larger button, earlier placement).
- For FAQs specifically: all FAQ items share a viewport, so RPS is equal across
  them. Use RPC, not RPS, to rank FAQ content.

**Decision rule:** Any element with > 50 clicks and `revenue = 0.00` should be
examined as a Conversion Killer. Any element with RPC > 3× the page's average
RPC deserves a promotion test (move it above the fold).

---

### Recipe 3 — Quadrant-native performance split

**Question:** How is this page/site performing for first-time mobile vs.
returning desktop users?

**MCP tool:** `get_pageview_analytics` (run 4 times, or `get_session_analytics`
once per quadrant)

**Arguments (First-Time Mobile):**
```json
{
  "site_id": "<id>",
  "date_range": { "start": "2026-04-01", "end": "2026-04-30" },
  "metrics": ["SESSIONS", "REVENUE", "REVENUE_PER_SESSION", "CONVERSION_RATE"],
  "filters": { "new_user": true, "device": "MOBILE" }
}
```

Repeat with `{ "returning_user": true, "device": "DESKTOP" }` etc. for each
quadrant.

**Interpretation:** Compare RPS and CR across quadrants. First-Time Mobile
typically converts at ~0.5% CR; Returning Desktop at ~4%+. A sitewide CR of
2% is usually a blended number that obscures both a struggling paid-traffic
funnel (FTM) and a healthy email/SMS funnel (RD). Treat each quadrant as a
separate patient.

**Decision rule:** If First-Time Mobile RPS is < 0.5× Returning Desktop RPS,
the paid-traffic funnel needs attention before the email funnel. Run heatmap
and funnel analysis filtered to `new_user: true, device: "MOBILE"` first.

---

### Recipe 4 — Funnel drop-off diagnosis

**Question:** Where in the purchase funnel are we losing the most sessions?
What's the revenue impact of the worst drop step?

**MCP tool:** `get_funnels`

**Arguments (standard ecommerce funnel):**
```json
{
  "site_id": "<id>",
  "date_range": { "start": "2026-04-01", "end": "2026-04-30" },
  "steps": [
    { "type": "EVENT", "event": "PRODUCT_VIEWED" },
    { "type": "EVENT", "event": "ADD_TO_CART" },
    { "type": "EVENT", "event": "CHECKOUT_STARTED" },
    { "type": "EVENT", "event": "CHECKOUT_COMPLETED" }
  ]
}
```

**Output shape:** One row per step with `sessions`, `completed_users`,
`completed_rate`, `abandoned_users`, `abandoned_rate`, `revenue`,
`revenue_per_completed_user`.

**Interpretation:** The step with the highest `abandoned_rate` is the primary
leak. Multiply `abandoned_users × revenue_per_completed_user_at_that_step` to
estimate the revenue opportunity if you closed that gap by 20%.

**Decision rule:** Any step with `abandoned_rate > 0.60` (60% of arrivals
abandoned) is a crisis-tier leak that justifies an immediate test. Steps with
`abandoned_rate 0.40–0.60` are material but not emergency. Below 0.40 is
expected friction.

---

### Recipe 5 — Journey continuation diagnostic

**Question:** After users visit the product page and then the cart, where do
they go next? Are they checking out or bouncing to a non-checkout page?

**MCP tool:** `get_journey_continuation_analytics`

**Arguments:**
```json
{
  "site_id": "<id>",
  "date_range": { "start": "2026-04-01", "end": "2026-04-30" },
  "sequence_page_ids": ["<pdp-page-id>", "<cart-page-id>"],
  "anchor_must_be_entry": false,
  "include_continuation": true,
  "limit": 20
}
```
Page IDs come from `get_site_pages`.

**Output shape:** Rows grouped by `next_page_name` and `next_page_id`, showing
what page users visited after the sequence. Count how many rows point to
checkout vs. non-checkout destinations.

**Interpretation:** If > 40% of users after `/pdp → /cart` go to a page that
is not `/checkout`, that's an unexpected exit. Investigate the non-checkout
destinations with a click heatmap to find the distraction.

---

### Recipe 6 — Click heatmap → revenue heatmap drill

**Question:** A specific page has low RPS. Where are users clicking, and which
clicks have revenue?

**Step 1 — Get click counts (engagement map):**
MCP tool: `get_heatmap`
```json
{
  "site_id": "<id>",
  "date_range": { "start": "2026-04-01", "end": "2026-04-30" },
  "url": "/products/widget",
  "event_type": "CLICK",
  "limit": 200
}
```
Output: rows with `selector`, `events` (click count).

**Step 2 — Get revenue per element (revenue map):**
MCP tool: `get_raw_event_analytics`
```json
{
  "site_id": "<id>",
  "date_range": { "start": "2026-04-01", "end": "2026-04-30" },
  "page_url": "/products/widget",
  "order_by": "REVENUE",
  "sort_direction": "DESC"
}
```
Output: rows with `selector`, `value`, `orders`, `revenue`, `aov`.

**Join:** Match on `selector`. Elements that appear in Step 1 (high `events`)
but have near-zero `revenue` in Step 2 are Conversion Killers. Elements with
moderate click counts but high revenue in Step 2 are hidden gems worth
promoting.

---

### Recipe 7 — Split test result validation by quadrant

**Question:** Our test looks flat sitewide. Did it win or lose within any
single quadrant?

**MCP tool:** `get_session_analytics` or `get_pageview_analytics` (run once
per quadrant, filtered to the test URL if possible)

**Arguments (mobile-only validation):**
```json
{
  "site_id": "<id>",
  "date_range": { "start": "<test-start>", "end": "<test-end>" },
  "metrics": ["SESSIONS", "REVENUE", "REVENUE_PER_SESSION", "CONVERSION_RATE"],
  "filters": { "new_user": true, "device": "MOBILE" },
  "page_url": "/products/widget"
}
```

Compare control period vs. variant period (or use `get_custom_events` with
variant exposure tags if the test platform instruments them).

**Interpretation:** A sitewide-flat result that shows +15–20% RPS on
First-Time Mobile is a targeted win. Implement for mobile only. Never discard
a test without running all four quadrant cuts.

---

---

### Critical parsing note — `table_rows`, `series_rows`, `totals`

`get_pageview_analytics` and `get_session_analytics` return these three fields
as **raw JSON strings**, not pre-parsed arrays or objects. You must call
`JSON.parse(response.table_rows)` (and equivalently for `series_rows` and
`totals`) before iterating over the results. Treating these fields as objects
without parsing first will silently fail or throw a type error.

---

## Decision rules

1. **Always bootstrap first.** `viewer_access` → `list_sites` → `get_site_pages` before any analytical query.
2. **Default date window: last 28 days.** Use the user's specified window if given; never use the literal example dates from this skill's recipes.
3. **Prefer `get_pageview_analytics` for page-level ranking.** It returns structured `table_rows` already grouped by page. Use `get_raw_pageview_analytics` when you need per-date rows or scroll/event detail.
4. **Use `get_raw_event_analytics` for any element-level question.** RPC is always computed from this tool's `revenue / value`.
5. **Quadrant filters are server-side and composable.** Never aggregate sitewide data and then try to segment it client-side. Always pass `filters: { new_user: true, device: "MOBILE" }` etc. at query time.
6. **`conversionRate` is a ratio.** `0.025` means 2.5%. Always multiply × 100 before displaying.
7. **Parse `table_rows`, `series_rows`, `totals` as JSON strings.** See Critical parsing note above.
8. **Funnel steps need at minimum 2 entries.** The API returns one row per step. The first step's `sessions` is your funnel top-of-funnel volume.
9. **`limit` is 500 by default, server-enforced max.** For sites with > 500 pages, paginate by narrowing `page_url` filters or requesting sorted subsets.

---

## Gotchas

**Gotcha 1 — Quadrants require separate calls, not a single grouped call.**
There is no "group by new_user/device" breakdown in one call. To compare all
four quadrants you make four calls with different `filters` combinations. This
is expected behavior — each call is fast and the results are directly comparable.

**Gotcha 2 — RPC is always derived, never a native field.**
No tool returns a `rpc` or `revenue_per_click` field. Always compute it from
`get_raw_event_analytics` rows as `row.revenue / row.value`. If `row.value`
is 0, RPC is undefined (the element exists in the DOM but was never clicked).

**Gotcha 3 — `get_pageview_analytics` `table_rows` is a JSON string, not an array.**
Call `JSON.parse(response.table_rows)` before iterating. Same for
`series_rows` and `totals` from `get_session_analytics`.

**Gotcha 4 — `selector` strings are CSS selectors as captured by the snippet.**
They may include dynamic IDs or deeply nested paths. When a user says "the
Reviews nav item," translate that to a `selector` search by fetching all events
on the page and scanning the `selector` field for recognizable patterns (e.g.
`contains "review"` or `contains "nav"`).

**Gotcha 5 — Funnel steps must use exact `FunnelEventName` enum values.**
Valid events: `CART_VIEWED`, `CHECKOUT_STARTED`, `ADD_TO_CART`,
`HOMEPAGE_VIEWED`, `PRODUCT_VIEWED`, `CHECKOUT_COMPLETED`,
`COLLECTION_VIEWED`. Any other event string requires `type: "PAGE_VIEW"` with
a `page` filter instead.

**Gotcha 6 — `get_analytics` with no `breakdown` returns only `summary.total`.**
This is intentional. If you want page-level or date-level data, use
`get_pageview_analytics` or `get_session_analytics` instead.

**Gotcha 7 — `get_journey_continuation_analytics` needs page IDs, not URLs.**
Use the `id` values from `get_site_pages`, not URL strings. Passing a URL as
a sequence ID will return no results.

---

## Cross-references

- `heatmap-metrics-literacy` — full interpretation guide for every metric this
  tool returns
- `quadrants-of-traffic` — the four quadrant framework; uses `filters.new_user`
  / `filters.returning_user` / `filters.device` from this skill
- `revenue-heatmap-playbooks` — the top-10 element optimizations; each one
  specifies which recipe from this skill to run
- `funnel-and-journey-diagnostics` — deeper funnel and journey methodology
  built on Recipes 4 and 5 here
- `split-test-interpretation` — test result validation uses Recipe 7
- `experiment-design-and-prioritization` — ICE prioritization inputs come from
  Recipe 1 (page RPS ranking) and Recipe 6 (click→revenue drill)
