# Components

The catalog every skill renders from, and the grammar for invoking it: what each component
is composed of, what it encodes, what it degrades to when the host cannot draw it. Values
— colour, type, number formats — are `design-tokens.md`. Which components a skill emits,
in what order, from which fields, is that skill's own `SKILL.md`. This file names
components, never a product.

## Mechanism

Render **inline in the conversation** at the richest rung the host displays there:

1. **The host's own inline visualization** — a chart, component or card rendered in the
   conversation. Full palette and geometry apply.
2. **Fallback when rich cards and charts are unavailable: markdown tables.** No colour, so
   labels and printed values carry every encoding.

**Find the capability before choosing the rung**, once per response, before the first
block: scan the active tools, then any deferred or searchable registry, for an inline card,
chart, component, image or visualization channel. A deferred tool lists a name with no
schema, so load it before ruling it out, and search by capability rather than one guessed
name — chart, graph, plot, diagram, visual, render, display, card, canvas, image, widget.
Only when both come up empty do markdown tables apply; carry that answer through the
response.

An available tool is used. Unsure it fits this block? Call it — a render that fails is
what licenses the fallback. Never choose markdown tables for speed, familiarity, or
because the fallback example is easier to copy. Two rungs are excluded because they have
failed: **raw HTML or SVG in the chat body**, escaped and shown as tags, and
**box-drawing or bar art in a code block**, which wraps.

Do not render into a document, canvas, webpage, side panel, file, or download. Never
describe the mechanism or a fallback to the reader.

That rule fixes where the reader's **answer** lives. A tool's **mark** may arrive as an
attachment, an image or a host-rendered component and is still rung 1, so it is the one to
call. Do not relocate the answer to get it: prose, figures and labels stay in the
conversation, with the mark alongside them.

## Page

- Heading levels: response header `##`, sections `###`. Nothing else is a heading.
- Vertical rhythm: `24px` between blocks · `8px` between a block label and its mark ·
  `4px` between a mark and its legend · `16px` before prose.
- Every block is **label** → **mark** → **legend** where specified. Never append a
  qualifier to a label.
- Weights cap at 500: the host's visualizer will not render heavier, so 600 silently
  degrades. Emphasis comes from size and ink.

## Marks

Data-bearing. A mark declares `Encode`, and a `Scale` where the column says one.

| Mark | Composition | Channels | Scale | Markdown fallback |
|---|---|---|---|---|
| Share bar | one full-width bar: outer ends radius `4px`, segments square between, `2px` surface-colour gap between them. Width no segment covers stays track, and segments are never stretched into it | `segment` ← the figure sizing it · `fill` ← categorical slot · `name` | `share` | one row per segment: name, figure, `█` cells |
| Paired bars | one row per series: label `160px` primary ink · track filling the remaining width · bar filling from the left · value `56px` right-aligned | `row` ← the label · `length` ← the figure · `fill` ← categorical slot | `peak` | one row per series: label, value, `█` then `░` to 20 cells |
| Column chart | plot `280px` tall, full width, the plot being the mark area: the category band beneath is reserved on top of that height, so nothing drawn lands in the text after the block. Y-axis `56px` left, tick labels `11px` secondary ink right-aligned to it, no x-axis line. Categories equally spaced; column `60%` of its slot for one series, `28%` each for two, first series left of its pair. Value label rides the free end of its own column, centred, primary ink, always inside the plot — below a negative end, above a positive one, never collected into a row along the axis. Category names share one baseline in the band, `11px` secondary ink, centred under their slot, wrapping as far as the text needs; the band is as tall as the tallest name, and a name never rides its column's label | `x` ← the category · `y` ← the figure sizing the column · `series` ← one or two, each on its own slot | `auto-step` | one row per category, one column per series |
| Timeline | one horizontal axis in axis colour, ticks at each labelled step with `11px` secondary ink labels beneath; one `12px` dot per item in its status role fill with a `2px` page-background ring, item label `11px` weight 500 above. Items at one position share a dot, labelled with every item joined by ` · ` on one line; where labels from different positions still collide, label the nearest and leave the rest unlabelled | `position` ← the step · `fill` ← status role · `name` | — | one row per item, no axis |
| Bar | height `20px`, radius `4px`; track behind it full width in gridline colour, same radius | `length` ← the figure · `fill` ← categorical slot | `peak` | `█` cells, **20 cells = the scale maximum**, minimum one cell for a non-zero value; track `░` to 20 cells |
| Column | width to fit, radius `4px` at the free end, `2px` gap within a pair, `24px` between groups; value label `12px` primary ink at the free end | `length` ← the figure · `fill` ← categorical slot | `auto-step` | one table row per category |

