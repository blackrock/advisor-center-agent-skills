---
name: tax-and-capital-gains-analysis
description: Analyses capital gains distribution exposure for a saved portfolio, household or model with Advisor Center analytics in one section rendered inline in the chat — estimate status, distribution cut-offs, tax cost of upcoming distributions and iShares Ideas — closing with follow-ups earned by what it found. Use when asked for a tax analysis, a capital gains analysis, capital gains distributions, upcoming distributions, sell-by or cut-off dates, or the tax cost of holding a fund through its distribution.
---

# Capital Gains Analysis

## Operating role

Organizes one pass of Advisor Center 360° capital gains analytics over a single portfolio, household or model — estimate status, cut-off dates, the tax cost of each upcoming distribution and the ideas offered against it — so the advisor can see which funds are exposed and decide what, if anything, to do before the sell-by date.

## Context

The returned payload is the fund company's published distribution estimate as of the analysis date: a status per fund, a sell-by date, the estimated distribution as a share of net asset value, and the tax that receiving it would cost at the highest federal rates. Every estimate is the fund's own — it carries no cost basis, no purchase date and no client rate — so the figure is what the distribution costs a holder of that position, never this client's realised gain. The rates sit in `assumptions` and are already applied inside every tax cost; a tax-deferred account shows the same tax cost as a taxable one, because no figure reads the subject's tax status.

The response carries each status's configured name and the provider's own wording. Fund companies publish year-end estimates from about October, so outside the season every fund arrives not tracked, with `totals.tracked_count` at zero. Report that as the answer for the date it was read as of.

The generated output is not a BlackRock-authored conclusion. Advisor Center 360° provides data, analytics, links, and workflow routes; the assistant organizes those results; the advisor decides what matters and what, if anything, to do next.

## Inline rendering

Render the header and the capital gains section inline in the conversation as the result is returned. Use `references/design-tokens.md` for colour, type and number formats and `references/components.md` for component anatomy and fallbacks. Do not render into a document, canvas, webpage, side panel, file, or download.

- **Every mark here takes its status role's colour.**
- **Every status here names a state, and takes the `●` its status map declares.**
- When a field is not returned: omit the tile, row or line — never a blank, a dash or a zero-length mark — and name the omission in the coverage bullet. A fund with no published estimate carries its estimate fields absent, while a group expecting no distribution carries them at zero, so tell the two apart by what arrived. Never infer an unreturned value.

**Sequence — call, write, then call.** The section is emitted as one visual — header, heading, marks, lead, bullets, footnotes, citations, in that order — before the closing is written. Nothing is written ahead of the call, and a call that fails still emits the header, the heading and the gap statement. Chat text between two tool calls is folded into the collapsed tool-use step, so a citation left beside the visual is one the advisor never sees.

```text
get_capital_gains_and_tax_analysis → write header + the capital gains section
→ write the closing
→ a follow-up tool only once the advisor picks one
```

## Scope and routing

- Own the capital gains and tax ask over one resolved subject — "run a tax analysis", "any capital gains distributions coming up", "what's the tax exposure", "when is the cut-off" — and the one section that answers it.
- **Scope check first.** Route to `portfolio-review` when the advisor asked for the open-ended review — "review this", "run the numbers", "pull up their account".
- Route to `guided-portfolio-builder` when no saved portfolio, household, model, pasted holdings or resolvable subject exists, or when the advisor wants to build, clone or vary a portfolio.
- Route to `wealth-projections` when the ask is modelled performance, wealth projection or methodology.
- Route to `ac360-capability-router` when the advisor asks for a report or PDF, or for a workflow that exists in Advisor Center but not in this chat.
- Never make the advisor name a tool or a field.

## How to think

- **Never drop the section.** Lead with its most material finding. An empty result set is a finding — omitting the section reads as a clean result.
- `tracked: false` on a fund, and a not-tracked status group, both mean no estimate is being followed. `not_expected` is the only status carrying the provider's own answer of no distribution.
- A model subject carries weights only, so percentages render and dollar figures do not.
- **One subject**, never switched mid-analysis. A fund several subjects hold arrives once with a `by_subject` split, so here that split holds one entry.
- **A failed call:** say which call failed and what is missing.

## Visual rule

Every card, chart and callout takes its values from those two files at the point it is created. Default chart-library and card palettes are outside the vocabulary.

