# THE PREMIUM MAP

**The second mortgage nobody signed.**

### ▶ [Launch the live instrument →](https://ashbybrewer.github.io/premium-map/)

Homeowners insurance is quietly becoming a second housing payment. This is a national instrument on that repricing: county-level premium escalation, non-renewals, insured losses, federal disaster history, and insurer withdrawals — 3,142 counties, 2018–2025ᴱ, in one interactive. Journalists have covered this story state by state; this is the national view, built entirely from public records, with every figure tagged **`SOURCED`** or **`MODELED`**.

**Demo:** open `index.html`. No build step, no server required (all data ships as local JS modules; a static host like GitHub Pages works out of the box).

---

## What it shows

**Instrument I — the county choropleth.** Six lenses over 3,100+ counties:

| Lens | What it is | Tag |
|---|---|---|
| Premium / policy | Average annual premium, scrubbable 2018→2022, projected 2023→2025 | `SOURCED` → `MODELED` tail |
| Escalation ’18→’22 | Percent change in premium per policy | `SOURCED` |
| Non-renewal rate | Share of policies insurers declined to renew, 2018→2023 | `SOURCED` |
| Paid loss ratio | Losses paid ÷ written premium — above 1.0× the insurer lost money | `SOURCED` |
| Claim frequency | Paid claims per policy in force — where weather lands on rooftops | `SOURCED` |
| Repricing Index | 0–100 composite of escalation, non-renewal, loss ratio, disasters | `MODELED` |

**Instrument II — the repricing frontier.** Every county plotted: loss ratio vs. premium escalation, sized by policies in force, colored by non-renewal rate. The upper-right quadrant is where insurance is repricing America.

**The ledgers.** Below the map: NOAA's billion-dollar disaster ledger 1980–2024 (the bars stop in 2024 because NCEI retired the product in May 2025 — the counting stopped, not the weather), and a curated, source-linked timeline of 21 market shocks 2021–2026: nine Florida insolvencies, Louisiana's post-Ida cascade, the California exits, the LA-fire emergency rates, and the first thaw signals.

**County dossiers.** Click any county: premium trajectory with modeled tail, non-renewal bars vs. the national reference, loss-ratio breakeven chart, FEMA declaration history, national percentile meters, and a Repricing Index dial. Pin one county to compare against another.

## Data lineage

| Source | Vintage | Feeds |
|---|---|---|
| [U.S. Treasury FIO / NAIC — PCMI Supporting Underlying Metrics](https://home.treasury.gov/news/press-releases/jy2791) | Jan 2025 release · 2018–2022 | ZIP-level premiums/policy, non-renewals, paid loss ratio, claim frequency & severity (~131,900 ZIP-year rows, ~80% of HO-3/HO-5 premium nationally) |
| [U.S. Senate Budget Committee — county non-renewal data](https://www.budget.senate.gov/chairman/newsroom/press/new-data-reveal-climate-change-driven-insurance-crisis-is-spreading) | Dec 2024 release · 2018–2023 | County non-renewal rates & policies in force (insurers ≈65% of the market) |
| [NOAA / NCEI — Billion-Dollar Weather & Climate Disasters](https://www.ncei.noaa.gov/access/billions/) | Final release · 1980–2024 | National disaster-cost ledger (CPI-adjusted) |
| [OpenFEMA — Disaster Declarations Summaries v2](https://www.fema.gov/openfema-data-page/disaster-declarations-summaries-v2) | Pulled Jul 2026 · 2000– | County-level declarations by hazard type (~50,000 rows) |
| [Census 2020 ZCTA↔county relationship file](https://www.census.gov/geographies/reference-files/time-series/geo/relationship-files.html) | 2020 | ZIP→county assignment (largest land-area overlap) |
| [S&P Global Market Intelligence rate filings](https://www.spglobal.com/market-intelligence/en/news-insights/articles/2025/1/us-homeowners-rates-rise-by-double-digits-for-2nd-straight-year-in-2024-87061085) (via press) | 2023–2025 | `MODELED` nowcast anchors: +12.7% / +10.4% / +6.0% |
| [us-atlas](https://github.com/topojson/us-atlas) | counties-10m | Topology (pre-projected AlbersUSA) |
| Curated exits timeline | 2021–2026 | 21 events, each with its own linked source (FIGA, Insurance Journal, CalMatters, LDI, etc.) |

## The SOURCED / MODELED doctrine

Every number on the instrument is one of two things:

- **`SOURCED`** — comes straight from a public record above, transformed only by aggregation.
- **`MODELED`** — an estimate we constructed. The method is stated where the number appears, and modeled values never mix silently with sourced ones (modeled map years render with a violet `ᴹᴼᴰ` mark; the modeled premium tail draws dashed).

The two `MODELED` artifacts here:

1. **2023–25 premium nowcast** — county 2022 premium × national S&P approved-rate anchors. A uniform factor; real county-level filings vary widely around it.
2. **Repricing Index** — `0.35·pct(escalation) + 0.25·pct(non-renewal ’23) + 0.20·pct(loss ratio) + 0.20·pct(declarations since ’21)`. An editorial gauge of repricing pressure, not an actuarial product. Its top five counties (Lake, Trinity, Mariposa, El Dorado, Plumas — all CA) match the geography of the 2024 State Farm non-renewals it knew nothing about, which is the kind of sanity check we like.

## Reproduce

```bash
bash pipeline/fetch.sh     # pulls all raw public data into raw/
python3 pipeline/build.py  # emits data/counties.js, data/national.js, data/topology.js + processed CSV
open index.html
```

Requirements: `python3` + `openpyxl`. The site itself is dependency-free (D3 v7 and topojson-client are vendored in `vendor/`).

`data/processed/county_metrics.csv` is the flat export — every county, every metric, `MODELED` columns labeled in the header — if you just want the data.

## Honest limitations

- **ZIP ≈ ZCTA.** FIO reports USPS ZIPs; the crosswalk uses Census ZCTAs. Close, not identical.
- **Weighting.** Treasury publishes policy-count *deciles*, not counts, so county figures are decile-weighted ZIP means. Treasury notes its public subset (ZIPs with ≥10 insurers & ≥50 policies) drops ~22% of ZIPs and won't exactly match the FIO report's aggregates; ours inherit that caveat and run composition-smoothed relative to policy-weighted national figures.
- **Premiums ≠ rates.** Written premium ÷ policies in force reflects coverage growth as well as price.
- **The floor, not the ceiling.** FAIR plans, Citizens, and E&S carriers — the markets that absorb homeowners when private carriers leave — are outside the FIO data entirely. Where this map shows stress, the uncounted stress is higher.
- One row in ~18,900 of the Senate file references "ARKANSAS County, TX," which does not exist. It is lovingly preserved in the unmatched log.

## Design

Dark-instrument idiom: IBM Plex Mono for data, Space Grotesk for display, one amber. Hash-addressable state (`#l=nr&y=2023&s=FL`) so any view is a shareable URL. Keyboard: `/` search, `space` play, `←→` step years, `esc` deselect.

---

Built by **William Brewer** — public data, labeled honestly. Code: MIT (see LICENSE). Data remains the property of the cited public sources.
