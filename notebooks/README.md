# Analysis Notebook Guide

`urgency_message_analysis.ipynb` contains the full analysis: data cleaning, exploratory checks, four regression models, the coin-flip test, and segment targeting.

## What the notebook answers

Do hotel prices rise as the check-in date approaches, and is a "prices are rising" message honest? The notebook tests this with four progressively stricter models and then looks for segments where prices really do rise.

## Key variables

| Variable | Meaning |
|---|---|
| `adr` | Average daily rate in USD |
| `booking_window` | Days between booking date and check-in (counts down from 60 to 0) |
| `days_to_checkin` | `booking_window.max() - booking_window`. Counts up as check-in approaches, so a negative slope reads as "price falls toward check-in" |
| `stay_id` | Hotel x check-in date, used for the same room-night comparison |
| `rises` | True if a room-night's price slope is positive as check-in approaches |

## Notebook walkthrough

| Step | What it does | Output |
|---|---|---|
| 1. Setup | Imports, colour palette, data path | None |
| 2. Load and clean | Combines 5 city sheets, fixes the accommodation-type column, removes 3 rows where check-in is before booking | ~49,061 clean bookings |
| 3. Feature engineering | Builds `booking_window`, `days_to_checkin`, `stay_id`, weekend flag, length of stay | None |
| 4. Price trend | Average ADR across the 60 to 0 day window | Line chart |
| 5. M1 Naive | `adr ~ days_to_checkin` on all bookings | `fig_m1_naive.png` |
| 6. M2 Controls | Adds star rating, chain, weekend, stay length, city, month | `fig_m2_coefficients.png` |
| 7. M3 Hotel fixed effects | Demeans within each hotel, clustered by hotel | `fig_m3_hotel_fe.png` |
| 8. M4 Stay fixed effects | Demeans within each hotel-night, clustered by stay | `fig_m4_stay_fe_and_summary.png` |
| 9. Model comparison | Bar chart of the four coefficients | `fig1_model_collapse.png` |
| 10. Coin-flip test | Per room-night slope, share rising vs falling | `fig2_coinflip_donut.png`, `fig2b_slope_distribution.png` |
| 11. Segments | Share of room-nights with rising prices by city, star tier, chain, weekend | `fig4_segment_targeting.png` |
| 12. Combinations | 2-way and 3-way segment combinations (minimum 50 cases) | `fig5_2way_combinations.png`, `fig6_3way_combinations.png` |
| 13. Summary | Prints key findings in one place | Console output |

## Headline results

| Model | Comparison | Beta ($/day) | 60-day move |
|---|---|---|---|
| M1 | All bookings | -0.974 | -39.5% |
| M2 | With controls | -0.423 | -17.1% |
| M3 | Same hotel | -0.254 | -10.3% |
| M4 | Same room-night | -0.005 (p = 0.88) | -0.2% |

- Price rose in 47.5% of room-nights and fell in 52.5%.
- Best segment: City D + Weekend + 4-5 star, prices rose in 82% of room-nights (n = 112).

## How to run

1. Install dependencies:
   ```bash
   pip install -r requirements.txt
   ```
2. The dataset is not included in this repository. Place your own file with the same 5 city sheets in the working directory.
3. Update the path in the first code cell:
   ```python
   file_path = 'your_data_file.xlsx'
   ```
4. Run all cells in order. Later cells depend on earlier ones (for example, `stay_df` feeds the segment cells).
5. Figures are saved to the working directory. Move them into `figures/` if you want them to match the README.

## Notes on method

- **Why fixed effects:** the question is causal, not predictive. Fixed effects remove every time-invariant hotel characteristic, observed or not. ARIMA and tree models optimise forecasts and cannot separate timing from hotel mix.
- **Standard errors:** HC1 for M1 and M2, clustered by hotel for M3 and by stay-night for M4.
- **Scatter plots** use a random sample of 3,000 rows for readability. Every regression uses the full dataset.
- **Coin-flip test** uses room-nights with at least 5 bookings and at least 3 distinct booking windows.

## Limitations

- One quarter (Q4 2016) and five cities.
- Realised booking prices only, with no quoted prices, room types or occupancy.
- A within-stay demand shock could still bias M4. An instrumental variable would address this.
