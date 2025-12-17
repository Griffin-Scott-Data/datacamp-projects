## Data Dictionary:
<img width="430" height="559" alt="image" src="https://github.com/user-attachments/assets/1d13a046-77cb-431a-b555-1a4efb60cd3e" />

## Problem 1:
> What is the oldest business on each continent? Save your query with four columns: continent, country, business, and year_founded in any order.

- Two tables, 'businesses' and 'countries', are joined together into common table expression, 'data.'
	- This is done to simplify the next steps of the solution, and because those two tables contain all the information necessary to answer the question.
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
> Calculate the quantity for orders with missing values in the quantity column by determining the unit price for each product_id using available order data, considering relevant pricing factors such as discount, market, or region. Then, use this unit price to estimate the missing quantity values. The calculated values should be stored in the calculated_quantity column.

- To complete this task, the problem is broken down into parts. First, two common table expressions are created. The first establishes a list of all fields within the orders orders table that contain a null quantity.
```sql
WITH missing AS (
	SELECT
		product_id,
		product_name,
		sales,
		market,
		region,
		discount,
		quantity
	FROM orders
	JOIN products
	USING (product_id)
	WHERE quantity IS NULL
),
```
- Next, the second CTE uses available order data to establish unit price for each product irrespective of discounts to serve as baseline price for each product.
```sql
unit_prices AS (
	SELECT
		DISTINCT product_id,
		AVG(sales/quantity * (1 - discount)) AS unit_price
	FROM orders
	WHERE discount = 0 AND sales/quantity * (1 - discount) <> 0
	GROUP BY product_id
)
```
- Finally, these two CTEs are both referenced in the final query, which estimates missing quantity values for the orders identified in the 'missing' CTE with no listed quantity, by using the unit prices CTE and taking into account respective discounts.
```sql
SELECT
	m.product_id,
	m.discount,
	m.market,
	m.region,
	m.sales,
	m.quantity,
	CASE WHEN m.discount = 0 THEN ROUND(CAST((m.sales/u.unit_price) AS numeric), 0)
	ELSE ROUND(CAST((m.sales/(u.unit_price*(1 - m.discount))) AS numeric), 0) END AS calculated_quantity
FROM missing AS m
INNER JOIN unit_prices AS u
ON m.product_id = u.product_id;
```
## Output:
<img width="754" height="179" alt="image" src="https://github.com/user-attachments/assets/1bf5180e-21bb-4fcf-89d0-294d8c78d6aa" />
