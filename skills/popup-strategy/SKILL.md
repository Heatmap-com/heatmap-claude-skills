---
name: popup-strategy
description: >
  Load when the user asks about popups, email capture, exit-intent triggers, the
  8-second rule, or phrases like "should we use a popup," "what makes a good
  popup," "our popup isn't converting," or "capture more emails."
---

# popup-strategy

## What this skill enables

This skill gives Claude Dylan Ander's complete popup framework from Chapter 11:
the two strategic modes, three structures, four offer types, four trigger types,
the 8-second magic number, and the Perfect Popup template. Together with four
MCP recipes that measure popup performance against real user data. After loading
this skill, Claude can recommend the right popup configuration for a site's goals,
diagnose underperforming popups, and measure whether a popup change improved or
hurt revenue.

Source material: *Billion Dollar Websites* Chapter 11.

---

## The Two Strategic Modes

### High-Volume, Low-Intent (HVLI)

Goal: Capture as many email/SMS addresses as possible from a large audience
that isn't ready to buy yet.

This is the standard ecommerce popup play. The audience arrived via paid ads
or organic discovery. Conversion probability is low (~0.5–1%). The email or
SMS address captured is worth more over time than the first-visit purchase
probability.

**Best popup types for HVLI:** Discount offers, spin-to-win, question popups
that lead to a personalized offer.

**Key metric:** Email capture rate (captured emails / popup impressions).
Target: 3–8% capture rate for a well-tuned HVLI popup.

### Low-Volume, High-Intent (LVHI)

Goal: Qualify the visitor (identify serious buyers) and gather contact info
for high-touch follow-up.

This mode applies to high-ticket products ($500+), B2B, or any sale that
requires a human touchpoint (consultation, demo, custom quote). The popup goal
is not email capture for broadcast — it's identifying who is worth a phone call.

**Best popup types for LVHI:** Question popup that segments intent level;
cashback offer for those who don't convert on first visit.

**Key metric:** Lead quality (qualified leads / popup submissions), not raw
capture rate.

---

## The Three Structures

### Single-Step Popup

