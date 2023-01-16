## What issues will you address by cleaning the data?

- `Consisentcy naming` for tables and columns
- Identify `irrelivant` and `duplicate` data and remove it
- Identify `outliers` to remove to prevent from intefering during analysis
- Filter data `outliers`
- `Converse` dates and times


## Queries:
### Title and Naming consistency:

The original table has unconsistency naming header with uppercase lowercase and underscore. As a result, I have chosen the snake case format when creating the table headers. An example query of how to change column names which is changed from `totalTransactionRevenue` to `total_transaction_revenue` in `all_sessions` table:

```SQL
ALTER TABLE all_sessions
RENAME COLUMN totalTransactionRevenue TO total_transaction_revenue
```

### List of distinct value:

Using `COUNT` and `DISTINCT` function to check the unique value of `full_visitor_id` and how many `COUNT` of that value in table `all_sessions` by `GROUP BY` column 1 and `ORDER BY` column 2 in the query:

```SQL
SELECT DISTINCT(full_visitor_id)
	   , COUNT(full_visitor_id)
FROM all_sessions
GROUP BY 1
ORDER BY 2 DESC
```

Check if there are NULL values in `full_visitor_id`:
```SQL
SELECT COUNT(*)
FROM all_sessions
WHERE full_visitor_id IS NULL
```

### Query to find duplications in a table:

-- Check  `all_sessions` table duplications:

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

1. `all_sessions` table appeared to have 1122 duplicated rows between a set of `full_visitor_id` and `visit_id`

2. Adding the third columns which is product_sku to the matching set, the duplications are reduced into 10 

3. It suggests that there are 1122 instances of user-generated product data, with more than one product per visit and there are 10 products which are visited by the same visitors more than one 

-- Check `ananlytics` table duplications:
1. `COUNT` all original rows:
```SQL
SELECT *
FROM analytics
-- There are 4,301,122 rows in total
```
2. Row duplications in analytics:
WITH dup_col AS (
				SELECT full_visitor_id
						,visit_id
						,COUNT(*) AS num_rows
				FROM analytics
				GROUP BY full_visitor_id
						 ,visit_id
						 HAVING COUNT(*) >1
				)
SELECT *
FROM analytics an
JOIN dup_col dup
ON an.full_visitor_id = dup.full_visitor_id 
AND an.visit_id = dup.visit_id
-- It appears to show 4,298,949 of all 4,301,122 rows are duplicated

### Drop irrelevant and empty columns
1. `product_refund_amount`: 
```SQL
ALTER TABLE all_sessions
DROP COLUMN product_refund_amount
```
2. `item_quantity`:
```SQL
ALTER TABLE all_sessions
DROP COLUMN item_quantity;
```
3. `item_revenue`:
```SQL
ALTER TABLE all_sessions
DROP COLUMN item_revenue;
```
4. `search_keyword`:
```SQL
ALTER TABLE all_sessions
DROP COLUMN search_keyword;
```
5. Refresh and check columns are dropped successfully
```SQL
SELECT *
FROM all_sessions;
```

### Set the price value to the real value by dividing by 1000000

#### Table all_sessions

1. adjust the value by dividing by 1000000
```SQL
UPDATE all_sessions
SET product_price = ROUND((product_price/1000000),2)
	,transaction_revenue = ROUND((transaction_revenue/1000000),2)
	,total_transaction_revenue = ROUND((total_transaction_revenue/1000000),2)
```

2. Confirm the results:
```SQL
SELECT product_price
	   ,transaction_revenue
	   ,total_transaction_revenue
FROM all_sessions
```

3. Set NULL value into 0 in `total_transaction_revenue`,`product_quantity` and `transaction_revenue`
```SQL
UPDATE all_sessions
SET total_transaction_revenue = 0
WHERE total_transaction_revenue IS NULL
```

```SQL
UPDATE all_sessions
SET transaction_revenue = 0
WHERE transaction_revenue IS NULL
```

```SQL
UPDATE all_sessions
SET product_quantity = 0
WHERE transaction_revenue IS NULL
```

4. Confirm the results:
```SQL
SELECT total_transaction_revenue
		,transaction_revenue
		,product_quantity
FROM all_sessions
```

#### Table analytics

1. adjust the value by dividing by 1000000
```SQL
UPDATE analytics
SET unit_price = ROUND((unit_price/1000000),2)
	, revenue = ROUND((revenue/1000000),2)
```
