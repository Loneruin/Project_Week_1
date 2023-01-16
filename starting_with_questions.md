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
				,total_ordered
FROM sale_table st
JOIN all_sessions al
ON st.product_sku = al.product_sku
WHERE city <> 'not available in demo dataset'
AND city <> '(not set)'
AND v2_product_category <> '(not set)'
GROUP BY 1,2,3,4,5
ORDER BY 5 DESC
```

Answer:
Top-selling product from each city/country is Ballpoint LED pen. As we can see from the result, most of the top-selling products come from `home/office` category such as `Ballpoint LED pen`, `"Google 17oz Stainless Steel Sport Bottle` and `Leatherette Journal` which have more resonable price so visitor can easily order those products without hesistation




**Question 5: Can we summarize the impact of revenue generated from each city/country?**

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
				,EXTRACT(YEAR FROM al.date) AS year
				,al.v2_product_name
				,al.v2_product_category
				,SUM(total_ordered * product_price) AS revenue
FROM sale_table st
JOIN all_sessions al
ON st.product_sku = al.product_sku
WHERE city <> 'not available in demo dataset'
AND city <> '(not set)'
AND v2_product_category <> '(not set)' 
AND v2_product_category <> '${escCatTitle}'
GROUP BY 1,2,3,4,5
ORDER BY 3,6 DESC
```

Answer:
We can see from the data output that most of the revenue generated from most of the city in United State comes from Google products such as NEST thermostat and NEST security camera:
"country"	"city"	"v2_product_name"	"v2_product_category"	"revenue"
"United States"	"Mountain View"	"Nest® Cam Indoor Security Camera - USA"	"Home/Nest/Nest-USA/"	401574.00
"United States"	"Mountain View"	"Nest® Cam Outdoor Security Camera - USA"	"Home/Nest/Nest-USA/"	271824.00
"United States"	"Mountain View"	"Nest® Learning Thermostat 3rd Gen-USA - Stainless Steel"	"Home/Nest/Nest-USA/"	229266.00
"United States"	"Palo Alto"	"Nest® Cam Outdoor Security Camera - USA"	"Home/Nest/Nest-USA/"	138096.00
"United States"	"San Francisco"	"Nest® Learning Thermostat 3rd Gen-USA - Stainless Steel"	"Home/Nest/Nest-USA/"	121636.00
"United States"	"Sunnyvale"	"Nest® Cam Outdoor Security Camera - USA"	"Home/Nest/Nest-USA/"	115808.00
"United States"	"New York"	"Nest® Cam Outdoor Security Camera - USA"	"Home/Nest/Nest-USA/"	102480.00
"United States"	"Sunnyvale"	"Nest® Learning Thermostat 3rd Gen-USA - Stainless Steel"	"Home/Nest/Nest-USA/"	84224.00
"United States"	"Palo Alto"	"Nest® Learning Thermostat 3rd Gen-USA - Stainless Steel"	"Home/Nest/Nest-USA/"	74824.00
"United States"	"Palo Alto"	"Nest® Cam Indoor Security Camera - USA"	"Home/Nest/Nest-USA/"	73032.00