## Containers

A container declares `Slots`. Every slot it does not declare is absent from the output.

| Container | Composition | Channels | Slots it accepts | Markdown fallback |
|---|---|---|---|---|
| Stat tile | surface fill on the page background, `1px` card border, radius `8px`, padding `16px`; tiles in one row, `12px` apart, equal width whatever the content, never wrapping, always visibly bounded. Label `13px` secondary ink above, figure `24px` weight 500, sub-line `13px` secondary ink beneath the figure, all three left-aligned. No icon, no sparkline | `label` · `figure` · `sub` | the tiles the block declares | a two-row table: labels, then figures; `sub` joins its figure in the same cell |
| Card | `1px` card border, radius `8px`, `12px` padding, surface fill; three across unless the block names another column count. A slot's own line is primary ink, its first slot at weight 500; a line supporting a slot is `13px` secondary ink | — | whatever the block declares, in the order it declares them | one row of a table |
| Status card | a Card tinted by `fill`: the role's card wash across the whole card, its subtle border at `1px`. The dot and words still appear in the header, never replaced by the wash. The wash is fixed in either host mode, so every slot inside takes the fixed-fill ink of `design-tokens.md` § Ink | `fill` ← status role | whatever the block declares | one table row, the status words first |
| Side-by-side card | card `220px` fixed on the left, prose filling the rest, `24px` apart, both top-aligned; card `1px` card border, radius `8px`, `16px` padding, surface fill | — | `card.*` · `prose` · `action` · `source link` | the card as a table, then the prose beneath it |
| Status list | one row per state in the order the block fixes; each row a status dot, its short label, the count, then the member items joined by ` · `; rows parted by a `1px` card-border rule; no header row | — | `row` · `count` · `members` | a table, the status words first |
| Inset block | a block inside a card, radius `4px`, `8px` padding, setting one figure or one list apart from the card's other rows. Inside a tinted card it takes that role's subtle background; otherwise the surface fill. Label `13px` secondary ink, its lines beneath never repeating it | — | `label` · the lines beneath | a further line in the card's row, its label in bold |
| Footnotes | Block label ← `title`, `8px` above the first line; one Footnote line per entry in arrival order, `4px` apart, an entry carrying `marker` starting with its Footnote marker | — | `title` · `entries` | `title` in bold, then one line per entry, its marker a Unicode superscript digit (¹ ² ³ …) |
| Table | header row weight 500 with a `1px` card-border rule beneath; rows parted by a `1px` divider; `8px` cell padding; no fills | — | column heads · rows | as is |

## Atoms

Fixed form, no parameters beyond the string or role given.

| Atom | Form | Markdown fallback |
|---|---|---|
| Block label | `13px` secondary ink, sentence case, above the mark | bold line above the table |
| Legend | inline under the mark: `■` in the series colour, label in ink | the table's own column heads |
| Status dot | `10px` circle in the role's `Icon` colour, `6px` before the words | `●` before the words |
| Status tag | `12px`, radius `4px`, `4px 8px` padding, role subtle background and border; the words in the role's `Text` colour, and always the glyph its block declares, in the role's `Icon` colour. Words wrap right-aligned to as many lines as they need, the dot on the first line with the first word; never truncated, never shrunk, never overlapping what sits beside them | words prefixed with `●` |
| Table link | link colour, weight 500, underlined, trailing ` ↗`, wraps freely | a markdown link |
| Pill | `12px`, radius `4px`, `4px 12px` padding, Informational subtle background and border, Informational text | bold words on their own line |
| Footnote | `12px` secondary ink | italic line under the table |
| Footnote marker | the entry's `marker` as superscript, secondary ink, directly after the word it follows, no space | Unicode superscript digit |
| Action button | accent fill, surface ink, radius `4px`, `8px 16px` padding, weight 500, `13px`; the label is the exact string given, and a click sends that string into the conversation as the reader's next message | a plain line of text, no button |

## Scales

A mark names one, so no skill restates it and no two derive it differently.

