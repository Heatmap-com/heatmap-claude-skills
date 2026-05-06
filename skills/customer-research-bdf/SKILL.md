---
name: customer-research-bdf
description: >
  Load when the user asks about customer research, survey questions, the
  Billion-Dollar Data Funnel, user testing, competitor analysis, or phrases
  like "what should I ask customers," "how do I know what to test," or
  "why aren't people buying."
---

# customer-research-bdf

## What this skill enables

This skill teaches Claude Dylan Ander's Billion-Dollar Data Funnel (BDF) — the
six-source research framework that grounds every optimization in customer data
rather than guesses. After loading this skill, Claude can design customer
feedback surveys, structure user testing sessions, build competitor analyses,
and use MCP signals to direct qualitative research toward the highest-leverage
areas. This skill is a prerequisite for `cro-philosophy` and `landing-page-evaluation`
when the user has not yet run customer research.

Source material: *Billion Dollar Websites* Chapter 4.

---

## The Billion-Dollar Data Funnel

All optimization is downstream of customer research. Hypotheses not grounded
in BDF data are guesses. Guesses succeed at low rates. BDF-informed hypotheses
succeed at dramatically higher rates because they are based on what customers
actually said, not what the brand thinks customers want.

The six BDF inputs, in order of priority:

1. **Customer Feedback Surveys** — The most important. Customers tell you their Dream Outcome in their own words. Those words belong in your headline.
2. **Raw Data Analysis** — Heatmap MCP: RPS, RPC, scroll depth, conversion data. What customers *do*, not what they say.
3. **Competitor Analysis** — The Five Pillars applied to competitors' websites, messaging, and offers.
4. **User Testing** — Structured observation of real users interacting with your site.
5. **Expert Review** — An experienced CRO practitioner reviews the site against the Five Pillars.
6. **Screen Recordings** — Watch how real users navigate. Find the hesitations, the scroll-backs, the rage clicks.

---

## Input 1: Customer Feedback Surveys

### Question-writing rules (book-accurate)

The words in your survey questions determine what answers you get. Bad questions
produce unusable data.

**Use "What" and "How" questions.** These open up answers.
- "What made you almost not buy from us?"
- "What problem were you trying to solve?"
- "How did you find us?"
- "What almost made you leave without buying?"

**Avoid "Do," "Is," and "Does" questions.** These produce yes/no answers that
tell you nothing actionable.
- "Do you like our website?" → "Yes" (useless)
- "Is the checkout easy?" → "Yes" (useless)

**Be careful with "Why" questions.** "Why did you buy from us?" puts the
customer in an analytical mode that produces rationalized answers, not true
motivations. The Gator doesn't know why it bought. The Judge makes up a story.
Better: "What made you decide to buy today?" (surfaces the trigger, not the
rationalization).

### The Three Survey Templates

**Post-Purchase Survey (Customer Feedback Survey)**

Deploy 2–5 days after purchase, when the customer has used the product and
still remembers the purchase decision:

1. "What made you almost not buy from us today?" (Fear/hesitation trigger)
2. "What problem were you trying to solve when you found us?" (Dream Outcome)
3. "What made you decide to buy today rather than later?" (Purchase trigger)
4. "How did you hear about us?" (Traffic attribution)
5. "What words would you use to describe our product to a friend?" (Copy/UVP language)

Question 5 is gold: the exact words customers use to describe the product to
others are the words that should appear in your headline, your UVPs, and your
ad copy.

**Exit-Intent Survey**

Deploy via popup to visitors who trigger an exit-intent signal without purchasing:

1. "What stopped you from completing your purchase today?" (Barrier identification)
2. "Is there anything we could add to make you more comfortable buying?" (Gap identification)
3. Optional: "Would you like to receive a special offer?" (Lead capture)

Exit surveys generate your highest-leverage optimization insights because they
are capturing the exact moment of decision failure. The answers go directly
to ICE scoring.

**Customer Interview Template**

15–30 minute recorded interviews with recent buyers (in the last 30 days):

Opening: "Can you walk me through what made you decide to buy from us?"
Problem: "What was going on in your life when you went looking for this type of product?"
Competitor: "Did you look at any other options? What made you choose us?"
Hesitation: "Was there any point where you almost didn't buy?"
Outcome: "What's changed since you started using the product?"

Conduct minimum 5 interviews before drawing conclusions. Look for recurring
language patterns — words or phrases that multiple customers use independently
to describe the same thing. These phrases are your messaging.

---

## Input 2: Raw Data Analysis (Heatmap MCP)

This is what the data layer tells you about customer behavior. See
`heatmap-data-model` and `heatmap-metrics-literacy` for full recipes.

