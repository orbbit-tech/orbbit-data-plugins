# Nutrition Scorecard: Output Template

Write two files named after the product, `nutrition-scorecard-<product>.pptx` and `nutrition-scorecard-<product>.xlsx`. If files can't be written, put the summary and the table in the reply as Markdown.

## The slide (one slide, 16:9)

- **Title**: the finding in one line, with a number. Example: `Our bar trails on protein, sits mid-pack on sugar`.
- **Subtitle**: `<category>, <N> products, per 100 g`.
- **One horizontal bar chart per nutrient asked** (at most four on the slide). Each chart shows:
  - the user's product (the only bar in the accent colour)
  - the category median
  - the top quarter mark
  - any named competitors
  
  Label each bar with its value and unit. Label the axis with the nutrient and `g per 100 g` (or `mg per 100 g`).
- **Rank line under each chart**: `Rank 156 of 182 (more is better)`.
- **Footer**: the source line below.

## The table (`.xlsx`)

Sheet `Products`, one row per usable product, the user's product first and highlighted:

| Brand owner | Product | UPC | Serving (g) | `<nutrient>` per serving | `<nutrient>` per 100 g | ... | Label published |
|---|---|---|---|---|---|---|---|

Sort by the first nutrient the user asked about, best first. Leave a cell empty where the label doesn't list the nutrient.

Sheet `Summary`, one row per nutrient:

| Nutrient | Better when | Yours per 100 g | Median | Top quarter mark | Rank | Products ranked | Values skipped |
|---|---|---|---|---|---|---|---|

## Source line (slide footer and both sheets)

```
Source: USDA FoodData Central via Orbbit Data. <N> products in "<category>", labels published <earliest> to <latest>.
Labels as filed with USDA; no prices, sales or market share.
```
