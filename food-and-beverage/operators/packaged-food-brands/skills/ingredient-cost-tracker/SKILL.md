---
name: ingredient-cost-tracker
description: >
  Builds an ingredient cost tracker for a packaged food brand: the latest US
  wholesale price of each main ingredient from USDA, how it moved over the past
  4, 13 and 52 weeks, and, when the user gives amounts per unit, what those
  moves did to their cost per unit and their margin after ingredients. Uses the
  Orbbit Data connector. Use this whenever a food brand asks how its ingredient
  costs moved, including phrasings like "how have butter and cheese prices
  moved," "are my ingredient costs going up," "what is cheddar costing now vs
  last year," "what did dairy prices do to my margin," or "track my ingredient
  costs." Covers dairy (butter, cheese, cream, milk, milk powders, whey,
  lactose, casein) and chicken by cut.
---

# Ingredient Cost Tracker

Show a food brand how the market price of its main ingredients moved, and what that did to its cost per unit and margin.

The owner already knows what they pay their supplier. What they don't have is the market price that supplier's quote follows. Without it, they can't tell whether a price rise came from the market or from the supplier. This tracker gives them that market line, with dates and a source they can show their team.

## Rules

- **Cite every number.** Each price names its source ("USDA via Orbbit Data") and the week it covers. A price without a date is useless to a buyer.
- **Never invent a value.** Use only prices the connector returned and amounts the user gave. If a week is missing, say "no USDA price that week"; never show it as zero and never fill it in.
- **US wholesale only, for a US cost question.** Use the default series below. Never answer with a retail, foreign or farm series unless the user asks for that market by name.
- **Keep units straight.** Most series are in $/lb. Milk is in $/cwt (per 100 lb): divide by 100 for $/lb. Never mix units in one column.
- **Say what the data cannot do.** These are past prices, not a forecast; never predict next month's price. Labor, packaging and freight are not in the data, so the margin shown is margin after these ingredients only, and the tracker never tells the user to raise or hold their price.

## Before you start

If the Orbbit Data tools (`list_commodities`, `get_commodity_metric_values`, `search_branded_foods`) are not available, stop and tell the user to connect Orbbit Data in their AI app's connector settings first. Never answer from memory or a web search instead; those prices are stale or made up.

## Default series

Use these unless the user names another market. Each is US wholesale and weekly.

| Ingredient | Series (metric name) | Unit | Notes |
|---|---|---|---|
| Butter | `butter_margarine_us_wholesale_weekly_butter_price_usd_per_lb` | $/lb | USDA national average price. Daily Chicago close: `butter_margarine_aa_us_chicago_wholesale_daily_close_price_usd_per_lb` |
| Cheddar cheese (block) | `cheese_block_cheddar_40_lb_us_wholesale_weekly_cheese_40_price_usd_per_lb` | $/lb | 40 lb blocks |
| Cheddar cheese (barrel) | `cheese_other_barrel_cheddar_500_lb_us_chicago_wholesale_daily_close_price_usd_per_lb` | $/lb | Daily; take the last close of each week |
| Nonfat dry milk | `powdered_milk_nonfat_dry_milk_us_wholesale_weekly_nonfat_milk_price_usd_per_lb` | $/lb | |
| Whole milk powder | `powdered_milk_dry_whole_milk_us_wholesale_fob_weekly_price_min_usd_per_lb` and `..._price_max_usd_per_lb` | $/lb | A low and high; show the midpoint and say so |
| Dry whey | `cooking_baking_ingredients_dry_whey_us_wholesale_weekly_whey_price_usd_per_lb` | $/lb | |
| Whey protein concentrate (34%) | `cooking_baking_ingredients_34_pct_extra_whey_protein_concentrate_us_central_west_wholesale_fob_weekly_price_min_usd_per_lb` and `..._price_max_usd_per_lb` | $/lb | Central and West US only; say so |
| Lactose | `cooking_baking_ingredients_lactose_us_central_west_wholesale_fob_weekly_price_min_usd_per_lb` and `..._price_max_usd_per_lb` | $/lb | Central and West US only |
| Casein | `cooking_baking_ingredients_casein_rennet_us_wholesale_fob_weekly_price_min_usd_per_lb` and `..._price_max_usd_per_lb` | $/lb | Use `casein_acid` if the user says acid casein |
| Buttermilk powder | `powdered_milk_buttermilk_powder_us_west_wholesale_fob_weekly_price_min_usd_per_lb` and `..._price_max_usd_per_lb` | $/lb | West US only |
| Cream | `cream_us_central_wholesale_fob_weekly_price_min_usd_per_lb` and `..._price_max_usd_per_lb` | $/lb | Central US |
| Class III milk | `milk_iii_us_central_wholesale_fob_weekly_price_min_usd_per_cwt` and `..._price_max_usd_per_cwt` | $/cwt | Divide by 100 for $/lb |
| Chicken, boneless skinless breast | `chicken_breasts_boneless_skinless_refrigerated_us_wholesale_fob_weekly_wtd_avg_price_usd_per_lb` | $/lb | Other cuts follow the same pattern: `chicken_<cut>_us_wholesale_fob_weekly_wtd_avg_price_usd_per_lb` |
| Whole chicken | `chicken_whole_refrigerated_broiler_fryer_us_wholesale_delivered_weekly_wtd_avg_price_usd_per_lb` | $/lb | |

