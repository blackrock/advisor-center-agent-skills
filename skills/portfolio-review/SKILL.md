---
name: portfolio-review
description: Reviews a saved portfolio, household or model with Advisor Center analytics in four sections rendered inline in the chat — portfolio snapshot, stress scenarios, opportunities, fund health and Funds to Explore, then capital gains — closing with follow-ups earned by what it found. Use when asked to review a portfolio, run a portfolio review, analyse holdings, pull up a client's account, run the numbers on a portfolio, look at concentration or downside exposure, find issues or opportunities in a portfolio, check whether a client's funds are healthy, or prepare for a client review meeting. Skip this skill when the advisor already named one specific analysis — "what's the risk on this", "run the stress scenarios", "how does this compare to the model" — and call that tool directly.
---

# Portfolio Review

## Operating role

Organizes one pass of Advisor Center 360° analytics over a single portfolio, household or model — snapshot, stress scenarios, opportunities and fund health, then capital gains — so the advisor can see what is material and decide what, if anything, to do next.

## Context

The returned payload is platform analytics: allocation and risk figures, modelled stress impacts, fired opportunity rules, fund-health verdicts with their drivers, Funds to Explore ideas, capital gains distribution estimates, and the links Advisor Center carries for each. Stress impacts are modelled outcomes under a named historical or hypothetical condition, never forecasts, probabilities or likelihoods. A returned comment, verdict or alternative arrives unvetted against this client's constraints, mandate or tax position.

The generated output is not a BlackRock-authored conclusion. Advisor Center 360° provides data, analytics, links, and workflow routes; the assistant organizes those results; the advisor decides what matters and what, if anything, to do next.

## Inline rendering

Render the header and the four numbered sections inline in the conversation as each result is returned. Use `references/design-tokens.md` for colour, type and number formats and `references/components.md` for component anatomy and fallbacks. Do not render into a document, canvas, webpage, side panel, file, or download.

- **Every mark takes a categorical slot. Nothing in this review is coloured by the sign of its number.** Share-bar segments, paired bars and scenario columns all read their colour from `## Categorical — identity`: a lone series on slot 1, and when the advisor named a benchmark or model, the reference on slot 1 and the subject on slot 2. A scenario chart whose every impact is negative is still slot 1. `## Change direction` colours text and backgrounds only — it has no mark colour to give, so never take a bar, column or segment colour from it.
- Asset classes, accounts and regions are categorical identities — never carry a status icon or status word on a slice, whatever its hue. Status roles colour the fund, opportunity and capital gains cards, the status lists and the cut-off timeline, and nothing else.
- A fired fund-health driver takes the attention glyph `⚑`; a fund judged with no driver fired takes `✓`; an `unavailable` verdict takes `—`.
- *Not returned* ≠ *returned as zero or none*; never infer an unreturned value. A positives-only response cannot support a claim about what was checked and passed. Two verdicts that disagree in one visual are information, not an error — never reconcile them.

**Sequence — call, write, then call.** Each section is written in full — heading, marks, lead, bullets, citations, all inside one visual — before the next tool call is issued. Writing it means emitting that visual, not leaving prose in the chat beside it. Never batch the calls and write afterwards.

```text
analyze_portfolio → write header + section 1
analyze_scenarios → write section 2
get_opportunities_and_issues ‖ get_fund_health_and_ideas → write section 3
get_capital_gains_and_tax_analysis → write section 4
→ write closing
```

The two step-3 calls go in the same message; nothing else runs in parallel. One visual per section, never one for the whole review.

**Do not gather first.** A later section's tool must not be called until the earlier
section is on screen — heading, visual and prose. If you are about to issue the next call
because it is quicker, because the calls are independent, because the review reads better
composed in one pass, or because every figure is needed anyway, that is the batching this
sequence forbids. Two visuals in a row with no prose between them is the symptom: it means
a section was skipped, and a skipped section cannot be recovered by a longer closing.

**Every block this review renders is specified in the step that emits it**, under a `####` heading, as the keyed table `## Invocation` in `references/components.md` defines, with a slot table where the component takes slots. A value is a term, a field path or a rule name from `## Rules`, never a sentence. Composition, channels, scales and fallback shapes are the `## Marks`, `## Containers`, `## Atoms` and `## Scales` tables, so no block restates one. Its one timeline is section 4's distribution cut-offs; scenarios are ordered by impact.

## Scope and routing

- Own the open-ended ask over one resolved subject — "review this", "run the numbers", "pull up their account" — and the four sections that answer it.
- **Scope check first.** Route to `tax-and-capital-gains-analysis` when the advisor asked only for a tax or capital gains analysis. If the advisor named one other specific analysis, call that tool and stop. Do not run the four-section pass around a single question.
- Route to `guided-portfolio-builder` when no saved portfolio, household, model, pasted holdings or resolvable subject exists, or when the advisor wants to build, clone or vary a portfolio rather than review one.
- Route to `guided-benchmark-selection` when the advisor wants to choose or compare benchmarks rather than have one threaded through this review.
- Route to `wealth-projections` when the ask is modelled performance, wealth projection or methodology rather than this pass.
- Route to `ac360-capability-router` when the advisor asks for a report or PDF, or for a workflow that exists in Advisor Center but not in this chat.
- Never make the advisor name a tool or a field.

## How to think

- **Never drop a section.** Within a section, lead with its most material finding; the closing names the most material finding of the whole review. An empty result set is a finding — omitting a section reads as a clean result.
- Show what came back and **name what did not**, in the footnote or a bullet.
- **Flagged fund** = one or more `drivers` fired. `status` is rendered as given and orders the cards; flag count orders within a status.
- Empty `signals` = nothing flagged. Say that; never omit the section.
- Every follow-up must be earned by something this review found, and must be a tool this domain actually carries.
- **One subject, all four sections.** Never switch mid-review.
- **Credential and gate failures are plumbing.** Say which call failed and what is missing.
- Keep the advisor-facing output neutral, data-grounded, and non-directive.