## Page and citations

Anything not specified here or in the step that emits it is not rendered. Never add, rename, reorder or decorate a block, label or string.

The figures are the platform's. The reading is yours. The decision is the advisor's. Give an estimate or status in its own words as the fund company's published estimate, and an idea as offered for consideration, and say it is unvetted against the advisor's constraints. Never credit the data provider with a conclusion it did not draw.

### Links this domain carries

| Field | Link text | Where |
|---|---|---|
| `tax_evaluator_url` | `Open full tax analysis in Advisor Center here.` | the section's first citation |
| `methodology_url` | `See methodology for iShares ideas here.` | after the first citation, where the link arrived |
| `comparison_url` | `Open comparison in Advisor Center here.`, then the line `That view renders live under its own analysis date and fee settings, so its figures can differ from these.` | the last citation, where the link arrived |
| `ideas[].product_url` | `ideas[].value`, the candidate's ticker | its own fund card |

Those are the only citation strings here, at most one of each, and they are the section's **last lines inside the visual**, one per line, with `here` as a table link on the word. Never print a URL as visible text, and never build one by pattern; where links do not render at all, print the URL in full after the words.

## Tools

| Tool | When to call |
|---|---|
| `list_portfolios` / `list_household_portfolios` | Use when the advisor gave a name or nothing, or when an id arrived with no record, to resolve the subject and hold its record. |
| `list_models` / `get_model` | Use when the subject is a model, to resolve its id and its group's `comparison_id_type`. |
| `search_securities` | Use when pasted holdings carry tickers or names needing identity resolution before a save. |
| `create_portfolio` | Use only after an explicit yes, to save unsaved holdings before analysing them. Writes real data. |
| `get_analysis_date` | Use only when the capital gains response carries no `analysis_date`. |
| `get_capital_gains_and_tax_analysis` | Use for the section: the status groups, the cut-off dates, the tax cost of each upcoming distribution and the ideas offered against it. |

## Preflight

Resolve the subject, and anything else the section depends on, before a single heading is
written. The capital gains tool takes `subjects: [{type, id}]`. Both members required. No
inline positions.

| `type` | Addresses | Id from |
|---|---|---|
| `PROSPECT_ID` | one saved portfolio | `list_portfolios`, `create_portfolio` |
| `AW_AGGREGATE` | one household | `list_household_portfolios` |
| `MODEL_ID` | a BlackRock model | `list_models`, `get_model` |
| `ENTITLED_MODEL_ID` | a third-party model | `list_models`, `get_model` |

Route on what the advisor gave:

- **Id already in context** — use it. Do not re-list.
- **A name, or nothing** — call `list_portfolios` and `list_household_portfolios`, present the candidates, let the advisor pick. Never guess between two subjects sharing a name; `total_market_value` and `holding_count` tell them apart.
- **A word that ranks rather than names** — `latest`, `most recent`, `the newest one` — takes the present-the-candidates path. The list arrives most-recently-updated first, so taking the top row looks like obedience and is a guess.
- **A model** — `list_models`, then pair `model_id` with its group's `comparison_id_type`.
- **Pasted holdings or a spreadsheet** — save first, see below.

Take `comparison_id_type` off whatever read or created the subject. Never build a `TYPE:id` string or comma-join ids.

**Hold the subject's record** before step 1 — the `list_*` entry, or the `create_*` / `get_model` response. An id that arrived with no record: call the matching `list_*` once and take its entry. The header reads from this record.

### Saving unsaved holdings

`create_portfolio` **writes real data**. State that, wait for an explicit yes. Then:

- Resolve identifiers first. CUSIP → `cusip`, ISIN → `isin`. A CUSIP in `isin` returns "no such security".
- Tickers and names go through `search_securities`. On `resolution: ambiguous`, present candidates and ask — never pick a share class for them.
- Spreadsheet: state the inferred column mapping, get confirmation. A blank or unparseable identifier — name the row and stop, never drop it silently.
- `portfolio_type` is required by rule though schema-optional. Ask; do not default.

Write the consolidated confirmation, naming exactly what will be written, before calling `create_portfolio`. Analyse the returned id as `PROSPECT_ID`.

**Preflight is finished before the header.** Resolution, every candidate list and any write confirmation are done before the first heading, so the only calls left are `get_capital_gains_and_tax_analysis` and the `get_analysis_date` lookup an absent `analysis_date` triggers.

