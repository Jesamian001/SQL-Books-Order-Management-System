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
SELECT * FROM Books;

DROP TABLE IF EXISTS Customers;
CREATE TABLE Customers (
	Customer_ID SERIAL PRIMARY KEY,
	Name VARCHAR(100),
	Email VARCHAR(100),
	Phone VARCHAR(15),
	City VARCHAR(50),
	Country VARCHAR(150)
);
SELECT * FROM Customers;

DROP TABLE IF EXISTS Orders;
CREATE TABLE Orders (
	Order_ID SERIAL PRIMARY KEY,
	Customer_ID INT REFERENCES Customers(Customer_ID),
	Book_ID INT REFERENCES Books(Book_ID),
	Order_Date DATE,
	Quantity INT,
	Total_Amount NUMERIC(10,2)
);
SELECT * FROM Orders;

SELECT * FROM Books;
SELECT * FROM Customers;
SELECT * FROM Orders;


--Basic Queries
--1) Retrieve all books title in the "Fiction" genre
SELECT title, genre
FROM Books
WHERE genre = 'Fiction'
ORDER BY title ASC;

--2) Find books published after the year 1950 ordered by published year in descending order
SELECT title, published_year
FROM Books
WHERE published_year > 1950
ORDER BY published_year DESC;

--3) List all customer names from the Canada ordered by names in alphabetical order
SELECT name, country
FROM Customers
WHERE country IN ('Canada')
ORDER BY name ASC;

--4) Show all the columns for orders placed in November 2023 ordered by order date in ascending
SELECT *
FROM Orders
WHERE Order_Date BETWEEN '2023-11-01' AND '2023-11-30'
ORDER BY Order_Date ASC;

SELECT *
FROM Orders
WHERE EXTRACT(YEAR FROM Order_Date) = 2023 AND EXTRACT(MONTH FROM Order_Date) = 11
ORDER BY Order_Date ASC;

--5) Retrieve the total stock of books available
SELECT SUM(Stock) AS Total_Stock
FROM Books;

--6) Find the details of the most expensive book
SELECT *
FROM Books
WHERE Price IN (
	SELECT MAX(Price)
	FROM Books
);

--7) Show all customer names in alphabetical order who ordered more than 1 quantity of a book
SELECT c.Name, o.Quantity
FROM Orders AS o
LEFT JOIN Customers AS c
ON o.Customer_ID = c.Customer_ID
WHERE o.Quantity > 1
ORDER BY c.Name ASC;

--8) Retrieve all columns of orders where the total amount exceeds $20
SELECT *
FROM Orders
WHERE Total_Amount > 20;

--9) List all unique genres available in the Books table
SELECT DISTINCT genre
FROM Books;

--10) Find the book titles with the lowest stock
SELECT Title, Stock
FROM Books
WHERE Stock IN (
	SELECT MIN(Stock)
	FROM Books
);

--11) Calculate the total revenue generated from all orders
SELECT SUM(Total_Amount) AS Total_Revenue
FROM Orders;

--Advance Queries
--1) Retrieve the total number of books sold for each genre ordered by books sold in descending order
SELECT b.Genre, SUM(o.Quantity) AS Books_Sold
FROM Orders AS o
INNER JOIN Books AS b
	ON o.Book_ID = b.Book_ID
GROUP BY b.Genre
ORDER BY Books_Sold DESC;

--2) Find the average price of books in the "Fantasy" genre rounded to two decimal places
SELECT ROUND(AVG(Price),2) AS Average_Price
FROM Books
WHERE Genre = 'Fantasy';

--3) List customers name and customer id who have placed at least 2 orders
SELECT o.Customer_ID, c.Name, COUNT(Order_ID) AS No_of_Orders
FROM Orders AS o
LEFT JOIN Customers AS c
	ON o.Customer_ID = c.Customer_ID
GROUP BY o.Customer_ID, c.Name
HAVING COUNT(Order_ID) >= 2;

--4) Find the most frequently ordered book

SELECT o.Book_id, b.Title, COUNT(o.order_id) AS ORDER_COUNT
FROM Orders AS o
LEFT JOIN Books AS b 
	ON o.Book_ID=b.Book_ID
GROUP BY o.Book_id, b.Title
ORDER BY ORDER_COUNT DESC 
LIMIT 1;

--5) Show the top 3 most expensive books of 'Fantasy' Genre
SELECT *
FROM Books
WHERE Genre = 'Fantasy'
ORDER BY Price DESC
LIMIT 3;

--6) Retrieve the total quantity of books sold by each author in descending order
SELECT b.Author, SUM(o.Quantity) AS Total_Books_Sold
FROM Orders AS o
LEFT JOIN Books AS b
	ON o.Book_ID = b.Book_ID
GROUP BY b.Author
ORDER BY Total_Books_Sold DESC;

--7) List the cities where customers who spent over $30 are located 
SELECT DISTINCT c.City, o.Total_Amount
FROM Orders AS o
LEFT JOIN Customers AS c
	ON o.Customer_ID = c.Customer_ID
WHERE o.Total_Amount > 30;

--8) Find the customer who spent the most on orders
SELECT c.Name, SUM(Total_Amount) AS Total_Spent
FROM Orders AS o
LEFT JOIN Customers AS c
	ON o.Customer_ID = c.Customer_ID
GROUP BY c.Name
ORDER BY Total_Spent DESC
LIMIT 1;

--9) Calculate the stock remaining after fulfilling all orders order by book title in alphabetical order
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

--10) Find the book id and book title with the most number of quantity sold
SELECT o.Book_ID, b.Title, SUM(o.Quantity) AS No_Books_Sold
FROM Orders AS o
LEFT JOIN Books AS b
	ON o.Book_ID = b.Book_ID
GROUP BY o.Book_ID, b.Title
ORDER BY SUM(o.Quantity) DESC
LIMIT 1;
--Another Way
SELECT o.Book_ID, b.Title, SUM(o.Quantity) AS No_Books_Sold
FROM Orders AS o
LEFT JOIN Books AS b
	ON o.Book_ID = b.Book_ID
GROUP BY o.Book_ID, b.Title
HAVING SUM(o.Quantity) IN (
	SELECT MAX(No_Books_Sold)
	FROM (
		SELECT Book_ID, SUM(Quantity) AS No_Books_Sold
		FROM Orders
		GROUP BY Book_ID
	) AS subquery
);
