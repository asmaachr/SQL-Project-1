What issues will you address by cleaning the data?
- creating back- during this project I've done table by table. in the future I will prefer to do it by database.
- empty columns
- change data type  ex timestamps, price in $..
- Remove redudant colunms from one table to another
- Remove duplicated data when it is obvious



Queries:
Below, provide the SQL queries you used to clean your data.
1- Sales_report:
```
<!-- # creating a back-up to the table sales_report#  -->

CREATE TABLE sales_report_copy AS
SELECT * FROM sales_report WHERE 1=0;

INSERT INTO sales_report_copy
SELECT * FROM sales_report;

SELECT * FROM sales_report_copy
```

```
<!-- # change the ratio format to percentage - calculation formula not clear so I couldn't fill blank values# -->

UPDATE sales_report
SET ratio = round(CAST(ratio AS Numeric)*100,2);
```

```
<!-- # removing leading and trailing space for significant varchar data ex product sku and name# -->
UPDATE sales_report
SET productsku = TRIM(both ' ' FROM productsku),
    name = TRIM(both ' ' FROM name);
```
```
<!-- # looking for empty column# -->
Select * from sales_report
where productsku is null
or total_ordered is null
or name is null
or stocklevel is null
or restockingleadtime is null
or sentimentscore is null
or sentimentmagnitude is null
or ratio is null;

<!-- # we have 78 records with null in Ratio value. Since I am not able to guess the calculation formula, I need input from SME to update this column # -->
```
```
<!-- # looking for duplicate productsku and name # -->
Select count(*) from sales_report
group  by productsku
Having count(*)>1

<!-- # there is no duplication from productsku#*/ -->

Select count(name) from sales_report
group  by productsku
Having count(name)>1

<!-- # there is no duplication from productname#*/ -->

2- Sales_by_sku
```
<!-- # I will start by creating a back-up to the table sales_report#  -->

CREATE TABLE sales_by_sku_copy AS
SELECT * FROM sales_by_sku WHERE 1=0;

INSERT INTO sales_by_sku_copy
SELECT * FROM sales_by_sku;

SELECT * FROM sales_by_sku_copy;
```
```
<!-- # removing leading and trailing space# -->
UPDATE sales_by_sku
SET productsku = TRIM(both ' ' FROM productsku)*/
```
```
<!-- # looking for duplication# -->
/*select count(*)
from sales_by_sku
group by sales_by_sku
having count(*)>1 */
/*#no duplication#*/
```

```
<!-- # comparison sales_by_sku and sales_report -->
select count(*)
from sales_by_sku
<!-- #results 462# -->

select count(*)
from sales_report
#results 454#
```
```
<!-- #Identifying products in sales_by_sku but not in sales_report and compare sales numbers# -->

Select sbs.productsku, sbs.total_ordered,sr.total_ordered
from sales_by_sku sbs
full join sales_report sr
on sbs.productsku = sr.productsku
where sr.total_ordered != sr.total_ordered or sr.total_ordered is null

<!-- # the results shows that:
1 the query without condition returned 462 rows therefore we have 462 items since we checked earlier from duplicate productskuids
2 the sales_by sku has more accurate numbers when it comes  totat items and total sales# or maybe the 8 items are terminated
3- sales_sku has exactly the sam columns with identic values - we can completely remove this table from the data base -->

3- products 
