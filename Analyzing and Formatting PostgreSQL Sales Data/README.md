## Data Dictionary:
<img width="777" height="857" alt="Screenshot 2025-11-25 210359" src="https://github.com/user-attachments/assets/3ceeeeff-3341-4109-8a42-af5b65a81f49" />

## Problem 1:
> Find the top 5 products from each category based on highest total sales. The output should be sorted by category in ascending order and by sales in descending order within each category, i.e. within each category product with highest margin should sit on the top.

- Firstly, two relevant columns are of wrong data type: quantity and profit, both of 'double precision' data type need to be converted to 'numeric' data type.
- Next, a RANK() function partitioned by category and ordered by product total sales is used to sort by sales.
- These two actions are incorporated into a query using the 'products' table and accompanied by an 'inner join' with the 'orders' table.
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
