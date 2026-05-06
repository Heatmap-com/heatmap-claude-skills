---
name: split-test-interpretation
description: >
  Load when the user asks about A/B tests, test structures, statistical
  significance, ICE prioritization, or phrases like "should I call this test,"
  "is my test significant," "what tests should I run first," or "how do I
  design a split test."
---

# split-test-interpretation

## What this skill enables

This skill gives Claude the complete split testing methodology from Chapters 13,
14, and 15 of *Billion Dollar Websites*: four test structure types, nine anti-
patterns that destroy test integrity, the ICE prioritization framework, statistical
thresholds, the Do No Harm rule, and the Blanket vs. Targeted distinction —
together with three MCP recipes for reading test data and validating results by
Quadrant. After loading this skill, Claude can design, prioritize, interpret, and
derive follow-up hypotheses from any A/B test.

Source material: *Billion Dollar Websites* Chapters 13, 14, 15.

---

## Chapter 13: Test Structures (Four Types)

**Structure and targeting are separate decisions.** Test structure (what you're
changing and how) is a Chapter 13 concept. Blanket vs. Targeted targeting
(who sees the test) is a Chapter 15 concept. Do not confuse them.

### Standard A/B Test

The baseline. One control; one variant. A single change between them. The
cleanest test design because the change is isolated and the result is
attributable.

*Use when:* The hypothesis is specific enough to test as a single variable
(headline copy, hero image, button text, nav item order).

### Multivariate Test (MVT)

Multiple elements changed simultaneously in multiple combinations. An MVT with
2 elements × 2 variants creates 4 combinations. MVT finds the winning
combination faster than running sequential A/B tests on each element — but
requires significantly more traffic because each combination must achieve
adequate sessions to be statistically valid.

*Use when:* You have high traffic (> 200,000 sessions/month on the test page)
AND multiple elements you believe interact with each other (headline + hero
image, where neither independently predicts the combined effect).

*Do not use* when traffic is insufficient. Splitting traffic 4+ ways with
insufficient sessions produces unreliable results on all variants.

### URL Redirect Test

The control is the current page URL; the variant is a completely different URL
with a different page. The test platform redirects a portion of traffic to the
variant URL. Used when the variant requires a fundamentally different page
structure (not just element changes).

*Use when:* Testing a different page type (Long-Form vs. Listicle), a different
URL structure, or a major layout overhaul that can't be done via DOM injection.

*Caveat:* URL redirect tests have higher implementation complexity and slight
redirect latency. Use a standard A/B test whenever element-level changes are
sufficient.

### Reverse A/B Test

The variant is the current live page; the "control" is the previous version
being reintroduced. Used when you want to validate that a prior change is still
outperforming the original, or when a prior test result needs to be re-tested
on changed traffic composition.

*Use when:* Traffic mix has changed significantly since a prior test result
(e.g., new paid ad channel added) and you want to validate whether the old
winner is still the winner on the new traffic.

### Live Testing (Low-Traffic Sites)

For sites with < 10,000 sessions/month, running a statistically valid A/B test
in a reasonable time window is often impossible. Live testing is the
alternative: make a change directly to the live site, run for 30–60 days, and
compare performance vs. the pre-change baseline.

*Important caveats:*
- Seasonal factors, ad spend changes, and external events all confound results.
- Live testing is hypothesis generation, not hypothesis validation. It tells
  you "this might be better" — not "this is definitely better."
- If a live test shows strong positive results (> 20% RPS improvement) for 30+
  days, implement it with high confidence. If it shows small or mixed results,
  treat it as inconclusive and build a proper A/B test when traffic grows.

---

## Chapter 15: Blanket vs. Targeted (Targeting, Not Structure)

This is a targeting decision, not a test structure decision.

**Blanket Test:** All site visitors see one of the two variants. No pre-filtering
by segment. Quadrant analysis is done *after the fact* on the results.

**Targeted Test:** The test is shown only to a specific demographic segment
(e.g., first-time mobile users, returning desktop users, visitors from a
specific traffic source). All sessions outside the segment see the control.

