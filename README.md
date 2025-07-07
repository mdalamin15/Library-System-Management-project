# 📚 Library Management System – SQL Project

This project showcases a structured and practical application of SQL for managing a library system. It includes data manipulation, reporting, and analytical queries covering key operations such as book issuance, returns, member tracking, overdue reporting, and revenue analysis.

---

## 📂 Project Overview

The Library Management System is designed to handle essential tasks like:

- Managing books, members, branches, employees
- Tracking issued and returned books
- Identifying overdue records
- Calculating revenue and performance metrics
- Analyzing member and employee activity

This project simulates a real-world relational database setup and uses SQL to extract insights, maintain data integrity, and support decision-making in a library context.

---

## 🧱 Database Structure

The database consists of the following tables:

- `books` – Contains book information such as ISBN, title, category, author, publisher, rental price, and availability.
- `members` – Stores member details like ID, name, address, and registration date.
- `employees` – Tracks employee details and branch assignment.
- `branch` – Contains information about library branches and their managers.
- `issued` – Records all issued book transactions.
- `return_status` – Logs return events related to issued books.

---

## 🛠️ Project Tasks Covered

Here are key tasks and queries executed in the project:

1. **Add a new book record**
2. **Update member details**
3. **Delete issued records**
4. **List books issued by specific employees**
5. **Find members with multiple issued books**
6. **Create summary tables using CTAS**
7. **Filter books by category**
8. **Calculate total rental income by category**
9. **List members who registered in the last 180 days**
10. **Employee–branch relationship report with manager mapping**
11. **Create a table for expensive books**
12. **List books not yet returned**
13. **Identify members with overdue books (30+ days)**
14. **Branch performance reporting with revenue and issue-return count**
15. **Top 3 employees by number of book issues processed**

---

## 📊 Data Analysis & Insights

The project performs data analytics on:

- Rental trends by category  
- Overdue book tracking and member accountability  
- Revenue per branch  
- Branch performance comparison  
- Employee contribution analysis  
- Real-time status of book returns

---

## ⚙️ Technologies Used

- **SQL** (PostgreSQL/MySQL compatible syntax)
- **Relational database design**
- **Data cleaning and joins**
- **Aggregate functions (SUM, COUNT)**
- **Subqueries and CTAS (Create Table As Select)**

---

## 📁 How to Use

1. Create the schema based on table structures.
2. Insert data if not pre-populated.
3. Run the SQL queries in the order provided for each task.
4. Review the outputs for reports and insights.

---


SELECT * FROM members;
SELECT * FROM books;
SELECT * FROM branch;
SELECT * FROM employees;
SELECT * FROM issued;
SELECT * FROM return_status;


-- Project Task

-- Task 1: Create a New Book Record -- "978-1-60129-456-2', 'To Kill a Mockingbird', 'Classic', 6.00, 'yes', 'Harper Lee', 'J.B. Lippincott & Co.')"

INSERT INTO books(isbn, book_title, category, rental_price, status, author, publisher)
VALUES
(
	'978-1-60129-456-2', 'To Kill a Mockingbird', 'Classic', 6.00, 'yes', 'Harper Lee', 'J.B. Lippincott & Co.'
);


-- Task 2: Update an Existing Member's Address

UPDATE members
SET member_address = '125 Main st'
WHERE member_id = 'C101';
SELECT * FROM members;


-- Task 3: Delete a Record from the Issued Status Table -- Objective: Delete the record with issued_id = 'IS121' from the issued_status table.

DELETE FROM issued
WHERE issued_id = 'IS121'

SELECT * FROM issued;


-- Task 4: Retrieve All Books Issued by a Specific Employee -- Objective: Select all books issued by the employee with emp_id = 'E101'.

SELECT * 
FROM issued
WHERE issued_emp_id = 'E101';


-- Task 5: List Members Who Have Issued More Than One Book -- Objective: Use GROUP BY to find members who have issued more than one book.

SELECT 
	issued_emp_id,
	COUNT(issued_id) as total_book_issued
FROM issued
GROUP BY issued_emp_id
HAVING COUNT(issued_id)>1;


--  CTAS (Create Table As Select)
-- Task 6: Create Summary Tables: Used CTAS to generate new tables based on query results - each book and total book_issued_cnt**

CREATE TABLE book_cnts 
as 
SELECT 
	b.isbn,
	b.book_title,
	COUNT(ist.issued_id) as no_issued