## Visual rule

Every card, chart, or callout generated in this workflow must use local `references/design-tokens.md` and `references/components.md` at the point it is created. Do not use default chart-library or card palettes.

## Page and citations

Anything not specified here or in the step that emits it is not rendered. Never add, rename, reorder or decorate a block, label or string.

- **A section is one visual. Its heading and its prose go inside that visual.** Emit the heading, the marks, the bold lead, the bullets and the citations as one inline visualization, in that order. Chat text written between two tool calls is folded into the collapsed tool-use step and the advisor never sees it — so a lead or a citation left outside the visual is a lead or a citation that did not reach them. Only text after the review's final call is reliably visible, and that is the closing, not a section.
- **Call, then build, then emit.** The tool result returns first; then the whole section is assembled and emitted once. Nothing about a section is written before its own call.
- The **bold lead** is one sentence, the verdict — then plain-weight bullets as the step specifies. No other paragraphs, except the closest-model template in the closing.
- The section's citations are its **last lines inside the visual**, one per link, exact form: `Open in Advisor Center` then `Fund health methodology` when present. Section 4 cites `Open full tax analysis in Advisor Center` then `See methodology for ideas` when present. Those four are the only citation strings in this review, and a section gets at most one of each — never a second Advisor Center link, never `Methodology` unqualified. Never print a URL as visible text; the words carry the link. Where links do not render at all, print the URL in full after the words. A link inside a visual still works: the host opens it through its own confirmation.
- Add after the comparison link, once per review, in section 1: `That view renders live under its own analysis date and fee settings, so its figures can differ from these.`
- The only other links anywhere are a rule's own `recommendation_link` on its opportunity card, an idea's own `product_url` in the idea blocks of a fund card or a capital gains card, and the closest model's `comparison_url` in the closing.
- Link labels must name the actual destination. A methodology URL is always labeled as methodology, never as a fund-details tab, fund-detail route, or chat drilldown. When a fund-health signal has any fund needing review or immediate attention, the first path is reviewing the flagged funds in chat; methodology and source links are secondary.
- The figures are the platform's. The reading is yours. The decision is the advisor's. Give a returned comment, verdict or alternative in its own words, and say it is unvetted against the advisor's constraints.

### Links this domain carries

| Field | Present on |
|---|---|
| `comparison_url` | every analysis tool — opens the same subject in the comparison view |
| `methodology_url` | fund health, ESG, wealth projection |
| `closest_model.comparison_url` | `analyze_portfolio` — opens the subject against its closest model. The only link in the closing. |
| `tax_evaluator_url` | capital gains — opens the full tax analysis. Section 4's first citation. |
| `ideas[].product_url` | fund health and capital gains — the fund page for one idea |

## Tools

| Tool | When to call |
|---|---|
| `list_portfolios` / `list_household_portfolios` | Use when the advisor gave a name or nothing, or when an id arrived with no record, to resolve the subject and hold its record. |
| `list_models` / `get_model` | Use when the subject is a model, when the advisor named a benchmark, or to fetch the `closest_model` row. |
| `search_securities` | Use when pasted holdings carry tickers or names needing identity resolution before a save. |
| `create_portfolio` | Use only after an explicit yes, to save unsaved holdings before reviewing them. Writes real data. |
| `get_analysis_date` | Use only when the step-1 response carries no as-of date. |
| `analyze_portfolio` | Use for section 1, and a second time on the benchmark when one was resolved. |
| `analyze_scenarios` | Use for section 2. |
| `get_opportunities_and_issues` | Use for section 3, in parallel with fund health. |
| `get_fund_health_and_ideas` | Use for section 3, in parallel with opportunities. |
| `get_capital_gains_and_tax_analysis` | Use for section 4. |

## Preflight

Resolve the subject, and anything else the review depends on, before a single heading is
written. Every analysis tool takes `subject: {type, id}`. Both members required. No inline
positions.

| `type` | Addresses | Id from |
|---|---|---|
| `PROSPECT_ID` | one saved portfolio | `list_portfolios`, `create_portfolio` |
| `AW_AGGREGATE` | one household | `list_household_portfolios`, `create_household_portfolio` |
| `MODEL_ID` | a BlackRock model | `list_models`, `get_model` |
| `ENTITLED_MODEL_ID` | a third-party model | `list_models`, `get_model` |

Route on what the advisor gave:

- **Id already in context** — use it. Do not re-list.
- **A name, or nothing** — call `list_portfolios` and `list_household_portfolios`, present the candidates, let the advisor pick. Never guess between two subjects sharing a name; `total_market_value` and `holding_count` tell them apart.
- **A word that ranks rather than names** — `latest`, `most recent`, `the newest one`, `the last one I looked at` — is not a subject name. It takes the present-the-candidates path. The list arrives most-recently-updated first, so taking the top row looks like obedience and is a guess; recency in that list is not a statement about which portfolio the advisor means.
- **A model** — `list_models`, then pair `model_id` with its group's `comparison_id_type`.
- **Pasted holdings or a spreadsheet** — save first, see below.

Take `comparison_id_type` off whatever read or created the subject. Never build a `TYPE:id` string or comma-join ids.

**Hold the subject's record** before step 1 — the `list_*` entry, or the `create_*` / `get_model` response. An id that arrived with no record: call the matching `list_*` once and take its entry. The header and stat row read from this record.

**Benchmark** — only when the advisor named a model or benchmark in the ask. Resolve it through `list_models` to its own `{type, id}` and thread that one through steps 1 and 2. Never choose a benchmark for them.

