---
name: nutrition-scorecard
description: >
  Builds a nutrition scorecard for a packaged food brand: where its product's
  label ranks against rival products in the same category, nutrient by
  nutrient, per serving and per 100 g, using USDA branded food labels from the
  Orbbit Data connector. Use this whenever a brand wants to compare its label
  with competitors, including phrasings like "how does my protein bar stack
  up," "is my sugar high for the category," "compare my label to other
  brands," "which granola bars have more protein than mine," "benchmark my
  nutrition panel," or "make a slide comparing us to competitors."
---

# Nutrition Scorecard

Show a food brand where its label ranks against rival products in its category, so it can see where it leads and where it trails.

Buyers, retailers and shoppers compare labels side by side. A brand usually knows a few competitors by heart, not the whole shelf. This scorecard ranks the brand against every matching product USDA holds, using the same per-100 g basis a buyer would use, so a bigger serving can't make a product look better than it is.

## Rules

- **Cite every number.** Name the source ("USDA FoodData Central via Orbbit Data") and the range of label dates (`published_on`) the comparison covers.
- **Never invent a value.** Use only label values the connector returned and values the user gave. A missing nutrient (`null`) is "not listed", never zero, and that product is left out of that nutrient's ranking.
- **Compare per 100 g first.** Per-serving values depend on serving size, so rank on per 100 g and show per serving beside it. Compute per 100 g as `value / serving_size × 100`, only when `serving_size_unit_normalized` is `g` (or `ml` for drinks; never mix g and ml in one ranking).
- **Drop impossible values.** Skip a value that cannot be real: more grams of a nutrient than the serving weighs, or sodium above 5,000 mg per serving. Some USDA labels carry sodium about 1,000 times too high (for example 65,100 mg on a 30 g bar). Count how many you skipped and say so.
- **Count each product once.** When two results share the same brand owner and description, keep the newest `published_on`.
- **Say what the data cannot do.** Labels carry no prices, sales or market share, so never claim a product sells better, costs less or leads the market. Labels are as filed with USDA and may be out of date; never call a rival's current recipe certain.

## Before you start

If the Orbbit Data tools (`search_branded_foods`, `list_branded_food_categories`) are not available, stop and tell the user to connect Orbbit Data in their AI app's connector settings first. Never answer from memory or a web search instead; those prices are stale or made up.

## Step 1: Ask once for what only the user knows

Ask in a single message:

1. **The product**: what it is and its category (for example "protein bar"), or its UPC barcode if it's already on shelves.
2. **Serving size in grams** (or ml for drinks).
3. **The label values to compare**, at least the nutrients they care about (for example protein 12 g, sugars 9 g).
4. **Optional: named competitors** to highlight.

If they give a UPC, call `search_branded_foods` with `upc` to read their own label from USDA, and confirm the values with them before going on.

## Step 2: Find the rival products

1. Pick the category. Call `list_branded_food_categories` and choose the closest one (for example "Snack, Energy & Granola Bars"). Tell the user which one.
2. Call `search_branded_foods` with that `category` and a `description` word that narrows to true rivals (for example `protein`). Use `limit` 50, because each result carries the full label and larger pages can overflow the conversation. Keep fetching the next page with `cursor` until you have 200 products or there are no more pages, and state how many you compared.
3. For named competitors, call `search_branded_foods` with `brand` to make sure they're included.

## Step 3: Compute

For each nutrient the user asked about:

- Per 100 g for every product, and for the user's product.
- **Rank**: the user's place among all usable products. For nutrients where more is better (protein, fiber), rank highest first. For nutrients where less is better (sugars, added sugar, sodium, saturated fat, calories), rank lowest first.
- **Category median and the top quarter mark** (the 25th or 75th percentile, whichever is the "good" end).
- **Number of products** in that ranking, after skipped and missing values.

## Step 4: Write the scorecard

Follow `reference/output-template.md`. Default to one slide (`.pptx`) plus a table (`.xlsx`). If a library is missing, install it (`pip install python-pptx openpyxl`). If files still cannot be written, put the summary and table in the reply as Markdown and say so in one line.

## Step 5: Self-check before replying

Run every item in `reference/self-check.md`. Fix what fails, then check again. Do not hand over a scorecard with a failing item.

## Step 6: Reply

One sentence per nutrient asked, each with the rank, then the files. Example: "Per 100 g, your bar ranks 156th of 182 on protein (20.0 g vs a median of 28.0 g) and 77th of 182 for low sugar (15.0 g vs a median of 17.3 g)."

## Worked example

User: "My protein bar is 60 g with 12 g protein and 9 g sugar. How does it stack up?"

- Category: Snack, Energy & Granola Bars. Search word: `protein`. 200 results, 181 after removing repeats, all in grams, no impossible values.
- User's bar per 100 g: protein 20.0 g, sugars 15.0 g.
- Category per 100 g: protein median 28.0 g, top quarter 33.3 g or more; sugars median 17.3 g, top quarter 8.3 g or less.

Reply: "Per 100 g, your bar has less protein than most protein bars (20.0 g vs a median of 28.0 g, rank 156 of 182) and a little less sugar than the median (15.0 g vs 17.3 g, rank 77 of 182). Source: USDA FoodData Central via Orbbit Data, labels published 2019-04-01 to 2021-07-29."
