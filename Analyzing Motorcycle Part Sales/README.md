## Data Dictionary:
<img width="666" height="635" alt="image" src="https://github.com/user-attachments/assets/cf41bbfe-951f-43f9-95f8-aae28124d268" />

## Problem 1:
> Find out how much Wholesale net revenue each product_line generated per month per warehouse in the dataset.
  > The query should be saved as revenue_by_product_line using the SQL cell provided, and contain the following:
        product_line,
        month: displayed as 'June', 'July', and 'August',
        warehouse, and
        net_revenue: the sum of total minus the sum of payment_fee.
  > The results should be sorted by product_line and month, followed by net_revenue in descending order.

- Firstly, two relevant columns are of wrong data type: quantity and profit, both of 'double precision' data type need to be converted to 'numeric' data type.

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

## Output:
<img width="756" height="421" alt="image" src="https://github.com/user-attachments/assets/86f7d84a-16c0-400d-b30b-5f75756b88f3" />
