# SQL-Books-Order-Management-System
This project analyzes a database Book Order Management System to answer key business questions and extract valuable insights.
![introduction](https://github.com/user-attachments/assets/d2941914-1618-45b3-b078-e77f4b1ac2cf)

# DATABASE SCHEMA IMAGE
![books schema](https://github.com/user-attachments/assets/262b0924-2ec7-418c-8e7c-eef22571958f)

# DATABASE SCHEMA SQL QUERY

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
