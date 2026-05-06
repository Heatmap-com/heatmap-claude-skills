---
name: funnel-and-journey-diagnostics
description: >
  Load when the user asks about funnel drop-off, cart abandonment, checkout
  conversion, where users go after a page, or phrases like "where are we losing
  people," "why is checkout abandonment high," or "what's our funnel CR."
---

# funnel-and-journey-diagnostics

## What this skill enables

This skill gives Claude the methodology and MCP recipes for diagnosing
purchase funnel leaks and post-page journey behavior. It binds the book's
framework for finding and fixing funnel drop-off points to the `get_funnels`
and `get_journey_continuation_analytics` tools. After loading this skill,
Claude can identify the single worst-drop step in any funnel, estimate the
revenue opportunity from fixing it, generate hypotheses about why the drop
is happening, and track where users actually go after key pages.

Source material: *Billion Dollar Websites* Chapters 2, 5, and methodology
throughout; MCP source at `heatmap-mcp` repository
(commit 275e777).

---

## Core Framework

### Expected Drop vs. Fixable Drop

Not all funnel drop-off is a problem. Every funnel has natural attrition.
The diagnostic question is: is this drop at the expected rate for this step
type, or is it higher than expected?

**Expected drop rates (general ecommerce benchmarks):**
- Product View → Add to Cart: 80–90% abandon (10–20% add-to-cart rate is normal)
- Add to Cart → Checkout Start: 40–60% abandon (40–60% begin checkout is typical)
- Checkout Start → Checkout Complete: 30–50% abandon (50–70% complete checkout is typical)

Any step with abandon rate significantly higher than these benchmarks is a
**fixable drop** — there is something specific about that step's design, copy,
or experience that is causing above-expected attrition.

**Revenue opportunity calculation:**
`Revenue opportunity = abandoned_users × revenue_per_completed_user_at_that_step × 0.20`

This estimates the revenue impact of a 20% improvement in the worst drop step.
If the worst step abandons 5,000 users at `revenue_per_completed_user = $85`,
a 20% improvement = `5,000 × $85 × 0.20 = $85,000` incremental revenue.

### The Funnel Diagnostic Loop

1. Pull the funnel with `get_funnels`
2. Identify the step with the highest `abandoned_rate` relative to benchmark
3. Pull a click heatmap for that step's page with `get_heatmap`
4. Pull element revenue data with `get_raw_event_analytics`
5. Generate a hypothesis: what element on that page is causing the drop?
6. Score the hypothesis with ICE and add to the test backlog

### Journey Continuation: Where Do Users Actually Go?

Funnel data tells you how many users made it to each step. Journey continuation
data tells you *where the abandoners went* — which pages they visited after
the step they dropped at. This is often more diagnostic than knowing they dropped.

**Example:** 60% of users who reach the cart page don't start checkout. Journey
continuation analysis might show that 35% of those go back to a product page
(they're comparison-shopping, not done), 15% go to the homepage (lost,
confused), and 10% leave the site entirely. Each of those destination patterns
suggests a different root cause.

---

## MCP Recipes

### Recipe 1 — Standard funnel drop-off identification

**Question:** Where in the purchase funnel are we losing the most users?

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

**Output shape:** One row per step with `step_order`, `step`, `sessions`,
`completed_users`, `completed_rate`, `abandoned_users`, `abandoned_rate`,
`revenue`, `revenue_per_completed_user`.

**Analysis:**

1. For each step, compute `expected_abandon_rate` from benchmarks above.
2. Compare `abandoned_rate` to expected. The step with the largest
   `abandoned_rate - expected_abandon_rate` is the priority.
3. Compute revenue opportunity: `abandoned_users × revenue_per_completed_user × 0.20`.
4. Report the single worst step, its revenue opportunity, and the next diagnostic step.

