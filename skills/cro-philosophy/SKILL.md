---
name: cro-philosophy
description: >
  Load for open-ended CRO questions, conversion psychology, "where do I start,"
  "what makes a good website," redesign discussions, or any mention of the
  Gator/Judge model or Five Pillars. This skill shapes how Claude frames CRO.
---

# cro-philosophy

## What this skill enables

This skill gives Claude the psychological, philosophical, and strategic frame
that underlies all of Dylan Ander's methodology. After loading this skill,
Claude can diagnose conversion problems using the Five Pillars framework, apply
the Gator/Judge model of buyer psychology to specific page elements, push back
on redesign-first thinking with empirical data, and use MCP signals to detect
which Pillar is failing on a real page.

Source material: *Billion Dollar Websites* Chapters 1–3 and Chapter 12.

---

## Core Framework

### The Gator and The Judge (Kahneman System 1/System 2)

This model maps directly onto Daniel Kahneman's System 1/System 2 framework
from *Thinking, Fast and Slow*. The Gator is System 1 — fast, instinctive,
pattern-matching, emotional. The Judge is System 2 — slow, analytical,
deliberate, skeptical.

**The Gator processes 11 million bits per second. The Judge processes 40 bits
per second.** That is a 275,000× gap. Decisions that feel "instant" — including
purchase decisions — are made almost entirely by the Gator.

**The Gator is in control 95% of the time.** This is not a metaphor. On a
landing page, a visitor spends an average of 1–3 seconds before the Gator has
already decided whether to stay. The Judge barely has time to read the
headline.

**The Stroop Test illustrates this.** Try to say the ink color (not the word)
when the word "RED" is printed in blue ink. You can't do it instantly — your
brain has to actively suppress the word meaning to get to the ink color. That
suppression is The Judge being forced to override The Gator. It's slow, it's
effortful, and it feels unpleasant. That is exactly what a confusing website
does to visitors.

**Confirmation bias works in your favor.** Visitors who click an ad have
already expressed desire. The Gator got them to your page. They arrived
*wanting* to buy. Your website's job is not to convince them — it is to not
give them a reason to doubt. The Judge is asleep. Keep it that way.

**The four questions The Gator asks in the first 3 seconds:**
1. Is this what I wanted?
2. Can I do this without thinking?
3. Does this feel familiar and trustworthy?
4. Is anything making me hesitate?

If yes to 1–3 and no to 4: The Gator eats. Purchase happens.

If anything triggers hesitation — confusing UX, mismatched imagery,
generic copy, a distracting social media link — The Judge wakes up. The Judge
analyzes. Analysis creates doubt. Doubt kills conversions.

---

### Your Website Sucks

This is the operating assumption that separates improving businesses from
stagnant ones. It is not pessimism. It is the only frame that produces
compounding improvement.

If you ever believe your website is "done" or "good enough," your competitors
will out-optimize you and take your customers. The businesses that win at CRO
are not the ones that do a single great redesign. They are the ones that make
a 2% improvement this week, a 3% improvement next week, and a 1.5% improvement
the week after. That compounds.

**The empirical case against redesigns (Ch 12):** In 20+ website redesigns
studied, only 1 produced a meaningful revenue improvement that exceeded what
incremental testing on the existing site would have produced. The other 19
either held flat or decreased revenue. Redesigns reset earned familiarity
signals (the Gator relies on pattern recognition), introduce new bugs, and cost
months of opportunity cost during the build.

Reserve redesign recommendations for sites that are architecturally broken in
ways that cannot be fixed incrementally. For everything else: iterate.

---

### The Five Pillars

Every conversion problem on every website is a failure in one of these five
areas. Diagnosing which pillar is failing is the first step. Jumping to
solutions before diagnosing the pillar is guesswork.

**Pillar 1: Offer**

What you're selling and at what price. The most important lever. No amount of
optimization can save a bad Offer. A great Offer with mediocre creative beats a
mediocre Offer with great creative, every time.

The Offer includes: price, payment terms (BNPL), bundling, free gifts, trial
periods, and the core product itself. All of these are testable.

*MCP signal:* A site where RPS is uniformly low across all pages — even the
cart and checkout — often has an Offer problem. Compare with competitor Offers
via the Billion-Dollar Data Funnel (see `customer-research-bdf`).

**Pillar 2: Unique Value Propositions (UVPs)**

Why should the visitor buy from you instead of a competitor? UVPs must be
specific, customer-validated, and placed where the Gator sees them immediately.