BDF uses raw data to prioritize: which pages need the most attention, which
elements are Conversion Killers, where in the funnel users are dropping.

The three questions raw data answers:
1. **Where do users go?** (navigation patterns, journey continuation)
2. **What do users click?** (element RPC, Conversion Killer detection)
3. **Where do users leave?** (funnel drop-off, scroll depth, exit pages)

Combine raw data with qualitative research: if customers say in exit surveys
"I couldn't find the ingredients" AND raw data shows near-zero clicks on the
ingredients section, you have a confirmed, high-confidence hypothesis.

---

## Input 3: Competitor Analysis via the Five Pillars

For each of the Five Pillars (Offer, UVPs, Copywriting, Creative, UI/UX),
evaluate your top 3 competitors:

**Offer:** What are they selling? At what price? What bundles, guarantees, or
payment options do they offer? Where are they stronger/weaker than you?

**UVPs:** What claims are they making? What differentiation are they leading
with? Are there unmet customer desires that none of the competitors address?
(This gap is your opportunity.)

**Copywriting:** What are their headlines? Are they Dream Outcome or
product-description? What emotional register do they use?

**Creative:** What does their hero image look like? People or product? What is
the "dream outcome" image conveying?

**UI/UX:** How are they structured? What does their navigation look like? What
trust signals do they lead with?

*The most valuable finding:* A customer desire that multiple customers express
in surveys AND that no competitor is addressing in their positioning. That is
an unmet need with no competitive noise — it becomes your leading UVP.

---

## Input 4: User Testing

### The Three Taxonomies

Structured user testing finds three types of problems:

**Pain Points:** Elements or steps that cause visible frustration, confusion,
or repeated attempts. User clicks the wrong thing, backtracks, or expresses
verbal frustration.

**Friction:** Unnecessary steps, unclear instructions, or decision points that
slow the user without providing value. Not painful, but slowing The Gator down
until The Judge wakes up.

**Unmet Needs:** Things users look for that the page doesn't provide. Common
unmet needs: specific product information (ingredients, dimensions, compatibility),
social proof for specific concerns, comparison with alternatives.

### User Testing Protocol

1. Recruit 3–5 users from your target customer demographic (not friends, not
   team members).
2. Give a task: "You're looking for [product]. Go through the process of finding
   one you'd consider buying."
3. Ask them to think aloud. Do not help them. Take notes on every hesitation.
4. After the session, ask: "Was there any moment where you almost left?"
5. Categorize findings into Pain Points, Friction, and Unmet Needs.

Minimum useful insight: 3 users. Diminishing returns after 8 users (the 4th–8th
user surface new pain points; after 8, you see the same ones repeatedly).

---

## Input 5: Expert Review

A CRO practitioner reviews the site against the Five Pillars and identifies the
most likely failure points before running a single test. This is not a visual
critique — it is a structured audit.

Expert review checklist:
- Does the hero section pass the 1-Second Test?
- Does the headline express the Dream Outcome (or a compelling pain point)?
- Is the hero image showing the customer's transformation?
- Is the buy box immediately visible with a clear unique Offer?
- Are there visible Conversion Killers in the navigation?
- Are UVPs specific and customer-validated, or generic?
- Is social proof present and matched to the Dream Outcome?
- Does the page structure earn each scroll?

---

## Input 6: Screen Recordings

Watch 20–50 recordings of sessions on your target page(s). Use Heatmap or a
screen recording tool. Note:

- Where do users pause before scrolling?
- Where do they scroll back up?
- What do they click that has no destination (dead clicks)?
- Where do sessions end?
- Are there rage-click patterns (repeated clicking on the same element)?

Screen recordings are best used to confirm hypotheses from other BDF sources —
not as standalone data. "5 users in surveys said they couldn't find the size
chart AND I see users scrolling up and down repeatedly in recordings" =
high-confidence hypothesis for a size-chart placement test.

---

## MCP Recipes for Directing Qualitative Research

### Recipe 1 — High-RPS pages → user-test and interview recruit targeting

**Question:** Which pages deserve the most attention for qualitative research?

The pages with the highest RPS and the highest sessions are your "A-tier"
pages. The pages with high sessions but below-average RPS are your "B-tier"
opportunities. Focus qualitative research (user testing, expert review) on
the B-tier pages — they have the traffic to validate findings but aren't
converting efficiently.

```json
{
  "site_id": "<id>",
  "date_range": { "start": "2026-04-01", "end": "2026-04-30" },
  "metrics": ["SESSIONS", "REVENUE_PER_SESSION"],
  "sort_by": "SESSIONS",
  "sort_direction": "DESC",
  "limit": 20
}
```

