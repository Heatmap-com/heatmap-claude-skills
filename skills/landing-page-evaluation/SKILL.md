---
name: landing-page-evaluation
description: >
  Load when the user asks about landing pages, listicles, advertorials, VSLs,
  product pages as landing pages, headlines, hero images, buy boxes, social
  proof, the 1-Second Test, or "why isn't my landing page converting."
---

# landing-page-evaluation

## What this skill enables

This skill gives Claude Dylan Ander's five-type landing page taxonomy, the
four-element evaluation rubric, the 1-Second Test, and the full set of
tactical improvements for each element type — together with four MCP recipes
that diagnose underperforming pages with real data. After loading this skill,
Claude can evaluate any landing page, identify the failure point, recommend
the right page type, and prescribe specific fixes backed by data.

Source material: *Billion Dollar Websites* Chapters 6–7; MCP source at
`heatmap-mcp` repository (commit 275e777).

---

## The 1-Second Test

Before any detailed analysis: show your landing page for exactly 1 second and
ask: what product is being sold, and what is it promising me?

If both questions can be answered in 1 second, the hero section is passing.
If either cannot be answered, you have a hero section failure — fix that before
optimizing anything else.

The Gator makes a stay/leave decision in the first 1–3 seconds. The headline
and hero image must immediately answer: "Is this what I wanted?"

---

## The Four-Element Rubric

Every landing page is evaluated on these four elements. If any one fails, the
page fails.

### 1. Headline and Subheadline

**Headline = Dream Outcome.** The result the customer wants. Not what the product
does — what the customer gets.

- "Stop Joint Pain in 30 Days" ✓ (Dream Outcome)
- "Introducing InflamaClear Supplements" ✗ (product description)

**Subheadline = The Vessel.** How your product delivers the Dream Outcome.
"The breakthrough formula that targets inflammation at the source."

**Pain-point headlines are equally valid.** Not all Dream Outcome headlines
lead with the positive. Pain-point framing is often stronger for audiences who
are actively suffering: "Discover the top doc's secret to reducing joint aches
and inflammation" leads with the problem. Both pain-point and dream-outcome
paths are valid. Use customer survey language to determine which frame resonates
more with your specific audience.

**How to validate the headline:** Pull your Customer Feedback Survey responses
to "why did you buy?" and "what problem were you trying to solve?" The exact
language customers use is the language that converts. Use their words, not yours.

**The 15-second pitch exercise:** Imagine you have 15 seconds to explain your
product to someone who's never heard of it. List the information you'd include
and the order. That sequence is your content bucket architecture. Most brands
put "nice to know" content above "need to know" content because it sounds
good internally. Reverse this. The Gator doesn't care what sounds good.

**Product name convention:** Use a descriptive adjective in your product name
on the page ("Ultra-Strength InflamaClear" not just "InflamaClear"). The
adjective activates the Gator's pattern-matching before the Judge reads the
full label.

---

### 2. Hero Image

- The first image must make it immediately clear what you're selling.
- Include people — the customer sees themselves in the outcome.
- Product as hero: the product should be the dominant subject, not a lifestyle
  setting that happens to include the product.
- 100% of visitors see the hero section. If screen recordings show immediate
  bounces, the hero is failing the 1-Second Test.

**Measure hero effectiveness:** Use `get_raw_event_analytics` on the carousel.
Compute `rpc = revenue / value` per image. The image with the highest RPC is
the Gator's preferred image — make it position 1 regardless of what the
creative team prefers.

---

### 3. Buy Box

- Price must be obvious without scrolling. Zero hunting.
- Create a unique Offer in the buy box: bundle option, free gift, BNPL, limited
  availability.
- **The power of "free":** Free shipping threshold, free gift with purchase,
  free trial. The word "free" activates the Gator disproportionately.
- Scarcity must be real and specific. "Only 12 left in stock" is a Gator
  accelerant. Fake scarcity is an integrity problem that also fails with
  sophisticated shoppers.
- **CTA button text:** "Add to Cart" is the default. Test alternatives that are
  more specific to the product and outcome: "Start My 30-Day Challenge," "Get
  My Kit," "Claim Free Shipping." The CTA text that converts is the one that
  matches the customer's self-narration as they click.
