Objective
This project simulates a Startup Evaluation Engine, similar to a credit score but for startups. We evaluate each startup on multiple business indicators like team quality, market size, traction, and financials — and generate a composite score (out of 100) to rank startup potential.





Why These Weights Were Chosen

| Feature                   | Weight | Reasoning                                                    |
| ------------------------- | ------ | ------------------------------------------------------------ |
| `monthly_active_users`    | 25%    | Traction is a key success driver — shows product-market fit. |
| `market_size_million_usd` | 20%    | Large addressable market implies scalability potential.      |
| `team_experience`         | 15%    | Strong founding teams improve execution.                     |
| `funds_raised_inr`        | 15%    | Shows investor confidence and runway.                        |
| `valuation_inr`           | 15%    | Often reflects market confidence.                            |
| `monthly_burn_rate_inr`   | 10%    | Penalized — leaner startups are more resilient.              |





How Negatively Correlated Metrics Were Handled

The Monthly Burn Rate is a cost-related metric — the lower, the better. However, since Min-Max normalization normally gives higher scores to higher values, we inverted it:
df_scaled['monthly_burn_rate_inr'] = 1 - scaler.fit_transform(df['monthly_burn_rate_inr'].values.reshape(-1,1))

This ensures that:

A startup with lower expenses scores closer to 1
A startup with higher burn scores closer to 0
Maintains consistency with other positively correlated features during score aggregation

Top Performer:
Startup ID: S004 — High MAUs (93K+), strong team (5+ yrs), large market size, and good funding. Moderate burn helped it score well.
Bottom Performer:
Startup ID: S063 — Despite decent valuation, this startup had very low MAUs, a small market, and high burn rate.





Any surprises or insights from your ranking

Burn rate alone doesn’t determine rank, but high burn + low users is a bad combo.
Some startups raised lots of funding, but still ranked low due to poor traction or small markets.
Market size and user base are most consistently correlated with high scores.
