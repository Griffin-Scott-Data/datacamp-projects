## Data Dictionary:
<img width="729" height="290" alt="image" src="https://github.com/user-attachments/assets/89942f98-9d41-4ba6-9faf-0e4ed7340fad" />

## Problem 1:
> Do more study hours and extracurricular activities lead to better scores? Analyze how studying more than 10 hours per week, while also participating in extracurricular activities, impacts exam performance. 

- Query is created that selects hours_studied, and uses aggregation function 'AVG' to create an average of exam scores.
- Selections are made from a subquery that limits data to fields with more than 10 hours of study time, and fields where students participated in extracurricular activities.
- Query is grouped by hours_studied, and ordered by hours_studied in descending order.
```sql
SELECT
	hours_studied,
	AVG(exam_score) AS avg_exam_score
FROM (
	SELECT *
	FROM student_performance
	WHERE extracurricular_activities = 'Yes' AND hours_studied > 10
) AS student_extracurricular
GROUP BY hours_studied
ORDER BY hours_studied DESC;
```
## Output:
<img width="740" height="752" alt="image" src="https://github.com/user-attachments/assets/fe7c85ef-7fa1-4103-b38c-b9dd8c1c9ea5" />

## Problem 2:
> Is there a sweet spot for study hours? Explore how different ranges of study hours impact exam performance by calculating the average exam score for each study range. Categorize students into four groups based on hours studied per week: 1-5 hours, 6-10 hours, 11-15 hours, and 16+ hours.

- Case when function is established to categorize students into specified groups based on hours studied per week.
- Then, aggregate function 'AVG' is used to create an average of exam scores.
- Query is grouped by the four specified groups established in the 'Case when' function, then ordered by average exam score in descending order.
```sql
SELECT
	CASE WHEN hours_studied >= 16 THEN '16+ hours'
		WHEN hours_studied >= 11 THEN '11-15 hours'
		WHEN hours_studied >= 6 THEN '6-10 hours'
		ELSE '1-5 hours' 
	END AS hours_studied_range,
	AVG(exam_score) AS avg_exam_score
FROM student_performance
GROUP BY hours_studied_range
ORDER BY avg_exam_score DESC;
```
## Output:
<img width="730" height="122" alt="image" src="https://github.com/user-attachments/assets/fd0739de-19cf-493d-8e3c-84b06e478997" />

## Problem 3:
> A teacher wants to show their students their relative rank in the class, without revealing their exam scores to each other. Use a window function to assign ranks based on exam_score, ensuring that students with the same exam score share the same rank and no ranks are skipped. Return the columns attendance, hours_studied, sleep_hours, tutoring_sessions, and exam_rank. The students with the highest exam score should be at the top of the results, so order your query by exam_rank in ascending order. Limit your query to 30 students.

- Dense_rank is used over exam score values to assign ranks based on student exam score performance, defined as 'exam_rank.'
- Query is ordered by exam_rank in ascending order.
```sql
A teacher wants to show their students their relative rank in the class, without revealing their exam scores to each other. Use a window function to assign ranks based on exam_score, ensuring that students with the same exam score share the same rank and no ranks are skipped. Return the columns attendance, hours_studied, sleep_hours, tutoring_sessions, and exam_rank. The students with the highest exam score should be at the top of the results, so order your query by exam_rank in ascending order. Limit your query to 30 students.
```
## Output:
<img width="732" height="749" alt="image" src="https://github.com/user-attachments/assets/59e12288-7114-47af-8076-6ab5dbde4f99" />