- **FAQ positive-language rewrites:** In the buy box or nearby FAQ, rewrite
  defensive language as positive framing. "Return policy" → "Money-back
  guarantee." "Shipping terms" → "Free shipping on orders over $X." The Gator
  responds to positive framing; the Judge is activated by defensive framing.
- For PDP-as-LP: Add to Cart button must be visible **above the fold** on
  mobile. Any layout where the first fold doesn't include the CTA on a 480×800
  mobile viewport is a conversion structure failure.

---

### 4. Social Proof

Required. A page with zero reviews loses a large fraction of Gators before The
Judge even shows up.

**Types that work:**
- Customer reviews (ecommerce) — select reviews that confirm the Dream Outcome
- Testimonials (B2B/service)
- Volume indicators: "Join 50,000+ customers"
- Expert endorsements and doctor/professional recommendations
- **Press and news mentions:** "As Seen In" bars with publication logos (Forbes,
  Healthline, Men's Health) are strong Gator signals. They activate social
  proof via authority, not individual experiences.
- Star ratings with a review count: "4.8 ★ (2,847 reviews)" — the count
  matters as much as the rating.

**Review curation rules:**
- Read 5–10 actual reviews before picking display reviews
- Cap curated display reviews at 20 — more than 20 displayed reviews adds
  cognitive load without proportional trust increase
- Select reviews that match the Dream Outcome the customer came for
- Include reviews that address the #1 purchase hesitation (e.g., "I was
  worried about X but...")

**About the Founder section:** For health/wellness, supplements, and
personal-brand-adjacent products, an About the Founder section with a real
photo and a personal story of the problem the founder solved converts as a
trust and authority signal. The Gator wants to know a real person made this.

---

## The Five Page Types

### 1. Long-Form Landing Page

Default ecommerce landing page format. 8–10+ sections. Appropriate for:
high-ticket products ($200+), products requiring education, products where
The Judge needs detailed reassurance before buying.

*Diagnostic:* Use `get_raw_pageview_analytics` scroll depth. Identify at which
bucket the scroll drops and check RPS by section via `get_raw_event_analytics`.

### 2. Listicle ("Five Reasons Why…")

Educational format. Presents 5+ reasons to buy before showing the buy box.
**No selling before the buy box.** The reader should feel informed, not sold to.
The buy box comes at the end and must be aggressive.

*Best for:* Health products, consumables, products with genuine differentiated
UVPs vs. competitors, cold skeptical audiences.

**Five Golden Rules (book-accurate):**

1. **Educate First, Sell Later.** Third-party, non-sales tone throughout until
   the buy box. If it reads like an ad, it fails as a listicle.
2. **Headline from customer surveys.** Use pain-point OR dream-outcome framing —
   both work. "Discover this top doc's secret to reducing joint aches" (pain-point)
   and "5 Reasons Thousands Are Now Waking Up Without Joint Pain" (dream-outcome)
   are both valid. Use your survey verbatims to decide which frame fits your
   audience better.
3. **Optimize for scroll depth.** Every section must earn the next scroll to get
   the reader to the buy box.
4. **Use images of people, not products.** Until the buy box, the images should
   show the transformation, not the thing.
5. **Sell hard at the buy box.** The reader has been educated for 5+ reasons. By
   the time they reach the buy box, The Judge is satisfied. The buy box should be
   aggressive — strong offer, scarcity, and a prominent CTA.

### 3. Advertorial

Story-based, written from a third-party perspective (customer, journalist,
blogger). The brand is not the narrator. Direct-response copywriting is the
#1 reason advertorials fail — any whiff of "the brand telling me to buy"
breaks the format.

*Best for:* Health/wellness, products with truly compelling founder stories,
audiences that have become immune to standard ad copy.

*Diagnostic:* Scroll depth is the primary signal. If readers aren't making it
through the story, the narrative isn't connecting. Validate with BDF survey
data.

### 4. Video Sales Letter (VSL)

Long-form landing page with a hero video above the fold. Video sells; page
copy reinforces for visitors who don't watch.

*Best for:* Education-heavy products, health/wellness, SaaS lead generation,
native advertising traffic.

*Video structure:* Hook (2–3 seconds) → Customer Pain → Dream Outcome →
The Vessel (your product) → Social Proof → Hard Sell.

**Never host the video on YouTube.** YouTube's recommended-video sidebar and
click-away behavior take the viewer out of your funnel and into YouTube's
engagement loop. Use Vimeo or a private video host. Monitor video completion
rates — drop-off point data tells you where the narrative loses the viewer.

### 5. Product Page as Landing Page (PDP-as-LP)

Effective when product appearance drives purchasing. Fashion, art, highly
visual products, and any product where seeing it is believing it.

**Mobile-first design is mandatory.** Design for a 480×800 viewport (smallest
common mobile screen). At this viewport:
- Product carousel must be visible above the fold
- "Add to Cart" button must be visible above the fold
- Price must be visible without scrolling

Above the fold contains: product image carousel + buy box. The page does the
work, not the copy.

*Diagnostic:* Use `get_heatmap` with `event_type: "CLICK"` on the PDP URL to
see which elements receive clicks. Combine with `get_raw_event_analytics` to
add revenue attribution to each clicked element.

### Page Type Matching

| Product Type | Recommended Page Type |
|---|---|
| High-ticket, education-heavy | Long-Form |
| Health/wellness, differentiated UVP | Listicle or Advertorial |
| SaaS, service, native ad traffic | VSL |
| Visual/fashion products | Product Page (PDP-as-LP) |
| Highly skeptical audience | Advertorial |
| Cold paid traffic, broad offer | Long-Form or Listicle |
| Low-ticket, impulse visual product | Product Page (PDP-as-LP) |

---

## MCP Recipes

### Recipe 1 — 1-Second Test validation via click heatmap

**Question:** Are visitors engaging with the hero section or bouncing
immediately?

```json
// get_heatmap — CLICK events on the landing page
{
  "site_id": "<id>",
  "date_range": { "start": "2026-04-01", "end": "2026-04-30" },
  "url": "/your-landing-page",
  "event_type": "CLICK",
  "limit": 50
}
```

```json
// get_raw_pageview_analytics — scroll depth
{
  "site_id": "<id>",
  "date_range": { "start": "2026-04-01", "end": "2026-04-30" },
  "page_url": "/your-landing-page",
  "include_revenue": true
}
```

**Interpretation:** If `scroll_depth < 0.25` AND most clicks are concentrated
in the header/nav area → 1-Second Test is failing. Visitors are leaving before
reading the headline. Fix: rewrite headline as Dream Outcome or pain-point;
replace hero image with best-converting image by RPC.

---

### Recipe 2 — Four-element diagnostic score

**Question:** Which of the four elements (Headline, Hero, Buy Box, Social
Proof) is most likely failing?

```json
// get_raw_event_analytics — buy box area
{
  "site_id": "<id>",
  "date_range": { "start": "2026-04-01", "end": "2026-04-30" },
  "page_url": "/your-landing-page",
  "order_by": "VALUE",
  "sort_direction": "DESC",
  "limit": 30
}
```

Map each selector to the four elements:
- Selectors matching `h1`, `hero`, `headline` → Element 1 (Headline)
- Selectors matching carousel, `img`, `product-image` → Element 2 (Hero)
- Selectors matching `add-to-cart`, `cta`, `buy`, price elements → Element 3 (Buy Box)
- Selectors matching `review`, `testimonial`, `stars`, `rating` → Element 4 (Social Proof)

Score each element by total `revenue` attributed to its selectors. The element
with the lowest revenue relative to its click volume is the failure point.

**Diagnostic table:**

| Scroll depth | Social proof clicks | Buy box clicks | Diagnosis |
|---|---|---|---|
| < 0.25 | Low | Low | Headline or Hero failure (1-Second Test) |
| 0.25–0.55 | Low | Low | Mid-page content losing them (listicle flow) |
| > 0.55 | High | Low | Buy box failure (unclear price/offer/CTA) |
| > 0.55 | Low | High but low CR | Social proof failure (reviews not convincing) |

---

### Recipe 3 — Page type classifier

**Question:** What type of landing page should this user use?

This is a decision recipe, not a data recipe. Ask the user:
1. What is the product and its price point?
2. What traffic source is driving to this page?
3. Is the product primarily visual (fashion, art) or education-dependent (supplements, SaaS)?
4. How skeptical is the typical buyer (cold paid traffic vs. warm retargeting)?

Then apply the matching table above. To validate the recommendation with data:

```json
// get_session_analytics — traffic source breakdown
{
  "site_id": "<id>",
  "date_range": { "start": "2026-04-01", "end": "2026-04-30" },
  "metrics": ["SESSIONS", "REVENUE_PER_SESSION"],
  "group_by": ["DATE"],
  "filters": { "new_user": true }
}
```

High new-user traffic = cold audience = education-first page type (Listicle or
Advertorial). High returning-user traffic = warm audience = page type matters
less; focus on Offer and buy box optimization.

---

### Recipe 4 — PDP-as-LP mobile diagnostic

**Question:** Is our Product Page functioning as an effective landing page on mobile?

```json
// get_raw_pageview_analytics — mobile filter
{
  "site_id": "<id>",
  "date_range": { "start": "2026-04-01", "end": "2026-04-30" },
  "page_url": "/products/your-product",
  "filters": { "device": "MOBILE" },
  "include_revenue": true
}
```

Check `scroll_depth` and `conversion_rate` for mobile. Compare to desktop
(run same query with `device: "DESKTOP"`).

If mobile `conversion_rate` is < 0.4× desktop: the mobile layout is failing
the PDP-as-LP test. Most likely cause: Add to Cart button is not above the
fold at 480×800. Second likely cause: carousel images are not loading correctly
on mobile. Third: price is not immediately visible.

---

## Decision Rules

- **1-Second Test first.** Before any other diagnostic, run the 1-Second Test. If the page fails it, nothing else matters until headline and hero image are fixed.
- **Scroll depth < 0.25:** Hero section failure — fix headline and hero before anything else.
- **Scroll depth > 0.55 but CR < 0.5× site average:** Buy box or social proof failure — diagnose those two elements next.
- **Page type selection:** Ask product category, price point, and traffic source. Match to the table. Default to Listicle for cold paid traffic on consumables.
- **Listicle Rule:** Educate First, Sell Later. Never use direct sales language before the buy box.
- **Listicle headline:** Use either pain-point OR dream-outcome framing — use survey verbatims to choose.
- **Social proof minimum:** At least one visible rating and at least one testimonial that addresses the Dream Outcome. No reviews = fix this before anything else.
- **PDP-as-LP:** Add to Cart above the fold at 480×800 mobile viewport is non-negotiable.

---

## Gotchas

**Gotcha 1 — Product-description headlines.**
"Introducing X Product" is not a headline. Rewrite every product-description
headline as Dream Outcome or pain-point before touching anything else.

**Gotcha 2 — Sales tone in Advertorials.**
The moment an Advertorial uses "buy now," "limited offer," or any brand
first-person language before the buy box, the format is broken.

**Gotcha 3 — Hosting VSL video on YouTube.**
YouTube click-aways are a funnel leak that cannot be patched with landing page
copy. Use Vimeo or a private host.

**Gotcha 4 — FAQs below the fold on paid traffic landing pages.**
Cold traffic has more objections. Move FAQs higher on paid traffic pages.

**Gotcha 5 — Long-Form for a $15 visual product.**
An impulse-buy product with strong imagery doesn't need 10 sections of copy.
Use Product Page (PDP-as-LP). Overbuilt pages create friction for The Gator.

---

## Cross-references

- `cro-philosophy` — Gator/Judge model and Five Pillars explain every element of the rubric
- `heatmap-metrics-literacy` — use RPC to evaluate hero images; scroll depth and time-on-page for content engagement
- `revenue-heatmap-playbooks` — Bucket Testing by RPS for long-form section ordering; carousel image ordering
- `customer-research-bdf` — customer survey verbatims are the source of headline language and the Five Golden Rules validation
- `split-test-interpretation` — landing page tests use ICE and require Quadrant-filtered results
- `aov-strategies` — buy box optimization (BNPL, free gift, threshold offers) connects here
- `heatmap-data-model` — `get_raw_event_analytics`, `get_heatmap`, `get_raw_pageview_analytics` used in recipes above

---

> **eBook Team flag:** Listicle Rule 1 corrected to "Educate First, Sell Later"
> and Rule 2 broadened to include pain-point headlines. Added: 1-Second Test,
> 15-second pitch, About the Founder, FAQ positive-language rewrites, press
> mentions, product-name adjective, CTA copy customization, mobile-first 480×800,
> Add-to-Cart-above-fold for PDP-as-LP. "Never host on YouTube" rule verified
> as VSL-specific in the book; applicability to all video on all landing pages
> should be confirmed with Dylan.
