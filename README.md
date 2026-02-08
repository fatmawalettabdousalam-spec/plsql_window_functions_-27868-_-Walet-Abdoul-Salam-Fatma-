# plsql_window_functions_-27868-_-Walet-Abdoul-Salam-Fatma-
    #PROJECT OVERVIEW  
This project shows how to build and analyze a store database. I created the tables, added the data, and used SQL queries to find useful information about sales and customers.

Step 1: Updated Problem Definition
  
Business Context
•	Company Type: "The Tech Spot," a local store.
•	Department: General Store Management.
•	Industry: Electronics industry.

Data Challenge
The shop owner needs to understand which customers are actually buying items and which products are just sitting on the shelves. Currently, it is hard to tell if a lack of sales is due to uninterested customers or products that aren't being promoted correctly.

Expected Outcome A set of reports that identifies "inactive" customers for email follow-ups and calculates which high-value electronics are driving the most revenue.

Step 2: Success Criteria

1.	Top Selling Products: I will use RANK() to find which products bring in the most money so we can see what the "Top 5" items are.
2.	Daily Running Total: I will use SUM() OVER() to calculate a running total of the sales. This helps us see how our money adds up day by day.
3.	Comparing Past Sales: I’ll use LAG() to look at the previous sale next to the current one. This is to see if customers are starting to spend more or less than they used to.
4.	Customer Tiers: I’ll use NTILE(4) to split the customers into 4 groups. This shill show who the "Big Spenders" are compared to the "Budget" shoppers.
5.	Price Averages: I’ll use AVG() OVER() to find the moving average of the sales prices. This helps smooth out the data if one person buys something really expensive

Step 3: Database Schema I designed a relational database with three tables:

1.	Customers Table: Contains cust_id (PK), name, and region.
2.	Products Table: Contains prod_id (PK), prod_name, and price.
3.	Sales Table: Contains sale_id (PK), sale_date, total_amount, plus cust_id (FK) and prod_id (FK) to link the tables together.

Entity Relationship Diagram (ERD):

<img width="587" height="430" alt="Diagram" src="https://github.com/user-attachments/assets/4a943967-403f-4f9a-af60-d1369e952656" />

Step 4: SQL JOINs

1. INNER JOIN

SELECT Customers.cust_name, Products.prod_name
FROM Customers,
JOIN Sales ON Customers.cust_id = Sales.cust_id
JOIN Products ON Products.prod_id = Sales.prod_id;

Interpretation: I used this join to see a clean list of successful sales that have both a customer name and a product name attached. This helps the business see exactly which people are buying which items without any empty data.

<img width="463" height="394" alt="1 inner join" src="https://github.com/user-attachments/assets/3f66592a-99ec-465b-a053-7b3618277195" />

2. LEFT JOIN

SELECT cust_name, sale_id
FROM Customers
LEFT JOIN Sales ON Customers.cust_id = Sales.cust_id
WHERE sale_id IS NULL;

Interpretation: This query identifies customers who haven't made a purchase yet. It creates a target group of inactive customers so we can send them a discount to encourage their first order.

<img width="436" height="283" alt="2 left join" src="https://github.com/user-attachments/assets/e8063b89-357b-4bdb-9c34-dcb09ebbd846" />

3. RIGHT JOIN 

SELECT prod_name, sale_id
FROM Sales
RIGHT JOIN Products ON Sales.prod_id = Products.prod_id
WHERE sale_id IS NULL;

Interpretation: I ran this to find products in our inventory that have never been sold. This helps the store owner identify dead stock that might need to be put on sale or removed from the catalog.

<img width="467" height="280" alt="3 right join" src="https://github.com/user-attachments/assets/cda0834c-24db-4491-9c19-a7e3ad1bb612" />

4. FULL OUTER JOIN

SELECT cust_name, prod_name
FROM Customers
FULL JOIN Sales ON Customers.cust_id = Sales.cust_id
FULL JOIN Products ON Sales.prod_id = Products.prod_id;

