Answer the following questions and provide the SQL queries used to find the answer.

    
**Question 1: Which cities and countries have the highest level of transaction revenues on the site?**

Highest level of transaction revenue could be :
1- highest revenue or
2-Count of transactions with revenue
It seems revenue amount is different fromm a table to another. We will make a big assumption that the all_sessions table reflect the correct ammount.
SQL Queries:

1- Highest revenue
```
Select country, city,round(cast(sum(totaltransactionrevenue) as numeric),2) as totalrevenue
from all_sessions
where transactions=1
group by country, city
order by round(cast(sum(totaltransactionrevenue) as numeric),2)desc
Limit 10
```
2- Highest amount of transactions with revenue:
```
Select country, city, count(*)  as transwrevenue
from all_sessions
where transactions=1
group by country, city
order by count(*) desc
Limit 10
```

Answer:

1-Highest revenue
"United States"	"not available in demo dataset"	6092.56
"United States"	"San Francisco"	1564.32
"United States"	"Sunnyvale"	992.23
"United States"	"Atlanta"	854.44
"United States"	"Palo Alto"	608.00
"Israel"	"Tel Aviv-Yafo"	602.00
"United States"	"New York"	530.36
"United States"	"Mountain View"	483.36
"United States"	"Los Angeles"	479.48
"United States"	"Chicago"	449.52
![C:\Users\asmaa\OneDrive\Desktop\LHL\Projects\Project1\Answer 1-1.png]

 2- Hignest amount of transactions with revenue
```
![C:\Users\asmaa\OneDrive\Pictures\Answer1-2.png]
```
"United States"	"not available in demo dataset"	25
"United States"	"San Francisco"	12
"United States"	"Mountain View"	8
"United States"	"New York"	8
"United States"	"Sunnyvale"	4
"United States"	"Chicago"	3
"United States"	"Palo Alto"	3
"United States"	"Austin"	2
"United States"	"Atlanta"	2
"United States"	"Los Angeles"	2
````
**Question 2: What is the average number of products ordered from visitors in each city and country?**

For this question I will consider consider the number of products as number of sku#

SQL Queries:
```
Select country,
city,
count(productsku) as totalproductordered,
count (distinct fullvisitorid) as totalvistors,
count(productsku)/count(distinct fullvisitorid) as averageproductbyordre
from all_sessions
where transactions = 1
group by country, city
order by count(productsku)/count (distinct fullvisitorid)desc
```
Answer:

It seems that everywhere the average product by vistor is equal to one
"C:\Users\asmaa\OneDrive\Pictures\Answer 2.png"
"Australia"	"Sydney"	1	1	1
"Canada"	"New York"	1	1	1
"Canada"	"Toronto"	1	1	1
"Israel"	"Tel Aviv-Yafo"	1	1	1
"Switzerland"	"Zurich"	1	1	1






**Question 3: Is there any pattern in the types (product categories) of products ordered from visitors in each city and country?**
By country by city
```
SQL Queries:
Select country,
city,
v2productcategory,
count(*) as countbycategory
from all_sessions
where transactions = 1
group by country,city, v2productcategory
order by country,count(v2productcategory)desc, city
```
By country
```
Select country,
v2productcategory,
count(*) as countbycategory
from all_sessions
where transactions = 1
group by country, v2productcategory
order by country, count(v2productcategory) desc
```









Answer:
By country and city
"C:\Users\asmaa\OneDrive\Pictures\Answer 3-1.png"