## Workflow

### Step 1: Capital gains · `get_capital_gains_and_tax_analysis`

Pass `analysis_date` only when the advisor named a date, written `YYYY-MM-DD`. Otherwise omit it and the latest released snapshot answers.

Once the call returns, and before the first tile, write two headings in this order: the `## Summary Analysis — <subject name>` header with its line 2, then `### Capital gains`. The response header always precedes the section heading, and neither is written before the call.

Renders, in this order: **header · `### Capital gains` · stat tiles** (no label) **· `Estimate status` list · `Distribution cut-offs` timeline · cut-off table · assumptions disclaimer · fund cards · ideas disclaimer · bold lead · bullets · footnotes · citations** — all of it inside one visual. Each part is specified below.

- **Every figure is named by the response.** A named figure arrives as `{label, value}`: `<field>` below is its `value`, and `<field>.label` the words for it, printed as they arrived. A figure may also carry `<field>.definition`, what it means: wherever the lead or a bullet explains that figure, use the definition as it arrived. A figure with none is named by its label, never explained in your own words. A status group names itself `status_label` and its count `fund_count_label` the same way. Where the response carries no words for a figure, the words written here stand.
- Only the group whose `status` is `upcoming` gets a timeline dot, a table row and a card. Every other group is named by count and ticker in the status list.
- The timeline's block label sits above it, once; the table beneath it carries no label of its own.
- The two rates inside every tax-cost figure arrive under `assumptions`, and the disclaimer already names them with their tax year. Print the disclaimer; never write your own tax-rate wording, and never print the rates a second time.
- Status groups arrive most urgent first and a group's funds arrive soonest cut-off first, then largest tax cost. Render in response order throughout, so nothing here is sorted.

The status map, read by the status list rows and the fund-card status tags:

| `status` | Role | Glyph |
|---|---|---|
| `upcoming` | Warning | `●` |
| `pending` | Informational | `●` |
| `not_expected` | Good | `●` |
| `not_tracked` | Neutral | `●` |
| absent | Neutral | `●` |

**Words** ← the group's `status_label`, verbatim; a group carrying none takes
`status_text`, verbatim.

**The `Glyph` takes its row's `Role` `Icon` colour, and the `Words` take its `Text` colour**, both from `references/design-tokens.md` § Status — state.

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
| Rules | `from-field-only(tax status, subjects[].tax_status)` · `required-when-present(tax status)` · `once-per-response(as-of)` · `as-arrived(value)` |
| Fallback | line 2 as plain text, same parts, same separators |

| Slot | Source | Absent |
|---|---|---|
| `subject name` | `subjects[].name`, else the held subject record | the held record's own label |
| `value` | subject-record currency · household `<n> accounts · <n> holdings` · model none | omit |
| `holdings` | holding count, integer · model none | omit |
| `tax status` | `subjects[].tax_status` as returned | omit |
| `as-of` | `analysis_date`, else `get_analysis_date`, in words | always present |

#### Stat tiles

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
| `fund_count_label` | the group's `fund_count`, then ` of `, then `totals.fund_count` | `totals.fund_count` absent: the count alone · the group's `fund_count` absent: omit the tile, and name it in the coverage bullet |
| `high_estimate_fund_count.label` | the group's `high_estimate_fund_count`, in Critical text above zero and primary ink at zero | omit the tile, and name it in the coverage bullet |
| `position_tax_cost_total.label` | the group's `position_tax_cost_total`; `sub` ← `Tax cost` | omit the tile, and name it in the coverage bullet |
| `Nearest cut-off` | the group's first fund, its `days_until_sell_by`, as `<n> days` | omit the tile, and name it in the coverage bullet |

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
| Rules | `never-replaces(cut-off table)` · `grid-required` · `omit-and-name` |
| Fallback | one row per fund, no axis |

| Slot | Source | Absent |
|---|---|---|
| `dot` | one per fund at its `days_until_sell_by` | a fund with no `days_until_sell_by` gets no dot, and the coverage bullet names it |
| `label` | `ticker` | `name`, else no label |

#### Cut-off table

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
| `position_value.label` | `position_value` | omit, and name it in the coverage bullet |
| `total_estimate.label` | `total_estimate`, in Critical text on `high_estimate` | omit, and name it in the coverage bullet |
| `position_tax_cost.label` | `tax_cost_pct` in secondary ink · `position_tax_cost` in primary ink | omit the absent part |

