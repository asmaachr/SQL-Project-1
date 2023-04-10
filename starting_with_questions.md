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
For both, US/ San Francisco has the highest value if ignore the unknown cities
1-Highest revenue

![C:\Users\asmaa\OneDrive\Desktop\LHL\Projects\Project1\SQL-Project-1\Answer 1-1.png](Answer%201-1.png)




 2- Hignest amount of transactions with revenue

![C:\Users\asmaa\OneDrive\Desktop\LHL\Projects\Project1\SQL-Project-1\Answer1-2.png](Answer1-2.png)




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

It seems that everywhere the average product ordered by visitor is equal to one

![C:\Users\asmaa\OneDrive\Desktop\LHL\Projects\Project1\SQL-Project-1\Answer 2.png](Answer%202.png)



**Question 3: Is there any pattern in the types (product categories) of products ordered from visitors in each city and country?**

 It seems to point-out to product category Home/Nest/Nest care in US mainly

### By country by city
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
### By country
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

![C:\Users\asmaa\OneDrive\Desktop\LHL\Projects\Project1\SQL-Project-1\Answer 3-1.png](Answer%203-1.png)



By country
Home

![C:\Users\asmaa\OneDrive\Desktop\LHL\Projects\Project1\SQL-Project-1\Answer3-2.png](Answer3-2.png)

It is seems to point out to Home/Nest/Nest care

**Question 4: What is the top-selling product from each city/country? Can we find any pattern worthy of noting in the products sold?**

Since the average product by visitor is one. I will use this as an assumption for the following:

For this query , I retrieve the Units_sold form analytics 
by fullvisitorid and visitid when revenue is not zero, joined the resulted table with all_sessions using both ids as the connectors with the condition transactions =1. 
Then I ranked product according to unit_sold by country and city. 



SQL Queries:
```
With total_sales_by_sku as (Select 
country,
city,
productsku,
v2productname,							
sum(temp.qtysold) as totalqty
from all_sessions a
join (
Select 
    fullvisitorid, 
    visitid, 
    SUM(units_sold) as qtysold
From 
    analytics
Where 
    revenue != 0
GROUP BY 
    fullvisitorid, visitid) temp
On  a.fullvisitorid = temp.fullvisitorid and a.visitid = temp.visitid
where transactions = 1
group by country, city, productsku,v2productname
order by country, city, productsku, sum(temp.qtysold) desc)

Select 
   country,
   city,
   productsku,
   v2productname,
   totalqty,
   RANK() OVER(PARTITION BY country, city ORDER BY totalqty DESC) AS rank
From 
   total_sales_by_sku

Order By 
   country, city
```



Answer:
This is just a snapshot
![C:\Users\asmaa\OneDrive\Desktop\LHL\Projects\Project1\SQL-Project-1\Answer 4-1.png](Answer%204-1.png)

It seems that the product in trends  is Nest® Learning Thermostat 3rd Gen-USA


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
Answer

![C:\Users\asmaa\OneDrive\Desktop\LHL\Projects\Project1\SQL-Project-1\Answer 5.png](Answer%205.png)Answer:



It seems that the product most sold is: Nest® Learning Thermostat 3rd Gen-USA



