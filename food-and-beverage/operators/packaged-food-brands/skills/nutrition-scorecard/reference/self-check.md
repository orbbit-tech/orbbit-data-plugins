# Nutrition Scorecard: Self-Check

Run every item before replying. Each one passes or fails. Fix any failure, then run the whole list again.

## Data

- [ ] Every rival value came from this run's `search_branded_foods` results. None were typed in from memory.
- [ ] The user's values are the ones they gave (or the USDA label they confirmed), not assumed ones.
- [ ] Every product in the ranking is in the stated category and matches the search word, so it is a true rival.
- [ ] The search fetched 200 products, or every product it returned if there were fewer, and the count compared is stated.
- [ ] Each product appears once (same brand owner and description kept once, newest label).
- [ ] No missing nutrient shows as `0`. Products missing a nutrient are left out of that nutrient's ranking.
- [ ] Impossible values were skipped, and the count of skipped values is shown.

## Math

- [ ] Per 100 g is `value / serving_size × 100`, and only products with servings in grams (or only in ml) are ranked together.
- [ ] Rank direction is right: more is better for protein and fiber; less is better for sugars, added sugar, sodium, saturated fat and calories.
- [ ] The rank's "of N" equals the number of products actually in that ranking, including the user's.
- [ ] Spot-check one rival by hand: its per-100 g value matches its label and serving size.

## Claims

- [ ] The source line names USDA FoodData Central, Orbbit Data, the category, the product count and the label date range.
- [ ] Nothing claims sales, price, market share or popularity.
- [ ] Nothing says a rival's current recipe is certain; labels are "as filed with USDA".

## Slide and reply

- [ ] The slide title states the finding with a number, not a topic.
- [ ] Only the user's bar uses the accent colour, and every chart axis is labeled with nutrient and unit.
- [ ] The reply gives one sentence per nutrient asked, each with its rank, and includes the files.
