---
name: aov-strategies
description: >
  Load when the user asks about increasing AOV, upsells, bundles, BNPL,
  cross-sells, post-purchase offers, cart upsells, free shipping thresholds,
  or phrases like "how do I get customers to spend more" or "increase my AOV."
---

# aov-strategies

## What this skill enables

This skill gives Claude Dylan Ander's complete AOV improvement framework: nine
strategies, three upsell types, the Empty Cart tactic, and the impulse-threshold
rule — together with four MCP recipes that pull real AOV data from the user's
site. Every AOV tactic is validated against RPS, not AOV alone. After loading
this skill, Claude can recommend the right AOV tactic for a site's current state
and measure whether it actually worked.

Source material: *Billion Dollar Websites* Chapters 8–9; MCP source
`heatmap-mcp` repository (commit 275e777).

---

## The "Free Money" Principle

Every dollar increase in AOV is nearly pure profit. Fulfillment and acquisition
costs don't scale proportionally with order value. But AOV is only "free money"
if it doesn't suppress Conversion Rate enough to lower Revenue Per Session.

**The invariant:** Every AOV tactic must be measured by RPS, not by AOV alone.
A 20% AOV increase with a 25% CR decrease is a revenue loss. Always check RPS.

---

## The Nine AOV Strategies

### 1. Bundles

Two types:
- Multiple units of the same product (buy 3, get per-unit discount)
- Complementary products together (product A + product B + bundle discount)

Bundles increase AOV by raising the minimum purchase size. Fewer customers buy
(CR typically dips slightly), but those who do spend more. The winning bundle
is whichever configuration produces the highest RPS — not the highest AOV.

*Trigger:* Products frequently purchased together in the same order → test a
bundle at a meaningful per-unit discount (15–25%).

*MCP recipe:* See Recipe 2 (bundle effectiveness via `get_conversions`).

### 2. Buy Now, Pay Later (BNPL)

Klarna, Afterpay, and similar services. Customer pays in installments; you
receive full amount upfront (minus a small service fee). Strong lift for
high-ticket items ($200+). Rarely hurts CR; typically improves it by lowering
the psychological barrier of the full price.

*Trigger:* High-ticket product + meaningful cart abandonment → test BNPL
visibility in the buy box.

*15-minute implementation:* Add to Shopify checkout via the Klarna or Afterpay
app. No custom build needed.

### 3. Discounts and Cashback (Threshold Discounts)

Threshold discounts only increase AOV if the threshold is **above your current
AOV**. If your AOV is $75, offer a discount at $90+. Offering discounts at or
below your current AOV destroys both AOV and margin simultaneously.

Cashback (via Fondue or similar) is the preferred tactic for protecting margin:
customers pay full price and receive cashback credit redeemable on a future
purchase. This protects current AOV while incentivizing return purchases.

*Trigger:* Current AOV is below a natural bundle threshold → test a threshold
discount that pulls spend upward.

### 4. Price Testing

Most brands are afraid to raise prices. The empirical record says they should
test it. Run a three-way split test at +10%, +15%, and +25% of current price.
Price elasticity varies enormously by product. The winning price point is the
one with the highest RPS — which may be a higher price if the CR dip is small.

*Trigger:* No recent price test → this is the first AOV tactic to run. It
requires no design work and often produces the largest lift.

### 5. Free Gift Tiers

Set a "free gift" threshold above current AOV. "Spend $100 and receive X free"
pushes customers past the floor. The gift should have high perceived value and
low COGS (sample products, accessories, branded merchandise). Display the
threshold and a progress bar visibly in the cart.

*Trigger:* Cart page analysis shows users abandoning just below a round-number
threshold → set a free gift at that threshold.

### 6. Order of Product Tiles

