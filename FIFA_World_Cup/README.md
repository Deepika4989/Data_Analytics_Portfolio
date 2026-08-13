# FIFA 2026 Player Performance Analysis

## Overview
This project analyzes player-level match data from the FIFA 2026 tournament to explore scoring performance, age-vs-rating trends, and — most notably — surfaces a significant **data quality issue** in the dataset's `expected_goals_xg` (xG) column, which appears to be synthetically generated rather than derived from real shot-quality modeling.

## Objective
Explore individual player performance across the tournament, identify top performers relative to statistical expectation (goals vs. expected goals), and investigate whether player age correlates with match performance rating.

## Dataset
- **Source:** FIFA 2026 Players dataset
- **Rows:** 54,600 player-match records
- **Columns:** 75, covering player attributes (age, position, market value), match context (opponent, stadium, tournament stage), and performance stats (goals, shots, xG, passes, possession)

## Methodology

### 1. Initial Exploration
- Loaded and inspected the dataset structure (`.info()`, `.columns.tolist()`) to understand the full scope of available features
- Spot-checked an individual player's (Memphis Zerrouki) match-by-match record as a sanity check before aggregating

### 2. Player-Level Aggregation
- Grouped by `player_name` to sum `goals`, `shots`, and `expected_goals_xg` across the full tournament
- Calculated an **overperformance** metric: `goals - expected_goals_xg`, to identify players scoring well beyond what their shot quality would statistically predict

### 3. Data Quality Finding — The xG Column
While reviewing top overperformers, individual match records surfaced values that are **statistically implausible in real football**:
- One match recorded **4 goals on 4 shots with a combined xG of 0.04** — meaning the model considered these shots almost certain misses, yet all four scored
- Similar extreme mismatches appeared repeatedly across other high-scoring matches (e.g., 3 goals on 3 shots with 0.00 xG, 2 goals on 1 shot with 0.00 xG)
- At the aggregate level, **total goals scored were 3.44× total expected goals** across all players — a real-world xG model would expect this ratio to sit close to 1.0 over a large sample, since xG is specifically calibrated to predict long-run scoring rates

**Conclusion:** the `expected_goals_xg` column does not reflect genuine shot-quality modeling and was very likely synthetically/randomly generated for this dataset, rather than computed from actual shot location, angle, and defensive pressure data as real xG models are. This is an important caveat for any analysis relying on this column and was flagged rather than treated as a real finding about player performance.

### 4. Age vs. Performance Rating
- Aggregated `player_rating` (average per player) against `age`
- Visualized the relationship with a scatter plot and fitted a linear trend line (`np.polyfit`)
- Calculated the correlation coefficient: **-0.145**

**Interpretation:** a weak negative correlation — there's only a slight tendency for average player rating to decline with age across this dataset, far too weak to be a meaningful predictive relationship on its own. Age alone does not appear to be a strong driver of match performance rating in this tournament data.

## Key Takeaways
- **Sanity-checking a derived metric against domain knowledge caught a serious data quality issue.** Knowing that real-world xG should track actual goals reasonably closely (ratio near 1.0) — not 3.44× — made it possible to catch this rather than taking the column at face value.
- **Individual match-level spot checks matter, not just aggregate stats.** The 4-goals-on-0.04-xG example was far more convincing evidence of a problem than the aggregate ratio alone — it's the kind of check that's easy to skip when moving fast through EDA.
- **A weak correlation is still a valid, reportable finding.** The age-rating relationship (-0.145) doesn't support a strong narrative, and that's fine to state plainly rather than overstating a weak pattern.

## Possible Extensions
- Cross-reference this dataset against real FIFA/Opta xG data (if available) to quantify exactly how far off the synthetic column is
- Investigate whether other advanced metrics in the 75-column dataset (e.g., `expected_assists_xa`) show similar synthetic-data red flags
- Explore performance rating against other player attributes (position, minutes played, market value) instead of age alone

## Tools
`Python`, `pandas`, `numpy`, `matplotlib`, Google Colab
