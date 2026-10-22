# India Natural Gas Demand Dataset (2023)

A monthly, government-sourced dataset of India's natural gas consumption for calendar year 2023: net domestic production, LNG imports, and total consumption.

**Scope — read before using:**
- **National level** — this is all-India consumption, **not Tamil Nadu-specific**. No official state-wise natural gas consumption data is published in India; only state-wise petroleum liquid-fuel (petrol/diesel) sales are, which is a different thing entirely.
- **Monthly resolution** — 12 rows, one per month. There is no publicly available daily natural gas consumption series for India, unlike the companion [TN electricity demand dataset](https://github.com/ShaikHafreed/tn-electricity-demand-dataset), which does have real daily data.
- **No modeled or synthetic values** — every number here is exactly as published by the source, with no disaggregation, interpolation, or estimation applied.

## What's in it

| File | Description |
|---|---|
| `data/india_natural_gas_consumption_2023.csv` | 12 monthly rows, Jan–Dec 2023 |

**Columns**
- `Month` — calendar month, `YYYY-MM`
- `Net_Production_MMSCM` — domestic natural gas net production for consumption (gross production minus flaring/loss), in Million Standard Cubic Metres
- `LNG_Import_MMSCM` — imported LNG consumed, in MMSCM
- `Total_Consumption_MMSCM` — `Net_Production_MMSCM + LNG_Import_MMSCM`

All figures are provisional, as published by the source (totals may not tally exactly due to rounding).

## How it was built

Calendar-year 2023 spans two Indian fiscal years (April–March), so this dataset combines:
- January–March 2023 from PPAC's "Month wise 2022-23" report (FY 2022-23)
- April–December 2023 from PPAC's "Month wise 2023-24" report (FY 2023-24)

Every value was independently cross-checked (Net Production + LNG Import = Total Consumption, for all 12 months) before publishing.

## Source and license

**Source:** Petroleum Planning & Analysis Cell (PPAC), Ministry of Petroleum & Natural Gas, Government of India — Natural Gas Consumption historical report: [ppac.gov.in/natural-gas/consumption](https://ppac.gov.in/natural-gas/consumption)

This is Indian government open data. This repository's packaging (the merge across fiscal years, documentation, and verification) is released under **CC BY 4.0** — see `LICENSE`. Please credit PPAC as the original data source in any reuse.

## Citation

```
Shaik Hafreed (2026). India Natural Gas Demand Dataset (2023).
GitHub: https://github.com/ShaikHafreed/india-natural-gas-demand-dataset

Original source: Petroleum Planning & Analysis Cell (PPAC),
Ministry of Petroleum & Natural Gas, Government of India.
https://ppac.gov.in/natural-gas/consumption
```

## Related

Companion dataset: [TN Electricity Demand Dataset (2023)](https://github.com/ShaikHafreed/tn-electricity-demand-dataset) — daily, Tamil Nadu-specific. Built as part of a generic multi-utility demand-forecasting pipeline on the [MANGO](https://mango.wcte.iitm.ac.in) analytics platform (IIT Madras WCTE).
