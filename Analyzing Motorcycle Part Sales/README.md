## Data Dictionary:
<img width="662" height="354" alt="image" src="https://github.com/user-attachments/assets/8e990102-d799-4dff-9efb-294a544e7487" />

## Project Instructions:
<img width="796" height="185" alt="image" src="https://github.com/user-attachments/assets/b3465900-f774-46d0-b871-9f363803b3a3" />


- First, all relevant criteria are selected and aliased where necessary.
- All selections are made from the 'sales' table.
- Selections are filtered for specifically 'Wholesale' client type.
- Selections are grouped by product_line, month, and warehouse for the net_revenue aggregate function.
- Selections are ordered by product_line then by month, both in ascending order, and then by net_revenue in descending order.

```sql
SELECT
	product_line,
	TO_CHAR(date, 'Month') AS month,
	warehouse,
	SUM(total) - SUM(payment_fee) AS net_revenue
FROM sales
WHERE client_type = 'Wholesale'
GROUP BY product_line, month, warehouse
ORDER BY product_line, month, net_revenue DESC;
```

## Output:
<img width="1155" height="448" alt="image" src="https://github.com/user-attachments/assets/a432f376-0f01-4a37-87d3-f69d2d50d923" />
