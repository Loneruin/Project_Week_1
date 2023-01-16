Answer the following questions and provide the SQL queries used to find the answer.

    
**Question 1: Which cities and countries have the highest level of transaction revenues on the site?**


SQL Queries:
```SQL
SELECT  country
		,city
		,MAX(total_transaction_revenue) as highest_revenue
FROM all_sessions
WHERE city <> 'not available in demo dataset'
GROUP BY country
		 ,city
ORDER BY 3 DESC
```


Answer:
1. Top highest countries: United State, Israel, Australia
2. Top highest cities: Atlanta, Sunnyvale, Tel Aviv-Yafo, Los Angeles, Sydney, Seattle, Chicago, Palo Alto, San Francisco Nashville




**Question 2: What is the average number of products ordered from visitors in each city and country?**


SQL Queries:
```SQL
SELECT  country
		,city
		,ROUND(AVG(product_quantity),2) AS average_product_ordered
FROM all_sessions
WHERE city <> 'not available in demo dataset' AND city <> '(not set)'
GROUP BY country
		 ,city
ORDER BY 3 DESC
```


Answer:
"country"	"city"	"average_product_ordered"
"United States"	"Columbus"	0.50
"Spain"	"Madrid"	0.50
"United States"	"Detroit"	0.25
"United States"	"Salem"	0.16
"United States"	"Atlanta"	0.06
"United States"	"Houston"	0.04
"United States"	"Dallas"	0.03
"Ireland"	"Dublin"	0.02
"United States"	"Ann Arbor"	0.02
"India"	"Bengaluru"	0.01
"United States"	"Sunnyvale"	0.01
"United States"	"Mountain View"	0.01
"United States"	"Palo Alto"	0.01
"United States"	"Chicago"	0.01
"United States"	"New York"	0.01
"United States"	"Seattle"	0.01


**Question 3: Is there any pattern in the types (product categories) of products ordered from visitors in each city and country?**


SQL Queries:
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


Answer:
There is a high demand for `home and office products in most the cities in United State. Moreover, India and South Korea also have a high number of products ordered in the same product categories:

"country"	"city"	"v2_product_category"	"total_ordered"
"India"	"Pune"	"Home/Office/"	456
"South Korea"	"Seoul"	"Home/Office/Writing Instruments/"	456
"United States"	"Boston"	"Home/Office/"	456
"United States"	"Los Angeles"	"Home/Office/"	456
"United States"	"Mountain View"	"Home/Office/"	456
"United States"	"Mountain View"	"Home/Office/Writing Instruments/"	456
"United States"	"New York"	"Home/Office/"	456
"United States"	"Palo Alto"	"Home/Office/"	456
"United States"	"San Francisco"	"Home/Office/"	456
"United States"	"San Jose"	"Home/Office/"	456




**Question 4: What is the top-selling product from each city/country? Can we find any pattern worthy of noting in the products sold?**


SQL Queries:



Answer:





**Question 5: Can we summarize the impact of revenue generated from each city/country?**

SQL Queries:



Answer:







