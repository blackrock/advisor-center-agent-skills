# Design tokens

Values and roles only: colour, type, number formats. How to render is `components.md`. A
skill binds these roles to its domain in its own `SKILL.md`. Never use a colour absent
from these tables.

## Typeface

Inherit the host's. Never set `font-family`, never ship or link a font.

## Ink, surface and chrome

**Inherit every role below from the host, the way the typeface is inherited.** The host
knows whether it is drawing light or dark, and a hard-coded light value goes unreadable
the moment it is not.

**A fixed fill brings its own ink.** The brand hues are printed in either mode, so a
status card wash, a solid status fill, a subtle background and a pill are light whatever
the host is drawing. Text and dividers inside one take the fixed column — inherit there
and the host's dark-mode white lands on a light wash, which is how a card goes blank. A
status glyph takes no value from this table: its colour is its role's `Icon`.

| Role | Take from the host | Inside a fixed fill |
|---|---|---|
| Primary text, axis labels | its body text colour | `#000` |
| Secondary text, axis line and ticks | its muted text colour | `#757575` |
| Disabled text | its disabled text colour | `#9e9e9e` |
| Divider, gridline, track, tooltip stroke | its divider colour | `#e0e0e0` |
| Surface | its card or panel background | the fill itself |
| Page background | its page background | — |
| Card border | its border colour | the role's subtle border |
| Plot band | its body text colour at 5% opacity | — |

**Accent `#ff4713`** and **Link `#cb2600`**, and every value in the three tables below,
are brand identity: printed as given in either mode. Accent is interactive emphasis only
— an active legend item, a selected state — never a data series, which always takes a
categorical slot.

Text always wears an ink token, never a series colour. Grid and axis are recessive.

## Categorical — identity

Thirteen slots, fixed order, assigned from 1, never cycled. Colour follows the entity,
never its rank, and a capped list folds its remainder into Other. Every slot carries a
direct label on the mark or its legend entry; slots 2, 3 and 5 share hues with the status
roles, so a categorical mark never carries a status icon or status words.

**Comparison series** take slots in source order: the reference on slot 1, the subject on
slot 2. A lone series takes slot 1.

| Slot | Value | | Slot | Value |
|---|---|---|---|---|
| 1 | `#8f63bc` | | 8 | `#00a384` |
| 2 | `#008b5c` | | 9 | `#00a6f4` |
| 3 | `#ffce00` | | 10 | `#bf00b6` |
| 4 | `#fc9bb3` | | 11 | `#ff6d00` |
| 5 | `#cc0018` | | 12 | `#5d8e38` |
| 6 | `#6556ff` | | 13 | `#693aa0` |
| 7 | `#e74065` | | Other | `#616161` |

## Status — state

Always an icon **and** words; never colour alone. Words on a solid status fill take `#fff`,
except Warning's `#000`; elsewhere the role's `Text` and `Icon` below. Neutral means no
state was determined: not a fifth severity, never Good.

**Glyphs**: state `●` · attention `⚑` · pass `✓` · not assessed `—`. A status naming a
state takes `●`; the other three report a verdict, so they belong to the slot carrying
one. **The block declares which glyph its status takes**, in the table mapping the status.

**These five hues carry the verdict**, so they hold in either host mode and never adapt to
one. A card wash darkened to suit a dark host is a different verdict.

| Role | Fill | Text | Icon | Subtle background | Card wash | Subtle border |
|---|---|---|---|---|---|---|
| Good | `#008b5c` | `#008b5c` | `#008b5c` | `#e5f5ed` | `#f2faf6` | `#008b5c` |
| Warning | `#ffce00` | `#000` | `#ffc300` | `#ffedb0` | `#fff6d7` | `#ffce00` |
| Critical | `#cc0018` | `#d91120` | `#d91120` | `#ffeaed` | `#fff4f6` | `#d91120` |
| Informational | `#007ac9` | `#007ac9` | `#007ac9` | `#efe9f5` | `#f7f4fa` | `#8f63bc` |
| Neutral / unknown | `#616161` | `#313131` | `#313131` | `#e0e0e0` | `#f0f0f0` | `#e0e0e0` |

**A status glyph takes `Icon` whatever it sits on**, so § Ink's fixed-fill column never
reaches one.

## Change direction

| Role | Text | Background | Series |
|---|---|---|---|
| Positive | `#008b5c` | `#e5f5ed` | `#00a6f4` |
| Negative | `#d91120` | `#ffeaed` | `#f92f24` |

`Series` is for a delta sparkline or a single-value change indicator only. A bar, column
or segment takes a categorical slot whatever the sign of its values — a chart whose every
value is negative still takes slot 1.

## Numbers

**Read the arrival form first.** The first row wins whenever it applies: a figure that
arrived formatted is never re-rounded or restyled, even beside one you formatted
differently. Sizing a bar from a formatted figure is fine. Never rank or total from one.

**Rounding is the tool's decision.** Currency arrives rounded to the cent, so re-rounding
prints a figure that disagrees with the response.

| Kind | Format |
|---|---|
| **A figure that arrived carrying its own `%`, currency symbol or thousands separator** | **verbatim, every place it carries.** A string is already a decision someone made |
| A rate or ratio that arrived as a bare fraction | percent, to one decimal place, with `%` |
| Bare currency | thousands-separated, keeping every decimal place that carries a figure and dropping a trailing zero. `2410.48` prints `$2,410.48`, `2000000.0` prints `$2,000,000` |
| A rate difference below 1% | basis points, integer |
| A count, rank or percentile | as-is |
| A date shown to a reader | day, month and year in words, read from the date's own parts — never through a timezone conversion, which moves the day |

- Negatives take a minus sign, never parentheses. A value rounding to zero from below
  prints as zero.
- One precision per block, among the figures whose precision you choose. A currency figure
  keeps the places it arrived with, so two of them in one block can differ.
- Unit named once, in the label.
- A template never appends `%` or a currency symbol: a figure carries its own, whether it
  arrived with one or gained one from the table. A second prints `%%`.
