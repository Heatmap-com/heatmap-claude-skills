# heatmap-claude-skills

Claude Skills that turn [Heatmap.com](https://heatmap.com) MCP data into revenue-focused CRO analysis. Built on the methodology from *Billion Dollar Websites* by Dylan Ander.

## What this is

The [Heatmap MCP](https://mcp.heatmapcore.com/mcp) gives Claude direct access to your web analytics data. These skills give Claude the analytical judgment to do something useful with it.

Without skills, an LLM looking at heatmap data defaults to industry-standard CRO framing — sitewide conversion rate as the headline metric, undifferentiated traffic treated as one population, generic funnel analysis. With skills, Claude approaches the same data using Dylan Ander's methodology: Revenue Per Session as the North Star, RPC versus RPS for element-level analysis, the Quadrants of Traffic as the mandatory segmentation lens — and it can execute that analysis by calling the right MCP tools with the right arguments.

Same data. Better framing. Executable recipes.

## Architecture

```
┌────────────────────────────────────────────────────────┐
│                   Your Question                        │
└────────────────────────────────────────────────────────┘
                          │
                          ▼
┌────────────────────────────────────────────────────────┐
│   Claude Skills  ←  this repository                    │
│   methodology, frameworks, MCP recipes, decision rules  │
└────────────────────────────────────────────────────────┘
                          │
                          ▼
┌────────────────────────────────────────────────────────┐
│   Heatmap MCP  (13 tools, SharedAnalyticsFilterInput)   │
│   data access, queries, real-time analytics            │
└────────────────────────────────────────────────────────┘
                          │
                          ▼
┌────────────────────────────────────────────────────────┐
│         Revenue-Focused Answer + Executed Analysis     │
└────────────────────────────────────────────────────────┘
```

The skills are the playbook. The MCP is the data access layer. Together they let Claude function as a senior CRO analyst working against your live site data — not just giving advice, but pulling the actual numbers.

## Skills

### Foundation

| Skill | Purpose |
|-------|---------|
| `heatmap-data-model` | The anchor skill: 13 MCP tools with exact argument shapes, `SharedAnalyticsFilterInput` reference, auth, 7 canonical recipes, and output field maps. Load this first. |
| `heatmap-metrics-literacy` | RPS, RPC, AOV, CR, LPS — exact API fields, formulas, when to use each, why sitewide conversion rate is a vanity metric, and 4 MCP recipes |
| `cro-philosophy` | Gator/Judge psychology (Kahneman System 1/2), Five Pillars, Stroop Test, "95% Gator," confirmation bias, Ch 12 redesign empirical data, 2 MCP recipes |
| `quadrants-of-traffic` | The four-Quadrant segmentation framework using native `new_user`/`returning_user`/`device` filters — natively queryable, no post-hoc joins required. 4 MCP recipes |

### Analysis playbooks (book → data)

| Skill | Purpose |
|-------|---------|
| `revenue-heatmap-playbooks` | Ten revenue-heatmap-driven element optimizations (nav, carousels, tiles, tabs, Conversion Killers, FAQs via RPC, buy box, UVPs, upsells), each with an MCP recipe |
| `aov-strategies` | Nine AOV strategies, three upsell types, Empty Cart upsell, 10% impulse threshold rule, 4 MCP recipes including AOV-band segmentation |
| `landing-page-evaluation` | 1-Second Test, four-element rubric, five LP types, Listicle "Educate First, Sell Later," PDP-as-LP mobile diagnostics, 4 MCP recipes |
| `popup-strategy` | HVLI vs LVHI modes, three popup structures, four offer types, four trigger types, 8-second rule, Perfect Popup template, 4 MCP recipes |
| `funnel-and-journey-diagnostics` | Worst-drop step identification, revenue opportunity calculation, journey continuation analysis, Quadrant-stratified funnel comparison, 5 MCP recipes |

### Methodology

| Skill | Purpose |
|-------|---------|
| `customer-research-bdf` | Six BDF inputs, survey question rules (What/How, avoid Do/Is/Does), three survey templates, User Testing taxonomy, 3 MCP recipes that direct qualitative research |
| `split-test-interpretation` | Four test structure types (Standard A/B, MVT, URL Redirect, Reverse A/B, Live Testing), nine mistakes, ICE, Do No Harm, Blanket vs. Targeted, 3 MCP recipes |
| `experiment-design-and-prioritization` | ICE scoring with BDF-grounded inputs, hypothesis writing, test design checklist, MDE calculation, Do No Harm guardrail, 3 MCP recipes |

Each skill includes book theory, MCP execution recipes (tool, args, expected output, interpretation), decision rules, gotchas, and cross-references.

> **Open review flags (needs Dylan):** RPV metric status — is it a named Heatmap UI metric or a derived concept? (Experimental sidebar in `heatmap-metrics-literacy`.) Does "never host on YouTube" apply only to VSLs or to all video on landing pages? (`landing-page-evaluation`.)

See `SKILL_TEMPLATE.md` in the repo root for the canonical structure all skills follow.

## Installation

### Claude.ai (web)

1. Clone or download this repository
2. In Claude.ai, navigate to **Settings → Customize → Skills**
3. Upload each `SKILL.md` from `skills/<skill-name>/` — one file per skill directory (12 total)
4. Toggle the skills on

> **Tip:** Load `heatmap-data-model` first. It is the execution layer that every other skill references for MCP tool details.

### Claude Code

```bash
git clone https://github.com/Heatmap-com/heatmap-claude-skills.git
cp -r heatmap-claude-skills/skills/* ~/.claude/skills/
```

The skills will be available in your next Claude Code session.

### Claude Desktop / Team / Enterprise

For organization-wide deployment on Team and Enterprise plans, see [Anthropic's documentation on provisioning skills](https://support.claude.com/en/articles/12512180-use-skills-in-claude). Organization owners can upload the skills once and make them available to all users.

### Claude API

If you're embedding Claude in your own product, bundle the `SKILL.md` files with your application and load them in your API calls. See [Anthropic's Skills API documentation](https://docs.claude.com) for the current loading pattern.

## Pairing with the Heatmap MCP

These skills are designed to work alongside the Heatmap MCP at `https://mcp.heatmapcore.com/mcp`. The MCP provides 13 tools (from `viewer_access` and `list_sites` through `get_funnels`, `get_heatmap`, and `get_journey_continuation_analytics`) that the skills reference by exact name.

1. Add the Heatmap MCP to your Claude environment. See [Heatmap MCP documentation](https://insights-api.heatmapcore.com/docs/v1/overview) for setup.
2. Install these skills.
3. Ask Claude analytical questions about your site. The skills tell Claude how to think and which MCP tool to call; the MCP returns the data.

Example questions that work well once both are loaded:

- "Segment our RPS by the four Quadrants of Traffic for last month."
- "Which elements on our homepage are Conversion Killers?"
- "Where are we losing the most users in the checkout funnel and what's the revenue opportunity?"
- "Run Recipe 2 from revenue-heatmap-playbooks on our product carousel."
- "Our A/B test looks flat sitewide — run the Quadrant validation."

## Methodology source

The frameworks encoded in these skills come from *Billion Dollar Websites: Lessons From Optimizing $5 Billion in Online Revenue* by Dylan Ander (Damn Gravity Media, 2025). Dylan founded Heatmap.com after selling his CRO agency, and the book is the operating manual for the methodology Heatmap was built around.

The skills operationalize the book's frameworks and bind them to the Heatmap MCP's data surface. They do not reproduce the book's prose. If you want the full reasoning, the case studies, and the founder's voice behind the methodology, [read the book](https://billiondollarwebsites.com).

## Roadmap (v0.2)

- **Resolve open Dylan flags** — RPV canonical status; YouTube-scope clarification
- **Split `heatmap-data-model`** — separate the 13-tool reference from the 7 recipes to reduce per-conversation load cost
- **Update cross-references** — `popup-strategy`, `customer-research-bdf`, `funnel-and-journey-diagnostics`, and `experiment-design-and-prioritization` aren't yet referenced from the original seven skills
- **Audit the four newer skills** for the same quality bar as the original seven (tighter gotchas, worked examples)
- **Add `CHANGELOG.md`** and tag `v0.1.0` / `v0.2.0` in git

## Contributing

Issues and pull requests are welcome. The skills encode a specific methodology — proposed changes should align with the frameworks in *Billion Dollar Websites* or explain clearly why a deviation is warranted.

Use `SKILL_TEMPLATE.md` as the starting point for any new skill. The template codifies the required sections (Purpose / Core Framework / MCP Recipes / Decision Rules / Gotchas / Cross-References / eBook Team Flags).

If you're a Heatmap customer with feedback on how the skills perform against real questions, that's the highest-signal contribution. Open an issue with the question, the skill that fired (or didn't), and the response you got.

## License

[MIT License](LICENSE) — use, modify, fork, and distribute freely. The methodology source material in *Billion Dollar Websites* is separately copyrighted by Dylan Ander and Damn Gravity Media LLC.

## About Heatmap.com

[Heatmap.com](https://heatmap.com) is the world's first revenue-based heatmap and analytics tool, showing Revenue Per Click and Revenue Per Session for every element on your site. These skills are part of Heatmap's effort to make revenue-focused CRO analysis accessible to every business — not just those with a senior CRO analyst on staff.
