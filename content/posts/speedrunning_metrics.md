+++
title = "Speedrunning metrics"
date = 2024-11-10
draft = true

[taxonomies]
categories = ["blog"]
tags = ["speedrunning", "rankings"]

[extra]
lang = "en"
toc = true
comment = true
copy = true
math = false
mermaid = false
outdate_alert = false
outdate_alert_days = 120
display_tags = true
truncate_summary = false
featured = false
reaction = false
+++

## Introduction text
Goal: set of metrics that can be applied to any speedrunning event


## Dominance metric
Three variants:
- Comparable across games across time: percentage in lead
  - Per player: how dominant is this player?
  - For the game: weighted average by time in lead (which is sum of squares) -> 1 is full dominance
- Comparable within game across time: distance to next best
    - Transform max to min or reverse
    - Need metric that:
      - Is neutral (0 or 1) with no gap
      - Could extrapolate for calculation as 2nd best as well
      - Scales for bigger gaps but with a natural bound
      - Is same when calculated in minutes, seconds, etc
      - Aggregating over a year, or averaged over each month, should get same result
- Comparable at one moment in time: single score

Discussion: dominance vs lack of competition. Could look into number of entries but that is also not a full view so I choose to keep it as two sides of the same coin.

## Performance metric
How close to the best are you? How close to a WR?
- Applicable across all positions, not just top
- Ideally analog to above calculations (above is specialization of this)
- Could get a “best performance metric” as alternative PB for how close to WR you are


## Calculation details
- Python and Rust -> calculation details
- Could use intervalues for each subperiod (no update to top or 2nd; or to top and you)
- Could introduce diminishing effect for periods without change (but: needs changes in intervalues)
- Could think about granularity of calculation
  - In theory can work with exact time stamps
  - Practically, could round up to days, weeks, months, etc
    - This can be combined with bounds to when it actually happened


## Examples
- Simpsons ranking we just had -> shows dominant period
- Most popular speed ranking -> probably can find that somewhere, otherwise like Tetris, SMB, MK, etc
- Yogs cart as example of restricting to a smaller group
- CS rankings (all four, incl mine)
- Football seasons -> comparing across countries as well

## Specific rankings can require specific calculations
Such as scaling towards top 10 or top 30, if that makes sense