**Example output interpretation:**
- Step: CHECKOUT_STARTED → CHECKOUT_COMPLETED
- `abandoned_rate: 0.68` (benchmark: 0.30–0.50)
- `abandoned_users: 3,200`
- `revenue_per_completed_user: $87.50`
- Revenue opportunity: `3,200 × $87.50 × 0.20 = $56,000/month`
- Hypothesis: Checkout page has friction (too many form fields, no BNPL, unclear
  shipping cost). Run `get_raw_event_analytics` on checkout page to identify
  Conversion Killers.

---

### Recipe 2 — Custom funnel for specific page sequences

**Question:** What fraction of users who land on our PDP actually complete a purchase?

Use `PAGE_VIEW` type steps when the milestone is page visitation rather than
a standard ecommerce event:

```json
{
  "site_id": "<id>",
  "date_range": { "start": "2026-04-01", "end": "2026-04-30" },
  "steps": [
    { "type": "PAGE_VIEW", "page": { "operator": "CONTAINS", "value": "/products/" } },
    { "type": "EVENT", "event": "ADD_TO_CART" },
    { "type": "EVENT", "event": "CHECKOUT_COMPLETED" }
  ],
  "filters": { "new_user": true, "device": "MOBILE" }
}
```

This runs the funnel filtered to First-Time Mobile visitors only — the paid
traffic funnel. Compare results to the unfiltered version to see whether the
funnel leak is specific to cold paid traffic or universal.

---

### Recipe 3 — Journey continuation: Where do cart abandoners go?

**Question:** Users who reach the cart page but don't checkout — where do they go next?

Step 1: Get the page ID for the cart page:
```json
// get_site_pages
{
  "site_id": "<id>",
  "url_filter": { "operator": "CONTAINS", "value": "/cart" }
}
```
Returns `[{ "id": "8240", "page_name": "Cart", "screenshot_url": "..." }]`.

Step 2: Get journey continuation from the cart:
```json
{
  "site_id": "<id>",
  "date_range": { "start": "2026-04-01", "end": "2026-04-30" },
  "sequence_page_ids": ["8240"],
  "anchor_must_be_entry": false,
  "include_continuation": true,
  "limit": 20
}
```

**Output:** `rows[]` each with `next_page_name`, `next_page_id`. Count rows
by destination page. The distribution tells you where abandoners went.

**Interpretation:**
- Top destination is a product page: users are comparison-shopping or
  reconsidering their selection → add "You May Also Like" or product comparison
  content in the cart.
- Top destination is the homepage: users are lost or restarting → improve
  cart UI clarity or add a persistent "continue shopping" CTA.
- Top destination is out of site (0 next_page_id): users are leaving entirely →
  investigate with exit-intent survey on cart page.

---

### Recipe 4 — Click and revenue drill on the worst-drop step's page

**Question:** Which elements on the worst-drop-step page are causing abandonment?

After identifying the worst step from Recipe 1 (e.g., Checkout page), run:

```json
// get_heatmap — CLICK on the checkout page
{
  "site_id": "<id>",
  "date_range": { "start": "2026-04-01", "end": "2026-04-30" },
  "url": "/checkout",
  "event_type": "CLICK",
  "limit": 100
}
```

Then:
```json
// get_raw_event_analytics — revenue per element on checkout page
{
  "site_id": "<id>",
  "date_range": { "start": "2026-04-01", "end": "2026-04-30" },
  "page_url": "/checkout",
  "order_by": "REVENUE",
  "sort_direction": "DESC",
  "limit": 50
}
```

