This project explores what drives restaurant busyness in New York City using real-world data. As an avid foodie and a daily NYC commuter, I often find myself wondering which subway stop to get off at on my way to or from school or work to find good food without long waits. That curiosity led me to build a heatmap based on restaurant density, subway ridership, and distance to transit.

The long-term goal is to use this model to predict how busy restaurants will be on days when my friends and I want to eat out without a reservation, helping us identify areas that are most likely to have good food with reasonable wait times. Over time, this can be further refined by cuisine type, commute patterns, ratings, and reviews.

Beyond prediction, the goal is to understand how location, transit access, and demand interact in an urban environment. To do this, I combined Yelp business data with MTA subway ridership and applied geospatial analysis and machine learning to model patterns of restaurant activity across NYC.

---

## Why These Data Sources

- **Yelp dataset** I have been using the app ever since I came back from undergrad to navigate my way around NYC's food scence, it provides rich restaurant metadata (ratings, reviews, categories, locations). I would love to look at Beli's in the future.
- **MTA ridership data** acts as a proxy for foot traffic and accessibility, trust me I know, I use it every day - hence how we got here.
  
Subway ridership is a strong real-world signal for customer flow in NYC, making it a meaningful feature for modeling restaurant activity.

---

## Key Design Decisions (and Why)

### 1. Station-level aggregation
I aggregated ridership at the **station complex level** instead of individual turnstiles.
- It reduces noise
- It aligns better with how people think about stations
- It improves interpretability

Alternatives
- Raw turnstile-level data (brings higher granularity but much noisier)

---

### 2. Geospatial matching using BallTree + Haversine
To associate each restaurant with its **nearest subway station**, I used
- Haversine distance (accounts for Earth curvature)
- BallTree for efficient nearest-neighbor search
- Haversine distance accurately measures real-world distances on the Earth’s surface, which is essential for geographic data. 
- BallTree allows fast nearest-neighbor searches at scale, making it efficient for matching thousands of restaurants to stations. 
- Together, they balance accuracy and performance.

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
- Engineered features like distance to transit and ridership capture meaningful signals more clearly than raw data alone. 
- They help the model learn relationships that are easier to interpret and reason about. 
- This also improves transparency when explaining results.

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
- I used **TimeSeriesSplit** instead of random splits.
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

This project demonstrates an end-to-end applied ML workflow: data ingestion → spatial reasoning → feature engineering → modeling → validation → visualization.

And, based on food density, variety, and accessibility, the analysis consistently points to 14th Street–Union Square as the best subway stop to get off if you’re looking for food in NYC.