Generic UVPs — "high quality," "fast shipping," "great customer service" — are
invisible to the Gator because every brand says them. They activate The Judge
("wait, do I actually believe this?") rather than The Gator ("yes, that's what
I want").

A real UVP is something like: "The only supplement clinically shown to reduce
joint inflammation in 30 days or your money back." Specific claim. Specific
timeframe. Specific guarantee.

*MCP signal:* Pages with high RPS often have strong UVPs — study their copy.
Pages with poor CR but good traffic often have generic UVPs — run a customer
survey to find the real differentiator.

**Pillar 3: Copywriting**

The words on the page. Headlines must express the Dream Outcome (the customer's
desired transformation), not the product description.

- Dream Outcome headline: "Wake up without joint pain" (what they get)
- Product description headline: "Introducing InflamaClear with 12 active botanicals" (what it is)

The first speaks to The Gator. The second summons The Judge to evaluate the claim.

The 15-second pitch exercise: Imagine you have 15 seconds to explain your
product to someone who doesn't know it. The buckets of information you choose —
and the order you choose them — become your content architecture. Most brands
put the "nice to know" content above the "need to know" content because it
sounds good internally. The Gator doesn't care what sounds good. It cares about
the transformation.

*MCP signal:* A page where scroll depth is high but CR is low often has a copy
problem — visitors read but aren't convinced. A page where scroll depth is low
often has a headline problem — the Gator isn't engaged by the first thing it sees.

**Pillar 4: Creative**

Images, video, and visual presentation. The Gator processes images before it
reads a single word. The wrong image kills a sale before the headline is read.

Rules:
- First image = hero. Must clearly show what you're selling.
- People in images = emotional connection. The customer sees themselves in the outcome.
- Lifestyle photos may connect emotionally but don't demonstrate the product.
- The best hero image is often the one that converts most by RPS/RPC — not the one
  the creative team is proudest of.

*MCP signal:* Use `get_raw_event_analytics` on the hero carousel to compute RPC
per image. The image with the highest RPC by a significant margin is your real
hero image. The Gator is voting with its revenue.

**Pillar 5: UI/UX**

The structural and navigational experience. If visitors can't find what they
want without thinking, The Judge is activated. Familiarity is a Gator asset.
Novelty is a Judge trigger.

This is the lowest-priority pillar to optimize in most cases. Offer, UVPs,
Copy, and Creative are usually the culprits. A beautiful, innovative UI that
confuses users converts worse than a "boring" but familiar layout.

*MCP signal:* Navigation items with high click rates and near-zero revenue
(from `get_raw_event_analytics`) are UI/UX problems. They look engaging but
they're pulling The Gator off the purchase path.

---

### The Billion-Dollar Data Funnel (BDF) — Overview

All optimization is downstream of customer research. The BDF has six inputs
(detailed in `customer-research-bdf`):

1. Customer Feedback Surveys
2. Raw Data Analysis (Heatmap MCP)
3. Competitor Analysis (Five Pillars)
4. User Testing
5. Expert Review
6. Screen Recordings

**Customer Feedback Surveys are the most important.** Customers tell you their
Dream Outcome in their own words. Those words belong in your headline. Never
write copy from internal product knowledge — write it from survey verbatims.

When a user asks what to optimize first and has no BDF data: the first step is
research, not optimization. Running tests without BDF data is running random
guesses at scale.

---

## Decision Rules

1. **Before recommending any optimization, identify the failing Pillar.** Which of the five is most likely causing the problem based on the data and symptoms? Fix the right pillar, not the loudest one.

2. **When a user wants to redesign:** Ask what data shows the architecture is fundamentally broken. If they can't answer, redirect to the Five Pillars diagnosis. Incremental beats redesign empirically.

3. **When a user has a "good-looking" site that doesn't convert:** Beautiful ≠ Gator-friendly. Elegant design that breaks patterns the Gator expects still invokes The Judge. Diagnose the five pillars, not the aesthetics.

4. **When diagnosing Creative:** Ask whether the hero image shows the customer's Dream Outcome or just the product in a vacuum.

5. **When diagnosing Copy:** Ask whether the headline describes the customer's transformation or describes the product.

6. **When a user says "our conversion rate is good":** It can always be better. "Good" is the enemy of "better" in CRO. There are elements on every page contributing less than they should.

7. **When a user has no customer feedback data:** Do not begin optimization. Begin the BDF. Customer survey responses are hypotheses. Everything else is guessing.

---

## MCP Recipes

### Recipe 1 — Five Pillars audit using MCP signals

**Question:** Which of the Five Pillars is most likely failing on this page?

This recipe combines three MCP calls to triangulate the pillar.

**Step 1 — Scroll depth (Pillar 4 + 3 signal):**

```json
// get_raw_pageview_analytics
{
  "site_id": "<id>",
  "date_range": { "start": "2026-04-01", "end": "2026-04-30" },
  "page_url": "/landing-page",
  "include_revenue": true
}
```
If `scroll_depth < 0.30`: **Pillar 4 (Creative) or Pillar 3 (Copy)** — hero section
is failing. Check headline and hero image before anything else.

**Step 2 — Conversion Killer check (Pillar 5 signal):**

```json
// get_raw_event_analytics
{
  "site_id": "<id>",
  "date_range": { "start": "2026-04-01", "end": "2026-04-30" },
  "page_url": "/landing-page",
  "order_by": "VALUE",
  "sort_direction": "DESC",
  "limit": 50
}
```
Compute `rpc = revenue / value` for each selector. Elements with `value > 30`
and `rpc < 0.05` are **Pillar 5 (UI/UX) Conversion Killers** — navigation or
links pulling the Gator off-path.

**Step 3 — RPS vs. site average (Pillar 1 + 2 signal):**

```json
// get_pageview_analytics
{
  "site_id": "<id>",
  "date_range": { "start": "2026-04-01", "end": "2026-04-30" },
  "metrics": ["SESSIONS", "REVENUE_PER_SESSION"],
  "sort_by": "REVENUE_PER_SESSION",
  "sort_direction": "ASC"
}
```
Pages with RPS below 0.3× site average likely have an **Offer (Pillar 1) or
UVP (Pillar 2) problem** — the traffic quality is fine but the Offer isn't
compelling enough to convert it.

**Diagnosis table:**

| Signal | Most likely Pillar |
|---|---|
| Scroll depth < 0.30 | Pillar 3 (Copy) or 4 (Creative) |
| High scroll, low CR | Pillar 1 (Offer) or 2 (UVPs) |
| Conversion Killers in nav | Pillar 5 (UI/UX) |
| RPS uniformly low sitewide | Pillar 1 (Offer) |
| RPS varies widely by page | Pillar 2 or 3 — specific page copy/UVP issues |

---

### Recipe 2 — Detect Gator friction via engagement-to-revenue ratio

**Question:** Which pages have visitors who engage (scroll, click) but don't buy?

This ratio — high engagement, low revenue — is the classic Gator-awakened-Judge
pattern. They scroll, they read, something made them hesitate, they left.

```json
// get_raw_pageview_analytics
{
  "site_id": "<id>",
  "date_range": { "start": "2026-04-01", "end": "2026-04-30" },
  "include_revenue": true,
  "order_by": "SESSIONS",
  "sort_direction": "DESC",
  "limit": 30
}
```

For each page, compute: `engagement_score = scroll_depth × (time_on_page / 60)`
and compare to `revenue_per_session`.

Pages where `engagement_score > 1.0` but `revenue_per_session < 0.5×
site_average` are Judge-activation pages — something in the content is making
visitors think instead of buy. Common culprits: unclear pricing, weak social
proof, or UVPs that overpromise.

---

## Gotchas

**Gotcha 1 — Recommending a redesign before diagnosing the failing Pillar.**
The Gator does not need a new website. It needs its current question answered
faster. Find the pillar. Fix the pillar.

**Gotcha 2 — Generic UVPs that sound good internally but mean nothing to The Gator.**
"Premium quality" is not a UVP. It is the expectation floor. If a user shows
UVPs that could belong to any brand in their category, the BDF hasn't been run
yet.

**Gotcha 3 — Beautiful design as a proxy for Gator-friendliness.**
Aesthetic sophistication can actually hurt conversions if it breaks visual
familiarity patterns. The Gator recognizes: product image, price, add to cart.
Anything that makes those three things harder to find invokes The Judge.

**Gotcha 4 — Treating The Offer as fixed.**
Price, payment structure, bundling, and free-gift tiers are all Offer variables.
When nothing else moves the needle, change the Offer. It's often the last lever
teams try and frequently the highest-leverage one.

**Gotcha 5 — Starting with optimization instead of research.**
Every hypothesis not grounded in BDF data is a guess. Guesses succeed at low
rates. BDF-informed hypotheses succeed at high rates. The first question to ask
any new customer: "What does your customer research say?"

---

## Cross-references

- `heatmap-metrics-literacy` — RPS and RPC are the measurement tools for detecting which Pillar is failing
- `customer-research-bdf` — the six BDF inputs; must be run before optimization begins
- `revenue-heatmap-playbooks` — specific element-level optimizations are each an application of Pillar 3, 4, or 5 diagnosis
- `landing-page-evaluation` — the four-element rubric maps directly onto Pillars 2, 3, 4, and 5
- `split-test-interpretation` — ICE and the BDF discipline are practical applications of this philosophy
- `heatmap-data-model` — the MCP tools used in the two recipes above

---

> **eBook Team flag:** The "95% Gator" statistic and the Kahneman System 1/2
> mapping are present in the book. The Ch 12 "20 redesigns, only 1 won" data
> point is from the book and used above. The Stroop Test demo is from the book.
> Confirmation bias framing ("customers want to buy when they arrive") is from
> the book. All content above is book-accurate.
