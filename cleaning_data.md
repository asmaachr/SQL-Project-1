What issues will you address by cleaning the data?
# Summary
## Creating back
During this project I've done table by table. in the future I will prefer to do it by database.
## General cleaning points
- change data type  ex timestamps, price in $..
- Remove redudant colunms from one table to another
- Remove empty column or with similar value for all rows
- Remove duplicated data when it is obvious   
## tables decision summary
- Sales_by_sku to be removed from data base
- Sales_report to be removed stocklevel, restockingleadtime
Key:Productsku (foreign to products)
- Products: to be removed sentimentscore, sentimentmagnitude
Primary Key: Sku

```
ALTER TABLE products ADD PRIMARY KEY (sku);

ALTER TABLE sales_report
    ADD CONSTRAINT fk_productsku FOREIGN KEY (productsku) REFERENCES products (sku);
```
-  Analytics:removed userid, Social engagement level
I believe this table is missing a product field
No identified primary key- should be grouped by fullvisitorid and visitid to be used for quantitave fields
- All_sessions: removed currency,searchKeyword, itemrevenue, itemprice, productrefund amount
Primary key: Create Session id with status (identify visitor actions on website) or breakdown table to two 
a-transactions with revenues
b-Visitor actions in the web
May be remove rows with "total revenue in all_sessions" after SME inputs
- Important points:
Total revenue in all_sessions much lower total revenue in analytics
Total skus in all Sessions lower by 141 by comparison with Sales_report
8 sku # in sales_by-sku but no whereelse considered discarded

# Queries:

##  Sales_report:

### Creating a back-up to the table sales_report
```
CREATE TABLE sales_report_copy AS
SELECT * FROM sales_report WHERE 1=0;

INSERT INTO sales_report_copy
SELECT * FROM sales_report;

SELECT * FROM sales_report_copy
```


### Change the ratio format to percentage 
Calculation formula not clear so I couldn't fill blank values
```
UPDATE sales_report
SET ratio = round(CAST(ratio AS Numeric)*100,2);
```

### Removing leading and trailing space for significant varchar data ex product sku and name
```
UPDATE sales_report
SET productsku = TRIM(both ' ' FROM productsku),
    name = TRIM(both ' ' FROM name);
```

### Looking for empty column
```
Select * from sales_report
where productsku is null
or total_ordered is null
or name is null
or stocklevel is null
or restockingleadtime is null
or sentimentscore is null
or sentimentmagnitude is null
or ratio is null
```

There are 78 records with null as value for Ratio column. Since I am not able to guess the calculation formula, I need input from SME to update this column.


### Looking for duplicate productsku and name 
```
Select productsku, count(*) from sales_report
group  by productsku
Having count(*)>1
```
=> there is no duplication

```
Select name, count(name) from sales_report
group  by productsku
Having count(name)>1
```

=> there are duplication however the product name is not unique to a sku (no brand, general name ex blue t-shirt)

## Sales_by_sku

### Creating a back-up to the table sales_report
```
CREATE TABLE sales_by_sku_copy AS
SELECT * FROM sales_by_sku WHERE 1=0;

INSERT INTO sales_by_sku_copy
SELECT * FROM sales_by_sku;

SELECT * FROM sales_by_sku_copy;
```

### Removing leading and trailing space
```
UPDATE sales_by_sku
SET productsku = TRIM(both ' ' FROM productsku)*/
```
### Looking for duplication

```
select productsku, count(*)
from sales_by_sku
group by productsku
having count(*)>1
```
=> no duplication



## Comparison sales_by_sku and sales_report
```
select count(*)
from sales_by_sku
```
results 462 rowsa
```
select count(*)
from sales_report
```
results 454

Identifying products in sales_by_sku but not in sales_report and compare sales numbers
```
Select sbs.productsku, sbs.total_ordered,sr.total_ordered
from sales_by_sku sbs
full join sales_report sr
on sbs.productsku = sr.productsku
where sr.total_ordered != sr.total_ordered or sr.total_ordered is null
```
The results show that:
- the query without condition returned 462 rows therefore we have 462 items confirm #rows in sku_by_sale
- the sales_by sku has more accurate numbers when it comes  totat items and total sales numbers or maybe the following 8 items are terminated
Sku         qty ordered
"9184677"	0
"9182779"	0
"9182763"	0
"GGOEYAXR066128"	3
"9182182"	0
"9180753"	0
"9184663"	0
"GGOEGALJ057912"	2#
- sales_sku has exactly the same columns with identique values - we can completely remove this table from the data base if we ignore the items above.

## Products Table

### Creating a back-up to the table sales_report
```
CREATE TABLE products_copy AS
SELECT * FROM products WHERE 1=0;

INSERT INTO products_copy
SELECT * FROM products;

SELECT * FROM products_copy
```

### Cleaning the empty space in varchar# -->
```
UPDATE products
SET sku = TRIM(both ' ' FROM sku),
name = TRIM(both ' ' FROM name)
```

The table seems to track the inventory by product.
It has 1092 records.

### Looking for blank columns and duplicate rows

```
SELECT sku, count(*) FROM products
group by sku
Having count(*)>1
```

 We will skip name duplication because similarly the product name is not unique to a sku

### Comparing  the value of the column to products to the similar column in sales_report and sales_sku -->
a- product sku

