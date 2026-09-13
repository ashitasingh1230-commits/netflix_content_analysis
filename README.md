# Netflix Content Analysis (Python + SQL)

## Background

This project was built to demonstrate a more advanced level of SQL — beyond basic filtering and aggregation — using relational database design, joins, window functions, and CTEs, alongside Python for data reshaping. It follows two earlier projects (an IT ticket analysis in Python/pandas, and a skincare sales analysis in SQL/Excel).

## Dataset

- **Source:** Netflix Movies and TV Shows (Kaggle, by shivamb)
- **Size:** 8,807 titles, 12 original columns
- **Data quality:** `director` missing in ~30% of rows; `country` and `cast` missing in smaller proportions; `listed_in` (genre) and `date_added` were nearly complete.

## Data modeling decision: normalizing the genre field

The raw `listed_in` column stored multiple genres per title as a single comma-separated text string (e.g., `"Dramas, International Movies"`). Rather than treating this as one flat category per row (which would undercount genre-level analysis and make genre-based grouping unreliable), it was split and exploded into a separate `genres` table — one row per title-genre pair, linked back to the main `titles` table via `show_id`.

This mirrors a standard database design decision: a title can have many genres, and a genre applies to many titles — a many-to-many relationship that a single flat table cannot represent cleanly. Normalizing it this way enabled proper SQL joins and genre-level aggregation that would not have been possible, or would have been inaccurate, working from the raw column directly.

The result: 8,807 titles expanded into 19,323 title-genre pairs (avg. ~2.2 genres per title).

## Tools

Python (pandas) for reshaping the genre data; SQL (SQLite) for relational analysis, including joins, window functions, and CTEs.

## Key findings

- **International Movies (2,752 titles) and Dramas (2,427) are the most common genres overall**, followed by Comedies (1,674).
- **Using a `RANK() OVER (PARTITION BY country)` window function**, the top genre was identified for each country individually. Excluding the U.S. (Dramas) and U.K. (British TV Shows), nearly every other country's top genre was "International Movies" or "International TV Shows." Rather than reflecting genuine audience preference, this most likely reflects Netflix's own catalog labeling convention, which defaults non-U.S. content to an "International" tag — a data artifact worth accounting for in any genre-based analysis, not a real content pattern.
- **Content additions show a U-shaped trend in TV Show share, not a simple linear shift:** TV Shows made up 41% of new titles added in 2016, dropped to a low of 25% in 2018, then climbed back to 34% by 2021 — suggesting a temporary shift toward Movie investment around 2017-2018, followed by a rebalancing back toward TV content. (Note: 2021 figures are likely incomplete, as the dataset appears to have been compiled mid-year.)
- Country data itself contains some multi-value entries (e.g., titles listing multiple co-production countries) which were not further split — a scoping decision, since the primary relational structure needed was the genre field.

## Why this project matters

Compared to a single flat-table analysis, this project demonstrates: (1) recognizing when raw data needs to be restructured before it can be analyzed correctly, (2) using SQL joins, window functions, and CTEs to answer multi-step analytical questions, and (3) distinguishing a genuine finding from a data labeling artifact — a distinction that matters in any real analytical role.

## Files

- `01_netflix_analysis.ipynb` — full analysis notebook (data loading, genre normalization, SQL queries)
