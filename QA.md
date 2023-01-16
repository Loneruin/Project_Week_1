What are your risk areas? Identify and describe them.
1. NULL values: data values contain plain information
2. Duplicate values: data are mistaken inserted multiple times
3. Invalid data: data which doesn't have any meaning such as `not set`, `not available in data set demo`, etc
QA Process:
Describe your QA process and include the SQL queries used to execute it.
1. Make sure the Query is readable:

Upper case for function

Use aliases for readable

Break long query into small sub-query
```SQL
WITH sale_table AS (
					SELECT sap.total_ordered
						   ,sap.product_sku
					FROM sales_report sap
					JOIN sales_by_sku sas
					ON sap.product_sku = sas.product_sku
				   )
SELECT DISTINCT country
				,city
				,al.v2_product_category
				,total_ordered
FROM sale_table st
JOIN all_sessions al
ON st.product_sku = al.product_sku
WHERE city <> 'not available in demo dataset'
AND city <> '(not set)'
GROUP BY 1,2,3,4
ORDER BY 4 DESC
LIMIT 10
```

2. Using data assertion to look for duplicate in the data set. If the query returns any rows then the assertion fails. For example:
```SQL
WITH dup_col AS (
				SELECT full_visitor_id
						,visit_id
						--,product_sku
						,COUNT(*) AS num_rows
				FROM all_sessions
				GROUP BY full_visitor_id
						 ,visit_id
						 --,product_sku
				HAVING COUNT(*) >1
				)
SELECT *
FROM all_sessions al
JOIN dup_col dup
ON al.full_visitor_id = dup.full_visitor_id 
AND al.visit_id = dup.visit_id
--AND al.product_sku = dup.product_sku
```

3. Check the number of row with and without `DISTINCT` function:
```SQL
SELECT full_visitor_id
FROM all_sessions
```

```SQL
SELECT DISTINCT full_visitor_id
FROM all_sessions
```

4. Using the `CONDITION` function to omit mistaken and misleading values

Without using the `CONDITION` function, the output is going to contain misleading values such as `"not set"` and `"not available in demo dataset"`

```SQL
WITH sale_table AS (
					SELECT sap.total_ordered
						   ,sap.product_sku
					FROM sales_report sap
					JOIN sales_by_sku sas
					ON sap.product_sku = sas.product_sku
				   )
SELECT DISTINCT country
				,city
				,al.v2_product_name
				,al.v2_product_category
				,ROUND(SUM(total_ordered * product_price),2) AS revenue
FROM sale_table st
JOIN all_sessions al
ON st.product_sku = al.product_sku
GROUP BY 1,2,3,4
ORDER BY 5 DESC
```
Using `WHERE` FUNCTION to exclude those misleading values

```SQL
WITH sale_table AS (
					SELECT sap.total_ordered
						   ,sap.product_sku
					FROM sales_report sap
					JOIN sales_by_sku sas
					ON sap.product_sku = sas.product_sku
				   )
SELECT DISTINCT country
				,city
				,al.v2_product_name
				,al.v2_product_category
				,ROUND(SUM(total_ordered * product_price),2) AS revenue
FROM sale_table st
JOIN all_sessions al
ON st.product_sku = al.product_sku
WHERE city <> 'not available in demo dataset'
AND city <> '(not set)'
AND v2_product_category <> '(not set)' 
AND v2_product_category <> '${escCatTitle}'
GROUP BY 1,2,3,4
ORDER BY 5 DESC
```