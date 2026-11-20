# Inventory Forecasting and Newsvendor Stocking in R: From Demand Distributions to Order Quantities

A forecast is only useful if it changes a decision. This project forecasts weekly demand for 40 SKUs as a full probability distribution and turns it into stocking decisions with the **newsvendor rule**, which balances the cost of lost sales against the cost of excess stock. Because the demand is simulated from a known law, every policy is priced against the true optimum.

## Question

How much should each SKU be stocked each week? Do probabilistic forecasts and cost-based quantiles beat a rule of thumb, and what goes wrong if every SKU is held to the same "95% service level"?

## Key findings

*(Fill in after running the scripts. Suggested: expected cost per SKU-week for each policy and the gap to the oracle; the saving of the regression model over the rule of thumb with its bootstrap interval; fill rate versus cost for the flat 95% policy; the best flat service level against the per-SKU quantile; which demand class benefits most.)*

## What is in it

- **Simulated retail demand** for 40 SKUs over 156 weeks: negative-binomial counts with trend, seasonality and promotion lift, from slow movers to fast sellers, with SKU-specific margins and holding costs. Messy raw data: duplicates, missing and negative units, inconsistent promo flags and SKU labels.
- **Cleaning** with a log of every fix.
- **Demand pattern classification** (smooth, erratic, intermittent, lumpy) using ADI and CV².
- **Forecasting models**: Poisson regression with trend, seasonal harmonics and promotion plus a negative-binomial spread, Croston's method with the Syntetos-Boylan correction, and an 8-week moving rule of thumb.
- **Newsvendor decisions**: order quantity at the critical ratio cu / (cu + co), derived in the report.
- **Evaluation**: pinball loss, service level, fill rate, stock-out and overstock cost, and *exact* expected cost under the true demand law, against an oracle.
- **Flat service level versus cost-based quantiles**: a sweep showing why one target for all SKUs is expensive.
- **Bootstrap** over SKUs for the saving versus the rule of thumb.
- **A Quarto report** with the method, charts and limitations.

## Tools

R, dplyr, tidyr, tibble, ggplot2, knitr, Quarto. Models are written with base R (`glm.fit`, `qnbinom`); no forecasting packages needed.

## Reproduce

```r
install.packages(c("dplyr", "tidyr", "tibble", "ggplot2", "knitr"))
source("R/00_simulate_data.R")
source("R/01_clean_explore.R")
source("R/02_forecast_and_order.R")
source("R/03_decision_analysis.R")
quarto::quarto_render("reports/report.qmd")
```

Run from the project root (open `inventory-newsvendor-forecasting.Rproj`). The seeds are fixed, so results are reproducible.

## Project structure

```
inventory-newsvendor-forecasting/
├── R/
│   ├── inventory_functions.R     # simulator, cleaning, forecasting, newsvendor, cost evaluation
│   ├── 00_simulate_data.R
│   ├── 01_clean_explore.R
│   ├── 02_forecast_and_order.R
│   └── 03_decision_analysis.R
├── data/
│   ├── raw/                      # weekly_sales.csv, sku_economics.csv (generated)
│   └── clean/                    # .rds results (generated)
├── reports/report.qmd
├── inventory-newsvendor-forecasting.Rproj
└── README.md
```

## Limitations

- Demand is simulated from the family the model assumes; real demand has heavier tails and structural breaks.
- Sales stand in for demand; stock-outs censor sales in real data.
- Single-period decisions with fixed costs; no lead times, minimum order sizes, shelf life or shared capacity.
- Models are fitted once and not refreshed; the promotion calendar is assumed known.
- 26 test weeks and 40 SKUs give noisy estimates of small differences.