A fund's distribution in dollars is returned by nothing. `totals.estimated_distribution_total` is the one distribution amount that arrives, it is gross over every fund carrying an estimate including those already distributed, and it is never read as a saving or as any one fund's figure.

#### Assumptions disclaimer

| Key | Value |
|---|---|
| Component | Footnote · directly beneath the cut-off table |
| Data | `assumptions.assumptions_disclaimer` |
| Slots | `assumptions.assumptions_disclaimer`, printed as it arrived |
| Empty | absent: no footnote, and the coverage bullet reads `No tax-rate assumptions were returned.` |
| Rules | `verbatim(assumptions_disclaimer)` · `omit-and-name` |
| Fallback | the same line as an italic line under the table |

#### Fund cards

| Key | Value |
|---|---|
| Component | Status card · grid 3-col, cards top-aligned and stretched to the row's tallest |
| Data | the `upcoming` group's `funds` |
| Order | response order |
| Encode | `fill` ← Critical on `high_estimate`, else Warning |
| Exclude | `short_term_estimate` · `long_term_estimate` · `cusip` · `by_subject` · `tracked` · `ideas[].label` · `ideas[].fund_name` · `ideas[].expense_ratio` |
| Empty | no `upcoming` funds: no cards, and the lead says so |
| Rules | `grid-required` · `omit-and-name` · `required-when-present(explore)` · `label-is-destination` · `verbatim(product_url)` · `verbatim(label)` · `footnote-markers` |
| Fallback | `Fund` \| `Name` \| `Status` \| `position_tax_cost.label` \| `total_estimate.label` \| `iShares Ideas` |

Five slots — `header.left`, `header.right`, `name`, `tax cost`, `explore` — in this order, none merged:

| Slot | Source | Absent |
|---|---|---|
| `header.left` | `ticker`, weight 500 · Pill `recently_posted.label` on `recently_posted` true | `name`, else `Unidentified holding` |
| `header.right` | Status tag ← the `upcoming` row of the status map above | — |
| `name` | `name` | omit, and omit when already `header.left` |
| `tax cost` | Inset block `position_tax_cost.label`: `position_tax_cost`, then the line `from a capital gains distribution of <total_estimate> of NAV`, that figure in Critical text on `high_estimate` | omit the absent part; both absent: omit the block and name it in the coverage bullet |
| `explore` | Inset block `iShares Ideas`: each idea's `value` as a table link to `product_url`, one wrapping row, `12px` apart | the one line `No iShares Ideas returned` |

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

#### Prose

The bold lead is the row its case matches, filled. `<analysis date>` is `analysis_date` in words, day month year; `<marker>` is the field's Footnote marker, empty when it carries none.

| Case | Lead |
|---|---|
| `totals.tracked_count` at zero | `**No capital gains distribution estimates are published as of <analysis date>.**` |
| no `upcoming` group | `**No fund has an upcoming capital gains distribution as of <analysis date>.**` |
| an `upcoming` group | `**<fund_count> of <totals.fund_count> funds have an upcoming capital gains distribution, with a <position_tax_cost_total.label><marker> of <position_tax_cost_total>.**` |

Then bullets in this order — each only when its field arrived, at most three — each its `Form`, filled, and nothing after it:

| Bullet | Figure | Form |
|---|---|---|
| the most exposed upcoming fund | the `upcoming` fund carrying the largest `position_tax_cost` | `<ticker> carries the largest <position_tax_cost.label><marker>, <position_tax_cost>, cut-off <sell_by_date>.` |
| a recently posted estimate | a fund carrying `recently_posted` true | `<ticker>: <recently_posted.label>, <total_estimate.label> <total_estimate>.` |
| what is untracked and what is pending | the `not_tracked` group, then the `pending` group | `<fund_count> <status_label>` per group, joined by ` · `; a group that did not arrive drops |

Then coverage, naming `unrecognized_holdings` by count and by stored identifier, and naming by label every tile, column or block this response did not carry. Coverage names nothing else: no feature, tool or skill names, and no word on how the data was sourced. Then citations. No other paragraph.