FROM books b
JOIN
issued as ist
on ist.issued_book_isbn = b.isbn
GROUP BY 1,2;


SELECT *
FROM book_cnts;

--4. Data Analysis & Findings
--he following SQL queries were used to address specific questions:

-- Task 7: Retrieve All Books in a Specific Category:


SELECT * FROM books
WHERE category = 'Classic'

-- Task 8: Find Total Rental Income by Category:

SELECT 
	b.category,
	SUM(b.rental_price),
	COUNT(*)
FROM books as b
JOIN
issued as ist
on ist.issued_book_isbn = b.isbn
GROUP BY 1;

-- Task 9: List Members Who Registered in the Last 180 Days:

SELECT * FROM members
WHERE reg_date >= CURRENT_DATE - INTERVAL '180 days';

INSERT INTO members(member_id, member_name, member_address, reg_date)
VALUES
	('C211', 'sam', '145 Main St', '2024-06-01'),
	('C212', 'john', '133 Main St', '2024-05-01');

DELETE FROM members
WHERE member_id ='C211' OR member_id ='C212' ;


-- Task 10: List Employees with Their Branch Manager's Name and their branch details:

SELECT 
	e1.*,
	b.branch_id,
	e2.emp_name as manger
FROM employees as e1
JOIN
branch as b
ON b.branch_id = e1.branch_id
JOIN
employees as e2
ON b.manager_id = e2.emp_id

SELECT * FROM branch


-- Task 11: Create a Table of Books with Rental Price Above a Certain Threshold:

CREATE TABLE expensive_books AS
SELECT * FROM books
WHERE rental_price > 7.00;

-- Task 12: Retrieve the List of Books Not Yet Returned

SELECT * FROM issued as ist
LEFT JOIN
return_status as rs
ON rs.issued_id = ist.issued_id
WHERE rs.return_id IS NULL;


-- Task 13: Identify Members with Overdue Books
-- Write a query to identify members who have overdue books (assume a 30-day return period). Display the member's_id, member's name, book title, issue date, and days overdue.

SELECT 
    ist.issued_member_id,
    m.member_name,
    bk.book_title,
    ist.issued_date,
    -- rs.return_date,
    CURRENT_DATE - ist.issued_date as over_dues_days
FROM issued as ist
JOIN 
members as m
    ON m.member_id = ist.issued_member_id
JOIN 
books as bk
ON bk.isbn = ist.issued_book_isbn
LEFT JOIN 
return_status as rs
ON rs.issued_id = ist.issued_id
WHERE 
    rs.return_date IS NULL
    AND
    (CURRENT_DATE - ist.issued_date) > 30
ORDER BY 1


-- Task 14: Branch Performance Report
-- Create a query that generates a performance report for each branch, showing the number of books issued, the number of books returned, and the total revenue generated from book rentals.

CREATE TABLE branch_reports
AS
SELECT 
    b.branch_id,
    b.manager_id,
    COUNT(ist.issued_id) as number_book_issued,
    COUNT(rs.return_id) as number_of_book_return,
    SUM(bk.rental_price) as total_revenue
FROM issued as ist
JOIN 
employees as e
ON e.emp_id = ist.issued_emp_id
JOIN
branch as b
ON e.branch_id = b.branch_id
LEFT JOIN
return_status as rs
ON rs.issued_id = ist.issued_id
JOIN 
books as bk
ON ist.issued_book_isbn = bk.isbn
GROUP BY 1, 2;

SELECT * FROM branch_reports;


-- Task 15: Find Employees with the Most Book Issues Processed
-- Write a query to find the top 3 employees who have processed the most book issues. Display the employee name, number of books processed, and their branch.

SELECT 
    e.emp_name,
    b.*,
    COUNT(ist.issued_id) as no_book_issued
FROM issued as ist
JOIN
employees as e
ON e.emp_id = ist.issued_emp_id
JOIN
branch as b
ON e.branch_id = b.branch_id
GROUP BY 1, 2


-- END OF PROJECT 


## ✍️ Author

**MD AL AMIN**  
SQL Developer | Data Analyst | AI & Business Intelligence Enthusiast

---

## 📌 Note

This project is intended for educational and portfolio purposes to demonstrate SQL querying, data modeling, and real-world problem-solving using databases.

