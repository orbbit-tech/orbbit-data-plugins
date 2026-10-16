# Ingredient Cost Tracker: Output Template

Write one Excel workbook named `ingredient-cost-tracker-<YYYY-MM-DD>.xlsx`, dated with the newest price week. If files can't be written, put the same tables in the reply as Markdown.

## Sheet 1: Summary

Title row: `Ingredient cost tracker, US wholesale prices through <newest week>`

**Price moves**

| Ingredient | Series | Unit | Latest week | Latest price | vs 4 wk | vs 13 wk | vs 52 wk | 52-wk low (date) | 52-wk high (date) |
|---|---|---|---|---|---|---|---|---|---|

- Prices to 4 decimal places, changes to 1 decimal place with a sign (`+3.2%`, `−7.0%`).
- When a series gives a low and a high, show the midpoint and write "midpoint of USDA low and high" in the Series cell.
- When a series covers only part of the US, name the region in the Series cell.

**Cost per unit and margin** (only when the user gave amounts per unit)

| | 52 wk ago | 13 wk ago | 4 wk ago | Latest |
|---|---|---|---|---|
| Date | | | | |
| `<ingredient>` cost (lb × price) | | | | |
| Ingredient cost per unit | | | | |
| Selling price per unit | | | | |
| Margin after these ingredients | | | | |

Show money as dollars with 2 decimal places and margin as a percentage with 1 decimal place.

## Sheet 2: Weekly prices

One row per week and one column per ingredient, covering the full year fetched. Leave a cell empty where USDA has no price that week. Add a line chart of the same data, with weeks on the horizontal axis and $/lb on the vertical axis, and label both axes.

## Footer (on both sheets)

```
Source: USDA (<usda-mpr or usda-mars, as returned>) via Orbbit Data, US wholesale, weekly. Prices through <newest week>.
Past prices only, not a forecast. Margin covers the listed ingredients only; labor, packaging and freight are not included.
```
