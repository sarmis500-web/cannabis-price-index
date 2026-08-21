# WeedBuddy Cannabis Price Index — Open Dataset

**What legal cannabis actually costs right now, measured from live dispensary menus.**

[WeedBuddy](https://weedbuddylink.com) tracks live menu prices at **1,135 licensed recreational
dispensaries across seven states — Michigan, Arizona, Ohio, Colorado, Maryland, California and
Nevada** (907,152 live listings in the current snapshot). This repository publishes that price
data as free, citable dated snapshots — median flower prices by state and by city.

Nobody else publishes this openly: existing cannabis price benchmarks are paywalled, and
state-reported averages lag months behind the shelf. These figures come straight from what
dispensaries are charging **today**.

Interactive version, always current: **<https://weedbuddylink.com/price-index/>**

> **Citing this?** Free to use under CC BY 4.0. Please credit:
> **Source: [WeedBuddy Cannabis Price Index](https://weedbuddylink.com/price-index/)** —
> a link to <https://weedbuddylink.com/price-index/> satisfies attribution.
> The live page is always current and carries every state; this repo is a dated archive.

<!-- LATEST:BEGIN -->
**Latest snapshot: August 2026** (data date 2026-08-20)

| State | Median eighth (3.5g) | Median ounce (28g) | Cheapest ounce | Dispensaries tracked |
|---|---|---|---|---|
| Maryland | $37.00 | $190.00 | $78.00 | 83 |
| Ohio | $36.00 | $147.35 | $133.00 | 76 |
| California | $33.75 | $79.99 | $12.99 | 151 |
| Arizona | $30.00 | $120.00 | $49.99 | 140 |
| Nevada | $30.00 | $120.00 | $54.00 | 77 |
| Colorado | $23.89 | $106.82 | $16.73 | 168 |
| Michigan | $20.00 | $99.00 | $6.99 | 440 |
<!-- LATEST:END -->

## Headline finding

Cannabis prices vary dramatically between neighboring legal states — an eighth of flower in
**Ohio typically costs close to 2× what it costs in Michigan**, a two-hour drive away — $36.00
against $20.00 in the current snapshot. The dated snapshots in `data/` let you track how (and
whether) that gap closes.

## What's in the data

```
data/
  YYYY-MM/                 one snapshot per month (kept forever — builds a time series)
    state_medians.csv      per-state medians + cheapest verified ounce
    city_medians.csv       per-city median eighth price
    ounce_grades.csv       median ounce split by grade (flower / shake / smalls)
  latest/                  stable-URL copy of the newest snapshot (safe to deep-link)
```

> **Scope.** This dataset is deliberately **aggregate only** — state and city medians.
> It contains no per-store or per-product prices, and names no individual dispensary.

### `state_medians.csv`

| column | meaning |
|---|---|
| `date` | snapshot date (ISO) |
| `state` | 2-letter state code |
| `state_name` | full state name |
| `median_eighth_usd` | median price of a 3.5 g eighth of flower |
| `median_ounce_usd` | median price of a 28 g ounce of **whole flower** (blank if under 20 listings) |
| `cheapest_ounce_usd` | lowest plausible **whole-flower** ounce price live on a menu at snapshot time |
| `cheapest_ounce_city` | the city it was found in |
| `dispensaries_tracked` | licensed dispensaries with a live menu in the snapshot |
| `flower_price_sample` | number of eighth prices behind the median |
| `live_listings` | total live product listings tracked in the state |

### `ounce_grades.csv`

Not every "ounce" is the same product. An ounce of shake and an ounce of whole bud are
different goods at very different prices, so averaging them into one figure describes
nothing real. This file reports each grade separately, classified by **the grade the
dispensary itself publishes** — not inferred from price.

| column | meaning |
|---|---|
| `date` | snapshot date (ISO) |
| `state` | 2-letter state code |
| `grade` | `whole_flower`, `shake_trim`, `smalls_popcorn` or `infused_bud` |
| `median_ounce_usd` | median ounce price within that grade |
| `price_sample` | number of prices behind the median (min 20, or the grade is omitted) |

`whole_flower` here is the same figure as `median_ounce_usd` in `state_medians.csv`.

### `city_medians.csv`

| column | meaning |
|---|---|
| `date` | snapshot date (ISO) |
| `state` | 2-letter state code |
| `city` | city name |
| `median_eighth_usd` | median eighth price across that city's listings |
| `price_sample` | number of prices behind the median (min 15, or the city is omitted) |

## Methodology

- **Source:** live product menus of licensed recreational dispensaries, collected continuously
  by WeedBuddy. Prices are what a customer would pay today, before tax.
- **Medians, not averages** — so a handful of loss-leader promos or premium outliers can't skew
  a figure.
- **Plausibility bounds:** eighths outside $8–$120 and ounces outside $5–$500 are excluded as
  data errors. The lower ounce bound is deliberately permissive — a genuinely advertised $5 ounce
  is a real price a shopper can go and pay, so it is reported under its grade rather than deleted.
- **Ounce figures are whole flower.** Shake, trim, smalls and infused bud are different products at
  very different prices; averaging them together describes nothing real. Each grade is reported
  separately in `ounce_grades.csv`, classified by **the grade the dispensary itself publishes** —
  never inferred from price.
- **Minimum samples:** a state needs ≥50 eighth prices to be reported; a city needs ≥15; ounce
  medians need ≥20 listings. Anything thinner is omitted rather than reported noisily.
- **Flower only.** Vapes, edibles and concentrates are priced per-unit rather than by weight and
  aren't comparable across states the way flower is.
## Methodology changes

We publish changes to how these figures are calculated, with dates, so a number that moves for a
methodological reason can be told apart from one that moves because the market moved.

- **2026-07-29 — ounces are now split by grade.** Previously any ounce priced under $30 was dropped
  as implausible. That single threshold was doing two incompatible jobs: rejecting bad data *and*
  deciding what counted as an ounce. In Michigan it silently excluded 4,597 real listings (13% of
  all ounce prices), most of them shake and trim that dispensaries genuinely sell at $19–$25.
  Ounce medians are now **whole flower only**, with the other grades reported separately in the new
  `ounce_grades.csv`, and the lower bound relaxed to $5. Effect on published ounce medians:
  **MI $100.00 → $99.00 · OH $164.22 → $147.35 · AZ $160.00 → $180.00 · CO $106.50 → $110.00.**
  Arizona and Colorado *rose* because cheap shake had been pulling their flower medians down.
  Eighth prices and all city figures are unaffected by this change.

- These CSVs are generated by the **same code** that renders the live
  [Price Index](https://weedbuddylink.com/price-index/). The site regenerates on every deploy and is
  always current; this repository is a **dated snapshot**, so between publications the live page can
  be newer. Each file carries its own `date` column — cite that date, and the two will always agree
  for it. Superseded snapshots are kept (see *Update cadence*) so any figure we have ever published
  stays checkable.

## Update cadence

`data/latest/` holds the most recent snapshot. Every published snapshot is also kept under its
own dated folder — `data/2026-08-01/`, `data/2026-08/` — so a figure quoted in an article remains
verifiable after the market has moved on. Prices genuinely change: promotions cycle weekly, and a
"cheapest ounce" is a single price that can move a long way in a fortnight. Always cite the `date`
column with the number.

Need mid-month or custom figures (a specific city, a longer time window, a state not yet listed)?
Open an issue — happy to pull them.

## License & attribution

Data is published under **[CC BY 4.0](https://creativecommons.org/licenses/by/4.0/)** — free to
use, share and republish (including commercially) **with attribution**:

> Source: WeedBuddy Cannabis Price Index — weedbuddylink.com

For journalists: a link to <https://weedbuddylink.com/price-index/> satisfies attribution.

## About

WeedBuddy is a free cannabis price-comparison app — it searches every dispensary at once and
ranks results by actual price, with real lab (COA) terpene data. No pay-to-play placement.
Built and run independently in Michigan. **<https://weedbuddylink.com>**
