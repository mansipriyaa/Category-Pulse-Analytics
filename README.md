# Category Pulse Analytics

An end-to-end analytics layer that gives leadership a single "glance and decide" view of
how every product category is performing — **which categories and SKUs to scale, which to
fix, and where paid spend is actually working across channels.**

## Problem

A D2C brand selling across many categories (lunchboxes, bottles, insulated jars, bags)
and many sales channels (own D2C store, plus multiple marketplaces and quick-commerce)
had no single place to see category health. Revenue lived in one system, SKU-level sales
in another, ad spend split across ad platforms, and category definitions in a master
sheet — so no one could answer, in one view: which categories drive revenue, which
individual SKUs are worth scaling vs. rationalising, how each product's sales split across
channels, and whether paid spend is efficient per channel.

Two problems made this non-trivial. First, a SKU that stops selling because it's *out of
stock* looks identical to a genuinely weak SKU unless you account for stock. Second,
"marketing efficiency" means different things depending on the denominator, and mixing
them produces misleading numbers.

## Approach

- **Modeled a category → product → SKU hierarchy** from a master SKU sheet, so every SKU
  rolls up to exactly one category, and built an expandable scorecard that drills from
  category down to product and then to individual variant.
- **Built an OOS-aware SKU decision layer** — a SKU's revenue is measured *per active
  day*, where any gap of ≥7 consecutive no-sale days is treated as out-of-stock and those
  days are excluded from the denominator. This stops stocked-out SKUs from being mistaken
  for weak ones. Each SKU is also scored on variability (coefficient of variation of daily
  revenue) so steady performers are separated from volatile spikes.
- **Plotted a scale-vs-fix decision quadrant** — revenue-per-active-day against
  variability — so a category owner can instantly see which SKUs to scale/add depth to,
  which are stable-but-low, and which to rationalise.
- **Reconciled channel mix** — computed each category's and each product's revenue split
  across all sales channels (D2C, marketplaces, quick-commerce, B2B), including a
  per-product 100%-stacked view, with B2B identified via order-code convention rather than
  a channel field.
- **Separated two efficiency metrics deliberately** — MER (total category revenue ÷ ad
  spend) and Blended ROAS (ad-attributed revenue ÷ ad spend) are computed on different
  denominators and shown side by side, plus ROAS by channel and category market-share
  benchmarks, so paid decisions rest on the right number.

## Tech

- **SQL (BigQuery)** — category hierarchy modeling, stock-aware active-day/OOS logic,
  coefficient-of-variation scoring, channel reconciliation, and the MER-vs-ROAS metric split
- **Python** — generates the polished dashboard sections as HTML/CSS, alongside Hex-native
  chart cells for the decision scatter
- **Hex** — multi-tab dashboard hosting the Python/HTML cells and native charts
- Daily SKU revenue and stock-availability tables, a master SKU/category sheet, and
  Meta + Google ad-spend sources

## What you'd see

A multi-tab category dashboard covering:

- **SKU decision quadrant** — every SKU plotted on revenue-per-active-day vs. variability,
  colour-coded into scale / stable / rationalise zones, with out-of-stock days already
  stripped out so the signal is real demand, not stock gaps
- **Category scorecard** — an expandable table drilling category → product → variant, with
  revenue, SKU counts, and revenue-per-SKU at each level
- **Channel & spend mix** — overall category channel split plus per-product 100%-stacked
  channel breakdowns and revenue-per-unit by product
- **Ad efficiency & market share** — ad spend, MER, and blended ROAS for the selected
  period, ROAS by channel, and category-level market-share benchmarks across marketplaces
