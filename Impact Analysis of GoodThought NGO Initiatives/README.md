## Data Dictionary:
This comprehensive dataset includes:
<img width="906" height="55" alt="image" src="https://github.com/user-attachments/assets/4bd754fa-654f-46b2-af77-44cd27089c57" />

Table relationships:
<img width="1670" height="900" alt="image" src="https://github.com/user-attachments/assets/4f139871-932e-417a-855e-a8ccf3a14bac" />

## Problem 1:
> List the top five assignments based on total value of donations, categorized by donor type. The output should include four columns:
	- 1) assignment_name, 2) region, 3) rounded_total_donation_amount rounded to two decimal places, and 4) donor_type, sorted by rounded_total_donation_amount in descending order.

- First, a common table expression is created that identifies the amount donated to *each* project categorized by *each* donor type.
- Two tables, 'donations' and 'donors', are joined on the 'donor_id' column.
- The selection is grouped by assignment_id and donor_type to categorize amount donated by project and donor type.
```sql
SELECT
		d.assignment_id,
		d2.donor_type,
		SUM(d.amount) AS total_donation_amount
	FROM donations AS d
	JOIN donors AS d2
	USING (donor_id)
	GROUP BY d.assignment_id, d2.donor_type
	ORDER BY total_donation_amount DESC
```

- Now, this CTE is referenced in the main query.
- Assignment name, assignment region, total donation amounts (rounded to 2 digits), and type of donor are all included in the select statement.
- The selection is made from the 'donation_breakdown' CTE, which is then joined with the 'assignments' table to allow selection of relevant name and region for each assignment.
### Final code
```sql
WITH donation_breakdown AS (
	SELECT
		d.assignment_id,
		d2.donor_type,
		SUM(d.amount) AS total_donation_amount
	FROM donations AS d
	JOIN donors AS d2
	USING (donor_id)
	GROUP BY d.assignment_id, d2.donor_type
	ORDER BY total_donation_amount DESC
)

SELECT
	a.assignment_name,
	a.region,
	ROUND(db.total_donation_amount, 2) AS rounded_total_donation_amount,
	db.donor_type
FROM donation_breakdown AS db
JOIN assignments AS a
USING (assignment_id)
LIMIT 5;
```

## Output:
<img width="1151" height="179" alt="image" src="https://github.com/user-attachments/assets/559c3798-0ec1-458d-b5ae-adda5e31978b" />

## Problem 2:
> Identify the assignment with the highest impact score in each region, ensuring that each listed assignment has received at least one donation. The output should include four columns: 1) assignment_name, 2) region, 3) impact_score, and 4) num_total_donations, sorted by region in ascending order. Include only the highest-scoring assignment per region, avoiding duplicates within the same region.

- To complete this task, the problem is broken down into parts, including two subqueries.
- A subquery is created to assign a rank to each assignment based on its impact score
```sql
SELECT
		region,
		assignment_id,
		impact_score,
		ROW_NUMBER() OVER (PARTITION BY region ORDER BY impact_score DESC) AS impact_score_rank
FROM assignments
```
- A second subquery is created to select the amount of donations per assignment.
```sql
SELECT
		a.assignment_name,
		a.assignment_id,
		COUNT(d.donation_id) AS num_total_donations
FROM assignments AS a
JOIN donations AS d
USING (assignment_id)
GROUP BY a.assignment_name, a.assignment_id
```

- Now, the final query is created and incorporates both of these subqueries.
- Selections include region, assignment name, impact score, and number of donations, referencing both subqueries.
- Selection is narrowed by a WHERE clause to only include assignments ranked with the highest impact score in their respective region.

### Final code
```sql
SELECT
	r.region,
	a.assignment_name,
	r.impact_score,
	a.num_total_donations
FROM (
	SELECT
		region,
		assignment_id,
		impact_score,
		ROW_NUMBER() OVER (PARTITION BY region ORDER BY impact_score DESC) AS impact_score_rank
	FROM assignments
) AS r
JOIN (
	SELECT
		a.assignment_name,
		a.assignment_id,
		COUNT(d.donation_id) AS num_total_donations
	FROM assignments AS a
	JOIN donations AS d
	USING (assignment_id)
	GROUP BY a.assignment_name, a.assignment_id
) AS a
	USING (assignment_id)
WHERE impact_score_rank = 1
ORDER BY region ASC;
```

## Output:
<img width="754" height="179" alt="image" src="https://github.com/user-attachments/assets/1bf5180e-21bb-4fcf-89d0-294d8c78d6aa" />
