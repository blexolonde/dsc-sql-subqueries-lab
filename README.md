# SQL Subqueries - Lab

## Introduction

Now that you've seen how subqueries work, it's time to get some practice writing them! Not all of the queries will require subqueries, but all will be a bit more complex and require some thought and review about aggregates, grouping, ordering, filtering, joins and subqueries. Good luck!  

## Objectives

You will be able to:

* Write subqueries to decompose complex queries

## CRM Database ERD

Once again, here's the schema for the CRM database you'll continue to practice with.

<img src="https://curriculum-content.s3.amazonaws.com/data-science/images/Database-Schema.png" width="600">

## Connect to the Database

As usual, start by importing the necessary packages and connecting to the database `data.sqlite`.


```python
import sqlite3
```


```python
# Your code here; create the connection
```

## Write an Equivalent Query using a Subquery

The following query works using a `JOIN`. Rewrite it so that it uses a subquery instead.

```
SELECT
    customerNumber,
    contactLastName,
    contactFirstName
FROM customers
JOIN orders 
    USING(customerNumber)
WHERE orderDate = '2003-01-31'
;
```


```python
# Your code here
SELECT
    customerNumber,
    contactLastName,
    contactFirstName
FROM customers
WHERE customerNumber IN (
    SELECT customerNumber
    FROM orders
    WHERE orderDate = '2003-01-31'
);

```

## Select the Total Number of Orders for Each Product Name

Sort the results by the total number of items sold for that product.


```python
# Query 2
query2 = """
SELECT
    p.productName,
    COUNT(DISTINCT o.customerNumber) AS totalCustomers
FROM products p
JOIN orderdetails od
    ON p.productCode = od.productCode
JOIN orders o
    ON od.orderNumber = o.orderNumber
GROUP BY p.productName
ORDER BY totalCustomers DESC;
"""
cursor.execute(query2)
result2 = cursor.fetchall()

print("\nQuery 2: Total Number of People Who Ordered Each Product")
for row in result2:
    print(f"Product: {row[0]}, Total Customers: {row[1]}")


```

## Select the Product Name and the  Total Number of People Who Have Ordered Each Product

Sort the results in descending order.

### A quick note on the SQL  `SELECT DISTINCT` statement:

The `SELECT DISTINCT` statement is used to return only distinct values in the specified column. In other words, it removes the duplicate values in the column from the result set.

Inside a table, a column often contains many duplicate values; and sometimes you only want to list the unique values. If you apply the `DISTINCT` clause to a column that has `NULL`, the `DISTINCT` clause will keep only one NULL and eliminates the other. In other words, the DISTINCT clause treats all `NULL` “values” as the same value.


```python
# Your code here
# Hint: because one of the tables we'll be joining has duplicate customer numbers, you should use DISTINCT

query3 = """
SELECT
    p.productName,
    COUNT(DISTINCT o.customerNumber) AS totalCustomers
FROM products p
JOIN orderdetails od
    ON p.productCode = od.productCode
JOIN orders o
    ON od.orderNumber = o.orderNumber
GROUP BY p.productName
ORDER BY totalCustomers DESC;
"""
```

## Select the Employee Number, First Name, Last Name, City (of the office), and Office Code of the Employees Who Sold Products That Have Been Ordered by Fewer Than 20 people.

This problem is a bit tougher. To start, think about how you might break the problem up. Be sure that your results only list each employee once.


```python
# Your code here
query4= """
SELECT DISTINCT
    e.employeeNumber,
    e.firstName,
    e.lastName,
    o.city,
    e.officeCode
FROM employees e
JOIN offices o
    ON e.officeCode = o.officeCode
JOIN customers c
    ON e.employeeNumber = c.salesRepEmployeeNumber
JOIN orders od
    ON c.customerNumber = od.customerNumber
JOIN orderdetails odd
    ON od.orderNumber = odd.orderNumber
WHERE odd.productCode IN (
    SELECT p.productCode
    FROM products p
    JOIN orderdetails od
        ON p.productCode = od.productCode
    JOIN orders o
        ON od.orderNumber = o.orderNumber
    GROUP BY p.productCode
    HAVING COUNT(DISTINCT o.customerNumber) < 20
);
"""

# Execute the query
cursor.execute(query)
results = cursor.fetchall()

# Print the results
for row in results:
    print(f"Employee Number: {row[0]}, First Name: {row[1]}, Last Name: {row[2]}, City: {row[3]}, Office Code: {row[4]}")


```

## Select the Employee Number, First Name, Last Name, and Number of Customers for Employees Whose Customers Have an Average Credit Limit Over 15K


```python
# Your code here
query5 = """
SELECT
    e.employeeNumber,
    e.firstName,
    e.lastName,
    COUNT(c.customerNumber) AS numberOfCustomers
FROM employees e
JOIN customers c
    ON e.employeeNumber = c.salesRepEmployeeNumber
GROUP BY e.employeeNumber, e.firstName, e.lastName
HAVING AVG(c.creditLimit) > 15000;
"""

# Execute the query
cursor.execute(query)
results = cursor.fetchall()

# Print the results
for row in results:
    print(f"Employee Number: {row[0]}, First Name: {row[1]}, Last Name: {row[2]}, Number of Customers: {row[3]}")

```

## Summary

In this lesson, you got to practice some more complex SQL queries, some of which required subqueries. There's still plenty more SQL to be had though; hope you've been enjoying some of these puzzles!