Interpretation: This gives me a complete view of every customer and every product in the database at once. It helps me see all the data in one place, including the people who haven't shopped and the items that haven't sold.

<img width="437" height="472" alt="4 full join" src="https://github.com/user-attachments/assets/9f6ffe46-64b4-43ee-8d00-d65d63644b03" />

5. SELF JOIN

SELECT A.cust_name, B.cust_name, A.region
FROM Customers A, Customers B
WHERE A.region = B.region
AND A.cust_id < B.cust_id;

Interpretation: Pairs up customers from the same region to see where most of our shoppers are located.

<img width="339" height="338" alt="5 self join" src="https://github.com/user-attachments/assets/0bd72ef3-ba92-41eb-8fc6-70b0a6f8e19d" />

Step 5: WINDOW FUNCTIONs

1.Ranking Functions

SELECT prod_name, total_amount,
       RANK()       OVER (ORDER BY total_amount DESC) as rank_pos,
       DENSE_RANK() OVER (ORDER BY total_amount DESC) as dense_pos
FROM Sales, Products
WHERE Sales.prod_id = Products.prod_id;

Ranking (RANK/DENSE_RANK) Interpretation: I used these to rank our items from most expensive to cheapest. It clearly shows which products are our "Top Tier" items and handles ties perfectly.

<img width="512" height="415" alt="6 ranking functions" src="https://github.com/user-attachments/assets/4981bb05-8cc3-4d2f-8d07-a20bf5d08cf0" />

2. Aggregate Window Functions

SELECT sale_id, total_amount,
       SUM(total_amount) OVER (ORDER BY total_amount ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) as rows_total,
       SUM(total_amount) OVER (ORDER BY total_amount RANGE BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) as range_total
FROM Sales;

Aggregates (SUM/AVG) Interpretation: These show a running total of our money as each sale happens. It helps the owner see how much the bank account is growing after every single transaction.

<img width="876" height="399" alt="7 aggregate functions" src="https://github.com/user-attachments/assets/01ab8894-0107-48fd-9c3e-397f424ee8b0" />

3. Navigation Functions

SELECT sale_id, total_amount,
       LAG(total_amount) OVER (ORDER BY sale_id) as previous_sale,
       LEAD(total_amount) OVER (ORDER BY sale_id) as next_sale
FROM Sales;

Navigation (LAG/LEAD) Interpretation: This puts the "previous" sale amount right next to the current one. It makes it really easy to see if a customer spent more or less than they did the last time.

<img width="515" height="398" alt="8 navigation functions" src="https://github.com/user-attachments/assets/51f4f130-cacb-413b-a95b-25c53ed9758c" />

4. Distribution Functions

SELECT  cust_id, total_amount,
       NTILE(4) OVER (ORDER BY total_amount DESC) as quartile
FROM Sales;

Distribution (NTILE) Interpretation: I used this to split our customers into 4 equal groups based on their spending. It helps us see who our best customers are and who spends the least.

<img width="478" height="375" alt="9 distribution functions" src="https://github.com/user-attachments/assets/528ce9d0-a052-4cc6-bc18-648c8470cb33" />

Step 7: Results Analysis

1. Descriptive (What happened?)
My analysis shows that most of our revenue comes from a small group of high-spending customers in specific regions. The data also identifies several products that have remained in the inventory for a long time without a single sale.

2. Diagnostic (Why did it happen?)
The data shows that our occasional shoppers are not buying our most expensive products. By using the LEFT JOIN and NTILE functions, I found that we are missing sales because we haven't offered the right discounts to the right groups.

3. Prescriptive (What should be done next?)
We should start a loyalty program for our top spenders to keep them coming back. We also need to have a clearance sale for the products that aren't selling so we can make room for new items.

Step 8: References

W3Schools: SQL Tutorial for Join Logic and Syntax.
Geeksforgeeks: Windows functions in SQL 
ToughtSpot: SQL Window Functions | Avanced SQL 

“All sources were properly cited. Implementations and analysis represent original work. No AI-generated content was copied without attribution or adaptation.”