```
Select count(*) from products
```
Results 1092

```
select count(*)
from products p
full join sales_by_sku sbs
on p.sku = sbs.productsku
```
Results 1100 records#

#### Identifing the sku that are in sale_by_sku but not in produtcs#
```
select *
from products p
full join sales_by_sku sbs
on p.sku = sbs.productsku
where p.sku is null 
```
"9184677"	0
"9182779"	0
"9182763"	0
"GGOEYAXR066128"	3
"9182182"	0
"9180753"	0
"9184663"	0
"GGOEGALJ057912"	2
 
 Same 8 pordict sku # as the sales_report. 
A quick check in all_sessions ans analytics for the products quantities reveals records.
Therefore we will ignore this items since they don't exist in any other table. we will make the assumption that these products were terminated.

Following this decision the sku_by_sale table is redudant: to be removed

####  Redudant colunms
```
select p.sentimentmagnitude, sr.sentimentmagnitude
from products p
join sales_report sr
on p.sku = sr.productsku
where  p.sentimentmagnitude != sr.sentimentmagnitude
```
The following is conditions are run seperatly for quick reading
or p.name!=sr.name
or p.orderedquantity!=sr.total_ordered
or p.stocklevel!= sr.stocklevel
or p.frestockingleadtime != sr.restockingleadtime
or p.sentimentscore != sr.sentimentscore
or p.sentimentmagnitude != sr.sentimentmagnitude

The colunms orderquantity and total_ordered seems to be different by 371 records
These two fields seems different. Other ones are the same  therefore these colunms are redundant between the tables products and sales_report.

Cleaning up action to be taken (Not act upon to allow above action to run)
- remove sale_by_sku from data base (drop table)
- products : remove sentimentscore,sentimentmagnitude (drop colunm)
- sales_report: remove sr.restockingleadtime, stocklevel, 
ratio (could be removed but waiting for SME input)

##  Analytics table

Similar exercise as above except the following:

### Empty columns
```
SELECT count(*) From analytics
where userid is not  null
```
Results:0
```
SELECT count(*) From analytics
where socialEngagementType != 'Not Socially Engaged' 
```
Results:0

Action => remove both column

### changing visitstarttime
- Checking that the time is time as Unix timestamps
```
 Select To_timestamp (visitstarttime) as formatted_visitstarttime
from analytics
```
When trying to update the table with above value:
I get error message, therefore I created a temporary columns where
I stored the value above in temporary column,
Then I changed the datatype of visitstartingtime and transfering the values to the temp column
Then I removed the temp column
```
ALTER TABLE analytics ADD COLUMN create_time_holder TIMESTAMP without time zone NULL;
UPDATE analytics SET create_time_holder = To_timestamp (visitstarttime);
ALTER TABLE analytics ALTER COLUMN visitstarttime TYPE TIMESTAMP without time zone USING create_time_holder;
ALTER TABLE analytics DROP COLUMN create_time_holder
```
### Duplication
Similar queries as above.
When we look for duplication, we recognize the following we have
same visitor/visitid but units_sold with different unit prices or same unit price.

We have some duplicated records however because it is
analytics for an ecommerce business we can assume that the vistor was looking to products with similar unit cost. Since we don't have any identication to the item visited. We need SME inputs for further cleaning 

#DECISION: Remove userid, socialEngagementType, 
we may also remove rows without revenue since the questions of analysis are mainly focused on that but for potential future analysis I prefered to keep them for now and use filters.

## All_sessions table
Similar process except

This table has many colunms like the previous one but because revenue don't match ( question 1 Starting with data) and total # sku in all_sessions is lower than sales 
report, I decided to leave it as is except removing empty columns.


### empty columns
```
Select count(*) from all_sessions
```
result: 15134

TheseUsing silmilar queries as above the following columns have no records:
productrefundamount
itemquantity
itemrevenue
searchKeyword 

Also the only currency is USD therefore this column to be removed


### removing empty columns
```
ALTER TABLE all_sessions DROP COLUMN currencycode
ALTER TABLE all_sessions DROP COLUMN searchKeyword
ALTER TABLE all_sessions DROP COLUMN itemrevenue
ALTER TABLE all_sessions DROP COLUMN itemquantity
ALTER TABLE all_sessions DROP COLUMN productrefundamount
```

### comparison of sku number between all_sessions and sales_report 

```
Select  count(*)
from all_sessions a
join Sales_report sr on sr.productsku = a.productsku
join sumanalytics sa on sa.fullvisitorid =a.fullvisitorid and sa.visitid=a.visitid
where total_ordered!=0 and a.transactions is null
```
It seems that some products were ordered on the sales reports by they are not reflected on all sessions exactly 2223 rows = 141 sku numbers
Therefore some revenue won't be counted for in all_sessions. This require further investigation.

Since we don't have access to the SME and I am not clear on the process of identifying a sale. I prefer to keep all 
rows and not to remove the ones with blank value. I will use filters for my analysis

## Adding keys to the two tables
```
ALTER TABLE products ADD PRIMARY KEY (sku);

ALTER TABLE sales_report
    ADD CONSTRAINT fk_productsku FOREIGN KEY (productsku) REFERENCES products (sku);
```
Other Tables needs more clarification.
