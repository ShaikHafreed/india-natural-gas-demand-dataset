# India Natural Gas Demand Dataset (2023)

A monthly, government-sourced dataset of India's natural gas consumption for calendar year 2023: net domestic production, LNG imports, and total consumption.

**Scope — read before using:**
- **National level** — this is all-India consumption, **not Tamil Nadu-specific**. No official state-wise natural gas consumption data is published in India; only state-wise petroleum liquid-fuel (petrol/diesel) sales are, which is a different thing entirely.
- **Monthly resolution** — 12 rows, one per month. There is no publicly available daily natural gas consumption series for India, unlike the companion [TN electricity demand dataset](https://github.com/ShaikHafreed/tn-electricity-demand-dataset), which does have real daily data.
- **No modeled or synthetic values** — every number here is exactly as published by the source, with no disaggregation, interpolation, or estimation applied.

## What's in it

| File | Description |
|---|---|
| `data/india_natural_gas_consumption_2023.csv` | 12 monthly rows, Jan–Dec 2023, 32 columns |

**Supply-side columns** (production/import reconciliation)
- `Month` — calendar month, `YYYY-MM`
- `Net_Production_MMSCM` — domestic natural gas net production for consumption (gross production minus flaring/loss), in Million Standard Cubic Metres
- `LNG_Import_MMSCM` — imported LNG consumed, in MMSCM
- `Total_Consumption_MMSCM` — `Net_Production_MMSCM + LNG_Import_MMSCM`

**Sector-wise consumption columns** (as reported directly by oil & gas companies to PPAC — Domestic + RLNG/Others combined per sector)
- `Power_MMSCM`, `CityGasDistribution_MMSCM`, `Refinery_MMSCM`, `InternalConsumption_Pipeline_MMSCM`, `Agriculture_TeaPlantation_MMSCM`, `Industrial_MMSCM`, `Manufacturing_MMSCM`, `Other_Misc_MMSCM`, `Fertilizer_MMSCM`, `Petrochemical_MMSCM`, `LPG_Shrinkage_MMSCM`, `SpongeIron_Steel_MMSCM`
- `Sectoral_Total_Check_MMSCM` — sum of all 12 sector columns (verified to match PPAC's own "Total" row for every month, to within rounding)

**A note on the two totals:** `Total_Consumption_MMSCM` (supply-side) and `Sectoral_Total_Check_MMSCM` (demand-side, company-reported) differ by roughly 1–2% month to month (e.g. Jan 2023: 4,822.62 vs. 4,905.72). This is not an error — they come from two different official PPAC reporting methods (production+import reconciliation vs. direct company reporting) that don't perfectly reconcile, as PPAC's own notes acknowledge ("totals may not tally due to rounding off"). Both are published as-is; neither has been adjusted to match the other.

**Calendar / cyclical columns** (computed from the `Month` column — no external data, deterministic)
- `Year`, `Month_Num` (1–12), `Month_Name`
- `Month_Sin`, `Month_Cos` — cyclical encoding of the month (`sin/cos(2π·Month_Num/12)`), so a model sees December and January as adjacent rather than far apart. This is the monthly-resolution analog of `Day_of_Year_Sin/Cos` in the companion electricity dataset.

**Lag / rolling columns** (derived from this dataset's own real `Total_Consumption_MMSCM` values — no external data)
- `Gas_Lag_1M`, `Gas_Lag_2M`, `Gas_Lag_3M` — total consumption 1/2/3 months prior. Blank where there's no prior month in this 12-month series (e.g. `Gas_Lag_3M` is blank for Jan–Mar).
- `Gas_RollingMean_3M` — mean of the current and prior 2 months' total consumption. Blank for Jan–Feb (fewer than 3 months available). This is the analog of `Energy_RollingMean_7D/14D/28D` in the electricity dataset.

All figures are provisional, as published by the source. The calendar and lag/rolling columns are exact arithmetic on those published figures — nothing estimated or modeled.

**Holiday calendar column** (real — Government of India official source)
- `National_Holiday_Count` — number of gazetted Central Government holidays falling in that month, per the DoPT (Department of Personnel & Training) Office Memorandum dated 16.06.2022, the official list of 17 gazetted holidays for Central Government offices in 2023. Cross-checked against a second independent published list; the two agree except for a well-known India-specific quirk (Janmashtami has two traditional dates — Smarta and Vaishnava — and DoPT's official list uses only the Vaishnava date), which is why this dataset follows the official DoPT count rather than the larger unofficial list.

**Weather columns** (real — India Meteorological Department, IMD)
- `All_India_Mean_Temp_C` — all-India area-averaged mean temperature for the month, °C (normal base 1981–2010), from IMD's monthly climate summary press releases.
- `All_India_Rainfall_mm` — all-India area-weighted rainfall for the month, mm (normal/LPA base 1971–2020; June–September figures are the finalized values from IMD's Annual Climate Summary 2023 monsoon table, which differ very slightly from the provisional monthly press-release figures — the same kind of two-report discrepancy noted above for gas consumption).
- `Weather_Data_Status` — honest status per row: 9 months are fully verified against an official IMD monthly climate summary; September has verified rainfall but no mean-temperature figure yet located; **November and December are blank** — IMD's specific monthly summary documents for these two months could not be located despite an extensive search, so the cells are left empty rather than estimated. This mirrors the one documented missing value in the companion electricity dataset: a gap is flagged, not filled.

**Extreme weather columns** (real, documented events — no synthetic flagging)
- `Extreme_Weather_Flag` (0/1) and `Extreme_Weather_Type` — whether a major, independently documented extreme weather event affected India that month, and which one (named cyclones with landfall dates, the record Delhi/Yamuna flood, the Sikkim glacial lake outburst flood, and IMD's own "warmest month on record" findings for Aug/Sep/Nov 2023). Sourced from IMD press materials, Wikipedia's cyclone pages (cross-checked against news coverage), and Down To Earth's climate reporting. January and February 2023 have no flagged event because no major nationally-documented extreme weather event was found for those two months specifically — this is a deliberate absence, not an oversight.

## How it was built

Calendar-year 2023 spans two Indian fiscal years (April–March), so this dataset merges two real PPAC source tables, split and recombined by calendar month:
- January–March 2023 from PPAC's "Month wise 2022-23" consumption report, and "NG-H-SC FY 2022-23" sectoral consumption report
- April–December 2023 from PPAC's "Month wise 2023-24" consumption report, and "NG-H-SC FY 2023-24" sectoral consumption report

Every value was independently verified before publishing: Net Production + LNG Import = Total Consumption for all 12 months, and the sum of all 12 sector columns matches PPAC's own reported monthly Total row exactly, for every month. No values were estimated, interpolated, or formula-generated — everything here is as published by PPAC.

The calendar and lag/rolling columns (`Year` through `Gas_RollingMean_3M`) were added as a second pass, mirroring the feature set used in the companion electricity dataset: only the columns with a genuine real-data or deterministic-arithmetic basis at this dataset's national/monthly scope were carried over (see column dictionary above for the mapping). Columns that don't have a real-data analog at this scope (e.g. Tamil Nadu festival calendar, daily weather) were left out rather than approximated — see "Related work" below for what's planned next.

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
