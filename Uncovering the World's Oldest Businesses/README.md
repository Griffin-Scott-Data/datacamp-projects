## Data Dictionary:
<img width="430" height="559" alt="image" src="https://github.com/user-attachments/assets/1d13a046-77cb-431a-b555-1a4efb60cd3e" />

## Problem 1:
> What is the oldest business on each continent? Save your query with four columns: continent, country, business, and year_founded in any order.

- Two tables, 'businesses' and 'countries', are joined together into common table expression, 'data.'
	- This is done to simplify the next steps of the solution, and because these two tables contain all the information necessary to answer the question.
```sql
WITH data AS (
SELECT *
FROM businesses AS b
JOIN countries AS c
USING (country_code)
)
```

- Then, the data table is joined with a subquery of the data table that specifically identifies the exact *year* each continent's oldest business was founded.
```sql
SELECT
	data.continent,
	data.country,
	data.business,
	data.year_founded
FROM data
JOIN (
	SELECT
		continent,
		MIN(year_founded) AS year_founded
	FROM data
GROUP BY continent
) AS years
```
- The join function uses the specific founding years and continents of each continent's respective oldest business to narrow the selection down to only the relevant information (each continent's oldest business).
```sql
ON data.year_founded = years.year_founded AND data.continent = years.continent
```

### Final code
```sql
WITH data AS (
SELECT *
FROM businesses AS b
JOIN countries AS c
USING (country_code)
)

SELECT
	data.continent,
	data.country,
	data.business,
	data.year_founded
FROM data
JOIN (
	SELECT
		continent,
		MIN(year_founded) AS year_founded
	FROM data
GROUP BY continent
) AS years
ON data.year_founded = years.year_founded AND data.continent = years.continent;
```

## Output:
<img width="1152" height="201" alt="image" src="https://github.com/user-attachments/assets/431e1ba3-e539-4f17-8585-564791dd7077" />

## Problem 2:
> How many countries per continent lack data on the oldest businesses? Does including new_businesses change this? Count the number of countries per continent missing business data, including new_businesses; store the results in a DataFrame count_missing with columns continent and countries_without_businesses.

### 2a:
- First, counting the number of countries per continent missing business data.
- To complete this task, the problem is broken down into parts.
- A subquery is created that joins the 'countries' and 'businesses' tables.
	- A left join is used to ensure that all records with values in 'countries' are included, regardless of whether each record has a respective value in the 'businesses' table.
```sql
SELECT *
FROM countries AS c
LEFT JOIN businesses AS b
USING (country_code)
```

- Now, we calculate a count of all countries without businesses, ensuring to specify records where there is no value for business name by using a WHERE clause.
```sql
SELECT
	d.continent,
	COUNT(*) AS countries_without_businesses
FROM (
	SELECT *
	FROM countries AS c
	LEFT JOIN businesses AS b
	USING (country_code)
	) AS d
WHERE d.business IS NULL
GROUP BY continent;
```

## Output
<img width="1148" height="198" alt="image" src="https://github.com/user-attachments/assets/ba0bf09c-0306-47de-b161-0583fdab4179" />

### 2b:
- Now, we will evaluate if including new_businesses changes the output.
- This is done by creating a common table expression, 'businesses_2', that performs a union between businesses and new_businesses.
```sql
WITH businesses_2 AS (
	SELECT * FROM businesses
	UNION
	SELECT * FROM new_businesses
)
```

- Then, in the main query, the subquery that joins the 'countries' and 'businesses' tables is altered to now include 'businesses_2'.
```sql
WITH businesses_2 AS (
	SELECT * FROM businesses
	UNION
	SELECT * FROM new_businesses
)

SELECT
	d.continent,
	COUNT(*) AS countries_without_businesses
FROM (
	SELECT *
	FROM countries AS c
	LEFT JOIN businesses_2 AS b
	USING (country_code)
	) AS d
WHERE d.business IS NULL
GROUP BY continent;
```

## Output:
<img width="1148" height="198" alt="image" src="https://github.com/user-attachments/assets/9dc5c7a5-8e01-4bd4-b0b7-4512fc92a112" />

There is no discernible difference in number of countries per continent lacking data on oldest businesses.

## Problem 3:
> Which business categories are best suited to last many years, and on what continent are they? Store your query with the oldest founding year for each continent and category combination. It should contain three columns: continent, category, and year_founded, in that order.

- All three tables are joined together with inner joins using the country_code column.
- MIN(year_founded), in conjunction with group by function of continent and category, identifies the oldest business for each continent and category combination, using all three tables.
```sql
SELECT
	continent,
	category,
	MIN(year_founded) AS year_founded
FROM businesses
JOIN countries
USING (country_code)
JOIN categories
USING (category_code)
GROUP BY continent, category
ORDER BY continent ASC, year_founded ASC;
```

## Output:
<img width="1150" height="337" alt="image" src="https://github.com/user-attachments/assets/a14ac79c-4cfe-40d3-82e4-9188eeaa04ea" />