*When to use Targeted:* When prior BDF data or a prior Blanket test result shows
a clear segment-specific effect. If a Blanket A/B test shows a 20% lift for
First-Time Mobile but no effect on desktop, the follow-up is a Targeted test
for First-Time Mobile only. This maximizes traffic allocation toward the segment
where the effect is real.

*When to start with Blanket:* On first hypothesis tests where the effect is
expected to be broad. Running Targeted from the start narrows traffic pool and
can extend time-to-significance for uncertain hypotheses.

---

## Chapter 14: The Nine Mistakes

**Mistake 1 — Calling tests "winners" or "losers"**
There are no losing tests. A 35% revenue drop on one test produced the intel
that led to an 11% total revenue lift on the follow-up. The intel from a
negative result is often more valuable than a small positive.

**Mistake 2 — Dismissing "small" tests**
Changing one nav item from "Best Sellers" to "Drinkware" based on heatmap
data drove a $1.2M revenue lift. The size of the change has no predictive
relationship with the size of the revenue impact.

**Mistake 3 — "Low-hanging fruit" thinking**
"Low-hanging fruit" implies a test is obviously good before it's run. Tests
aren't fruit — they're hypotheses. Run the tests the data suggests, not the
ones that feel obvious.

**Mistake 4 — "Split tests will hurt my brand"**
The empirical record on this is clear: split test-induced brand damage at the
margin of any individual test is not measurable. The cost of not testing always
exceeds the cost of a test that underperforms.

**Mistake 5 — "We tested that already"**
Context changes. A test run on different traffic mix, different time of year,
or different product mix may produce a completely different result. Prior test
results are data points, not closed cases.

**Mistake 6 — Improper QA**
Tests that are broken on Android, widescreen monitors, or specific browsers
generate false negatives that corrupt ICE data for all future tests. QA
every test on: Chrome desktop, Safari desktop, iOS mobile, Android mobile,
multiple viewport widths (320px, 480px, 768px, 1280px).

**Mistake 7 — Ending tests too early**
Two weeks is non-negotiable regardless of traffic volume. Amazon runs tests
for a minimum of two weeks. The reason is behavioral variety, not just volume
— day/night patterns, weekday/weekend patterns, and weekly seasonal cycles
all matter. A 20% lift on Day 3 may be a weekend effect.

**Mistake 8 — Running too many tests at once**
Maximum two tests per user journey. A user journey = the full path from ad
to checkout. Running 5 simultaneous tests across the same journey creates
attribution distortion where no single test can be evaluated reliably.

**Mistake 9 — Running low-impact tests**
The ICE method exists specifically to prevent this. Running a headline color
test when funnel data shows 70% cart abandonment is a resource misallocation.

---

## Statistical Thresholds

**Statistical Significance:** ≥ 95%. The probability the result is real, not
random noise.

**Statistical Power:** ≥ 80%. The probability the result would repeat if the
test were run again.

Both are determined by: (1) session volume and (2) effect size. A larger lift
reaches significance faster. Low-traffic sites require either longer test windows
or larger changes to achieve sufficient power.

### The Do No Harm Exception

You may implement below 95%/80% thresholds if:
- The change cannot plausibly hurt the site
- All secondary metrics (CR, AOV, CTR, Add-to-Cart rate) are neutral or positive
- RPS held flat or improved