### Saving unsaved holdings

`create_portfolio` **writes real data**. State that, wait for an explicit yes. Then:

- Resolve identifiers first. CUSIP → `cusip`, ISIN → `isin`. A CUSIP in `isin` returns "no such security".
- Tickers and names go through `search_securities`. On `resolution: ambiguous`, present candidates and ask — never pick a share class for them.
- Spreadsheet: state the inferred column mapping, get confirmation. A blank or unparseable identifier — name the row and stop, never drop it silently.
- `portfolio_type` is required by rule though schema-optional. Ask; do not default.

Write the consolidated confirmation, naming exactly what will be written, before calling `create_portfolio`. Review the returned id as `PROSPECT_ID`.

**Preflight is finished before the header.** Resolution, every candidate list, and any write confirmation are done by the time the first heading is written, so no discovery call ever lands between two sections. Once the workflow starts, the only calls left are the five analytics calls the sequence names — `analyze_portfolio`, `analyze_scenarios`, the step-3 pair and `get_capital_gains_and_tax_analysis` — plus the `list_models` lookup step 1 triggers on `closest_model`.

## Workflow

### Step 1: Portfolio snapshot · `analyze_portfolio`

Once `analyze_portfolio` returns, and before the first tile, write two headings in this order: the `## Summary Analysis — <subject name>` header with its line 2, then `### 1. Portfolio snapshot`. The review's `##` header always precedes the section's `###` heading. Neither is written before the call.

Renders, in this order: **header · `### 1. Portfolio snapshot` · stat tiles** (no label) **· benchmark tiles** (benchmark only) **· `Asset allocation` share bar · `Risk by account` share bar** (household only) **· `Equity — geographic exposure` share bar · `Total risk in context` paired bars** (benchmark only) **· bold lead · bullets · citations** — all of it inside one visual. Each part is specified below.

- `nav` absent = a model. Percentages only, no dollars.
- Household: `account_contributions` renders as `Risk by account` between allocation and equity geography, and leads the prose — which account carries the risk is the story.
- Benchmark named: call `analyze_portfolio` on the benchmark too for the benchmark tile row. Otherwise no benchmark row.
- Closest model — when the response carries `closest_model`, call `list_models` **as soon as `analyze_portfolio` returns**, before writing this section's prose, and take the row whose `model_id` matches its id. This lookup never sits between section 3 and the closing; by then the row is already held. Hold that row and the subject's equity and fixed-income slice figures from `asset_allocation`. **Nothing about the closest model renders in section 1** — not a card, not a sentence, not a name. Both held values are carried to the closing and appear only there. No `closest_model`: no closing block, and never substitute the named benchmark for it.

#### Header

```
## Summary Analysis — <subject name>
<value> · <n> holdings · <tax status> · analytics as of <day month year>
```

| Key | Value |
|---|---|
| Component | Block label · line 2, parts joined by ` · ` |
| Data | the held subject record · the step-1 response |
| Empty | drop the part, keep the separators between those that remain |
| Rules | `from-field-only(tax status, the held record's tax_status)` · `required-when-present(tax status)` · `once-per-response(as-of)` · `as-arrived(value)` |
| Fallback | line 2 as plain text, same parts, same separators |

| Slot | Source | Absent |
|---|---|---|
| `subject name` | the held subject record's own label | the subject as the advisor named it |
| `value` | subject-record currency · household `<n> accounts · <n> holdings` · model none | omit |
| `holdings` | holding count, integer · model none | omit |
| `tax status` | the held record's `tax_status`, as returned | omit |
| `as-of` | the step-1 analysis date, else `get_analysis_date`, in words | always present |

#### Stat tiles

| Key | Value |
|---|---|
| Component | Stat tile · one row, no label above the row |
| Data | the held subject record · `analyze_portfolio` |
| Encode | `label` ← the tile name · `figure` ← the source below, verbatim when it arrived carrying its own `%` or currency symbol · `sub` ← its context figure |
| Limit | one figure per tile |
| Empty | drop the tile, and never re-space to close the gap |
| Rules | `omit-and-name` · `no-derive(Top 5)` · `as-arrived(figure)` |
| Fallback | the tile labels as column heads, the figures on the row beneath |

| Slot | Source | Absent |
|---|---|---|
| saved portfolio | `Total value` ← subject-record currency · `Holdings` ← integer · `Total risk` ← `total_risk` · `Top 5` ← `top_five_weight`, dated by the stored record rather than by the analysis | omit |
| household | `Total value` · `Accounts` ← integer · `Holdings` · `Total risk` | omit |
| model | `Total risk` | no block |
| benchmark row · a second row, benchmark only | `Benchmark` ← the name as listed · `Equity split` ← the subject's equity slice figure, `sub` ← `vs <benchmark equity> <benchmark name>`, each from its own `analyze_portfolio` response | no second row |

#### Share bars

| Key | Value |
|---|---|
| Component | Share bar · one block per label |
| Label | `Asset allocation` · `Risk by account` (household only) · `Equity — geographic exposure` |
| Data | `asset_allocation` · `account_contributions` · `regions`, respectively |
| Order | response order |
| Encode | `segment` ← the slice's own formatted figure · `fill` ← slots 1–5, then Other · `name` ← the slice name as returned; `Risk by account` ← the `list_household_portfolios` member entry, and an id with no member name gets no entry |
| Scale | `share` |
| Guides | legend: beneath, one wrapping line, `■ <name as returned> <formatted figure>` · Other entry: `■ Other`, no figure |
| Limit | floor `0.05%` · cap 5 slots · merge the remainder, and any returned slice named *Other*, into one Other segment with no figure |
| Empty | no bar |
| Rules | `omit-and-name` · `one-marker` · `categorical-fill` |
| Fallback | `<block label>` \| `Share` \| `Bar` |