If an ingredient is not in this table, call `list_commodities` with its category (for example `food_beverages_tobacco/food_items/dairy_products`) and pick a series that is `market_level: wholesale`, region `us` (or the nearest US region, named in the tracker), and `last_observed_on` within the past month. If nothing fits, tell the user that ingredient is not covered. Don't swap in a lookalike.

## Step 1: Ask once for what only the user knows

Ask in a single message. Only the ingredient list is required:

1. **Main ingredients**, by name (for example butter and cheddar).
2. **Optional: pounds of each per unit** they sell (for example 0.25 lb butter per tray).
3. **Optional: selling price per unit** (their wholesale price to the retailer, or the shelf price, whichever they track).

If they skip 2 and 3, build the price section only and say that adding them would show cost per unit and margin.

## Step 2: Fetch a year of prices

Call `get_commodity_metric_values` once with all chosen series, comma-separated, and no dates. It returns the past year of weekly points ending at the newest week. One call costs one request.

## Step 3: Compute

For each ingredient:

- **Latest price**: the newest point and its date.
- **Change vs 4, 13 and 52 weeks ago**: `(latest / then − 1) × 100`, using the point 4, 13 and 52 weeks before the latest. If that exact week is missing, use the nearest earlier week and name its date.
- **52-week low and high**, with their dates.

When amounts are given:

- **Cost per unit** at each of the four dates: `Σ (lb per unit × price that week)`.
- **Margin after ingredients** at each date: `(selling price − cost per unit) / selling price × 100`.

## Step 4: Write the tracker

Follow `reference/output-template.md`. Default to an Excel workbook (`.xlsx`). If the spreadsheet library is missing, install it (`pip install openpyxl`). If files still cannot be written, put the tables in the reply as Markdown and say so in one line.

## Step 5: Self-check before replying

Run every item in `reference/self-check.md`. Fix what fails, then check again. Do not hand over a tracker with a failing item.

## Step 6: Reply

One sentence on the biggest move, then the file. Example: "Butter is down 21.6% from a year ago at $1.37/lb (USDA, week of 2026-10-03), which lifted your margin after ingredients from 78.5% to 82.1%."

## Worked example

User: "Butter and cheddar are our main costs. Each tray uses 0.25 lb butter and 0.5 lb cheddar and sells for $6.00. How have they moved, and what does it do to our margin?"

Fetched (USDA via Orbbit Data, weekly US wholesale):

| Ingredient | 2026-10-03 | vs 4 wk | vs 13 wk | vs 52 wk |
|---|---|---|---|---|
| Butter | $1.3694/lb | −7.0% | −13.3% | −21.6% |
| Cheddar, 40 lb block | $1.4579/lb | −9.6% | −3.8% | −14.4% |

| | 2025-10-04 | 2026-07-04 | 2026-09-05 | 2026-10-03 |
|---|---|---|---|---|
| Ingredient cost per tray | $1.29 | $1.15 | $1.17 | $1.07 |
| Margin after ingredients | 78.5% | 80.8% | 80.4% | 82.1% |

Reply: "Both ingredients are cheaper than a year ago (butter −21.6%, cheddar −14.4%), so your margin after butter and cheddar rose from 78.5% to 82.1%. Labor, packaging and freight are not included."