This is a practitioner judgment, not a scientific standard. Reserve it for
changes where the downside risk is asymmetrically low (e.g., reordering nav
items based on RPC data — if it's wrong, you reorder them back in 5 minutes).

---

## The ICE Method

Score each test hypothesis 1–5 on three dimensions:

- **Impact:** How much revenue lift is this likely to produce? High-traffic
  pages and 100%-visible elements score higher.
- **Confidence:** How strongly does BDF data (surveys + heatmap + screen
  recordings) support this hypothesis? Confirmed by multiple BDF sources = 5;
  intuition only = 1.
- **Effort:** Inverse scale. Low effort = 5; high effort = 1. Do not let Effort
  dominate — a high-effort test at the top of the ICE list is still the right
  test to run.

Sum all three. Run highest ICE score tests first. Update scores after each test
completes — the intel from each test changes the confidence score for related
hypotheses.

---

## Test Design Checklist (Four Elements, Ch 15)

Every valid split test has exactly four elements:

1. **The Test:** A specific, ICE-prioritized hypothesis with a predicted,
   measurable outcome. "We believe changing the hero image from lifestyle to
   product-as-hero will increase RPS by 10–20% for First-Time Mobile users,
   because 3 of 5 user tests showed hesitation about what the product looked like."

2. **Target Demographics:** Blanket (all users, Quadrant analysis post-hoc) or
   Targeted (specific segment, e.g., `filters: { new_user: true, device: "MOBILE" }`).

3. **Target Webpage:** The specific page URL in the user journey being tested.

4. **Measurement:** RPS as primary metric. CR, AOV, Add-to-Cart rate, and
   time-on-page as secondary metrics. Two-week minimum duration.

---

## MCP Recipes

### Recipe 1 — Read test results from MCP via custom events

**Question:** How did our two test variants perform?

Requires the test platform to instrument variant exposures as custom events:
- `variant_a_exposed` — session was shown control
- `variant_b_exposed` — session was shown variant

```json
// Total variant A exposures
{
  "site_id": "<id>",
  "date_range": { "start": "<test-start>", "end": "<test-end>" },
  "event_name": "variant_a_exposed",
  "breakdown": "DATE"
}
```

Repeat for `variant_b_exposed`. Compare total event counts (as proxy for
traffic split).

Then get RPS per variant by using `get_session_analytics` or
`get_raw_pageview_analytics` with date ranges corresponding to variant-active
periods (or use `get_conversions` with a conversion name that includes the
variant tag if the test platform supports that instrumentation).

**Note:** True variant-level revenue attribution from the MCP requires the
test platform to either tag sessions with variant exposure or pass a custom
event with the conversion. Pure time-based comparison (control period vs.
variant period) introduces seasonal confounds. Instrument properly first.

---

### Recipe 2 — Quadrant-stratified test validation

**Question:** Our sitewide result looks flat. Did the test win or lose within
any quadrant?

Run `get_pageview_analytics` once per quadrant for both the test period and a
matched control period.

```json
// Test period — First-Time Mobile
{
  "site_id": "<id>",
  "date_range": { "start": "<test-start>", "end": "<test-end>" },
  "metrics": ["SESSIONS", "REVENUE_PER_SESSION", "CONVERSION_RATE"],
  "filters": { "new_user": true, "device": "MOBILE" },
  "page_url": "/the-tested-page"
}
```

```json
// Control period — First-Time Mobile (matched window, prior period or prior year)
{
  "site_id": "<id>",
  "date_range": { "start": "<control-start>", "end": "<control-end>" },
  "metrics": ["SESSIONS", "REVENUE_PER_SESSION", "CONVERSION_RATE"],
  "filters": { "new_user": true, "device": "MOBILE" },
  "page_url": "/the-tested-page"
}
```

Repeat for all four quadrants.

**Decision logic:**
- If First-Time Mobile shows ≥ 10% RPS lift AND other quadrants are neutral:
  implement for First-Time Mobile only (Targeted rollout).
- If one quadrant shows ≥ 10% RPS lift AND another shows ≥ -5% RPS:
  implement for the winning quadrant only.
- If all quadrants are flat (< 5% movement either direction):
  call the test inconclusive, build the follow-up hypothesis.
- If one quadrant shows strong negative (< -10% RPS): do not implement sitewide;
  investigate what the test broke for that segment.

---

### Recipe 3 — Test postmortem and follow-up hypothesis generation

**Question:** Our test concluded. Now what?

After every test, answer these five questions with data before moving on:

1. **What was the RPS result by Quadrant?** (Recipe 2)
2. **What was the result on secondary metrics?** (CR, AOV, Add-to-Cart rate)
3. **Was there a segment that won when the aggregate was flat?** (If yes → Targeted rollout)
4. **What does the result tell us about the hypothesis?** (Confirmed, refuted, or inconclusive — and why)
5. **What is the follow-up test?** Every concluded test suggests a next test.

The follow-up test is where the large revenue lifts come from. If male models
outperformed female models on a PDP, the follow-up tests are: male models on
the homepage hero, in email creative, and in ad landing page images. Each
follow-up test builds on confirmed intel from the prior test.

```json
// Compare performance of the winning quadrant after full rollout
// (run same query 30 days post-implementation to verify the lift sustained)
{
  "site_id": "<id>",
  "date_range": { "start": "<post-implementation-start>", "end": "<post-implementation-end>" },
  "metrics": ["SESSIONS", "REVENUE_PER_SESSION", "CONVERSION_RATE"],
  "filters": { "new_user": true, "device": "MOBILE" },
  "page_url": "/the-tested-page"
}
```

If the lift sustained: confirmed win. If the lift evaporated: the effect was
a time artifact (seasonal period, ad spend change). Consider a reverse A/B test.

---

## Decision Rules

1. **Before calling any test inconclusive:** Apply all four Quadrant filters. Only call it inconclusive after Quadrant analysis shows no statistically meaningful segment result.
2. **Minimum two weeks, no exceptions.** Do not call a test at Day 10, regardless of apparent significance.
3. **Maximum two tests per user journey.** More than two creates attribution distortion on all tests.
4. **RPS is the primary metric.** CR up + AOV down = check RPS. RPS is the tiebreaker.
5. **When a test wins for one Quadrant and loses for another:** Implement for the winning Quadrant only. Never implement sitewide when Quadrant results diverge.
6. **Always build the follow-up test.** The ICE sheet must be updated after every concluded test with follow-up hypotheses.
7. **Blanket vs. Targeted is targeting, not structure.** Do not call it a "different type of test."

---

## Gotchas

**Gotcha 1 — Ending tests early because "it's clearly winning."**
A 40% lift at Day 10 may be a weekend effect. Wait the two weeks.

**Gotcha 2 — Running 5 tests across the same user journey.**
Attribution is meaningless with 5 simultaneous active tests. Two maximum.

**Gotcha 3 — Using Statistical Significance without checking Statistical Power.**
These are not interchangeable. Significance = probably real. Power = probably
repeatable. High significance + low power = result may not replicate.

**Gotcha 4 — Never running the follow-up test.**
Most of the large revenue lifts in CRO come from follow-up tests, not from
the initial test. The initial test generates intel; the follow-up test monetizes it.

**Gotcha 5 — Not QA-ing on Android.**
Most teams test on iPhone only. Android viewport behavior differs. A broken
test on Android generates a false negative and corrupts the ICE data for all
future related tests.

---

## Cross-references

- `quadrants-of-traffic` — Quadrant filtering is mandatory for every test result; native API filters make this easy
- `heatmap-metrics-literacy` — RPS as primary test metric; CR, AOV as secondary
- `revenue-heatmap-playbooks` — ICE scoring for element-level optimizations
- `experiment-design-and-prioritization` — full ICE methodology and test design checklist
- `customer-research-bdf` — BDF Confidence scores feed directly into ICE Confidence
- `heatmap-data-model` — `get_custom_events`, `get_pageview_analytics`, `get_session_analytics` used in recipes above

---

> **eBook Team flag:** Ch 13 test structures (Standard A/B, MVT, URL Redirect,
> Reverse A/B, Live Testing) are added in this version — they were missing from
> the prior skill. Ch 14 Mistakes #3 (low-hanging fruit), #4 (brand damage fear),
> and #5 (tested already) are short in the book but accurately represented here.
> Blanket vs. Targeted is correctly identified as targeting (Ch 15), not
> structure (Ch 13) — the prior version conflated these.
