What are your risk areas? Identify and describe them.
There sre many risks:
1- All sessions table doesn't have the realized revenue but potential revenue 
example refund revenue, in cart revenue...
2- City with Unkonw names are not real related to a system demo
3- The period of the tables are not matching
4- We made the assumption that un order = one product in average to use unit_solds from analytics
5- Data entry error
6- wrong understanding for data field
7- upolading duplicate data


QA Process:
Describe your QA process and include the SQL queries used to execute it.

I believe the first step here is to talk to subject matter Expert and re-ass the data and queries based on that.

Than rework the table All_sessions and analytics to comeup with tables with primary key and establish clear relations in the ERD

If we assume that our thought process is correct among few we can develop following queries
- checking for unique fields

example:
QA1
Select sku, sum(1) As count
from products
Group By 1
Having sum(1)>1
![C:\Users\asmaa\OneDrive\Desktop\LHL\Projects\Project1\SQL-Project-1\QA -1.png](QA%20-1.png)
No duplicate

QA2
Select sku, sum(1) As count
from sales_report
Group By 1
Having sum(1)>1
No duplicate

QA3
Select fullvisitorid, sum(1) As count
from all_sessions
where transactions =1
Group By 1
Having sum(1)>1

![C:\Users\asmaa\OneDrive\Desktop\LHL\Projects\Project1\SQL-Project-1\QA-3.png](QA-3.png)

It shows that we have one case with same fullvisitorID and visitID
therefore our assumption for question 4 is not true. We need to understand better the tables and transform them so we can have one key by table

Looking for empty values example

SELECT TransactionID, “missing_TransactionID” AS reason
FROM all_sessions
WHERE TransactionID IS NULL and transactions=1

![C:\Users\asmaa\OneDrive\Desktop\LHL\Projects\Project1\SQL-Project-1\QA4-1.png](QA4-1.png)


ERD picture:

![C:\Users\asmaa\OneDrive\Desktop\LHL\Projects\Project1\SQL-Project-1\ERDproject1.png](ERD%20project%201.png)