Reordering collections page tiles by RPS (see `revenue-heatmap-playbooks` #4)
indirectly increases AOV by exposing visitors to higher-revenue products first.
Higher-priced products tend to have higher RPS when they're the right fit. This
is an indirect AOV lever but one of the fastest to implement.

### 7. The Power of Free (Free Shipping Threshold)

"Free shipping on orders over $X" is one of the highest-converting threshold
tactics in ecommerce. Set X above your current AOV. Add a progress bar in the
cart showing how far the customer is from the threshold. The Gator responds
powerfully to "almost there" completion states.

*Implementation:* Display the threshold and remaining amount dynamically in the
cart. "You're $12 away from free shipping" is more effective than a static
"Free shipping over $75" message.

### 8. Recommended Products ("You May Also Like")

Place below the Add to Cart button on PDPs. Use social-proof framing:
"Customers Also Bought" or "Customers Who Loved This Also Got." Off-the-shelf
Shopify recommendation apps are sufficient — no custom build needed.

### 9. Pre- and Post-Purchase Upsells

See Upsell Types section below.

---

## The Three Upsell Types

### Type A: Product-Page Upsell

"You may also like" on the PDP. Below the Add to Cart button. Products should
be same price or cheaper than the main product. Use platform recommendation
apps for dynamic personalization.

### Type B: Pre-Purchase (Cart) Upsell

"One-click buy" offer inside the cart, before checkout. Requirements:
- Must be an impulse purchase
- Must be cheap relative to the cart total (~10% of cart is the right impulse
  threshold; $29 leather cleaner on a $2,000 sofa purchase, not a $1,000 chair)
- Must be framed with a discount (minimum 25%, ideally 50% for low-COGS items)
- Must require only one click to add — no quantity selection, no variant choice

**The impulse-threshold rule:** If the upsell forces the customer back into
consideration mode (comparing options, evaluating the price relative to their
primary purchase, or choosing variants), it will trigger The Judge and may kill
the primary sale. The question The Gator should ask is "why not?" not "should I?"

Never offer bundles or subscription upgrades as pre-purchase cart upsells.
These require commitment-level thinking and summon The Judge at exactly the
wrong moment.

### Type C: Post-Purchase Upsell

Offered after the sale is complete. The Gator has purchased. The Judge has
gone back to sleep. These can be more aggressive than pre-purchase upsells.

Example (from the book): Customer bought 5 bottles of a supplement → offered
8 more at 50% off → if declined, offered 3 more at 50% off. Result: ~25% AOV
lift from post-purchase offers alone.

Post-purchase upsells virtually never damage brand perception. In years of
ecommerce data across hundreds of brands, customers who decline post-purchase
offers don't report negative brand experience. The small percentage who accept
them can transform unit economics.

### The Empty Cart Upsell

When a customer's cart is empty (they've arrived at /cart with nothing added),
show them a curated "You might like this" tray of high-RPS products. This is
one of the most underleveraged pages in ecommerce — typically it's shown a
blank state or just the "continue shopping" link. Fill it with your
highest-converting products as a last-chance revenue opportunity before the
session ends.

*MCP recipe:* Run `get_pageview_analytics` for the cart URL with
`filters: { "non_purchase": true }` to understand how many sessions hit an
empty cart and what they do next.

---

## MCP Recipes

### Recipe 1 — AOV-band segmentation (find your high-value buyers)

**Question:** What percentage of our sessions come from high-AOV buyers? Are
high-AOV buyers also high-CR buyers?

Use `SharedAnalyticsFilterInput.average_order_value_min/max` to segment.

```json
// High-AOV segment (above $100)
{
  "site_id": "<id>",
  "date_range": { "start": "2026-04-01", "end": "2026-04-30" },
  "metrics": ["SESSIONS", "REVENUE", "REVENUE_PER_SESSION", "CONVERSION_RATE"],
  "filters": { "average_order_value_min": 100.0 }
}
```

```json
// Low-AOV segment (under $50)
{
  "site_id": "<id>",
  "date_range": { "start": "2026-04-01", "end": "2026-04-30" },
  "metrics": ["SESSIONS", "REVENUE", "REVENUE_PER_SESSION", "CONVERSION_RATE"],
  "filters": { "average_order_value_max": 50.0 }
}
```

**Interpretation:** If high-AOV sessions are also high-CR, your best customers
respond to premium bundling. If high-AOV sessions have low CR, those buyers
need more convincing — they want the high-value purchase but hesitate more.
Target them with BNPL and threshold discounts. If low-AOV sessions have high
CR, your base is impulse buyers — target them with recommended products and
cart upsells.

---

### Recipe 2 — Bundle effectiveness via conversion data

**Question:** Are customers who see the bundle offer converting at a higher
RPS than those who don't?

```json
// Sessions that purchased (bundle buyers)
{
  "site_id": "<id>",
  "date_range": { "start": "2026-04-01", "end": "2026-04-30" },
  "conversion_name": "bundle_purchase",
  "breakdown": "CONVERSION_NAME"
}
```

If bundle purchases aren't tracked as a named conversion, use `get_conversions`
and compare AOV between the bundle-test period and the control period:

```json
{
  "site_id": "<id>",
  "date_range": { "start": "<test-start>", "end": "<test-end>" },
  "breakdown": "DATE"
}
```

