This project explores **what drives restaurant busyness in New York City** using real-world data.  
The goal is not just to predict outcomes, but to understand how **location, transit access, and demand** interact in an urban environment.

I combined Yelp business data with MTA subway ridership and applied geospatial analysis and machine learning to model patterns of restaurant activity across NYC.

---

Why These Data Sources

- **Yelp dataset** provides rich restaurant metadata (ratings, reviews, categories, locations).
- **MTA ridership data** acts as a proxy for foot traffic and accessibility.
  
Subway ridership is a strong real-world signal for customer flow in NYC, making it a meaningful feature for modeling restaurant activity.

---

Key Design Decisions (and Why)

1. Station-level aggregation
I aggregated ridership at the **station complex level** instead of individual turnstiles.
- Reduces noise
- Aligns better with how people think about stations
- Improves interpretability

Alternatives
- Raw turnstile-level data (higher granularity but much noisier)

---

### 2. Geospatial matching using BallTree + Haversine
To associate each restaurant with its **nearest subway station**, I used:
- Haversine distance (accounts for Earth curvature)
- BallTree for efficient nearest-neighbor search

Why
- Scales efficiently to large datasets
- More accurate than naive Euclidean distance

Alternatives
- Brute-force distance computation (simpler but much slower)
- Grid-based approximations (less precise)

---

### 3. Feature engineering over raw prediction
Instead of feeding raw data directly into a model, I engineered interpretable features such as:
- Distance to nearest station
- Station ridership
- Restaurant ratings and review counts

Why
- Improves model learning
- Makes results easier to explain and reason about

---

### 4. Gradient Boosting Regressor
I chose Gradient Boosting because:
- Handles nonlinear relationships well
- Works strongly on tabular data
- Requires minimal feature scaling

Alternatives
- Linear regression (too simple)
- Neural networks (overkill for tabular + limited data)

---

### 5. Time-aware validation
I used **TimeSeriesSplit** instead of random splits.
- Prevents data leakage
- Better reflects real-world forecasting

---

## Evaluation

Model performance was evaluated using **Mean Absolute Error (MAE)**.

Rather than optimizing for minimal error alone, I focused on:
- Stability across time splits
- Sensible feature importance
- Consistent generalization

This ensured the model learned **real signals**, not noise.

---

## Results & Insights

Key takeaways
- Proximity to high-ridership subway stations correlates strongly with restaurant busyness.
- Location and transit access matter as much as ratings.
- High-rated restaurants still struggle in low-foot-traffic areas.
- Spatial patterns are clearer through visualization than metrics alone.

The NYC heatmap highlights clusters of high activity near transit hubs and dense neighborhoods.

---

## Limitations & Future Work

This project makes simplifying assumptions
- Busyness is approximated from static features rather than live transaction data.
- Causal relationships are not proven — only correlations.

Possible extensions
- Incorporate time-of-day and day-of-week effects
- Add delivery app data or mobile foot-traffic data
- Deploy as an interactive dashboard
- Perform causal analysis or A/B-style simulations

---

## Conclusion

This project demonstrates an end-to-end applied ML workflow:
data ingestion → spatial reasoning → feature engineering → modeling → validation → visualization.

More importantly, it shows how **real-world context** matters just as much as model choice when working with messy, human-centered data.