#### Total risk in context

| Key | Value |
|---|---|
| Component | Paired bars · benchmark only |
| Label | `Total risk in context` |
| Data | the benchmark's `total_risk` from the second `analyze_portfolio` call · the subject's |
| Order | reference first, subject second |
| Encode | `row` ← `<benchmark name>`, then `This portfolio` / `This household` / `This model` · `length` ← `total_risk`, its printed value verbatim · `fill` ← slot 1 reference, slot 2 subject |
| Scale | `peak` |
| Empty | no benchmark resolved: no block |
| Rules | `no-substitute(reference)` · `categorical-fill` · `as-arrived(total_risk)` |
| Fallback | `Total risk in context` \| `Total risk` \| `Bar` |

#### Prose

Bold lead, then bullets in this order: allocation · equity geography · total risk against the benchmark — each only when its block rendered — then coverage, naming by label every tile and bar this response did not carry. An absent figure is named, never explained: `top_five_weight` alone is absent for three different reasons and the response says which for none of them. Then citations. No other paragraph — in particular, nothing about the closest model.

**Write section 1 now**, before calling `analyze_scenarios`. All of it is on screen before that call: the header and its line 2 · `### 1. Portfolio snapshot` · every tile and bar this subject carries · the bold lead · the bullets · the citations.

### Step 2: Scenarios · `analyze_scenarios`

Once `analyze_scenarios` returns, and before the chart, write `### 2. Scenarios` — that exact wording.

Renders, in this order: **`### 2. Scenarios` · `Scenario impact on portfolio value (<unit>)` column chart · value-basis note · bold lead · observations · citations** — all of it inside one visual. Each part is specified below.


- Pass `initial_investment` only when the subject has no `nav` and the advisor named an amount.
- Pass `benchmark` only when one was resolved in preflight. Never pass `include_contributors` or `include_all_scenarios` — both are follow-ups.
- Impacts are modelled: never call one a prediction, a probability or a likelihood.

#### Column chart

| Key | Value |
|---|---|
| Component | Column chart |
| Label | `Scenario impact on portfolio value (<unit>)` · `<unit>` ← `$K` when `value_base_source` is `nav` and no benchmark is named, else `%` |
| Data | the returned scenarios |
| Order | most-negative first on `impact_value` |
| Encode | `x` ← `display_name` · `y` ← `impact_value` in dollars, the numeric impact field in percent · `series` ← one on slot 1; benchmark named: `benchmark_impact` slot 1, `impact` slot 2, both absolute |
| Exclude | `relative_impact` — quoted in observations, never drawn |
| Scale | `auto-step` |
| Guides | ticks: the axis unit at the derived step, `$0K`, `-$5K`, `-$10K`, … · value label, dollars: two lines, the full currency figure then the formatted `impact` in parentheses beneath · value label, percent: the formatted `impact`, one line · legend: below the plot, left-aligned, `■ <benchmark name>  ■ <subject name>`, two series only |
| Limit | cap 10 categories by magnitude, every positive-impact row inside the ten kept · overflow line `<n> further scenarios not shown.` directly beneath the chart |
| Empty | no chart; the lead names the omission |
| Rules | `categorical-fill` · `verbatim(display_name)` · `verbatim(impact)` · `omit-and-name` |
| Fallback | `Scenario` \| `<benchmark name>` \| `This portfolio` — a lone series keeps `This portfolio` alone |

#### Value-basis note

| Key | Value |
|---|---|
| Component | Footnote · directly beneath the chart |
| Data | `value_base_source` |
| Slots | the line `Dollar impacts use an illustrative investment amount, not this portfolio's value.` when `value_base_source` is `default` |
| Empty | any other value: no note |
| Fallback | the same line as plain text |

#### Observations

Take each of these in order whose data is present, stop at five; write fewer than three only when fewer are present, never pad:

1. The worst case and its cost.
2. Whether the response's own worst scenarios are materially worse than the shortlist this deployment always reports — only when the response distinguishes the two.
3. The mildest case and its cost, beside the worst already named.
4. Rate sensitivity on its own — only when a Treasury scenario is present.
5. Benchmark-relative direction — only when `relative_impact` exists.

Each bullet cites a figure, and each cites a figure no earlier bullet used. The mildest case and the lone rate scenario are often one scenario; when they are, it is named once.

**Write section 2 now**, before calling the step-3 tools. All of it is on screen before those calls: `### 2. Scenarios` · the column chart · the value-basis note when it applies · the bold lead · the observations · the citations.

### Step 3: Opportunities, fund health, Funds to Explore · `get_opportunities_and_issues` + `get_fund_health_and_ideas`

Issue `get_opportunities_and_issues` and `get_fund_health_and_ideas` **in the same message**. If this host cannot, call them back to back with nothing written between them.

Once both return, and before the first card, write `### 3. Opportunities, fund health, Funds to Explore` — that exact wording.

Renders, in this order: **`### 3. Opportunities, fund health, Funds to Explore` · bold lead · opportunity cards · status roll-up** (twenty or more funds only) **· fund-card grid · footnote · commentary · citations** — all of it inside one visual. Each part is specified below.


- **Lead** — it reports the opportunity rules only, so say which they are: `signals` empty, exactly `**No opportunity rules flagged.**`; otherwise `**<n> opportunity rules flagged.**` (`**1 opportunity rule flagged.**`). Never write a bare `Nothing flagged.` — a fund card carrying a fired driver sits directly beneath it, and an unscoped lead reads as a claim about the whole section. If clear checks are not returned, do not add a clear count.
- Name `non_fund_holdings` and `unknown_holdings` by count. No position vanishes silently.