vs. the same date range prior year or a matched control window. Watch for AOV
increase AND RPS increase. AOV up with RPS flat or down = bundle is hurting CR
enough to cancel the AOV benefit.

---

### Recipe 3 — Upsell uptake measurement via custom events

**Question:** What fraction of sessions that see the cart upsell actually add it?

This requires the upsell click to be instrumented as a custom event (e.g.,
`cart_upsell_clicked`). Then:

```json
{
  "site_id": "<id>",
  "date_range": { "start": "2026-04-01", "end": "2026-04-30" },
  "event_name": "cart_upsell_clicked",
  "breakdown": "EVENT_NAME"
}
```

Get total sessions for the same period from `get_session_analytics`. Compute:
`upsell_take_rate = cart_upsell_clicked_events / total_sessions`.

A take rate below 2% on a cart upsell means the offer or the price point is
wrong. A take rate above 8% is strong. The target is roughly 5–10% for a
well-tuned impulse offer.

---

### Recipe 4 — Free shipping threshold optimization

**Question:** At what order value do most abandoned sessions cut off? Where
should we set the free shipping threshold?

```json
// get_raw_pageview_analytics for the cart page
{
  "site_id": "<id>",
  "date_range": { "start": "2026-04-01", "end": "2026-04-30" },
  "page_url": "/cart",
  "filters": { "non_purchase": true },
  "include_revenue": true,
  "order_by": "SESSIONS",
  "sort_direction": "DESC"
}
```

This shows cart sessions that did not convert. Look at the `average_order_value`
of non-purchasing sessions — this tells you what cart totals visitors are
abandoning at. Set the free shipping threshold $10–15 above the median abandoned
cart value. This maximizes the number of visitors who are "close enough" to be
pushed over by the threshold incentive.

---

## Decision Rules

1. **Every AOV tactic is measured by RPS, not AOV.** A higher AOV with lower RPS is a failure.
2. **BNPL is the first tactic to implement on any high-ticket product.** 15 minutes to implement, almost never a net negative, often a significant CR lift.
3. **Threshold discounts must be set above current AOV.** Below-AOV discounts are guaranteed margin and AOV losses simultaneously.
4. **Pre-purchase cart upsells must be ~10% of cart total.** $29 on a $200 cart. Never a higher-priced sibling product. The question should be "why not?" not "should I?"
5. **Never offer bundles or subscriptions as pre-purchase upsells.** Commitment-level decisions summon The Judge at exactly the wrong moment.
6. **Post-purchase upsells: push hard.** The Gator has purchased and The Judge is asleep. The asymmetry strongly favors testing.
7. **Empty cart = revenue opportunity, not a dead end.** Show high-RPS products on the empty cart page.
8. **When asking which AOV tactic to try first:** BNPL → price test → bundles → upsells. In that order unless data suggests otherwise.

---

## Gotchas

**Gotcha 1 — Raising prices without testing.**
Price is the highest-sensitivity lever. Always run a three-way split before
committing to a new price point.

**Gotcha 2 — Recommended products above the Add to Cart button.**
This creates scroll debt and may send The Gator sideways to another product
before completing the primary purchase. Always below the CTA.

**Gotcha 3 — Bundle or multi-unit as pre-purchase cart upsell.**
That's a consideration purchase, not an impulse. Save bundles for the primary
Offer; reserve cart upsells for cheap complementary impulse items.

**Gotcha 4 — Celebrating AOV increases without checking RPS.**
The board wants AOV up. The business wants RPS up. They are not the same thing.
Always check RPS.

**Gotcha 5 — Ignoring post-purchase upsells because they feel aggressive.**
They don't damage brand perception. Years of data confirm this. Test them.

---

## Cross-references

- `heatmap-metrics-literacy` — AOV must always be evaluated via RPS impact
- `revenue-heatmap-playbooks` — product tile ordering and on-page upsell placement
- `cro-philosophy` — Gator/Judge model explains pre-purchase upsell design constraints
- `split-test-interpretation` — all AOV tactics should be tested with ICE prioritization and RPS as primary metric
- `heatmap-data-model` — `SharedAnalyticsFilterInput.average_order_value_min/max` and `get_conversions` used in recipes above

---

> **eBook Team flag:** The Empty Cart upsell and the ~10% impulse threshold
> rule are added in this version. The nine AOV strategies and three upsell types
> are book-accurate. Post-purchase upsell case study (5 bottles → 8 more at 50%
> off → ~25% AOV lift) is from the book.
