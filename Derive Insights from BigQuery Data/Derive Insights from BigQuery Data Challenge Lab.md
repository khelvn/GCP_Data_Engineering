# Derive Insights from BigQuery Data

#### I Ran the following Queries in BigQuery Editor

### Task 1. Total confirmed cases

```
SELECT sum(cumulative_confirmed) as total_cases_worldwide
FROM `bigquery-public-data.covid19_open_data.covid19_open_data`
WHERE date='YYYY-MM-DD'
```
### Task 2. Worst affected areas

```
WITH deaths_by_states AS (
    SELECT subregion1_name as state, sum(cumulative_deceased) as death_count
    FROM `bigquery-public-data.covid19_open_data.covid19_open_data`
    WHERE country_name="United States of America" and date='YYYY-MM-DD' and subregion1_name is NOT NULL
    GROUP BY subregion1_name
)
SELECT count(*) as count_of_states
FROM deaths_by_states
WHERE death_count > COUNTS
```
### Task 3. Identify hotspots

```
SELECT * FROM (
    SELECT subregion1_name as state, sum(cumulative_confirmed) as total_confirmed_cases
    FROM `bigquery-public-data.covid19_open_data.covid19_open_data`
    WHERE country_code="US" AND date='YYYY-MM-DD' AND subregion1_name is NOT NULL
    GROUP BY subregion1_name
    ORDER BY total_confirmed_cases DESC
)
WHERE total_confirmed_cases > CASES
```