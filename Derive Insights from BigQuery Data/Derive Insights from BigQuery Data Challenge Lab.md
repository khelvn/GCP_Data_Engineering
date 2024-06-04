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

### Task 4. Fatality ratio

```
SELECT sum(cumulative_confirmed) as total_confirmed_cases,
       sum(cumulative_deceased) as total_deaths,
       (sum(cumulative_deceased)/sum(cumulative_confirmed))*100 as case_fatality_ratio
FROM `bigquery-public-data.covid19_open_data.covid19_open_data`
WHERE country_name="Italy" AND date BETWEEN 'YYYY-MM-DD' and 'YYYY-MM-DD'
```

### Task 5. Identifying specific day

```
SELECT date
FROM `bigquery-public-data.covid19_open_data.covid19_open_data`
WHERE country_name="Italy" and cumulative_deceased>DEATH
ORDER BY date asc
LIMIT 1
```
### Task 6. Finding days with zero net new cases

```
WITH india_cases_by_date AS (
    SELECT date, SUM(cumulative_confirmed) AS cases
    FROM `bigquery-public-data.covid19_open_data.covid19_open_data`
    WHERE country_name ="India" AND date BETWEEN 'YYYY-MM-DD' AND 'YYYY-MM-DD'
    GROUP BY date
    ORDER BY date ASC
), india_previous_day_comparison AS (
    SELECT date, cases, LAG(cases) OVER(ORDER BY date) AS previous_day, cases - LAG(cases) OVER(ORDER BY date) AS net_new_cases
    FROM india_cases_by_date
)
SELECT count(*)
FROM india_previous_day_comparison
WHERE net_new_cases=0
```