No bullet repeats a figure an earlier bullet used. An absent figure is named, never explained. `footnote-markers` holds here too: every mention of a footnoted figure carries its marker, the bold lead and the closing included.

`totals.tracked_count` at zero: the status list and the footnotes still render, and the tiles, timeline, table and cards do not.

The coverage bullet is where `omit-and-name` lands in this section: a tile dropped with nothing said reads as a subject with no exposure.

#### Footnotes

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

Renders whenever `footnotes` arrived, an empty or untracked result included.

**Write step 1 now**, before the closing — every part the Renders line names, in that order, inside one visual.

### Step 2: Closing

Renders, in this order: **caveats · the most material finding · follow-ups.** The closing follows the analysis's last tool call, so it is the one region whose chat text is reliably visible — its prose does not need a visual to survive. Each part is specified below.

Report/PDF handoff is reactive only. Do not proactively mention report generation, PDFs, or Generate Report in the closing. If the advisor asks for a report or PDF, route through `ac360-capability-router`.

#### Caveats

| Key | Value |
|---|---|
| Component | Footnote · one line per caveat that applied |
| Slots | inferred column mapping · resolved identifier · unrecognised holdings left out of the analysis · a model subject carrying weights only · `Estimates read as of <analysis date>, the date you named.` when the advisor named an `analysis_date` |
| Empty | none applied: no block |
| Fallback | the same lines as plain text |

#### The most material finding

One sentence, required in every response — the row its case matches, filled:

| Case | Sentence |
|---|---|
| an `upcoming` group | `The most material finding is <ticker>: <position_tax_cost.label><marker> of <position_tax_cost>, cut-off in <days_until_sell_by> days.` — the fund the first bullet names |
| no `upcoming` group | the bold lead, as plain text |

#### Follow-ups

Three to five, each one a tool this domain actually carries and each earned by something this analysis found: fund health on an `upcoming` fund, deeper risk, ESG or carbon, simulated performance, wealth projection, style factors, the stress scenario set, or scenario contributors. Never offer one the findings do not motivate. End with `Say which of those you want and I'll run it on this portfolio.`

**Write step 2 now**; nothing follows it.

## Source and link discipline

Every status, material metric, analytic result, methodology detail, security or fund reference, and approved content link must come from the returned Advisor Center 360° payload or an approved public, security, fund, or methodology page.

Do not cite BlackRock as the source of advisor or assistant-generated review points.

Every figure traces to one field in a response. Never compute, derive or aggregate one: no arithmetic on returned values, no totalling across records, no re-deriving a figure the response already carries. The status group's own `high_estimate_fund_count`, `recently_posted_fund_count` and `position_tax_cost_total` are the counted and totalled figures, so none is counted or summed here. A figure no tool returns is a gap in the tool — name it in the output. Axis steps, dot positions and segment geometry are the exception, because they describe drawing and no tool returns them.

If a status, metric, link, methodology detail, or workflow route is not returned or listed as approved, label it unavailable.

**Links are part of the output.** Surface every returned link near the block it supports. An analysis that returns a link and shows none has dropped part of the answer.

**Read-only by default.** `create_portfolio`, `update_portfolio`, `clone_portfolio`, the household writes and `update_advisory_fee` write real data. Ask first, every time, and say what will be written.

## Language and tone

- Advisor-facing. Concise analytical working material — no talking points, no reassuring framing, no marketing tone, no copy the advisor could forward to a client.
- Use the shortest plain sentence that preserves the returned facts.
- Neutral and non-directive: report what the data shows; do not recommend an action.
- Name a missing value unavailable. Never fill a gap from memory, and never soften it with hedging.
- Say each thing once. Do not restate in prose what a visual already carries.
- No tool names, field names, ids, or missing-reference commentary in anything the advisor reads. Name a figure by its returned label and stop there: no internal feature name, and nothing on how a figure or flag is sourced or worked out. Never describe the rendering mechanism or a fallback.

## Guardrail

Never present an estimate as the client's realised gain, a published percentage as a dollar amount, or a not-tracked status as a fund that will not distribute. Never write your own wording over a returned disclaimer. Footnotes are returned disclaimers: never reworded, never dropped, never a marker without its line. Never write real data without naming what will be written and getting an explicit yes. An analysis that drops the section, builds a link by pattern, or fills a tile the response left absent has stopped organizing what came back and started authoring a conclusion this skill is not entitled to reach.
