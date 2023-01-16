Question 1: Is there any relationship between the time spend on site with units sold by visitors

SQL Queries:
```SQL
SELECT an.full_visitor_id
		,an.time_on_site
		,an.page_views
		,al.v2_product_name
		,al.v2_product_category
		,an.units_sold
FROM all_sessions al
JOIN analytics an
ON al.full_visitor_id = an.full_visitor_id
WHERE an.page_views IS NOT NULL
GROUP BY 1,2,3,4,5,6
ORDER BY 6 DESC
```
Answer: 
There is no relationship between the time spent on site and units sold by visitors. As we can see in the result, there are visitors who spent lots of time on the site, however, they didn't make any purchasing decision and there are visitors who spend less time on the site and make a purchase right away and vice versa


Question 2:  top products have the high number of time spent on site before visitors decide to purchase.

SQL Queries:
```SQL
SELECT 	an.full_visitor_id
		,city
		,country
		,an.time_on_site
		,an.page_views
		,al.v2_product_name
		,al.v2_product_category
		,an.units_sold
FROM all_sessions al
JOIN analytics an
ON al.full_visitor_id = an.full_visitor_id
WHERE an.page_views IS NOT NULL
AND city <> 'not available in demo dataset'
AND city <> '(not set)'
AND v2_product_category <> '(not set)'
AND v2_product_category <> '${escCatTitle}'
GROUP BY 1,2,3,4,5,6,7,8
ORDER BY 8 DESC
LIMIT 10
```
Answer:
Top products with high number of time spent on site before purchasing:

1. Waze Baby on Board Window Decal
2. Google Hard Cover Journal
3. 22 oz Android Bottle
4. 22 oz Youtube Bottle Infuser
5. Google Device Stand
6. Google Luggage Tag

"full_visitor_id"	"city"	"country"	"time_on_site"	"page_views"	"v2_product_name"	"v2_product_category"	"units_sold"
"1869599090210681270"	"Mountain View"	"United States"	412	17	"Waze Baby on Board Window Decal"	"Home/Accessories/Stickers/"	825
"1869599090210681270"	"Mountain View"	"United States"	3221	23	"Waze Baby on Board Window Decal"	"Home/Accessories/Stickers/"	825
"5033655160631190733"	"Sunnyvale"	"United States"	37	3	"Google Hard Cover Journal"	"Home/Office/Notebooks & Journals/"	401
"4739164425580137473"	"San Jose"	"United States"	339	10	"22 oz Android Bottle"	"Home/Drinkware/Water Bottles and Tumblers/"	173
"1729388992500827205"	"San Bruno"	"United States"	934	17	"22 oz YouTube Bottle Infuser"	"Home/Shop by Brand/YouTube/"	146
"1729388992500827205"	"San Bruno"	"United States"	869	26	"22 oz YouTube Bottle Infuser"	"Home/Shop by Brand/YouTube/"	130
"1729388992500827205"	"San Bruno"	"United States"	869	26	"22 oz YouTube Bottle Infuser"	"Home/Shop by Brand/YouTube/"	120
"4471415710206918415"	"Mountain View"	"United States"	330	13	"Google Device Stand"	"Home/Accessories/"	120
"1729388992500827205"	"San Bruno"	"United States"	271	5	"22 oz YouTube Bottle Infuser"	"Home/Shop by Brand/YouTube/"	100
"2475309279727826329"	"Seattle"	"United States"	106	9	"Google Luggage Tag"	"Home/Accessories/Fun/"	100


Question 3: Products which have lowest sentiment score less than 0

SQL Queries:
```SQL
WITH sentiment_table AS (
						 SELECT al.product_sku
								,al.v2_product_name
								,pr.sentiment_score
								,al.full_visitor_id
						 FROM all_sessions al
						 JOIN products pr
						 ON al.product_sku = pr.sku
						)
SELECT se.v2_product_name
		,se.sentiment_score
FROM sentiment_table se
JOIN analytics an
ON se.full_visitor_id = an.full_visitor_id
WHERE se.sentiment_score < 0
GROUP BY 1,2
ORDER BY 2 
LIMIT 10
```

Answer:
There are `40 sold products` which have sentiment scores less than 0. Moreover, `7 dog Frisbee` which has `-0.6`, is considered the `lowest one`. The customer sentiment score is computed by weighing each found sentiment event (positive or negative), with greater significance placed on the sentiment events that occurred toward the end of the interaction.  In essence, the customer sentiment score answers the question, did the customer leave the interaction happy, or dissatisfied? As a consequence, we need to take a look at the quality of the low sentiment score products and find the reason why customers are unhappy with those products

"v2_product_name"	"sentiment_score"
"7&quot; Dog Frisbee"	-0.6
"Google Men's Microfiber 1/4 Zip Pullover Blue/Indigo"	-0.5
"Google Women's Convertible Vest-Jacket Sea Foam Green"	-0.5
"Google Women's Short Sleeve V-Neck Tee Stone"	-0.5
"Google Women's Vintage Hero Tee Platinum"	-0.5
"YouTube Men's Vintage Henley"	-0.5
"Android Women's Short Sleeve Badge Tee Dark Heather"	-0.4
"Google Men's Performance Full Zip Jacket Black"	-0.4
"Google Pet Feeding Mat"	-0.4
"Google Women's Performance Full Zip Jacket Black"	-0.4


Question 4: Products have high sentiment score more than 0

SQL Queries:
```SQL
WITH sentiment_table AS (
						 SELECT al.product_sku
								,al.v2_product_name
								,pr.sentiment_score
								,al.full_visitor_id
						 FROM all_sessions al
						 JOIN products pr
						 ON al.product_sku = pr.sku
						)
SELECT se.v2_product_name
		,an.units_sold
		,se.sentiment_score
FROM sentiment_table se
JOIN analytics an
ON se.full_visitor_id = an.full_visitor_id
WHERE se.sentiment_score > 0
GROUP BY 1,2,3
ORDER BY 2 DESC
```
Answer: There are 650 sold products which have a sentiment score higher than 0. Moreover, Both `Google Collapsible Pet Bowl` and `Waze Baby On Board Window Decal` have very high sentiment scores which are 0.5 and 0.6 respectively. However, even though `Google Hard Cover Journal` has the highest sentiment score (0.9), the units sold are less than 50% of the above products. In my opinion, we should promote more the `Google Hard Cover Journal` to increase the sales

"v2_product_name"	"units_sold"	"sentiment_score"
"Google Collapsible Pet Bowl"	1000	0.5
"Waze Baby on Board Window Decal"	825	0.6
"Android Wool Heather Cap Heather/Black"	500	0.2
"Google Women's Fleece Hoodie"	500	0.3
"Google Women's Tee Purple"	500	0.2
"YouTube Leatherette Notebook Combo"	500	0.1
"Google Hard Cover Journal"	401	0.9
"Android Wool Heather Cap Heather/Black"	300	0.2
"Google Women's Fleece Hoodie"	300	0.3
"Google Women's Tee Purple"	300	0.2


Question 5: 

SQL Queries:

Answer:
