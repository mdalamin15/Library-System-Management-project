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

-- 🧾 Task 1: Add a New Book Record
INSERT INTO books (isbn, book_title, category, rental_price, status, author, publisher)
VALUES ('978-1-60129-456-2', 'To Kill a Mockingbird', 'Classic', 6.00, 'yes', 'Harper Lee', 'J.B. Lippincott & Co.');

-- 🛠 Task 2: Update a Member’s Address
UPDATE members
SET member_address = '125 Main st'
WHERE member_id = 'C101';

-- 🧹 Task 3: Delete Issued Record
DELETE FROM issued
WHERE issued_id = 'IS121';

-- 👨‍💼 Task 4: Books Issued by Specific Employee
SELECT * 
FROM issued
WHERE issued_emp_id = 'E101';

-- 📚 Task 5: Members Who Issued More Than One Book
SELECT issued_emp_id, COUNT(issued_id) AS total_book_issued
FROM issued
GROUP BY issued_emp_id
HAVING COUNT(issued_id) > 1;

-- 📊 Task 6: Create Book Summary Table (CTAS)
CREATE TABLE book_cnts AS
SELECT b.isbn, b.book_title, COUNT(ist.issued_id) AS no_issued
FROM books b
JOIN issued ist ON ist.issued_book_isbn = b.isbn
GROUP BY 1, 2;

-- 📖 Task 7: Retrieve All Classic Books
SELECT * FROM books
WHERE category = 'Classic';

-- 💰 Task 8: Rental Income by Category
SELECT b.category, SUM(b.rental_price), COUNT(*)
FROM books b
JOIN issued ist ON ist.issued_book_isbn = b.isbn
GROUP BY 1;

-- 🆕 Task 9: Members Registered in Last 180 Days
SELECT * FROM members
WHERE reg_date >= CURRENT_DATE - INTERVAL '180 days';

-- 🏢 Task 10: Employees with Their Branch Manager's Name
SELECT e1.*, b.branch_id, e2.emp_name AS manager
FROM employees e1
JOIN branch b ON b.branch_id = e1.branch_id
JOIN employees e2 ON b.manager_id = e2.emp_id;

-- 💵 Task 11: Expensive Books Table
CREATE TABLE expensive_books AS
SELECT * FROM books
WHERE rental_price > 7.00;

-- 📦 Task 12: Books Not Yet Returned
SELECT * FROM issued ist
LEFT JOIN return_status rs ON rs.issued_id = ist.issued_id
WHERE rs.return_id IS NULL;

-- 🕒 Task 13: Members with Overdue Books (>30 Days)
SELECT 
    ist.issued_member_id, m.member_name, bk.book_title, ist.issued_date,
    CURRENT_DATE - ist.issued_date AS over_dues_days
FROM issued ist
JOIN members m ON m.member_id = ist.issued_member_id
JOIN books bk ON bk.isbn = ist.issued_book_isbn
LEFT JOIN return_status rs ON rs.issued_id = ist.issued_id
WHERE rs.return_date IS NULL AND (CURRENT_DATE - ist.issued_date) > 30;

-- 🧾 Task 14: Branch Performance Report
CREATE TABLE branch_reports AS
SELECT 
    b.branch_id, b.manager_id,
    COUNT(ist.issued_id) AS number_book_issued,
    COUNT(rs.return_id) AS number_of_book_return,
    SUM(bk.rental_price) AS total_revenue
FROM issued ist
JOIN employees e ON e.emp_id = ist.issued_emp_id
JOIN branch b ON e.branch_id = b.branch_id
LEFT JOIN return_status rs ON rs.issued_id = ist.issued_id
JOIN books bk ON ist.issued_book_isbn = bk.isbn
GROUP BY 1, 2;

-- 🏅 Task 15: Top 3 Employees by Books Issued
SELECT 
    e.emp_name, b.*, COUNT(ist.issued_id) AS no_book_issued
FROM issued ist
JOIN employees e ON e.emp_id = ist.issued_emp_id
JOIN branch b ON e.branch_id = b.branch_id
GROUP BY 1, 2
LIMIT 3;



## ✍️ Author

**MD AL AMIN**  
SQL Developer | Data Analyst | AI & Business Intelligence Enthusiast

---

## 📌 Note

This project is intended for educational and portfolio purposes to demonstrate SQL querying, data modeling, and real-world problem-solving using databases.