| Scale | Rule |
|---|---|
| `share` | segments are shares of a whole 100%. The uncovered remainder stays track |
| `peak` | the largest value fills the track, rounded up to the next 5% or the equivalent currency step |
| `auto-step` | the axis spans the data's minimum and maximum, each rounded outward to a whole step. The step is the span over six, rounded up to the next `1`, `2`, `2.5` or `5` times a power of ten, giving four to eight gridlines. **Always derive the step from the data: a fixed step draws one gridline and leaves the plot empty.** Gridline at every step, zero line in axis colour, and at the plot's edge when every value shares a sign — top for all-negative, bottom for all-positive. Where value labels sit outside a mark's free end, the axis takes one further step beyond the extreme value, so the longest mark's label falls inside the plot |

## Rules

The closed set a block cites by name under `Rules`. A rule absent here does not exist: a
block needing one either names a parameter instead, or the rule is added here.

| Rule | Means |
|---|---|
| `omit-and-name` | omit the element whose field did not arrive, and name the omission in the block's footnote or a bullet. Never a blank, a dash or a zero-length mark |
| `required-when-present(<slot>)` | an optional slot turns mandatory the moment its field arrives |
| `verbatim(<field>)` | printed exactly as it arrived: never shortened, abbreviated, re-cased, re-rounded or restyled |
| `as-arrived(<figure>)` | the arrival form decides precision. Formatted — carrying its own `%`, symbol or separator — prints verbatim; raw takes its row in `design-tokens.md` § Numbers exactly, no place added or dropped |
| `no-derive(<element>)` | one returned field, never summed, differenced, ranked or otherwise derived |
| `no-substitute(<element>)` | a missing element is never filled from another value |
| `from-field-only(<element>, <field>)` | that field alone: never inferred from a neighbour, never from memory |
| `once-per-response(<element>)` | it appears in this block alone |
| `case-fold(<acronym>…)` | a lower-case value takes a capital on its first word only; the listed acronyms print upper-case |
| `categorical-fill` | a categorical slot whatever the sign of the values. Never a change-direction colour, a status role, or the accent |
| `one-marker` | one marker per legend entry — the swatch is the marker |
| `label-only(<slot>)` | the returned label and nothing after it: no explanation, no gloss |
| `grid-required` | a markdown table only when the host cannot render this component |
| `rich-rung-first` | the fallback applies only when the rung is unavailable; where the block sits is no reason to take it |
| `never-replaces(<block>)` | renders in addition to that block, never in place of it |
| `label-is-destination` | link text names what the link opens |
| `footnote-markers` | wherever a field named in a returned footnote's `attached_to` prints — label, column head, card label, a sentence naming it — that entry's Footnote marker follows it. Never a marker without its entry |
| `footnotes-verbatim` | every returned entry prints, in arrival order, `text` as it arrived: never shortened, reworded, merged or dropped |

## Invocation

A skill emits a block under its own `####` heading as a keyed table, keys in the order
below, omitting any the component does not take.

**A value is a term, a field path, a channel binding, a rule name or a table cell. A value
is never a sentence** — a sentence in a spec is a parameter nobody named, and a parameter
nobody named is a decision left to whoever renders it.

| Key | Value form | Required |
|---|---|---|
| `Component` | a catalog name, variant after ` · ` | always |
| `Label` | the exact label string, one per repeat | when it takes one |
| `Data` | a returned collection or field path | for a mark or a collection |
| `Order` | sort keys, in precedence order | when the order is fixed |
| `Encode` | `<channel> ← <field>`, ` · ` separated, one per channel | for marks and `fill` |
| `Slots` | the ordered parts inline, each a fixed string. A part needing a source or an absent case takes the slot table instead | for string parts |
| `Variant` | a named variant and the slots it keeps | when one exists |
| `Exclude` | returned fields this block must not draw | when any |
| `Scale` | a name from `## Scales` | for a scaled mark |
| `Guides` | `<guide>: <value>` — legend, ticks, value label | when fixed |
| `Limit` | `cap`, `floor`, `merge`, and the overflow line | when any apply |
| `Empty` | what stands in when the collection is empty | always |
| `Rules` | names from `## Rules`, ` · ` separated | when any apply |
| `Fallback` | the markdown column heads, left to right | always |

Slots follow as a second table, one row per slot, so a missing part has a row to be
missing from:

| Slot | Source | Absent |
|---|---|---|
| `<slot name>` | `<field>`, or the exact string | `<what stands in>`, or `omit` |

## Links

- Rich: descriptive link text, Link colour, underline, weight 500, trailing ` ↗`. Markdown
  fallback: a standard link, descriptive text.
- Never print a raw URL as visible text unless links do not render.
- Never build a product, methodology or workflow link by pattern. Returned or approved
  links only.
