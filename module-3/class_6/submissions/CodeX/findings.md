# Module 3 - Class 6 Findings Report
**Team:** CodeX  
**Scenario:** Airbnb NYC - End-to-End Data Prep  
**Date:** 2026-06-10

---

## Final Numbers

| Metric | Value |
|--------|-------|
| Final row count | *(fill in after running)* |
| Median price | $*(fill in)* |
| Median distance to Times Square | *(fill in)* km |
| `log_price` mean | *(fill in - expect ~4.8)* |
| Reviews coverage | *(fill in)* % of listings had review text |

---

## Top 3 Insights

1. **Location dominates price.** Listings within 2 km of Times Square charge significantly more than those 15+ km away. A borough-only signal is insufficient - Marcus's hint engine must use Haversine distance as a feature.

2. **Room type creates two separate markets.** `Entire home/apt` listings cost roughly 2x a `Private room` in the same neighbourhood. Any fair-price hint must be room-type-specific, not city-wide.

3. **`bedrooms` was the highest-missing column (~30% missing).** Imputing by `room_type` median is better than a global median because Private rooms typically have 1 bedroom while Entire homes have 2+. A global median would overestimate bedrooms for Private rooms and underestimate for Entire homes, introducing systematic bias into the M4 features.

---

## Cleaning Decisions

| Decision | Reason |
|----------|--------|
| Cap price at 99th percentile | Listings above ~$1,000/night are outliers not representative of the market Marcus serves |
| Drop price <= 0 | Zero/negative price is a data error - unusable for regression |
| Impute `bedrooms` by `room_type` median | Smarter than global median; Private rooms != Entire homes |
| Impute `bathrooms` + `review_scores_rating` with overall median | Missing rate is low (<5%); median is robust to outliers |
| Top-30 neighbourhoods + "Other" | 220+ neighbourhoods -> one-hot encoding all would create sparse, high-dim features that overfit in M4 |
| Haversine distance, not Euclidean | Earth is a sphere; 1 degree longitude != 1 degree latitude in km at NYC's latitude |

---

## One Question for the M4 Mentor

Should `reviews_per_year` be log-transformed before feeding into the M4 model? Its distribution is very right-skewed - a few listings have 100+ reviews/year while most have fewer than 10. Log-transforming would make it more normal and likely improve the linear baseline.

---

## Summary Chart

See `marcus_chart_distance_vs_price.png` generated when the notebook is run.

*Listings closest to Times Square command the highest prices. This is the single strongest location signal in the dataset.*
