# Ingredient Cost Tracker: Self-Check

Run every item before replying. Each one passes or fails. Fix any failure, then run the whole list again.

## Data

- [ ] Every price in the tracker came from this run's `get_commodity_metric_values` result. None were typed in from memory.
- [ ] Every series is `wholesale` and in the US (national, or a named US region), unless the user asked for another market by name.
- [ ] Each ingredient uses the default series from `SKILL.md`, or the tracker says why it used a different one.
- [ ] The newest week of every series is within about a month of today. If not, the tracker says the series is behind and names its last week.
- [ ] No missing week shows as `0` or as a filled-in guess. Empty cells stay empty.

## Math

- [ ] Each change is `latest / then − 1`, and its sign matches the direction (a lower price shows `−`).
- [ ] The 4, 13 and 52 week comparisons use those weeks or the nearest earlier week, and any substitute week is named.
- [ ] Every price in one column has the same unit. Any $/cwt price was divided by 100 before being shown as $/lb.
- [ ] Cost per unit equals the sum of pounds × price for that week. Spot-check one date by hand.
- [ ] Margin uses the user's own selling price and amounts, never assumed ones.

## Claims

- [ ] The source line names USDA, Orbbit Data and the newest week.
- [ ] Nothing predicts a future price.
- [ ] The margin is labeled "after these ingredients" and the tracker says labor, packaging and freight are not included.
- [ ] Nothing tells the user to raise, lower or hold their price.
- [ ] Any ingredient the data doesn't cover is named as not covered, not replaced with a lookalike.

## Reply

- [ ] The first sentence states the biggest move, with its number, unit and week.
- [ ] The file (or the Markdown tables) is attached or included.
