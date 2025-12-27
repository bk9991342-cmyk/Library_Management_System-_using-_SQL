# Library_Management_System-_using-_SQL
SQL Project
## Project Overview
This project involves the design and implementation of a Library Management System using SQL. It covers the creation and management of relational tables, execution of full CRUD (Create, Read, Update, Delete) operations, and the use of advanced SQL queries for data retrieval and analysis. The objective is to demonstrate strong skills in database design, data manipulation, and efficient querying.

## Objectives
- Database Setup: Designed and populated a Library Management System database with tables for branches, employees, members, books, issue records, and return records.
- CRUD Operations: Implemented complete Create, Read, Update, and Delete operations to efficiently manage library data.
- CTAS (Create Table As Select): Used CTAS to generate new tables directly from query results for reporting and analysis purposes.
- Advanced SQL Queries: Developed complex SQL queries to retrieve insights, perform data analysis, and support library operations.

## Project Structure
1.Project Structure
- Database Creation: Created a database named library_db to manage library-related data efficiently.
- Table Creation: Designed and created tables for branches, employees, members, books, issue records, and return records, with appropriate columns and defined relationships to ensure data integrity.
```sql
-- Create Database
CREATE DATABASE library_db;
USE library_db;

-- =========================
-- Create table: Branch
-- =========================
DROP TABLE IF EXISTS branch;
CREATE TABLE branch (
    branch_id VARCHAR(10) PRIMARY KEY,
    manager_id VARCHAR(10),
    branch_address VARCHAR(50),
    contact_no VARCHAR(15)
);

-- =========================
-- Create table: Employees
-- =========================
DROP TABLE IF EXISTS employees;
CREATE TABLE employees (
    emp_id VARCHAR(10) PRIMARY KEY,
    emp_name VARCHAR(30),
    position VARCHAR(30),
    salary DECIMAL(10,2),
    branch_id VARCHAR(10),
    FOREIGN KEY (branch_id) REFERENCES branch(branch_id)
);

-- =========================
-- Create table: Members
-- =========================
DROP TABLE IF EXISTS members;
CREATE TABLE members (
    member_id VARCHAR(10) PRIMARY KEY,
    member_name VARCHAR(30),
    member_address VARCHAR(50),
    reg_date DATE
);

-- =========================
-- Create table: Books
-- =========================
DROP TABLE IF EXISTS books;
CREATE TABLE books (
    isbn VARCHAR(50) PRIMARY KEY,
    book_title VARCHAR(80),
    category VARCHAR(30),
    rental_price DECIMAL(10,2),
    status VARCHAR(10),
    author VARCHAR(30),
    publisher VARCHAR(30)
);

-- =========================
-- Create table: Issued Status
-- =========================
DROP TABLE IF EXISTS issued_status;
CREATE TABLE issued_status (
    issued_id VARCHAR(10) PRIMARY KEY,
    issued_member_id VARCHAR(10),
    issued_book_isbn VARCHAR(50),
    issued_date DATE,
    issued_emp_id VARCHAR(10),
    FOREIGN KEY (issued_member_id) REFERENCES members(member_id),
    FOREIGN KEY (issued_book_isbn) REFERENCES books(isbn),
    FOREIGN KEY (issued_emp_id) REFERENCES employees(emp_id)
);

-- =========================
-- Create table: Return Status
-- =========================
DROP TABLE IF EXISTS return_status;
CREATE TABLE return_status (
    return_id VARCHAR(10) PRIMARY KEY,
    issued_id VARCHAR(10),
    return_date DATE,
    return_book_isbn VARCHAR(50),
    FOREIGN KEY (issued_id) REFERENCES issued_status(issued_id),
    FOREIGN KEY (return_book_isbn) REFERENCES books(isbn)
);
```

2.CRUD Operations:
- Create: Inserted sample records into the books table to populate initial data.
- Read: Retrieved and displayed data from multiple tables using SELECT queries for analysis and verification.
- Update: Modified existing records in the employees table to reflect updated information.
- Delete: Removed records from the members table when no longer required, ensuring accurate and up-to-date data.

Task 1. Create a New Book Record -- "978-1-60129-456-2', 'To Kill a Mockingbird', 'Classic', 6.00, 'yes', 'Harper Lee', 'J.B. Lippincott & Co.')"
```sql
INSERT INTO books(isbn, book_title, category, rental_price, status, author, publisher)
VALUES
('978-1-60129-456-2', 'To Kill a Mockingbird', 'Classic', 6.00, 'yes', 'Harper Lee', 'J.B. Lippincott & Co.');

SELECT * FROM books;
```
Task 2: Update an Existing Member's Address
```sql
UPDATE members
SET member_address = '125 Oak St'
WHERE member_id = 'C103';
```
Task 3: Delete a Record from the Issued Status Table -- Objective: Delete the record with issued_id = 'IS121' from the issued_status table.
```sql
DELETE FROM issued_status
WHERE issued_id = 'IS121';
```
Task 4: Retrieve All Books Issued by a Specific Employee -- Objective: Select all books issued by the employee with emp_id = 'E101'.
```sql
SELECT * FROM issued_status
WHERE issued_emp_id = 'E101'
```
Task 5: List Members Who Have Issued More Than One Book -- Objective: Use GROUP BY to find members who have issued more than one book.
```sql
SELECT
    issued_emp_id,
    COUNT(*)
FROM issued_status
GROUP BY 1
HAVING COUNT(*) > 1
```
3. CTAS (Create Table As Select)
-Task 6: Create Summary Tables: Used CTAS to generate new tables based on query results - each book and total book_issued_cnt**
```sql
CREATE TABLE book_issued_cnt AS
SELECT b.isbn, b.book_title, COUNT(ist.issued_id) AS issue_count
FROM issued_status as ist
JOIN books as b
ON ist.issued_book_isbn = b.isbn
GROUP BY b.isbn, b.book_title;
```
**4. Data Analysis & Findings** 
- The following SQL queries were used to address specific questions:
Task 7. Retrieve All Books in a Specific Category:
```sql
SELECT * FROM books
WHERE category = 'Classic';
```
Task 8: Find Total Rental Income by Category:
```sql
SELECT 
    b.category,
    SUM(b.rental_price),
    COUNT(*)
FROM 
issued_status as ist
JOIN
books as b
ON b.isbn = ist.issued_book_isbn
GROUP BY 1;
```
Task 9: List Members Who Registered in the Last 180 Days:
```sql
SELECT * FROM members
WHERE reg_date >= CURRENT_DATE - INTERVAL '180 days';
```
Task 10: List Employees with Their Branch Manager's Name and their branch details:
```sql
SELECT 
    e1.emp_id,
    e1.emp_name,
    e1.position,
    e1.salary,
    b.*,
    e2.emp_name as manager
FROM employees as e1
JOIN 
branch as b
ON e1.branch_id = b.branch_id    
JOIN
employees as e2
ON e2.emp_id = b.manager_id;
```
Task 11. Create a Table of Books with Rental Price Above a Certain Threshold:
```sql
CREATE TABLE expensive_books AS
SELECT * FROM books
WHERE rental_price > 7.00;
```
Task 12: Retrieve the List of Books Not Yet Returned:
```sql
SELECT * FROM issued_status as ist
LEFT JOIN
return_status as rs
ON rs.issued_id = ist.issued_id
WHERE rs.return_id IS NULL;
```