#### Opportunity cards

| Key | Value |
|---|---|
| Component | Status card · grid 3-col, cards top-aligned and stretched to the row's tallest |
| Data | `signals` — fired signals, and clear opportunity checks where returned |
| Order | response order |
| Encode | `fill` ← the returned severity for a flagged signal, default Warning; Good for a clear check |
| Exclude | `value` · `threshold_low` · `threshold_high` · `breach_threshold` — the comment carries the figure, commentary cites it |
| Empty | `signals` empty: no cards, and the lead says so |
| Rules | `verbatim(recommendation_link)` |
| Fallback | `Status` \| `Opportunity` \| `Portfolio observation` \| `Available path` |

| Slot | Source | Absent |
|---|---|---|
| `status label` | `FLAGGED` or `CLEAR`, uppercase, weight 500, letter-spaced, in the `fill` role | — |
| `opportunity` | `title` | — |
| `observation` | `comment`, then one line `<ticker> <amount>` per contributor, comma-separated | the contributor lines drop |
| `available path` | the returned alternative as link text → `recommendation_link` · fund-health signal with any fund needing review or immediate attention → `Review flagged funds in this chat` · a methodology URL → `Fund health methodology`, as a secondary source link | `None offered` |

#### Status roll-up

| Key | Value |
|---|---|
| Component | Status list · twenty or more entries in `funds` only, directly before the grid |
| Data | `funds`, grouped by status |
| Order | the fund-card status order |
| Empty | fewer than twenty funds: no roll-up |
| Rules | `never-replaces(fund cards)` |
| Fallback | the same rows as a table, the status words first |

| Slot | Source | Absent |
|---|---|---|
| `row` | the status dot and its words | the status has no row |
| `count` | the entries in that status | — |
| `members` | their tickers joined by ` · ` | an id with no ticker is left out |

#### Fund cards

| Key | Value |
|---|---|
| Component | Status card · grid 4-col, wrapped rows; full-width only when the host cannot render a grid, or one fund is returned |
| Data | `funds` |
| Order | status `consider_immediate_attention` → `consider_review` → `on_track` → `unavailable`, then drivers fired descending, then response order |
| Encode | `fill` ← the returned status |
| Variant | compact · `on_track` only, twenty or more holdings · keeps `header.left`, `header.right`, `meta` (category, weight), `explore` |
| Empty | `funds` empty: no grid, and the footnote names the coverage gap |
| Rules | `omit-and-name` · `required-when-present(meta)` · `required-when-present(explore)` · `label-only(flags)` · `verbatim(ideas[].label)` |
| Fallback | `Fund` \| `Name` \| `Status` \| `Meta` \| `Drivers fired` \| `Ideas` |

Five slots — `header`, `name`, `meta`, `flags`, `explore` — in this order, none merged:

| Slot | Source | Absent |
|---|---|---|
| `header.left` | `ticker` → `description`, weight 500 | `Unidentified holding` |
| `header.right` | Status tag ← the status map below | — |
| `name` | `description` | omit, and omit when already `header.left` |
| `meta` | `<category> · <weight as returned> · <expense ratio>bps · <yield> yield`; the expense ratio is the one arrived figure this review converts, percent to integer basis points | omit the absent part |
| `flags` | one row per fired driver: `⚑` in the `Icon` colour of the driver's `severity` role, then the driver's own label | judged: Good tag `✓ No drivers flagged` · `unavailable`: Neutral tag `— No verdict returned` |
| `explore` | one Inset block per distinct `ideas[].label`, in arrival order: block label ← that `label` · one line per idea carrying it, `value` → `product_url` | the one line `No ideas returned` |

| `status` | Role | Glyph | Words |
|---|---|---|---|
| `consider_immediate_attention` | Critical | `●` | `Consider immediate attention` |
| `consider_review` | Warning | `●` | `Consider review` |
| `on_track` | Good | `●` | `On track` |
| `unavailable` | Neutral | `●` | `No verdict` |

#### Footnote

| Key | Value |
|---|---|
| Component | Footnote · one line, the parts that apply, in order |
| Data | `non_fund_holdings` · `unknown_holdings` · the no-verdict count |
| Slots | `<n> non-fund holdings and <m> unresolved holdings sit outside fund health.` · `<k> funds returned no verdict.`, omitted when the status roll-up rendered |
| Empty | no part applies: no footnote |
| Fallback | the same line as an italic line under the table |

#### Commentary

Point form, after the cards. Signals and fund-level drivers already sit in the visual cards; do not restate each card in prose. Write at most three bullets: which opportunity or fund to inspect first · why it outranks the others, citing the separating metric · coverage gaps such as non-fund or unresolved holdings. No paragraph per flagged fund.

**Write section 3 now**, before calling the step-4 tool. All of it is on screen first: `### 3. Opportunities, fund health, Funds to Explore` · the bold lead · the opportunity cards when signals fired · the fund-card grid · the footnote when it applies · the commentary · the citations.

### Step 4: Capital gains · `get_capital_gains_and_tax_analysis`

The tool takes `subjects: [{type, id}]` — the same subject as steps 1 to 3, as a one-entry list.

Once the call returns, and before the first tile, write `### 4. Capital gains` — that exact wording.

Renders, in this order: **`### 4. Capital gains` · stat tiles** (no label) **· `Estimate status` list · `Distribution cut-offs` timeline · cut-off table · assumptions disclaimer · fund cards · ideas disclaimer · bold lead · bullets · footnotes · citations** — all of it inside one visual. Each part is specified below.