Cross-reference: elements with high `events` in the heatmap but low `revenue`
in the event analytics are the friction points. Common checkout friction:
- Users clicking the "back" button (navigation, not form interaction)
- Users clicking the promo code field (distraction — they go looking for a code
  and don't return)
- Users clicking the shipping cost expand — unclear shipping before checkout is
  a major trust failure

---

### Recipe 5 — Quadrant-stratified funnel comparison

**Question:** Is the funnel drop worse for first-time mobile users than for others?

Run Recipe 1 four times with each Quadrant filter:

```json
// First-Time Mobile funnel
{
  "site_id": "<id>",
  "date_range": { "start": "2026-04-01", "end": "2026-04-30" },
  "steps": [
    { "type": "EVENT", "event": "PRODUCT_VIEWED" },
    { "type": "EVENT", "event": "ADD_TO_CART" },
    { "type": "EVENT", "event": "CHECKOUT_COMPLETED" }
  ],
  "filters": { "new_user": true, "device": "MOBILE" }
}
```

Repeat with `{ "new_user": true, "device": "DESKTOP" }`,
`{ "returning_user": true, "device": "MOBILE" }`,
`{ "returning_user": true, "device": "DESKTOP" }`.

**Interpretation:** If First-Time Mobile has 2× the abandoned rate at
CHECKOUT_STARTED vs. Returning Desktop, the checkout friction is specifically
a cold-traffic trust problem (unknown brand, unfamiliar checkout). The fix is
different: add trust signals (reviews, guarantees, press mentions) visibly at
checkout for first-time visitors.

If all quadrants have similar drop rates, the checkout problem is structural —
affecting all users equally. Investigate form complexity, shipping cost
transparency, and payment options.

---

## Decision Rules

1. **Identify the single worst drop step first.** Do not try to fix all funnel steps simultaneously. Fix the worst one, measure, then move to the next.
2. **Compute revenue opportunity before prioritizing.** A step with 90% abandon rate that only affects 100 users is lower priority than a step with 55% abandon rate affecting 10,000 users.
3. **Expected drop rates give context.** Don't panic about 85% abandon on Product View → Add to Cart. That's normal. Panic about 85% abandon on Checkout Start → Checkout Complete. That is not normal.
4. **Journey continuation data is the "why."** Funnel data tells you the what (they dropped). Journey continuation tells you the where (they went). Both together generate a better hypothesis.
5. **Always run the Quadrant stratification on the worst step.** A checkout fix designed for all users may be wrong for first-time mobile and right for returning desktop. Quadrant context changes the fix.

---

## Gotchas

**Gotcha 1 — Treating all funnel drop-off as a problem.**
Natural attrition exists. 85% of product viewers not adding to cart is expected
behavior — they were browsing. Do not over-optimize "expected" steps.

**Gotcha 2 — Running the funnel on too short a date range.**
Low-traffic funnels need at least 30 days of data to produce stable step-to-step
rates. A 7-day funnel on a low-traffic site will have high variance.

**Gotcha 3 — Using PAGE_VIEW steps for events that are tracked as named events.**
If your Shopify setup tracks CHECKOUT_STARTED as a standard event, use
`{ "type": "EVENT", "event": "CHECKOUT_STARTED" }` not a page_view step for
`/checkout`. Using PAGE_VIEW for named events can produce different counts if
users land on the checkout page multiple times or refresh it.

**Gotcha 4 — Ignoring where journey continuation sessions go.**
If 30% of cart abandoners go back to a product page, that is comparison-shopping
behavior — not a checkout problem. The fix is in the product page or collections
experience, not in checkout optimization. Journey continuation diagnosis
prevents you from fixing the wrong page.

**Gotcha 5 — Funnel steps must be minimum 2 entries.**
A single-step funnel returns just one row with no drop rate to analyze.
Always include at least an entry event and an exit event.

---

## Cross-references

- `heatmap-data-model` — `get_funnels` and `get_journey_continuation_analytics` tool details and output shapes
- `revenue-heatmap-playbooks` — element-level optimizations for the pages at the worst drop step
- `quadrants-of-traffic` — Recipe 5 uses native Quadrant filters to segment funnel performance
- `customer-research-bdf` — exit surveys deployed at the worst drop step page collect the qualitative "why"
- `experiment-design-and-prioritization` — funnel drop opportunity feeds directly into ICE Impact scoring
- `aov-strategies` — checkout friction fixes include BNPL, threshold offers, and trust signals
