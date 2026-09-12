# BlackRock Advisor Center Skills

Advisor Center Skills contains skill files for Advisor Center 360° MCP workflows. The skills help AI environments organize returned portfolio data, analytics, links, and routes for advisor-facing review.

## Included skills

| Skill | Purpose |
|---|---|
| `ac360-capability-router` | Routes web-only or unsupported requests to the right Advisor Center 360° path. |
| `guided-benchmark-selection` | Benchmark selection across BlackRock models, indices, and saved portfolios. |
| `guided-portfolio-builder` | Portfolio creation, import, clone, variation, and write confirmation flows. |
| `portfolio-observations-and-opportunities` | Opportunity checks, fund-health detail, Funds to Explore, and approved drilldowns. |
| `wealth-projections` | Modeled performance, projected wealth, methodology, fees, horizons, and unsupported configurations. |
| `portfolio-review` | First-pass portfolio review: snapshot, scenarios, opportunities, fund health, Funds to Explore, links, and follow-ups. |

Each skill folder contains a `SKILL.md` file plus rendering and design-token references.

## Rendering posture

- Use rich inline cards, charts, and components first.
- Use markdown tables only when rich cards or charts are unavailable.
- Render opportunities as cards, not primary markdown tables.
- Render fund health as card grids with a Funds to Explore area on every holding.
- Put Advisor Center, comparison, methodology, fund, product, and route links near the block they support.
- Keep output concise, data-grounded, and advisor-facing.

## Requirements

- Access to Advisor Center 360°
- Access to the Advisor Center MCP server
- An AI environment that can load skill markdown or plugin packages

## Scope and review

This repository contains the Advisor Center skill layer. Output quality and presentation depend on the underlying MCP data and the host AI environment. Users should review generated outputs before use.

## License and notices

Licensed under the Apache License, Version 2.0. See LICENSE.

See NOTICE for BlackRock trademark, limitation-of-liability, and no-affiliation terms.