- **Every figure is named by the response.** A named figure arrives as `{label, value}`: `<field>` below is its `value`, and `<field>.label` the words for it, printed as they arrived. Where the lead or a bullet explains a figure, use its `<field>.definition` as it arrived; a figure with none is named by its label, never explained in your own words. A status group names itself `status_label` and its count `fund_count_label` the same way.
- Every estimate is subject to change. It carries no cost basis and no client rate, so a tax cost is what the distribution costs a holder of that position, never this client's realised gain, and it reads the same in a tax-deferred account.
- `not_tracked` means no estimate is being followed, never that the fund will not distribute; `not_expected` is the only status reporting no expected distribution.
- **Off season** — `totals.tracked_count` at zero: the section renders its heading · its bold lead from the lead table · the footnotes · the citations, and nothing else. A call that fails renders the heading and the gap statement.

The status map, read by the status list rows and the fund-card status tags:

| `status` | Role | Glyph |
|---|---|---|
| `upcoming` | Warning | `●` |
| `pending` | Informational | `●` |
| `not_expected` | Good | `●` |
| `not_tracked` | Neutral | `●` |
| absent | Neutral | `●` |

**Words** ← the group's `status_label`, verbatim; a group carrying none takes `status_text`, verbatim.

#### Capital gains tiles

| Key | Value |
|---|---|
| Component | Stat tile · one row, no label above the row |
| Data | the `upcoming` status group · `totals` |
| Encode | `label` ← the tile name · `figure` ← the source below, verbatim when it arrived carrying its own `%` or currency symbol · `sub` ← its context line |
| Limit | one figure per tile · `fund_count_label` takes its denominator after ` of ` |
| Empty | drop the tile, and never re-space to close the gap |
| Rules | `omit-and-name` · `no-derive(high estimates)` · `no-derive(potential tax impact)` · `no-derive(nearest cut-off)` · `as-arrived(figure)` · `verbatim(label)` · `verbatim(fund_count_label)` · `footnote-markers` |
| Fallback | the tile labels as column heads, the figures on the row beneath |

| Slot | Source | Absent |
|---|---|---|
| `fund_count_label` | the group's `fund_count`, then ` of `, then `totals.fund_count` | `totals.fund_count` absent: the count alone · the group's `fund_count` absent: omit |
| `high_estimate_fund_count.label` | the group's `high_estimate_fund_count`, in Critical text above zero and primary ink at zero | omit |
| `position_tax_cost_total.label` | the group's `position_tax_cost_total`; `sub` ← `Tax cost` | omit |
| `Nearest cut-off` | the group's first fund, its `days_until_sell_by`, as `<n> days` | omit |

No `upcoming` group: no tiles, and the lead reports the status groups that did arrive.

#### Estimate status

| Key | Value |
|---|---|
| Component | Status list |
| Label | `Estimate status` |
| Data | `funds_by_status` |
| Order | response order |
| Variant | recently-posted split · the `upcoming` row only, when any of its funds carries `recently_posted` true · keeps `row` and `count`, and splits `members` into two labelled lines |
| Empty | `funds_by_status` empty: no list, and the lead names the gap |
| Rules | `never-replaces(fund cards)` · `verbatim(status_label)` · `verbatim(status_text)` · `from-field-only(row, funds_by_status[].status)` |
| Fallback | the same rows as a table, the status words first |

| Slot | Source | Absent |
|---|---|---|
| `row` | the status dot and the words in the status map above | a status with no group has no row |
| `count` | `fund_count` | — |
| `members` | the group's funds' `ticker`, joined by ` · ` | a fund with no ticker is left out |
| `members` split lines | `recently_posted_fund_count.label` ← the funds carrying `recently_posted` true · `Posted earlier` ← the rest, each label in secondary ink and its tickers in primary ink, parted by a `1px` divider | no fund carries `recently_posted` true: one unsplit line |

#### Distribution cut-offs

| Key | Value |
|---|---|
| Component | Timeline |
| Label | `Upcoming capital gains distribution cut-offs` |
| Data | the `upcoming` group's `funds` |
| Order | response order |
| Encode | `position` ← `days_until_sell_by` · `fill` ← Critical on `high_estimate`, else Warning · `name` ← `ticker` |
| Guides | ticks: `today`, `30 days`, `60 days`, `90 days` |
| Limit | a fund past `90 days` sits at the right end |
| Empty | no `upcoming` funds: no timeline |
| Rules | `never-replaces(cut-off table)` · `omit-and-name` |
| Fallback | one row per fund, no axis |

| Slot | Source | Absent |
|---|---|---|
| `dot` | one per fund at its `days_until_sell_by` | omit |
| `label` | `ticker` | `name`, else no label |

#### Cut-off table

The timeline's label covers it; the table carries no label of its own.

| Key | Value |
|---|---|
| Component | Table |
| Data | the `upcoming` group's `funds` |
| Order | response order |
| Exclude | `short_term_estimate` · `long_term_estimate` · `estimate_date` · `cusip` · `by_subject` |
| Empty | no `upcoming` funds: no table, and the lead says so |
| Rules | `verbatim(total_estimate)` · `verbatim(tax_cost_pct)` · `as-arrived(position_value)` · `verbatim(label)` · `omit-and-name` · `footnote-markers` |
| Fallback | `Fund` \| `days_until_sell_by.label` \| `position_value.label` \| `total_estimate.label` \| `position_tax_cost.label` |

| Slot | Source | Absent |
|---|---|---|
| `Fund` | `ticker`, weight 500 | `name`, else `Unidentified holding` |
| `days_until_sell_by.label` | `<days_until_sell_by> days` in primary ink · `sell_by_date` as day and month in words, secondary ink, never wrapping | omit the absent part |
| `position_value.label` | `position_value` | omit |
| `total_estimate.label` | `total_estimate`, in Critical text on `high_estimate` | omit |
| `position_tax_cost.label` | `tax_cost_pct` in secondary ink · `position_tax_cost` in primary ink | omit the absent part |

