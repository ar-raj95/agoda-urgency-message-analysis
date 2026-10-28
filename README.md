# Hotel Pricing & Urgency Message: A Causal Analysis

Do hotel prices really rise as check-in approaches? This project tests whether a "prices are rising, book now" message would be honest, using about 49,000 hotel bookings across 5 cities (check-ins in Q4 2016).

## Key findings

- A simple comparison shows prices **falling** about 39.5% over the 60 days before check-in. This is misleading, because it mixes different hotels.
- Comparing the same room-night with itself, the effect shrinks to roughly **zero** (beta = -0.005, p = 0.88).
- Individual room-nights are close to a coin flip: 47.5% rise and 52.5% fall.
- Price rises are real only in specific segments. The strongest is City D + Weekend + 4-5 star, where prices rose in **82%** of room-nights (n = 112).
- **Recommendation:** do not show a blanket urgency message. Target it only at segments where prices genuinely rise, and run an A/B test first.

## Four models, progressively stricter

| Model | What it compares | Beta ($/day) | 60-day move |
|---|---|---|---|
| M1 Naive | All bookings | -0.974 | -39.5% |
| M2 Controls | Adds star, chain, weekend, stay length, city, month | -0.423 | -17.1% |
| M3 Hotel fixed effects | Same hotel over time | -0.254 | -10.3% |
| M4 Stay fixed effects | Same room-night over time | -0.005 (p = 0.88) | -0.2% |

## Repository contents

- [`notebooks/`](notebooks/): the analysis notebook and a step-by-step [guide](notebooks/README.md)
- `presentation/`: the executive deck
- `figures/`: charts from the analysis

## Tools

Python, pandas, statsmodels, matplotlib, seaborn, Power BI

## Limitations

- One quarter (Q4 2016) and five cities.
- Realised booking prices only, with no quoted prices, room types or occupancy.
- A within-stay demand shock could still bias M4. An instrumental variable would address this.

The dataset is not included.