"Australia"	"Sydney"	"Nest-USA"	1
"Canada"	"New York"	"Home/Apparel/Men's/Men's-Outerwear/"	1
"Canada"	"Toronto"	"Apparel"	1
"Israel"	"Tel Aviv-Yafo"	"Home/Nest/Nest-USA/"	1
"Switzerland"	"Zurich"	"Home/Apparel/Men's/Men's-T-Shirts/"	1
"United States"	"not available in demo dataset"	"Home/Nest/Nest-USA/"	6
"United States"	"San Francisco"	"Home/Nest/Nest-USA/"	3
"United States"	"Mountain View"	"Apparel"	2
"United States"	"not available in demo dataset"	"Electronics"	2
"United States"	"not available in demo dataset"	"Home/Apparel/Men's/Men's-T-Shirts/"	2
"United States"	"not available in demo dataset"	"Home/Apparel/Women's/Women's-T-Shirts/"	2
"United States"	"not available in demo dataset"	"Nest-USA"	2
"United States"	"Palo Alto"	"Home/Nest/Nest-USA/"	2
"United States"	"Atlanta"	"Home/Apparel/Men's/Men's-T-Shirts/"	1
"United States"	"Atlanta"	"Bags"	1
"United States"	"Austin"	"Home/Shop by Brand/Google/"	1
"United States"	"Austin"	"Apparel"	1
"United States"	"Chicago"	"Lifestyle"	1
"United States"	"Chicago"	"Home/Office/Writing Instruments/"	1
"United States"	"Chicago"	"Nest-USA"	1
"United States"	"Columbus"	"(not set)"	1
"United States"	"Houston"	"Home/Drinkware/"	1
"United States"	"Los Angeles"	"Home/Nest/Nest-USA/"	1
"United States"	"Los Angeles"	"Home/Apparel/Women's/"	1
"United States"	"Mountain View"	"Home/Apparel/Kid's/Kid's-Infant/"	1
"United States"	"Mountain View"	"Nest-USA"	1
"United States"	"Mountain View"	"Home/Nest/Nest-USA/"	1
"United States"	"Mountain View"	"Home/Electronics/Electronics Accessories/"	1
"United States"	"Mountain View"	"Home/Apparel/Women's/Women's-Outerwear/"	1
"United States"	"Mountain View"	"Waze"	1
"United States"	"Nashville"	"Nest-USA"	1
"United States"	"New York"	"Home/Shop by Brand/"	1
"United States"	"New York"	"Home/Accessories/Fun/"	1
"United States"	"New York"	"Apparel"	1
"United States"	"New York"	"${escCatTitle}"	1
"United States"	"New York"	"Home/Apparel/Kid's/Kid's-Infant/"	1
"United States"	"New York"	"Home/Apparel/Men's/Men's-Performance Wear/"	1
"United States"	"New York"	"Home/Nest/Nest-USA/"	1
"United States"	"New York"	"Home/Apparel/Men's/Men's-T-Shirts/"	1
"United States"	"not available in demo dataset"	"Waze"	1
"United States"	"not available in demo dataset"	"Apparel"	1
"United States"	"not available in demo dataset"	"Bags"	1
"United States"	"not available in demo dataset"	"Home/Accessories/Pet/"	1
"United States"	"not available in demo dataset"	"Home/Apparel/"	1
"United States"	"not available in demo dataset"	"Home/Apparel/Kid's/Kid's-Infant/"	1
"United States"	"not available in demo dataset"	"Home/Bags/"	1
"United States"	"not available in demo dataset"	"Home/Drinkware/"	1
"United States"	"not available in demo dataset"	"Home/Office/Notebooks & Journals/"	1
"United States"	"not available in demo dataset"	"Home/Shop by Brand/Google/"	1
"United States"	"not available in demo dataset"	"Lifestyle"	1
"United States"	"Palo Alto"	"Nest-USA"	1
"United States"	"San Bruno"	"Home/Apparel/Men's/"	1
"United States"	"San Francisco"	"Home/Accessories/Drinkware/"	1
"United States"	"San Francisco"	"Home/Accessories/Fun/"	1

By country
"C:\Users\asmaa\OneDrive\Pictures\Answer3-2.png"



**Question 4: What is the top-selling product from each city/country? Can we find any pattern worthy of noting in the products sold?**
Since we don't have the quantity sold in all-sessions. we join the tan

SQL Queries:



Answer:





**Question 5: Can we summarize the impact of revenue generated from each city/country?**

SQL Queries:
```
Select country,
city,
round(cast(sum(totaltransactionrevenue) as numeric),2) as totalrevenue,
round(cast((sum(totaltransactionrevenue)*100/ (SELECT sum(totaltransactionrevenue) from all_sessions))as numeric),2)as percentage
from all_sessions
where transactions=1
Group By country,city
Order By round(cast(sum(totaltransactionrevenue) as numeric),2) desc
```

Answer:

!["C:\Users\asmaa\OneDrive\Pictures\Answer 5.png"]