A fund's distribution in dollars is returned by nothing. `totals.estimated_distribution_total` is the one distribution amount that arrives, it is gross over every fund carrying an estimate including those already distributed, and it is never read as a saving or as any one fund's figure.

#### Assumptions disclaimer

| Key | Value |
|---|---|
| Component | Footnote · directly beneath the cut-off table; the review's only tax-rate wording |
| Data | `assumptions.assumptions_disclaimer` |
| Slots | `assumptions.assumptions_disclaimer`, printed as it arrived |
| Empty | absent: no footnote, and the coverage bullet reads `No tax-rate assumptions were returned.` |
| Rules | `verbatim(assumptions_disclaimer)` · `omit-and-name` |
| Fallback | the same line as an italic line under the table |

#### Capital gains cards

| Key | Value |
|---|---|
| Component | Status card · grid 3-col, cards top-aligned and stretched to the row's tallest |
| Data | the `upcoming` group's `funds` |
| Order | response order |
| Encode | `fill` ← Critical on `high_estimate`, else Warning |
| Exclude | `short_term_estimate` · `long_term_estimate` · `cusip` · `by_subject` · `tracked` · `ideas[].fund_name` · `ideas[].expense_ratio` |
| Empty | no `upcoming` funds: no cards, and the lead says so |
| Rules | `omit-and-name` · `required-when-present(explore)` · `verbatim(product_url)` · `verbatim(label)` · `verbatim(ideas[].label)` · `footnote-markers` |
| Fallback | `Fund` \| `Name` \| `Status` \| `position_tax_cost.label` \| `total_estimate.label` \| `Ideas` |

Five slots — `header.left`, `header.right`, `name`, `tax cost`, `explore` — in this order, none merged:

| Slot | Source | Absent |
|---|---|---|
| `header.left` | `ticker`, weight 500 · Pill `recently_posted.label` on `recently_posted` true | `name`, else `Unidentified holding` |
| `header.right` | Status tag ← the `upcoming` row of the status map above | — |
| `name` | `name` | omit, and omit when already `header.left` |
| `tax cost` | Inset block `position_tax_cost.label`: `position_tax_cost`, then the line `from a capital gains distribution of <total_estimate> of NAV`, that figure in Critical text on `high_estimate` | omit the absent part; both absent: omit the block |
| `explore` | one Inset block per distinct `ideas[].label`, in arrival order: block label ← that `label` · one line per idea carrying it, `value` → `product_url` | the one line `No ideas returned` |

#### Ideas disclaimer

| Key | Value |
|---|---|
| Component | Footnote · directly beneath the cards |
| Data | `ideas_disclaimer` |
| Empty | no card rendered an idea: no footnote |
| Rules | `verbatim(ideas_disclaimer)` · `omit-and-name` |
| Fallback | the same line as an italic line beneath the table |

| Slot | Source | Absent |
|---|---|---|
| `line` | `ideas_disclaimer`, printed as it arrived | no footnote, and the coverage bullet reads `No ideas disclaimer was returned.` |

#### Capital gains prose

The bold lead is the row its case matches, filled. `<analysis date>` is `analysis_date` in words, day month year; `<marker>` is the field's Footnote marker, empty when it carries none.

| Case | Lead |
|---|---|
| `totals.tracked_count` at zero | `**No capital gains distribution estimates are available as of <analysis date>.**` |
| no `upcoming` group | `**No fund has an upcoming capital gains distribution as of <analysis date>.**` |
| an `upcoming` group | `**<fund_count> of <totals.fund_count> funds have an upcoming capital gains distribution, with a <position_tax_cost_total.label><marker> of <position_tax_cost_total>.**` |

Then bullets in this order — each only when its field arrived, at most three — each its `Form`, filled, and nothing after it:

| Bullet | Figure | Form |
|---|---|---|
| the most exposed upcoming fund | the `upcoming` fund carrying the largest `position_tax_cost` | `<ticker> carries the largest <position_tax_cost.label><marker>, <position_tax_cost>, cut-off <sell_by_date>.` |
| a recently posted estimate | a fund carrying `recently_posted` true | `<ticker>: <recently_posted.label>, <total_estimate.label> <total_estimate>.` |
| what is untracked and what is pending | the `not_tracked` group, then the `pending` group | `<fund_count> <status_label>` per group, joined by ` · `; a group that did not arrive drops |

Then coverage, naming `unrecognized_holdings` by count and by stored identifier, and naming by label every tile, column or block this response did not carry. Then footnotes, then citations. No other paragraph.

No bullet repeats a figure an earlier bullet used. `footnote-markers` holds here too: every mention of a footnoted figure carries its marker, the bold lead and the closing included.

#### Capital gains footnotes

| Key | Value |
|---|---|
| Component | Footnotes · after the bullets, before the citations |
| Data | `footnotes` |
| Order | arrival order |
| Empty | `footnotes` absent: no block |
| Rules | `footnote-markers` · `footnotes-verbatim` |
| Fallback | `title` in bold, then one line per entry, its marker a superscript digit |

| Slot | Source | Absent |
|---|---|---|
| `title` | `footnotes.title` | omit |
| `entries` | `footnotes.entries`: Footnote marker ← `marker`, then `text` | an entry with no `marker` starts with its `text` |

Renders whenever `footnotes` arrived, the off-season section included.

**Write section 4 now**, before the closing. All of it is on screen first: `### 4. Capital gains` · every part the Renders line names that this response carries, or the off-season lead · the footnotes · the citations.

### Step 5: Closing

Renders, in this order: **caveats · closest-model side-by-side card with its action button** (only when step 1 carried one) **· the most material finding · follow-ups · disclaimer.** The closing follows the review's last tool call, so it is the one region whose chat text is reliably visible — its prose does not need a visual to survive. Each part is specified below.


Report/PDF handoff is reactive only. Do not proactively mention report generation, PDFs, or Generate Report in the closing. If the advisor asks for a report or PDF, route through `ac360-capability-router`.

#### Caveats

| Key | Value |
|---|---|
| Component | Footnote · one line per caveat that applied |
| Slots | inferred column mapping · resolved identifier · placeholder investment amount · equity-sleeve scope · benchmark assumed · unrecognised holdings left out of the capital gains analysis |
| Empty | none applied: no block |
| Fallback | the same lines as plain text |

#### Closest model

| Key | Value |
|---|---|
| Component | Side-by-side card · only when step 1 carried a `closest_model` |
| Data | the held `list_models` row · the held equity and fixed-income slice figures |
| Empty | no `closest_model`: no block |
| Rules | `omit-and-name` · `no-substitute(closest model)` · `from-field-only(card.rows, the list_models row)` · `case-fold(ETF, MF, SMA)` · `verbatim(prose)` |
| Fallback | the card as a two-column table — `Closest fit` \| `<name>`, then one row per `card.rows` line — with `prose` and the `action` label as plain text beneath |

Both halves required: a card alone, or a card whose rows absorb the prose, is not this block.

| Slot | Source | Absent |
|---|---|---|
| `card.pill` | Pill `Closest fit` | — |
| `card.name` | `<name>`, weight 500, beneath the pill | — |
| `card.rows` | `Model split` ← `<short_name>` · `Risk profile` ← `<risk_profile>` · `Objective` ← `<objective>` · `Vehicles` ← `<family_vehicle>` · `Rebalance` ← `<family_trade_frequency>`; label on one line, value wrapping flush right | omit the row |
| `prose` | the template below, filled | — |
| `action` | Action button `Run side-by-side comparison in this chat.` | — |
| `source link` | `Open comparison in Advisor Center` → `closest_model.comparison_url` | omit |

Template, filled and printed exactly:

Within the BlackRock '<family>' model family, the closest match on your portfolio's equity/fixed-income split is the '<name>' — '<short_name>' against your '<equity_pct>'/'<fixed_income_pct>'. The match is on the split only, not the underlying holdings. Do you want to run a side-by-side comparison in this chat?

#### The most material finding

One sentence naming the most material finding of the whole review, drawn from what sections 1 to 4 actually returned and citing the figure that makes it material. Required in every review.

#### Follow-ups

Up to five, each one a tool this domain actually carries and each earned by something this review found: a model or benchmark comparison when a closest model was named, the full tax analysis when section 4 found an upcoming distribution, deeper risk, ESG or carbon, simulated performance, wealth projection, style factors, the full scenario set, or scenario contributors. Offer only what the findings motivate. End with `Say which of those you want and I'll run it on this portfolio.` None earned: no follow-ups and no closing line.

#### Disclaimer

| Key | Value |
|---|---|
| Component | Footnote · the closing's last line |
| Data | `disclaimer` |
| Slots | `disclaimer`, printed as it arrived, from the first response carrying one |
| Empty | no response carried `disclaimer`: no line |
| Rules | `verbatim(disclaimer)` · `once-per-response(disclaimer)` |
| Fallback | the same line as plain text |

**Write the closing now**, in that order; nothing follows it. On screen when it is done: the caveats that applied · the closest-model card with both its prose and its button when step 1 carried one · the most material finding · the follow-ups, ending with the closing line · the disclaimer.

## Source and link discipline

Every status, material metric, analytic result, methodology detail, security or fund reference, and approved content link must come from the returned Advisor Center 360° payload or an approved public, security, fund, or methodology page.

Do not cite BlackRock as the source of advisor or assistant-generated review points.

If a status, metric, link, methodology detail, or workflow route is not returned or listed as approved, label it unavailable.

**Links are part of the output.** When a returned Advisor Center, comparison, methodology, fund or product link supports a rendered block, surface it near that block. If section 1 returns `comparison_url`, the review must contain at least one `Open in Advisor Center` link. Do not finish a review with no links if any returned link exists. Never paraphrase a link away, and never build a product, methodology or workflow link by pattern.

**Read-only by default.** `create_portfolio`, `update_portfolio`, `clone_portfolio`, the household writes and `update_advisory_fee` write real data. Ask first, every time, and say what will be written.

## Language and tone

- Advisor-facing. Concise analytical working material — no talking points, no reassuring framing, no marketing tone, no copy the advisor could forward to a client.
- Use the shortest plain sentence that preserves the returned facts.
- Neutral and non-directive: report what the data shows; do not recommend an action.
- Name a missing value unavailable. Never fill a gap from memory, and never soften it with hedging.
- Say each thing once. Do not restate in prose what a visual already carries.
- No tool names, field names, ids, or missing-reference commentary in anything you write. Name a figure by its returned label, else the words this skill gives it, and stop there: no internal feature name, and nothing on how a figure or flag is sourced or worked out. Never describe the rendering mechanism or a fallback. Returned wording — footnotes, disclaimers, labels, status words — prints as it arrived, under its block's rules.

## Guardrail

Never present a modelled stress impact as a prediction, a probability or a likelihood; never claim a clear count the response did not return; never invent a benchmark or a closest model, and never substitute a named benchmark for a missing one; never write real data without naming what will be written and getting an explicit yes; never switch subject mid-review. A review that drops a section, builds a link by pattern, or reconciles two disagreeing verdicts has stopped organizing what came back and started authoring a conclusion this skill is not entitled to reach.