Pages with sessions in the top quartile but RPS in the bottom quartile are your
B-tier targets for qualitative research. User test on those pages first.

---

### Recipe 2 — Exit-prone pages → target for exit-intent surveys

**Question:** Which pages are losing users most unexpectedly?

Use the funnel tool to identify drop-off steps, then compare actual vs. expected
drop rates.

```json
// Standard ecommerce funnel
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

The step with the highest `abandoned_rate` relative to benchmark (>60%) is
your exit-intent survey deployment target. Place the exit-intent survey on
that page specifically — not sitewide — to maximize qualitative signal from
the highest-friction step.

---

### Recipe 3 — Qualitative themes → quantitative test pipeline

**Question:** We've run surveys and user tests. How do we turn themes into
testable hypotheses?

This is a synthesis recipe. After collecting BDF data:

**Step 1:** List the top 3 recurring themes from customer surveys
(e.g., "Couldn't find size information," "Didn't trust the reviews," "Price
felt too high without knowing if it works").

**Step 2:** Map each theme to a Five Pillar:
- "Couldn't find size information" → Pillar 5 (UI/UX) + Pillar 3 (Copywriting)
- "Didn't trust the reviews" → Pillar 4 (Creative) + Social Proof
- "Price felt high without proof" → Pillar 1 (Offer) or Pillar 2 (UVPs)

**Step 3:** Use MCP to confirm the theme in behavioral data.
If "couldn't find size information" is the theme, run `get_raw_event_analytics`
on the PDP and look for high-click selectors near size-related content with low
revenue attribution — or look for rage-click patterns on the sizing area.

**Step 4:** Write the hypothesis:
"Because [N] customers in surveys said [theme], and we see [data signal] in
the MCP, we believe [specific change] will increase RPS by [estimated %]."

**Step 5:** ICE-score the hypothesis and add to the test backlog.
(See `experiment-design-and-prioritization` for ICE methodology.)

---

## Decision Rules

1. **Never run a test before completing BDF research.** A test without a BDF-grounded hypothesis is a random guess.
2. **Customer survey verbatims are headline copy.** Use the exact words customers use to describe the product and the problem — they have higher conversion rates than internal copywriting.
3. **Prioritize survey questions by actionability.** Questions that produce yes/no answers are useless. Every survey question should generate an answer you can act on.
4. **Use exit surveys for the highest-leverage insights.** Customers in the act of leaving are the most honest about what's wrong.
5. **3 user testing sessions is the minimum for actionable insights; 8 is the practical maximum before diminishing returns.**
6. **Combine data sources to increase confidence.** A theme that appears in surveys AND in screen recordings AND in MCP behavioral data is a high-confidence hypothesis. A theme from only one source requires additional validation before testing.

---

## Gotchas

**Gotcha 1 — Writing "Do/Is/Does" survey questions.**
These produce yes/no answers that generate no actionable insight. Every question
should start with "What" or "How."

**Gotcha 2 — Running user tests with team members or friends.**
They already know the product and will navigate it as power users, not as
first-time visitors. Recruit from your actual customer demographic.

**Gotcha 3 — Drawing conclusions from fewer than 3 user tests.**
One user's experience is anecdote, not data. Minimum 3 for any conclusion;
prefer 5.

**Gotcha 4 — Using competitor analysis to copy, not to differentiate.**
The goal of competitor analysis is to find the gap — the customer desire no
one is addressing. Copying what competitors are doing well means you're already
behind them on that dimension. Find the unmet need.

**Gotcha 5 — Treating the BDF as a one-time exercise.**
Customer needs and competitive landscapes change. Run customer surveys at least
quarterly. Run user tests whenever you're preparing a major optimization sprint.
The BDF is not a setup task — it is a recurring loop.

---

## Cross-references

- `cro-philosophy` — the Five Pillars framework; BDF is the research input to the pillars
- `landing-page-evaluation` — customer survey verbatims drive headline and UVP copy
- `split-test-interpretation` — ICE scoring relies on BDF Confidence scores
- `experiment-design-and-prioritization` — BDF outputs feed directly into hypothesis generation and ICE prioritization
- `heatmap-data-model` — MCP recipes for finding high-RPS pages and exit-prone steps
- `funnel-and-journey-diagnostics` — funnel tool used in Recipe 2 above

---

> **eBook Team flag:** All six BDF inputs, all three survey templates, the
> question-writing rules (What/How vs. Do/Is/Does, careful with Why), and the
> User Testing taxonomy (Pain Points, Friction, Unmet Needs) are from Ch 4.
> Book-accurate throughout.