One action: enter email and submit. Lowest friction. Best for straightforward
discount offers where the value exchange is immediately obvious ("Get 15% off
— enter your email").

Best for: High-discount offers, simple lead magnets.

### Two-Step Popup

Step 1: Ask a question or confirm intent ("Want 15% off your first order?")
Step 2: Email capture form.

The Yes/No first step activates micro-commitment. Users who click "Yes" in
Step 1 are more likely to complete Step 2 because they've already said yes to
the concept. Conversion rates on two-step popups are consistently higher than
one-step popups with equivalent offers.

### Three-Step Popup (The Perfect Popup structure)

Step 1: Qualifying question ("What are you shopping for today?" or a
pain-point/intent question)
Step 2: Email/phone capture ("Great! We'll send your personal recommendation")
Step 3: Confirmation + offer delivery ("Here's your 15% off code: WELCOME15")

The three-step structure does three things: it segments the user by intent (Step
1), captures contact info (Step 2), and delivers the offer with a reason to use
it now (Step 3). This is the highest-converting popup structure for HVLI use
cases.

---

## The Four Offer Types

### 1. Discount Offer

The most common: "Get X% off your first order." Immediate, clear, simple.
Works best when your product has a margin to absorb the discount AND when
price is a meaningful purchase barrier.

Risk: conditions visitors to wait for discounts. Use judiciously if you're
protecting margin.

### 2. Question / Personalization Offer

"What's your biggest pain with [category]?" or "What are you shopping for?"
Leads to a personalized recommendation or curated product page. No discount
required. Builds engagement before asking for email.

Best for: multi-product catalogs where personalization adds real value.

### 3. Spin-to-Win

Gamified offer. Users spin a wheel to win discounts of varying sizes (10%, 15%,
20% off, or "free gift"). High engagement rate; strong email capture. The Gator
responds to game mechanics. Segment your prize distribution so the odds favor
your most sustainable discount level.

### 4. Cashback Offer

"Pay full price today. Receive $X cashback credit for your next purchase."
Protects current-order margin while incentivizing a return purchase. Best for
repeat-purchase categories (consumables, subscriptions, refillable products).

---

## The Four Trigger Types

### 1. Time Trigger

Show popup after X seconds on the page. The 8-second rule is the benchmark:
wait at least 8 seconds before showing any popup. Popups that appear in the
first 5 seconds interrupt The Gator before it has formed any purchase intent.
Popups that appear after 8 seconds have higher capture rates than popups shown
earlier.

*Magic number:* 8 seconds.

### 2. Exit-Intent Trigger

Show popup when the user moves their cursor toward the browser chrome (desktop)
or exhibits a backward navigation gesture (mobile). The user is about to leave —
the popup gives The Gator one last decision point.

This is the lowest-interruption trigger. The session is about to end anyway,
so there is no opportunity cost from the interruption. Exit-intent popups
typically have higher conversion rates per impression than time-triggered popups.

### 3. Scroll Depth Trigger

Show popup after the user scrolls past X% of the page. 50% scroll depth is a
strong signal of engagement — the user has seen enough to be interested. A
popup at 50% scroll captures a more engaged audience than a time trigger.

### 4. Combination Trigger

Exit-intent **or** scroll depth trigger, whichever comes first. This is the
trigger used in the Perfect Popup template below. It captures both high-intent
scrollers (who reached 50%) and about-to-leave visitors, maximizing capture
without interrupting early-session browsing.

---

## The Perfect Popup Template

This is the highest-performing popup configuration for HVLI ecommerce,
combining all of the above:

**Structure:** 3-step
**Trigger:** Exit-intent OR 50% scroll depth, whichever comes first
**Offer:** Double discount — initial discount (15% off) shown at sign-up, with
a follow-up email offering a second, higher discount (25% off) for those who
don't use the first code within 48 hours

**Step 1:** Engaging question that creates micro-commitment ("What's stopping
you from grabbing this today?" or "What's your primary concern about [category]?")

**Step 2:** Email capture with the offer framing ("Enter your email and we'll
send your personal 15% off code")

**Step 3:** Confirmation and code delivery ("Your code is WELCOME15 — check
your email too for a special follow-up offer")

**Why double discount works:** Many first-visit abandoners don't use the first
discount. The follow-up email with a second, slightly higher discount converts
a meaningful fraction of those who got the first offer but didn't act on it.
The two-offer sequence is more effective than a single offer at either level.

---

## Popup Goals (What Are You Capturing For?)

- **Email address:** Standard ecommerce. Used for abandoned cart flows,
  welcome sequences, promotional broadcasts.
- **Phone number:** SMS marketing (Klaviyo, Postscript, Attentive). Higher
  open rates than email; more valuable per contact but higher opt-out risk.
  Never replace email capture with phone-only; collect both if possible.
- **Qualified customer ID (LVHI):** The popup is a qualification tool, not a
  capture tool. The submission goes to a CRM for sales follow-up, not a
  broadcast email list.

---

## When CRO Owns Popups vs. Email Marketing

CRO owns: popup structure, trigger timing, offer design, step flow, A/B testing.

Email marketing owns: what happens after the capture — welcome sequence content,
broadcast schedule, follow-up offer timing, unsubscribe management.

These must be coordinated: a popup that promises "15% off" but delivers a
welcome sequence that leads with brand story before the discount code will have
high unsubscribe rates and low discount utilization.

---

## MCP Recipes

### Recipe 1 — Popup impression and capture rate

**Question:** What fraction of sessions see the popup and what fraction submit?

This requires the popup platform to instrument two custom events:
- `popup_shown` (one event per popup impression)
- `popup_submitted` (one event per completed submission)

```json
// get_custom_events — popup impressions
{
  "site_id": "<id>",
  "date_range": { "start": "2026-04-01", "end": "2026-04-30" },
  "event_name": "popup_shown",
  "breakdown": "EVENT_NAME"
}
```

```json
// get_custom_events — popup submissions
{
  "site_id": "<id>",
  "date_range": { "start": "2026-04-01", "end": "2026-04-30" },
  "event_name": "popup_submitted",
  "breakdown": "EVENT_NAME"
}
```

Get total sessions from `get_session_analytics` for the same period.

**Compute:**
- `impression_rate = popup_shown_events / sessions` — what % of sessions see the popup
- `capture_rate = popup_submitted_events / popup_shown_events` — what % of popup viewers submit

**Benchmarks:**
- Impression rate < 30%: the trigger is too restrictive (e.g., 8-second trigger
  but most sessions are < 8 seconds long). Consider exit-intent OR scroll trigger.
- Capture rate < 2%: offer is too weak or structure has too much friction. Test
  a better offer or reduce to a single-step structure.
- Capture rate > 8%: strong — test a higher-quality offer to improve downstream
  email conversion, not just capture volume.

---

### Recipe 2 — Popup-engaged vs. non-engaged purchase comparison

**Question:** Do visitors who interact with the popup (even those who dismiss
it) buy at a different rate than visitors who never saw the popup?

```json
// Sessions that saw popup and purchased
// (requires purchase + popup_shown instrumentation on same session)
{
  "site_id": "<id>",
  "date_range": { "start": "2026-04-01", "end": "2026-04-30" },
  "filters": { "purchase": true }
}
```

Compare `sessions` from the `purchase: true` filter to `sessions` from the
`popup_submitted` custom event total. Also compare CR for periods before and
after popup implementation.

**Key question:** Did the popup improve overall site RPS, or did it cannibalize
purchases (interrupting buyers who would have converted without the discount)?

If RPS declined after popup implementation: the popup discount is cannibalizing
buyers who didn't need the offer. Test raising the trigger threshold (later
timing, higher scroll depth) to show the popup only to true abandoners.

---

### Recipe 3 — Trigger A/B comparison

**Question:** Does an exit-intent trigger capture more revenue than a time-based
trigger?

Run two variants instrumented with separate custom events:
- `popup_variant_a_shown` + `popup_variant_a_submitted` (time trigger, 8 seconds)
- `popup_variant_b_shown` + `popup_variant_b_submitted` (exit-intent trigger)

```json
{
  "site_id": "<id>",
  "date_range": { "start": "<test-start>", "end": "<test-end>" },
  "event_name": "popup_variant_a_submitted",
  "breakdown": "DATE"
}
```

Repeat for variant B. Compare capture rates. Also compare sitewide RPS for the
same period with `filters: { "new_user": true }` — the popup primarily affects
first-time visitors.

---

### Recipe 4 — Perfect Popup deployment audit

**Question:** Does our current popup match the Perfect Popup template?

This is a checklist recipe (no MCP call required):

- [ ] Three-step structure (or at minimum two-step)
- [ ] Trigger: exit-intent OR 50% scroll depth, NOT a fixed time < 8 seconds
- [ ] Offer includes a discount or clear value exchange
- [ ] Step 1 asks a qualifying question (not just "Enter email for 10% off")
- [ ] Step 3 delivers the code immediately AND sets expectation for follow-up
- [ ] Double discount follow-up email exists in the welcome sequence
- [ ] `popup_shown` and `popup_submitted` custom events are instrumented
- [ ] Popup is mobile-responsive and tested at 480×800 viewport

If any item is unchecked, that is the first thing to fix before testing offer type
or creative.

---

## Decision Rules

1. **Default to exit-intent OR 50% scroll trigger.** Never show a popup before 8 seconds. Never show a popup on the first pageview of the first visit without at least one engagement signal.
2. **Start with the Perfect Popup template before testing variants.** Establishing a baseline on the best-known configuration first gives you a real benchmark to beat.
3. **Measure by RPS, not by email capture rate.** A popup that collects 10,000 emails but reduces sitewide RPS is a bad popup. Email capture is an intermediate metric; downstream revenue is the real metric.
4. **Instrument before testing.** `popup_shown` and `popup_submitted` events must be instrumented before running any popup A/B test or you will have no data to analyze.
5. **CRO and email marketing must align.** The popup offer and the welcome sequence must deliver the same promise. Misaligned handoffs destroy the value of high capture rates.

---

## Gotchas

**Gotcha 1 — Popups before 8 seconds.**
Early popups interrupt The Gator before it has formed any purchase intent.
Capture rates are lower AND the interruption may push borderline buyers to
leave immediately. Wait the 8 seconds.

**Gotcha 2 — Single-step popup for all use cases.**
One-step popups convert less than two-step because they skip the micro-commitment
of Step 1. If capture rates are below 3%, adding a Step 1 question is usually
the highest-leverage fix.

**Gotcha 3 — Measuring popup success by email capture rate alone.**
High capture rate + low downstream revenue = a popup that discounts to the
wrong audience. Measure sitewide RPS before and after popup implementation.

**Gotcha 4 — Not A/B testing popup offers.**
The first offer you run is almost never the best one. The discount level, the
question asked in Step 1, and the CTA text are all testable. Run ICE on popup
variables the same way you would on any page element.

**Gotcha 5 — Not instrumenting popup events.**
If `popup_shown` and `popup_submitted` aren't in your custom event layer,
you're running blind. You cannot compute impression rate, capture rate, or
downstream revenue without these events.

---

## Cross-references

- `cro-philosophy` — The Gator/Judge model explains why trigger timing matters
- `aov-strategies` — discount offers in popups must be evaluated for margin impact
- `split-test-interpretation` — popup A/B testing follows the same rules as any test
- `heatmap-data-model` — `get_custom_events` tool and output shape used in all four recipes
- `customer-research-bdf` — survey data helps determine the most compelling popup offer

---

> **eBook Team flag:** All content above is from Ch 11. The 8-second magic
> number, the three structures, four offer types, four trigger types, and the
> Perfect Popup template are all book-accurate. The double-discount follow-up
> email is from the book.
