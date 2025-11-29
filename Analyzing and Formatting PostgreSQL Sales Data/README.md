## Data Dictionary:
<img width="777" height="857" alt="Screenshot 2025-11-25 210359" src="https://github.com/user-attachments/assets/3ceeeeff-3341-4109-8a42-af5b65a81f49" />

## Problem 1:
> Find the top 5 products from each category based on highest total sales. The output should be sorted by category in ascending order and by sales in descending order within each category, i.e. within each category product with highest margin should sit on the top.

- Firstly, two relevant columns are of wrong data type: quantity and profit, both of 'double precision' data type need to be converted to 'numeric' data type.
```
ROUND(CAST(SUM(sales) AS numeric), 2) AS product_total_sales,
ROUND(CAST(SUM(profit) AS numeric), 2) AS product_total_profit
```
- Next, a RANK() function partitioned by category and ordered by product total sales is used to sort by sales.
```sql
RANK() OVER (PARTITION BY a.category ORDER BY ROUND(CAST(SUM(sales) AS numeric), 2) DESC)
```
- These two actions are incorporated into a query using the 'products' table and accompanied by an 'inner join' with the 'orders' table.
```sql
SELECT
	a.category,
	a.product_name,
	ROUND(CAST(SUM(sales) AS numeric), 2) AS product_total_sales,
	ROUND(CAST(SUM(profit) AS numeric), 2) AS product_total_profit,
	RANK() OVER (PARTITION BY a.category ORDER BY ROUND(CAST(SUM(sales) AS numeric), 2) DESC) AS product_rank
FROM products AS a
INNER JOIN orders AS b
USING (product_id)
GROUP BY a.category, a.product_name
```
- This query then becomes a subquery referenced by the top_five_products_each_category query, so that a 'where' clause can limit the query to only records with a product_rank of _less than or equal to 5_.
```sql
-- top_five_products_each_category
SELECT *
FROM (
	SELECT
		a.category,
		a.product_name,
		ROUND(CAST(SUM(sales) AS numeric), 2) AS product_total_sales,
		ROUND(CAST(SUM(profit) AS numeric), 2) AS product_total_profit,
		RANK() OVER (PARTITION BY a.category ORDER BY ROUND(CAST(SUM(sales) AS numeric), 2) DESC) AS product_rank
	FROM products AS a
	INNER JOIN orders AS b
	USING (product_id)
	GROUP BY a.category, a.product_name
) AS a
WHERE product_rank <= 5;
```

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
