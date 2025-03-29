# SQL PROJECT-Books-Order-Management-System
This project analyzes a database Book Order Management System to answer key business questions and extract valuable insights.
![introduction](https://github.com/user-attachments/assets/d2941914-1618-45b3-b078-e77f4b1ac2cf)

## DATABASE SCHEMA IMAGE
![books schema](https://github.com/user-attachments/assets/262b0924-2ec7-418c-8e7c-eef22571958f)

## DATABASE SCHEMA SQL QUERY
```sql
DROP TABLE IF EXISTS Books;
CREATE TABLE Books (
	Book_ID SERIAL PRIMARY KEY,
	Title VARCHAR(100),
	Author VARCHAR(100),
	Genre VARCHAR(50),
	Published_Year INT,
	Price NUMERIC(10,2),
	Stock INT
);

DROP TABLE IF EXISTS Customers;
CREATE TABLE Customers (
	Customer_ID SERIAL PRIMARY KEY,
	Name VARCHAR(100),
	Email VARCHAR(100),
	Phone VARCHAR(15),
	City VARCHAR(50),
	Country VARCHAR(150)
);

DROP TABLE IF EXISTS Orders;
CREATE TABLE Orders (
	Order_ID SERIAL PRIMARY KEY,
	Customer_ID INT REFERENCES Customers(Customer_ID),
	Book_ID INT REFERENCES Books(Book_ID),
	Order_Date DATE,
	Quantity INT,
	Total_Amount NUMERIC(10,2)
);
```
## Business Questions
![set 1 books question](https://github.com/user-attachments/assets/59c8c5de-7620-46c3-b4f4-1d8763d9e332)
![Set 2 Business Questions](https://github.com/user-attachments/assets/b297ac11-4cc3-4aaf-985d-2fabcddd78f5)

## SET 1:Business Problems and Solutions

### 1). Retrieve all book titles in the "Fiction" genre
```sql
SELECT title, genre
FROM Books
WHERE genre = 'Fiction'
ORDER BY title ASC;
```
**Objective:** To retrieve the titles of books that belong to the "Fiction" genre from the Books table, helping to filter and display only the fiction books.

### 2). Find books published after the year 1950 ordered by published year in descending order
```sql
SELECT title, published_year
FROM Books
WHERE published_year > 1950
ORDER BY published_year DESC;
```
**Objective:** To identify books published after 1950 and display them in order of their publication year, from the most recent to the oldest, useful for analyzing newer releases.

### 3). List all customer names from Canada ordered by name in alphabetical order
```sql
SELECT name, country
FROM Customers
WHERE country IN ('Canada')
ORDER BY name ASC;
```
**Objective:** To filter and retrieve all customers located in Canada, and sort their names in alphabetical order, useful for organizing the list of Canadian customers.

### 4). Show all the columns for orders placed in November 2023 ordered by order date in ascending
```sql
SELECT *
FROM Orders
WHERE Order_Date BETWEEN '2023-11-01' AND '2023-11-30'
ORDER BY Order_Date ASC;
```
**Objective:** To list all orders placed in November 2023 with all the details (all columns), sorted by order date from the earliest to the latest, useful for reviewing the orders placed during that specific month.

### 5). Retrieve the total stock of books available
```sql
SELECT SUM(Stock) AS Total_Stock
FROM Books;
```
**Objective:** To calculate the total number of books available across all titles, which helps in tracking the overall stock of books in the inventory.

### 6). Find the details of the most expensive book
```sql
SELECT *
FROM Books
WHERE Price IN (
	SELECT MAX(Price)
	FROM Books
);
```
**Objective:** To retrieve the details (e.g., title, author, price, etc.) of the book with the highest price, useful for identifying premium or luxury items in the inventory.

### 7). Show all customer names in alphabetical order who ordered more than 1 quantity of a book
```sql
SELECT c.Name, o.Quantity
FROM Orders AS o
LEFT JOIN Customers AS c
ON o.Customer_ID = c.Customer_ID
WHERE o.Quantity > 1
ORDER BY c.Name ASC;
```
**Objective:** To find all customers who ordered more than one copy of a book and display their names sorted alphabetically, useful for identifying customers with larger purchases.

### 8). Retrieve all columns of orders where the total amount exceeds $20
```sql
SELECT *
FROM Orders
WHERE Total_Amount > 20;
```
**Objective:** To retrieve all the details of orders where the total amount exceeds $20, helping to focus on larger transactions or high-value orders.

### 9). List all unique genres available in the Books table
```sql
SELECT DISTINCT genre
FROM Books;
```
**Objective:** To retrieve a list of distinct genres from the Books table, useful for understanding the variety of genres available in the catalog and for categorization.

### 10). Find the book titles with the lowest stock
```sql
SELECT Title, Stock
FROM Books
WHERE Stock IN (
	SELECT MIN(Stock)
	FROM Books
);
```
**Objective:** To identify the book titles that have the lowest stock levels, helping to manage inventory and prioritize restocking.

### 11). Calculate the total revenue generated from all orders
```sql
SELECT SUM(Total_Amount) AS Total_Revenue
FROM Orders;
```
**Objective:** To calculate the total revenue earned from all customer orders, which helps to track overall sales performance and financial outcomes.

## SET 2:Business Problems and Solutions

### 1). Retrieve the total number of books sold for each genre ordered by books sold in descending order
```sql
SELECT b.Genre, SUM(o.Quantity) AS Books_Sold
FROM Orders AS o
INNER JOIN Books AS b
	ON o.Book_ID = b.Book_ID
GROUP BY b.Genre
ORDER BY Books_Sold DESC;
```
**Objective:** To analyze the sales performance by genre. This query will provide insight into which genre has the highest sales volume.

### 2). Find the average price of books in the "Fantasy" genre rounded to two decimal places
```sql
SELECT ROUND(AVG(Price),2) AS Average_Price
FROM Books
WHERE Genre = 'Fantasy';

```
**Objective:** To calculate the average price of books specifically within the Fantasy genre, helping to understand the pricing trends for books in this category.

### 3). List customers name and customer id who have placed at least 2 orders
```sql
SELECT o.Customer_ID, c.Name, COUNT(Order_ID) AS No_of_Orders
FROM Orders AS o
LEFT JOIN Customers AS c
	ON o.Customer_ID = c.Customer_ID
GROUP BY o.Customer_ID, c.Name
HAVING COUNT(Order_ID) >= 2;
```
**Objective:** To identify loyal or repeat customers who have made multiple purchases. This can help target customer retention efforts.

### 4). Find the most frequently ordered book
```sql
SELECT o.Book_id, b.Title, COUNT(o.order_id) AS ORDER_COUNT
FROM Orders AS o
LEFT JOIN Books AS b 
	ON o.Book_ID=b.Book_ID
GROUP BY o.Book_id, b.Title
ORDER BY ORDER_COUNT DESC 
LIMIT 1;
```
**Objective:** To identify which book has been ordered the most times, offering insight into the best-selling or most popular book.

### 5). Show the top 3 most expensive books of 'Fantasy' Genre
```sql
SELECT *
FROM Books
WHERE Genre = 'Fantasy'
ORDER BY Price DESC
LIMIT 3;
```
**Objective:** To highlight the most expensive books within the Fantasy genre, which can be useful for marketing or pricing strategies.

### 6). Retrieve the total quantity of books sold by each author in descending order
```sql
SELECT b.Author, SUM(o.Quantity) AS Total_Books_Sold
FROM Orders AS o
LEFT JOIN Books AS b
	ON o.Book_ID = b.Book_ID
GROUP BY b.Author
ORDER BY Total_Books_Sold DESC;
```
**Objective:** To assess the performance of each author based on the number of books sold, helping to identify the most successful authors.

### 7). List the cities where customers who spent over $30 are located 
```sql
SELECT DISTINCT c.City, o.Total_Amount
FROM Orders AS o
LEFT JOIN Customers AS c
	ON o.Customer_ID = c.Customer_ID
WHERE o.Total_Amount > 30;
```
**Objective:** To pinpoint cities with customers who have made larger purchases, which could be useful for targeted marketing or sales campaigns.

### 8). Find the customer who spent the most on orders
```sql
SELECT c.Name, SUM(Total_Amount) AS Total_Spent
FROM Orders AS o
LEFT JOIN Customers AS c
	ON o.Customer_ID = c.Customer_ID
GROUP BY c.Name
ORDER BY Total_Spent DESC
LIMIT 1;
```
**Objective:** To identify the customer who has made the highest monetary contribution, allowing businesses to reward or retain high-value customers.

### 9). Calculate the stock remaining after fulfilling all orders order by book title in alphabetical order
```sql
SELECT 
	b.Title,
	b.Stock,
	COALESCE(SUM(o.Quantity),0) AS Books_Sold,
	COALESCE((b.Stock - SUM(o.Quantity)),b.Stock) AS Stock_Remaining
FROM Books AS b
LEFT JOIN Orders AS o
	ON o.Book_ID = b.Book_ID
GROUP BY b.Title,b.Stock
ORDER BY b.Title ASC;
```
**Objective:** To track the current inventory levels for each book after sales, organized alphabetically. This helps with inventory management and stock replenishment.

### 10). Calculate the stock remaining after fulfilling all orders order by book title in alphabetical order
```sql
SELECT o.Book_ID, b.Title, SUM(o.Quantity) AS No_Books_Sold
FROM Orders AS o
LEFT JOIN Books AS b
	ON o.Book_ID = b.Book_ID
GROUP BY o.Book_ID, b.Title
ORDER BY SUM(o.Quantity) DESC
LIMIT 1;
```
**Objective:** To determine which specific book has sold the highest quantity, providing insight into the best-performing book.















