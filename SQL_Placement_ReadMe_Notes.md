# 📘 SQL PLACEMENT MASTER NOTES

### Database used for practice

`sql_placement`

### Main tables

```text
department
--------------------------------
dept_id       dept_name
10            Engineering
20            HR
30            Finance
40            Marketing
```

```text
employee
---------------------------------------------------------
emp_id  emp_name  dept_id  salary  hire_date
1       Amit      10       60000   2022-01-10
2       Priya     20       75000   2021-03-15
3       Rahul     10       50000   2023-06-01
4       Sneha     30       90000   2020-07-20
5       Karan     20       65000   2022-11-05
6       Neha      30       85000   2023-01-12
7       Arjun     10       70000   2021-09-18
8       Riya      20       55000   2024-02-10
9       Mohit     30       70000   2022-05-10
10      Anjali    10       80000   2020-12-15
```

Marketing (`dept_id = 40`) currently has **no employees**.

---

# 🧭 HOW TO USE THESE NOTES

Don't try to memorize 100 SQL queries.

For every question, ask:

> **"What is the question actually asking me to do?"**

For example:

```text
"employees earning more than average"
                    ↓
Need to calculate average first
                    ↓
SUBQUERY
```

```text
"employees with department name"
                    ↓
Information is in another table
                    ↓
JOIN
```

```text
"top 2 employees from every department"
                    ↓
Need ranking separately inside each department
                    ↓
ROW_NUMBER() + PARTITION BY
```

This way you learn **problem patterns**, not isolated queries.

---

# PART 1 — SQL COMMANDS

# 1. SQL COMMAND CATEGORIES

| Category | Meaning             | Commands                                   |
| -------- | ------------------- | ------------------------------------------ |
| DDL      | Define structure    | `CREATE`, `ALTER`, `DROP`, `TRUNCATE`      |
| DML      | Manipulate data     | `INSERT`, `UPDATE`, `DELETE`               |
| DQL      | Retrieve data       | `SELECT`                                   |
| DCL      | Control permissions | `GRANT`, `REVOKE`                          |
| TCL      | Transactions        | `BEGIN`, `COMMIT`, `ROLLBACK`, `SAVEPOINT` |

### Easy memory

```text
DDL → Design
DML → Modify data
DQL → Query
DCL → Control access
TCL → Transaction
```

---

# 2. CREATE DATABASE

```sql
CREATE DATABASE sql_placement;
```

PostgreSQL `psql` connection:

```sql
\c sql_placement
```

---

# 3. CREATE TABLE

```sql
CREATE TABLE department (
    dept_id INT PRIMARY KEY,
    dept_name VARCHAR(50) NOT NULL UNIQUE
);
```

```sql
CREATE TABLE employee (
    emp_id INT PRIMARY KEY,
    emp_name VARCHAR(50) NOT NULL,
    dept_id INT,
    salary NUMERIC(10,2),
    hire_date DATE,
    FOREIGN KEY (dept_id)
        REFERENCES department(dept_id)
);
```

---

# 4. CONSTRAINTS

## PRIMARY KEY

```sql
emp_id INT PRIMARY KEY
```

Uniquely identifies a row.

```text
Unique + NOT NULL
```

---

## FOREIGN KEY

```sql
FOREIGN KEY (dept_id)
REFERENCES department(dept_id)
```

Connects tables.

---

## NOT NULL

```sql
emp_name VARCHAR(50) NOT NULL
```

Value cannot be missing.

---

## UNIQUE

```sql
email VARCHAR(100) UNIQUE
```

Duplicate values are not allowed.

---

## CHECK

```sql
salary NUMERIC CHECK (salary > 0)
```

Restricts allowed values.

---

## DEFAULT

```sql
status VARCHAR(20) DEFAULT 'Active'
```

---

# ⭐ Placement Question

### Q: Can a table have multiple UNIQUE constraints?

**Yes.**

A table can have multiple unique constraints, but normally only one primary key constraint.

---

# 5. ALTER TABLE

Add column:

```sql
ALTER TABLE employee
ADD COLUMN email VARCHAR(100);
```

Drop column:

```sql
ALTER TABLE employee
DROP COLUMN email;
```

Rename column:

```sql
ALTER TABLE employee
RENAME COLUMN emp_name TO employee_name;
```

Rename table:

```sql
ALTER TABLE employee
RENAME TO employees;
```

---

# 6. DROP vs TRUNCATE vs DELETE

This is a classic interview question.

### DROP

```sql
DROP TABLE employee;
```

Removes the table itself.

### TRUNCATE

```sql
TRUNCATE TABLE employee;
```

Removes all rows but keeps the table.

### DELETE

```sql
DELETE FROM employee
WHERE emp_id = 5;
```

Removes selected rows.

---

## 🧠 Remember

```text
DROP
→ structure + data removed

TRUNCATE
→ all data removed
→ structure remains

DELETE
→ selected/all rows removed
→ WHERE can be used
```

### ⚠️ Common misconception

> "TRUNCATE is basically DELETE without WHERE."

Not exactly.

`TRUNCATE` is a DDL-style operation in PostgreSQL and has different transactional/locking and logging behavior from row-by-row `DELETE`.

For placement interviews, remember the conceptual difference rather than assuming they are interchangeable.

---

# PART 2 — INSERT / UPDATE / DELETE

# 7. INSERT

```sql
INSERT INTO department (dept_id, dept_name)
VALUES (10, 'Engineering');
```

Multiple rows:

```sql
INSERT INTO department (dept_id, dept_name)
VALUES
(20, 'HR'),
(30, 'Finance'),
(40, 'Marketing');
```

---

# 8. UPDATE

```sql
UPDATE employee
SET salary = 65000
WHERE emp_id = 1;
```

Multiple columns:

```sql
UPDATE employee
SET salary = 70000,
    dept_id = 20
WHERE emp_id = 1;
```

### ⚠️ Dangerous mistake

```sql
UPDATE employee
SET salary = 70000;
```

This updates **every employee**.

---

# 9. DELETE

```sql
DELETE FROM employee
WHERE emp_id = 5;
```

All rows:

```sql
DELETE FROM employee;
```

---

# ⭐ Placement Question

### Q: What happens if you forget `WHERE` in UPDATE/DELETE?

The operation applies to **all rows**.

This is one of the most common real-world SQL mistakes.

---

# PART 3 — SELECT

# 10. BASIC SELECT

```sql
SELECT emp_name, salary
FROM employee;
```

Everything:

```sql
SELECT *
FROM employee;
```

---

# 11. ALIAS

```sql
SELECT emp_name AS name,
       salary AS monthly_salary
FROM employee;
```

---

# 12. CALCULATED COLUMN

```sql
SELECT emp_name,
       salary * 12 AS annual_salary
FROM employee;
```

---

# ⭐ Placement Question 1

### Q: Display employee names and annual salaries.

```sql
SELECT emp_name,
       salary * 12 AS annual_salary
FROM employee;
```

---

# PART 4 — WHERE

# 13. WHERE

Used to filter rows.

```sql
SELECT *
FROM employee
WHERE salary > 70000;
```

Think:

> "Which rows satisfy this condition?"

---

# 14. COMPARISON OPERATORS

```text
=       equal
<>      not equal
!=      not equal
>       greater
<       smaller
>=      greater/equal
<=      smaller/equal
```

---

# 15. AND

Both conditions must be true.

```sql
SELECT *
FROM employee
WHERE salary > 60000
AND dept_id = 20;
```

---

# 16. OR

At least one condition must be true.

```sql
SELECT *
FROM employee
WHERE dept_id = 10
OR dept_id = 20;
```

---

# 17. IN

Instead of multiple OR conditions.

```sql
SELECT *
FROM employee
WHERE dept_id IN (10, 20, 30);
```

Equivalent:

```sql
WHERE dept_id = 10
   OR dept_id = 20
   OR dept_id = 30
```

---

# 18. NOT IN

```sql
SELECT *
FROM employee
WHERE dept_id NOT IN (10, 20);
```

---

# 19. BETWEEN

```sql
SELECT *
FROM employee
WHERE salary BETWEEN 60000 AND 80000;
```

### Important

`BETWEEN` is **inclusive**.

Equivalent:

```sql
salary >= 60000
AND salary <= 80000
```

---

# 20. LIKE

Starts with A:

```sql
SELECT *
FROM employee
WHERE emp_name LIKE 'A%';
```

Ends with a:

```sql
WHERE emp_name LIKE '%a';
```

Contains `an`:

```sql
WHERE emp_name LIKE '%an%';
```

Exactly 5 characters:

```sql
WHERE emp_name LIKE '_____';
```

### Wildcards

```text
% → zero or more characters
_ → exactly one character
```

---

# 21. ILIKE — PostgreSQL

Case-insensitive pattern matching.

```sql
SELECT *
FROM employee
WHERE emp_name ILIKE 'a%';
```

---

# ⭐ Placement Questions

### Q: Find employees earning between ₹60,000 and ₹80,000.

```sql
SELECT *
FROM employee
WHERE salary BETWEEN 60000 AND 80000;
```

### Q: Find employees whose name starts with A.

```sql
SELECT *
FROM employee
WHERE emp_name LIKE 'A%';
```

### Q: Find employees from departments 10, 20 or 30.

```sql
SELECT *
FROM employee
WHERE dept_id IN (10, 20, 30);
```

---

# PART 5 — NULL

# 22. NULL

`NULL` means:

```text
unknown / missing / unavailable
```

Wrong:

```sql
WHERE salary = NULL
```

Correct:

```sql
WHERE salary IS NULL
```

Not null:

```sql
WHERE salary IS NOT NULL
```

---

# ⭐ Interesting Fact

`NULL` is not equal to zero.

It is also not equal to an empty string conceptually.

And:

```sql
NULL = NULL
```

does **not** evaluate to TRUE.

That's why we use:

```sql
IS NULL
```

instead of:

```sql
= NULL
```

---

# PART 6 — DISTINCT + SORTING

# 23. DISTINCT

```sql
SELECT DISTINCT dept_id
FROM employee;
```

Removes duplicate result values.

---

# 24. ORDER BY

Ascending:

```sql
SELECT *
FROM employee
ORDER BY salary ASC;
```

Descending:

```sql
SELECT *
FROM employee
ORDER BY salary DESC;
```

Multiple columns:

```sql
SELECT *
FROM employee
ORDER BY dept_id,
         salary DESC;
```

---

# 25. LIMIT

```sql
SELECT *
FROM employee
ORDER BY salary DESC
LIMIT 3;
```

Top 3 salaries.

---

# 26. OFFSET

```sql
SELECT *
FROM employee
ORDER BY salary DESC
LIMIT 3 OFFSET 2;
```

Skip first 2 and return next 3.

---

# ⭐ Placement Question

### Q: Find the 3 highest-paid employees.

```sql
SELECT *
FROM employee
ORDER BY salary DESC
LIMIT 3;
```

### ⚠️ But what if two employees tie?

`LIMIT 3` simply returns three rows.

It does **not** mean "top 3 distinct salaries."

For that, the solution is different.

---

# PART 7 — AGGREGATE FUNCTIONS

# 27. AGGREGATE FUNCTIONS

The five most important:

```text
COUNT()
SUM()
AVG()
MIN()
MAX()
```

---

# 28. COUNT()

Number of rows:

```sql
SELECT COUNT(*)
FROM employee;
```

Count non-NULL salaries:

```sql
SELECT COUNT(salary)
FROM employee;
```

Count distinct departments:

```sql
SELECT COUNT(DISTINCT dept_id)
FROM employee;
```

---

# ⭐ Important Difference

```sql
COUNT(*)
```

counts rows.

```sql
COUNT(column)
```

counts non-NULL values in that column.

This is a very common interview question.

---

# 29. SUM()

```sql
SELECT SUM(salary)
FROM employee;
```

---

# 30. AVG()

```sql
SELECT AVG(salary)
FROM employee;
```

---

# 31. MIN() / MAX()

```sql
SELECT MIN(salary)
FROM employee;
```

```sql
SELECT MAX(salary)
FROM employee;
```

---

# ⭐ Placement Question

### Q: Find the highest salary.

```sql
SELECT MAX(salary)
FROM employee;
```

### Q: Find total salary paid by the company.

```sql
SELECT SUM(salary)
FROM employee;
```

### Q: Find average salary.

```sql
SELECT AVG(salary)
FROM employee;
```

---

# PART 8 — GROUP BY

# 32. GROUP BY

Use when you see:

```text
for each department
department-wise
per department
category-wise
for every group
```

Example:

```sql
SELECT dept_id,
       COUNT(*) AS employee_count
FROM employee
GROUP BY dept_id;
```

---

# 33. Average salary per department

```sql
SELECT dept_id,
       AVG(salary) AS avg_salary
FROM employee
GROUP BY dept_id;
```

---

# 34. Total salary per department

```sql
SELECT dept_id,
       SUM(salary) AS total_salary
FROM employee
GROUP BY dept_id;
```

---

# 35. GROUP BY + HAVING

Question:

> Find departments having more than 2 employees.

```sql
SELECT dept_id,
       COUNT(*) AS employee_count
FROM employee
GROUP BY dept_id
HAVING COUNT(*) > 2;
```

---

# ⭐ WHERE vs HAVING

```text
WHERE
↓
filters individual rows

GROUP BY
↓
creates groups

HAVING
↓
filters groups
```

### Example

Wrong:

```sql
WHERE COUNT(*) > 2
```

Correct:

```sql
HAVING COUNT(*) > 2
```

---

# ⭐ Placement Question

### Q: Find departments whose average salary is greater than 70,000.

```sql
SELECT dept_id,
       AVG(salary) AS avg_salary
FROM employee
GROUP BY dept_id
HAVING AVG(salary) > 70000;
```

---

# PART 9 — JOINS

# 36. Why JOIN?

Suppose:

```text
employee
   |
   | dept_id
   ↓
department
```

Employee has:

```text
dept_id = 10
```

Department table tells us:

```text
10 = Engineering
```

If we need:

```text
Amit | Engineering
```

we need a `JOIN`.

---

# 37. INNER JOIN

Returns matching rows.

```sql
SELECT e.emp_name,
       e.salary,
       d.dept_name
FROM employee e
INNER JOIN department d
    ON e.dept_id = d.dept_id;
```

Shortcut:

```sql
JOIN
```

usually means:

```sql
INNER JOIN
```

---

# 38. LEFT JOIN

Keeps every row from the left table.

```sql
SELECT d.dept_name,
       e.emp_name
FROM department d
LEFT JOIN employee e
    ON d.dept_id = e.dept_id;
```

Marketing appears even though it has no employees.

---

# 39. Departments with NO employees

Very important pattern.

```sql
SELECT d.dept_name
FROM department d
LEFT JOIN employee e
    ON d.dept_id = e.dept_id
WHERE e.emp_id IS NULL;
```

Pattern:

```text
LEFT JOIN
+
right_table.primary_key IS NULL
```

---

# 40. RIGHT JOIN

```sql
SELECT e.emp_name,
       d.dept_name
FROM employee e
RIGHT JOIN department d
    ON e.dept_id = d.dept_id;
```

Keeps every row from the right table.

---

# 41. FULL OUTER JOIN

```sql
SELECT *
FROM employee e
FULL OUTER JOIN department d
    ON e.dept_id = d.dept_id;
```

Keeps:

```text
matching rows
+
unmatched employee rows
+
unmatched department rows
```

---

# 42. SELF JOIN

A table joins with itself.

Typical use:

```text
employee → manager
```

Example if `manager_id` exists:

```sql
SELECT e.emp_name AS employee,
       m.emp_name AS manager
FROM employee e
JOIN employee m
    ON e.manager_id = m.emp_id;
```

---

# 43. CROSS JOIN

Every row from table A combines with every row from table B.

```sql
SELECT *
FROM employee
CROSS JOIN department;
```

If:

```text
10 employees
4 departments
```

then:

```text
10 × 4 = 40 rows
```

---

# ⭐ Placement Questions — JOIN

### Q1. Display employee name, salary and department name.

```sql
SELECT e.emp_name,
       e.salary,
       d.dept_name
FROM employee e
JOIN department d
ON e.dept_id = d.dept_id;
```

### Q2. Display all departments, including departments with no employees.

```sql
SELECT d.dept_name,
       e.emp_name
FROM department d
LEFT JOIN employee e
ON d.dept_id = e.dept_id;
```

### Q3. Find departments with no employees.

```sql
SELECT d.dept_name
FROM department d
LEFT JOIN employee e
ON d.dept_id = e.dept_id
WHERE e.emp_id IS NULL;
```

Answer:

```text
Marketing
```

---

# ⭐ Fun Fact

Many students think:

> "LEFT JOIN means left table is more important."

Not really.

It means:

> **all rows from the left input are preserved.**

You can often rewrite a `RIGHT JOIN` as a `LEFT JOIN` simply by swapping table order.

---

# PART 10 — SUBQUERIES

# 44. What is a subquery?

A query inside another query.

Think:

```text
Outer query
     ↓
needs some value
     ↓
Inner query calculates it
```

---

# 45. Employees earning more than company average

Question:

> Find employees whose salary is greater than the average salary of all employees.

First:

```sql
SELECT AVG(salary)
FROM employee;
```

Then:

```sql
SELECT *
FROM employee
WHERE salary > (
    SELECT AVG(salary)
    FROM employee
);
```

---

# 46. Highest-paid employee

```sql
SELECT *
FROM employee
WHERE salary = (
    SELECT MAX(salary)
    FROM employee
);
```

Important:

This returns all employees tied for highest salary.

---

# 47. Same salary as Mohit

```sql
SELECT *
FROM employee
WHERE salary = (
    SELECT salary
    FROM employee
    WHERE emp_name = 'Mohit'
);
```

---

# 48. Second-highest distinct salary

One traditional method:

```sql
SELECT MAX(salary)
FROM employee
WHERE salary < (
    SELECT MAX(salary)
    FROM employee
);
```

---

# 49. Departments whose average salary is above company average

```sql
SELECT dept_id,
       AVG(salary) AS avg_salary
FROM employee
GROUP BY dept_id
HAVING AVG(salary) > (
    SELECT AVG(salary)
    FROM employee
);
```

This is a very useful combination:

```text
GROUP BY
+
HAVING
+
SUBQUERY
```

---

# 50. Correlated Subquery

Question:

> Find employees earning more than the average salary of their own department.

```sql
SELECT e.*
FROM employee e
WHERE e.salary > (
    SELECT AVG(e2.salary)
    FROM employee e2
    WHERE e2.dept_id = e.dept_id
);
```

The inner query changes depending on the current outer employee.

---

# 🧠 How to recognize it

If the question says:

> "above the average **of their own department**"

think:

```text
correlated subquery
```

---

# PART 11 — CASE

# 51. CASE

SQL's equivalent of:

```text
if
else if
else
```

Example:

```sql
SELECT emp_name,
       salary,
       CASE
           WHEN salary >= 80000 THEN 'High'
           WHEN salary >= 60000 THEN 'Medium'
           ELSE 'Low'
       END AS salary_category
FROM employee;
```

---

# 52. CASE with department

```sql
SELECT emp_name,
       CASE
           WHEN dept_id = 10 THEN 'Engineering'
           WHEN dept_id = 20 THEN 'HR'
           WHEN dept_id = 30 THEN 'Finance'
           ELSE 'Other'
       END AS department
FROM employee;
```

---

# ⭐ Placement Question

### Q: Classify employees:

```text
salary >= 80000 → High
salary >= 60000 → Medium
otherwise → Low
```

```sql
SELECT emp_name,
       salary,
       CASE
           WHEN salary >= 80000 THEN 'High'
           WHEN salary >= 60000 THEN 'Medium'
           ELSE 'Low'
       END AS category
FROM employee;
```

---

# PART 12 — COALESCE / NULLIF

# 53. COALESCE

Returns the first non-NULL value.

```sql
SELECT COALESCE(salary, 0)
FROM employee;
```

If salary is:

```text
NULL
```

result becomes:

```text
0
```

---

# 54. NULLIF

```sql
SELECT NULLIF(10, 10);
```

Result:

```text
NULL
```

```sql
SELECT NULLIF(10, 20);
```

Result:

```text
10
```

Common use:

```sql
amount / NULLIF(count, 0)
```

This prevents division by zero.

---

# ⭐ Fun Fact

`COALESCE()` can accept more than two values:

```sql
COALESCE(phone, email, 'No contact information')
```

It checks from left to right and returns the first non-NULL value.

---

# PART 13 — WINDOW FUNCTIONS

# 55. What makes a window function different?

Compare:

### GROUP BY

```sql
SELECT dept_id,
       AVG(salary)
FROM employee
GROUP BY dept_id;
```

Rows become groups.

### Window function

```sql
SELECT emp_name,
       dept_id,
       salary,
       AVG(salary) OVER (
           PARTITION BY dept_id
       ) AS dept_avg
FROM employee;
```

Every employee remains visible.

This distinction is extremely important.

---

# 56. ROW_NUMBER()

Gives each row a unique sequential number.

```sql
SELECT emp_name,
       salary,
       ROW_NUMBER() OVER (
           ORDER BY salary DESC
       ) AS row_num
FROM employee;
```

Example:

```text
salary    row_num
90000       1
85000       2
80000       3
75000       4
70000       5
70000       6
65000       7
...
```

Even ties get different numbers.

---

# 57. APPLICATIONS OF ROW_NUMBER()

This is important.

### Application 1 — Top N rows

```sql
ROW_NUMBER() OVER (
    ORDER BY salary DESC
)
```

Then:

```sql
WHERE row_num <= 3
```

---

### Application 2 — Top N per department

```sql
ROW_NUMBER() OVER (
    PARTITION BY dept_id
    ORDER BY salary DESC
)
```

Then:

```sql
WHERE row_num <= 2
```

---

### Application 3 — Latest record per group

```sql
ROW_NUMBER() OVER (
    PARTITION BY customer_id
    ORDER BY date DESC
)
```

Then:

```sql
WHERE row_num = 1
```

Meaning:

> latest record for each customer.

---

### Application 4 — Remove duplicates

A common technique:

```sql
ROW_NUMBER() OVER (
    PARTITION BY email
    ORDER BY id
)
```

Then keep:

```text
row_number = 1
```

This is a major real-world use of `ROW_NUMBER()`.

---

### Application 5 — Pagination

Number rows and select a range.

---

# 58. RANK()

```sql
SELECT emp_name,
       salary,
       RANK() OVER (
           ORDER BY salary DESC
       ) AS rnk
FROM employee;
```

With:

```text
90000
85000
80000
75000
70000
70000
65000
```

Ranks:

```text
1
2
3
4
5
5
7
```

---

# 59. APPLICATIONS OF RANK()

### Application 1 — Competition ranking

Example:

```text
Students:

A → 1st
B → 2nd
C → 2nd
D → 4th
```

There is a gap after the tie.

---

### Application 2 — Highest-paid employees per department

```sql
RANK() OVER (
    PARTITION BY dept_id
    ORDER BY salary DESC
)
```

Then:

```sql
WHERE rnk = 1
```

Important:

If two employees have the highest salary, **both are returned**.

---

### Application 3 — Ranking with ties

Use `RANK()` when tied values should receive the same rank and subsequent positions should have gaps.

---

# 60. DENSE_RANK()

```sql
SELECT emp_name,
       salary,
       DENSE_RANK() OVER (
           ORDER BY salary DESC
       ) AS rnk
FROM employee;
```

Ranks:

```text
90000 → 1
85000 → 2
80000 → 3
75000 → 4
70000 → 5
70000 → 5
65000 → 6
```

---

# 61. APPLICATIONS OF DENSE_RANK()

### Application 1 — Second-highest distinct salary

```sql
SELECT *
FROM (
    SELECT e.*,
           DENSE_RANK() OVER (
               ORDER BY salary DESC
           ) AS rnk
    FROM employee e
) x
WHERE rnk = 2;
```

---

### Application 2 — Third-highest distinct salary

Change:

```sql
WHERE rnk = 3
```

---

### Application 3 — Rank distinct values

Whenever the question says:

> highest/second-highest/third-highest **distinct** value

`DENSE_RANK()` is usually a strong pattern.

---

# 62. RANK vs DENSE_RANK vs ROW_NUMBER

| Salary | ROW_NUMBER | RANK | DENSE_RANK |
| -----: | ---------: | ---: | ---------: |
|  90000 |          1 |    1 |          1 |
|  85000 |          2 |    2 |          2 |
|  80000 |          3 |    3 |          3 |
|  75000 |          4 |    4 |          4 |
|  70000 |          5 |    5 |          5 |
|  70000 |          6 |    5 |          5 |
|  65000 |          7 |    7 |          6 |
|  60000 |          8 |    8 |          7 |
|  55000 |          9 |    9 |          8 |
|  50000 |         10 |   10 |          9 |

### Memorize this:

```text
ROW_NUMBER
→ no ties

RANK
→ ties + gaps

DENSE_RANK
→ ties + no gaps
```

---

# ⭐ VERY IMPORTANT INTERVIEW TRAP

Question:

> Find the second-highest salary.

This question is **ambiguous if duplicate salaries exist**.

If salary values are:

```text
90000
85000
85000
80000
```

Then:

### Second-highest DISTINCT salary:

```text
85000
```

Use `DENSE_RANK()`.

### Second row after sorting:

```text
85000
```

Could use `ROW_NUMBER()` but it means something different conceptually.

Always pay attention to words such as:

```text
distinct
unique
different
```

---

# 63. PARTITION BY

`PARTITION BY` creates separate windows/groups for the window function.

Example:

```sql
SELECT emp_name,
       dept_id,
       salary,
       RANK() OVER (
           PARTITION BY dept_id
           ORDER BY salary DESC
       ) AS dept_rank
FROM employee;
```

Think:

```text
Department 10
→ rank separately

Department 20
→ rank separately

Department 30
→ rank separately
```

---

# 64. Highest-paid employee in each department

```sql
SELECT *
FROM (
    SELECT e.*,
           RANK() OVER (
               PARTITION BY dept_id
               ORDER BY salary DESC
           ) AS rnk
    FROM employee e
) x
WHERE rnk = 1;
```

Why `RANK()` instead of `ROW_NUMBER()`?

Because if two employees tie for highest salary, we usually want **both**.

---

# 65. Top 2 employees from each department

```sql
SELECT *
FROM (
    SELECT e.*,
           ROW_NUMBER() OVER (
               PARTITION BY dept_id
               ORDER BY salary DESC
           ) AS rn
    FROM employee e
) x
WHERE rn <= 2;
```

### Pattern

```text
Top N per group
        ↓
ROW_NUMBER()
        ↓
PARTITION BY group
        ↓
ORDER BY value DESC
        ↓
outer WHERE <= N
```

---

# 66. Latest employee/record per group

General pattern:

```sql
SELECT *
FROM (
    SELECT t.*,
           ROW_NUMBER() OVER (
               PARTITION BY group_column
               ORDER BY date_column DESC
           ) AS rn
    FROM table_name t
) x
WHERE rn = 1;
```

This pattern appears frequently in interviews.

---

# 67. Window Aggregate Functions

Window functions aren't only ranking functions.

You can use:

```sql
SUM() OVER()
AVG() OVER()
COUNT() OVER()
MIN() OVER()
MAX() OVER()
```

Example:

```sql
SELECT emp_name,
       salary,
       AVG(salary) OVER () AS company_avg
FROM employee;
```

Every row gets the company average.

---

# 68. Department average beside each employee

```sql
SELECT emp_name,
       dept_id,
       salary,
       AVG(salary) OVER (
           PARTITION BY dept_id
       ) AS dept_avg
FROM employee;
```

This is often easier than joining a grouped subquery.

---

# 69. Running total

```sql
SELECT emp_id,
       emp_name,
       salary,
       SUM(salary) OVER (
           ORDER BY emp_id
       ) AS running_total
FROM employee;
```

Useful for:

```text
sales
transactions
expenses
cumulative totals
```

---

# 70. LAG()

`LAG()` looks at a previous row.

```sql
SELECT emp_id,
       salary,
       LAG(salary) OVER (
           ORDER BY emp_id
       ) AS previous_salary
FROM employee;
```

Useful for:

```text
current vs previous month
current vs previous transaction
salary changes
sales growth
```

---

# 71. LEAD()

`LEAD()` looks at a following row.

```sql
SELECT emp_id,
       salary,
       LEAD(salary) OVER (
           ORDER BY emp_id
       ) AS next_salary
FROM employee;
```

Think:

```text
LAG  → previous
LEAD → next
```

---

# 72. FIRST_VALUE()

Gets the first value in the window.

```sql
FIRST_VALUE(salary) OVER (
    ORDER BY salary DESC
)
```

---

# 73. LAST_VALUE()

Gets the last value according to the window frame.

⚠️ This function has a common trap: the **window frame** matters.

For beginner placement preparation, know the concept, but don't blindly assume `LAST_VALUE()` always means "minimum/last row of the whole partition."

---

# PART 14 — POSTGRESQL DISTINCT ON

# 74. DISTINCT ON

PostgreSQL-specific feature.

Example:

> highest-paid employee from each department

```sql
SELECT DISTINCT ON (dept_id)
       dept_id,
       emp_name,
       salary
FROM employee
ORDER BY dept_id, salary DESC;
```

Important:

```text
DISTINCT ON
→ PostgreSQL-specific
```

SQL Server does not have the same syntax.

A portable alternative is:

```sql
ROW_NUMBER()
```

---

# PART 15 — SET OPERATORS

# 75. UNION

Combines result sets and removes duplicates.

```sql
SELECT emp_name
FROM employee
WHERE dept_id = 10

UNION

SELECT emp_name
FROM employee
WHERE dept_id = 20;
```

---

# 76. UNION ALL

Combines result sets and keeps duplicates.

```sql
SELECT emp_name
FROM employee
WHERE dept_id = 10

UNION ALL

SELECT emp_name
FROM employee
WHERE dept_id = 20;
```

---

# 77. INTERSECT

Returns common rows.

```sql
SELECT emp_name
FROM employee
WHERE salary >= 60000

INTERSECT

SELECT emp_name
FROM employee
WHERE dept_id = 20;
```

---

# 78. EXCEPT

Rows in first query but not second.

```sql
SELECT emp_name
FROM employee

EXCEPT

SELECT emp_name
FROM employee
WHERE dept_id = 10;
```

---

# ⭐ Common Misconception

`UNION` and `UNION ALL` are **not the same**.

```text
UNION
→ duplicate removal

UNION ALL
→ no duplicate removal
```

If duplicates are allowed and you don't need deduplication, `UNION ALL` avoids the extra duplicate-elimination work.

---

# PART 16 — DATE FUNCTIONS

# 79. CURRENT DATE

```sql
SELECT CURRENT_DATE;
```

Current timestamp:

```sql
SELECT CURRENT_TIMESTAMP;
```

---

# 80. EXTRACT

Year:

```sql
SELECT EXTRACT(YEAR FROM hire_date)
FROM employee;
```

Month:

```sql
SELECT EXTRACT(MONTH FROM hire_date)
FROM employee;
```

---

# 81. DATE ARITHMETIC

```sql
SELECT hire_date + INTERVAL '7 days'
FROM employee;
```

---

# 82. STRING FUNCTIONS

Length:

```sql
SELECT LENGTH(emp_name)
FROM employee;
```

Uppercase:

```sql
SELECT UPPER(emp_name)
FROM employee;
```

Lowercase:

```sql
SELECT LOWER(emp_name)
FROM employee;
```

Concatenate:

```sql
SELECT emp_name || ' - ' || salary
FROM employee;
```

Substring:

```sql
SELECT SUBSTRING(emp_name FROM 1 FOR 3)
FROM employee;
```

---

# PART 17 — TRANSACTIONS

# 83. BEGIN

```sql
BEGIN;
```

Start transaction.

---

# 84. COMMIT

```sql
COMMIT;
```

Save transaction changes.

---

# 85. ROLLBACK

```sql
ROLLBACK;
```

Undo uncommitted transaction changes.

Example:

```sql
BEGIN;

UPDATE employee
SET salary = salary + 5000
WHERE dept_id = 10;

ROLLBACK;
```

The update is undone.

---

# 86. SAVEPOINT

```sql
BEGIN;

UPDATE employee
SET salary = salary + 5000
WHERE dept_id = 10;

SAVEPOINT sp1;

UPDATE employee
SET salary = salary + 10000
WHERE dept_id = 20;

ROLLBACK TO sp1;

COMMIT;
```

The first update remains; the second is rolled back.

---

# ⭐ Placement Question

### Q: Which ACID property is responsible for ensuring a transaction is all-or-nothing?

**Atomicity**

Example:

```text
Debit account A
       ↓
system crashes
       ↓
Credit account B didn't happen
```

Atomicity ensures the partial transaction is not left committed.

---

# PART 18 — INDEXES

# 87. CREATE INDEX

```sql
CREATE INDEX idx_employee_salary
ON employee(salary);
```

Composite index:

```sql
CREATE INDEX idx_employee_dept_salary
ON employee(dept_id, salary);
```

Delete:

```sql
DROP INDEX idx_employee_salary;
```

---

# 88. Why indexes?

Without a suitable index, the database may need to inspect many rows.

With an appropriate index, it may locate matching rows more efficiently.

Common columns considered for indexes:

```text
WHERE columns
JOIN columns
ORDER BY columns
frequently searched columns
```

But don't blindly index every column.

Indexes also consume storage and can add write overhead.

---

# PART 19 — QUERY OPTIMIZATION

# 89. EXPLAIN

```sql
EXPLAIN
SELECT *
FROM employee
WHERE salary > 70000;
```

Shows the query plan.

---

# 90. EXPLAIN ANALYZE

```sql
EXPLAIN ANALYZE
SELECT *
FROM employee
WHERE salary > 70000;
```

Actually executes the query and provides runtime information.

---

# 91. ANALYZE

```sql
ANALYZE employee;
```

Updates planner statistics.

---

# PART 20 — SQL EXECUTION ORDER

One of the most important concepts.

You write:

```sql
SELECT
FROM
WHERE
GROUP BY
HAVING
ORDER BY
LIMIT
```

But conceptually the database processes the query in approximately this order:

```text
1. FROM
2. JOIN
3. WHERE
4. GROUP BY
5. HAVING
6. SELECT
7. DISTINCT
8. ORDER BY
9. LIMIT / OFFSET
```

This explains many SQL errors.

---

# ⭐ Example

Why can't you normally do:

```sql
SELECT salary * 12 AS annual_salary
FROM employee
WHERE annual_salary > 800000;
```

Because the alias is created in the `SELECT` stage, while `WHERE` is conceptually evaluated earlier.

Use:

```sql
SELECT *
FROM (
    SELECT emp_name,
           salary * 12 AS annual_salary
    FROM employee
) x
WHERE annual_salary > 800000;
```

---

# PART 21 — COMMON INTERVIEW QUESTIONS

# Q1. Find employees earning more than average salary.

```sql
SELECT *
FROM employee
WHERE salary > (
    SELECT AVG(salary)
    FROM employee
);
```

Pattern:

```text
greater than average
→ subquery
```

---

# Q2. Find employee(s) with highest salary.

```sql
SELECT *
FROM employee
WHERE salary = (
    SELECT MAX(salary)
    FROM employee
);
```

---

# Q3. Find second-highest distinct salary.

```sql
SELECT *
FROM (
    SELECT salary,
           DENSE_RANK() OVER (
               ORDER BY salary DESC
           ) AS rnk
    FROM employee
) x
WHERE rnk = 2;
```

---

# Q4. Find employees with the second-highest distinct salary.

```sql
SELECT *
FROM (
    SELECT e.*,
           DENSE_RANK() OVER (
               ORDER BY salary DESC
           ) AS rnk
    FROM employee e
) x
WHERE rnk = 2;
```

---

# Q5. Find highest-paid employee in every department.

```sql
SELECT *
FROM (
    SELECT e.*,
           RANK() OVER (
               PARTITION BY dept_id
               ORDER BY salary DESC
           ) AS rnk
    FROM employee e
) x
WHERE rnk = 1;
```

---

# Q6. Find top 2 employees from every department.

```sql
SELECT *
FROM (
    SELECT e.*,
           ROW_NUMBER() OVER (
               PARTITION BY dept_id
               ORDER BY salary DESC
           ) AS rn
    FROM employee e
) x
WHERE rn <= 2;
```

---

# Q7. Find departments with more than 2 employees.

```sql
SELECT dept_id,
       COUNT(*) AS employee_count
FROM employee
GROUP BY dept_id
HAVING COUNT(*) > 2;
```

---

# Q8. Find departments with no employees.

```sql
SELECT d.dept_name
FROM department d
LEFT JOIN employee e
ON d.dept_id = e.dept_id
WHERE e.emp_id IS NULL;
```

---

# Q9. Find employee names with department names.

```sql
SELECT e.emp_name,
       d.dept_name
FROM employee e
JOIN department d
ON e.dept_id = d.dept_id;
```

---

# Q10. Find employees earning more than their department's average.

```sql
SELECT e.*
FROM employee e
WHERE e.salary > (
    SELECT AVG(e2.salary)
    FROM employee e2
    WHERE e2.dept_id = e.dept_id
);
```

---

# Q11. Find the average salary of each department.

```sql
SELECT dept_id,
       AVG(salary) AS avg_salary
FROM employee
GROUP BY dept_id;
```

---

# Q12. Find departments whose average salary is greater than company average.

```sql
SELECT dept_id,
       AVG(salary) AS avg_salary
FROM employee
GROUP BY dept_id
HAVING AVG(salary) > (
    SELECT AVG(salary)
    FROM employee
);
```

---

# Q13. Find the employee with the third-highest distinct salary.

```sql
SELECT *
FROM (
    SELECT e.*,
           DENSE_RANK() OVER (
               ORDER BY salary DESC
           ) AS rnk
    FROM employee e
) x
WHERE rnk = 3;
```

---

# Q14. Find the latest employee hired in each department.

```sql
SELECT *
FROM (
    SELECT e.*,
           ROW_NUMBER() OVER (
               PARTITION BY dept_id
               ORDER BY hire_date DESC
           ) AS rn
    FROM employee e
) x
WHERE rn = 1;
```

---

# Q15. Find employees whose name starts with A.

```sql
SELECT *
FROM employee
WHERE emp_name LIKE 'A%';
```

---

# Q16. Count employees in every department, including Marketing.

Important: use `COUNT(e.emp_id)`, not blindly `COUNT(*)`.

```sql
SELECT d.dept_name,
       COUNT(e.emp_id) AS employee_count
FROM department d
LEFT JOIN employee e
ON d.dept_id = e.dept_id
GROUP BY d.dept_id, d.dept_name;
```

Marketing gets:

```text
0
```

---

# ⭐ Why `COUNT(e.emp_id)` instead of `COUNT(*)`?

For Marketing, the `LEFT JOIN` still produces a row:

```text
Marketing | NULL
```

Therefore:

```sql
COUNT(*)
```

would count that joined row.

But:

```sql
COUNT(e.emp_id)
```

ignores the NULL employee ID.

This is a **very useful SQL interview trick**.

---

# PART 22 — FUNCTIONS YOU SHOULD KNOW

## Essential

```text
COUNT()
SUM()
AVG()
MIN()
MAX()

COALESCE()
NULLIF()

CASE

ROW_NUMBER()
RANK()
DENSE_RANK()

LAG()
LEAD()

FIRST_VALUE()
LAST_VALUE()

CURRENT_DATE
CURRENT_TIMESTAMP

EXTRACT()

UPPER()
LOWER()
LENGTH()
SUBSTRING()
```

---

# PART 23 — FUNCTION APPLICATION MAP

| Function        | What it does               | Typical question          |
| --------------- | --------------------------- | -------------------------- |
| `COUNT()`       | Counts                     | How many employees?       |
| `SUM()`         | Total                      | Total salary?             |
| `AVG()`         | Average                    | Average salary?           |
| `MIN()`         | Minimum                    | Lowest salary?            |
| `MAX()`         | Maximum                    | Highest salary?           |
| `COALESCE()`    | First non-NULL             | Replace missing value     |
| `NULLIF()`      | Returns NULL when equal    | Avoid division by zero    |
| `CASE`          | Conditional logic          | Categorize employees      |
| `ROW_NUMBER()`  | Unique sequence            | Top N / latest row        |
| `RANK()`        | Ranking with ties + gaps   | Competition ranking       |
| `DENSE_RANK()`  | Ranking with ties, no gaps | Nth distinct salary       |
| `LAG()`         | Previous row               | Compare previous value    |
| `LEAD()`        | Next row                   | Compare next value        |
| `FIRST_VALUE()` | First value                | Compare with first record |
| `LAST_VALUE()`  | Last value in frame        | Window-based comparison   |
| `SUM() OVER()`  | Running/window total       | Cumulative sales          |
| `AVG() OVER()`  | Window average             | Employee vs dept average  |

---

# PART 24 — SQL MISCONCEPTIONS / FUN FACTS

These are worth remembering because interviewers sometimes test them.

---

## 🧠 Fact 1 — NULL is not zero

```text
0       → actual number
NULL    → missing/unknown
```

---

## 🧠 Fact 2 — NULL = NULL is not TRUE

Use:

```sql
IS NULL
```

---

## 🧠 Fact 3 — COUNT(*) and COUNT(column) differ

```sql
COUNT(*)
```

counts rows.

```sql
COUNT(column)
```

ignores NULL values.

---

## 🧠 Fact 4 — WHERE and HAVING aren't interchangeable

```text
WHERE  → rows
HAVING → groups
```

---

## 🧠 Fact 5 — LEFT JOIN can accidentally become an INNER JOIN

Consider:

```sql
SELECT d.dept_name,
       e.emp_name
FROM department d
LEFT JOIN employee e
ON d.dept_id = e.dept_id
WHERE e.salary > 60000;
```

The `WHERE` condition removes rows where `e.salary` is NULL.

So departments with no employees disappear.

This can effectively defeat the purpose of the `LEFT JOIN`.

---

## 🧠 Fact 6 — `DISTINCT` isn't the same as `DISTINCT ON`

PostgreSQL:

```sql
DISTINCT
```

removes duplicate result rows.

PostgreSQL:

```sql
DISTINCT ON (dept_id)
```

selects one row for each `dept_id`, based on the ordering.

---

## 🧠 Fact 7 — GROUP BY reduces rows, window functions don't

```text
GROUP BY
→ one/more rows per group

Window function
→ original rows remain
```

---

## 🧠 Fact 8 — ORDER BY doesn't automatically mean ranking

```sql
ORDER BY salary DESC
```

sorts.

It does not create:

```text
1, 2, 3, 4...
```

For that:

```sql
ROW_NUMBER()
```

---

## 🧠 Fact 9 — LIMIT 3 does not mean "three highest distinct salaries"

It means:

> three rows after sorting.

If ties matter, consider ranking.

---

## 🧠 Fact 10 — `RANK()` can skip numbers

```text
1
2
2
4
```

This is intentional.

---

## 🧠 Fact 11 — DENSE_RANK doesn't skip numbers

```text
1
2
2
3
```

---

## 🧠 Fact 12 — SQL is generally declarative

You tell SQL:

> **what data you want**

rather than explicitly telling the database every step of **how to retrieve it**.

The database optimizer chooses an execution plan.

---

## 🧠 Fact 13 — The order you write SQL isn't the conceptual execution order

You write:

```text
SELECT
FROM
WHERE
GROUP BY
...
```

But `FROM/JOIN/WHERE` conceptually happen before `SELECT`.

This is why SQL aliases can behave unexpectedly in `WHERE`.

---

## 🧠 Fact 14 — `UNION ALL` doesn't remove duplicates

That's its point.

```text
UNION
→ deduplicate

UNION ALL
→ keep everything
```

---

## 🧠 Fact 15 — Indexes aren't automatically good

More indexes ≠ always faster.

Indexes:

```text
help reads
but
consume storage
and
can increase write overhead
```

---

## 🧠 Fact 16 — `SELECT *` isn't always a good habit

For learning:

```sql
SELECT *
```

is fine.

In production queries, selecting only required columns can be clearer and avoid unnecessary data transfer.

---

## 🧠 Fact 17 — SQL keyword capitalization doesn't normally matter

These are equivalent:

```sql
select * from employee;
```

and:

```sql
SELECT * FROM employee;
```

SQL style usually uses uppercase keywords because it improves readability.

---

## 🧠 Fact 18 — JOIN doesn't necessarily mean "slow"

Students sometimes hear:

> "Avoid JOIN because JOIN is expensive."

That's misleading.

Joins are fundamental relational operations. Performance depends on:

```text
data size
indexes
join conditions
statistics
query plan
database engine
```

---

# PART 25 — POSTGRESQL vs SQL SERVER

Because you are practicing PostgreSQL but may face SQL Server questions.

| Task                  | PostgreSQL                          | SQL Server                              |
| --------------------- | ------------------------------------ | ---------------------------------------- |
| Top 5                 | `LIMIT 5`                           | `TOP 5`                                 |
| Pagination            | `LIMIT 5 OFFSET 10`                 | `OFFSET 10 ROWS FETCH NEXT 5 ROWS ONLY` |
| Current date/time     | `CURRENT_DATE`, `CURRENT_TIMESTAMP` | `GETDATE()`                             |
| Length                | `LENGTH()`                          | `LEN()`                                 |
| Concatenation         | `\|\|`                              | `+`                                     |
| Case-insensitive LIKE | `ILIKE`                             | Usually `LIKE` based on collation       |
| Date add              | `date + INTERVAL '7 days'`          | `DATEADD(day,7,date)`                   |
| Date difference       | date arithmetic / `AGE()`           | `DATEDIFF()`                            |
| Boolean               | `BOOLEAN`                           | `BIT`                                   |
| PostgreSQL cast       | `salary::INT`                       | not supported                           |
| General cast          | `CAST()`                            | `CAST()`                                |
| NULL fallback         | `COALESCE()`                        | `COALESCE()` / `ISNULL()`               |
| One row/group         | `DISTINCT ON`                       | `ROW_NUMBER()` pattern                  |
| Auto increment        | generated identity                  | `IDENTITY(1,1)`                         |

### Most important point

**DBeaver vs SSMS does NOT determine SQL syntax.**

The database engine determines syntax.

```text
DBeaver
→ can connect to PostgreSQL
→ can connect to SQL Server
→ can connect to MySQL
→ etc.

SSMS
→ primarily SQL Server
```

---

# PART 26 — MASTER ENGLISH → SQL CHEAT SHEET

This is the section I would revise before an OA.

| Question wording                    | Think                                   |
| ------------------------------------ | ----------------------------------------- |
| find/display employees              | `SELECT`                                |
| whose salary > X                    | `WHERE`                                 |
| not equal                           | `<>`                                    |
| either X or Y                       | `OR`                                    |
| among these values                  | `IN`                                    |
| between X and Y                     | `BETWEEN`                               |
| starts with                         | `LIKE 'X%'`                             |
| ends with                           | `LIKE '%X'`                             |
| contains                            | `LIKE '%X%'`                            |
| unique/distinct                     | `DISTINCT`                              |
| highest                             | `MAX()`                                 |
| lowest                              | `MIN()`                                 |
| average                             | `AVG()`                                 |
| total                               | `SUM()`                                 |
| number of                           | `COUNT()`                               |
| for each department                 | `GROUP BY`                              |
| groups having                       | `HAVING`                                |
| along with department details       | `JOIN`                                  |
| all departments                     | `LEFT JOIN`                             |
| no matching employees               | `LEFT JOIN + IS NULL`                   |
| greater than company average        | `SUBQUERY`                              |
| greater than own department average | `CORRELATED SUBQUERY`                   |
| categorize/classify                 | `CASE`                                  |
| replace NULL                        | `COALESCE`                              |
| prevent divide by zero              | `NULLIF`                                |
| rank everyone                       | `RANK()`                                |
| unique numbering                    | `ROW_NUMBER()`                          |
| nth distinct value                  | `DENSE_RANK()`                          |
| rank within department              | `PARTITION BY`                          |
| top N overall                       | `ORDER BY + LIMIT`                      |
| top N per department                | `ROW_NUMBER + PARTITION BY`             |
| highest per department              | `RANK + PARTITION BY`                   |
| latest per group                    | `ROW_NUMBER + PARTITION BY + date DESC` |
| previous row                        | `LAG()`                                 |
| next row                            | `LEAD()`                                |
| cumulative/running total            | `SUM() OVER()`                          |
| combine results                     | `UNION`                                 |
| combine and keep duplicates         | `UNION ALL`                             |
| common results                      | `INTERSECT`                             |
| first result minus second           | `EXCEPT`                                |

---

# PART 27 — FINAL PLACEMENT REVISION ORDER

Study/revise in this order:

```text
01. CREATE DATABASE
02. CREATE TABLE
03. Constraints
04. ALTER
05. DROP
06. TRUNCATE

07. INSERT
08. UPDATE
09. DELETE

10. SELECT
11. Aliases
12. Calculated columns

13. WHERE
14. AND / OR / NOT
15. IN
16. BETWEEN
17. LIKE
18. NULL

19. DISTINCT
20. ORDER BY
21. LIMIT
22. OFFSET

23. COUNT
24. SUM
25. AVG
26. MIN
27. MAX

28. GROUP BY
29. HAVING

30. INNER JOIN
31. LEFT JOIN
32. RIGHT JOIN
33. FULL JOIN
34. SELF JOIN
35. CROSS JOIN

36. Subqueries
37. Correlated subqueries

38. CASE
39. COALESCE
40. NULLIF

41. ROW_NUMBER
42. RANK
43. DENSE_RANK
44. PARTITION BY
45. LAG
46. LEAD
47. Window aggregates

48. DISTINCT ON
49. UNION
50. UNION ALL
51. INTERSECT
52. EXCEPT

53. Date functions
54. String functions
55. Casting

56. Transactions
57. Indexes
58. EXPLAIN
59. EXPLAIN ANALYZE
60. Query optimization

61. Mixed placement questions
```

---

# 🎯 THE 15 PATTERNS YOU SHOULD BE ABLE TO RECOGNIZE INSTANTLY

### 1. Top N overall

```sql
ORDER BY salary DESC
LIMIT N
```

### 2. Highest salary

```sql
MAX(salary)
```

### 3. Above average

```sql
WHERE salary > (
    SELECT AVG(salary)
)
```

### 4. Employee + department

```sql
JOIN
```

### 5. All departments including empty

```sql
LEFT JOIN
```

### 6. Departments with no employees

```sql
LEFT JOIN
+
IS NULL
```

### 7. Group-wise count/average

```sql
GROUP BY
```

### 8. Filter groups

```sql
HAVING
```

### 9. Rank everyone

```sql
RANK() OVER (ORDER BY ...)
```

### 10. Rank within group

```sql
RANK() OVER (
    PARTITION BY ...
    ORDER BY ...
)
```

### 11. Top N per group

```sql
ROW_NUMBER()
+
PARTITION BY
```

### 12. Nth distinct value

```sql
DENSE_RANK()
```

### 13. Latest record per group

```sql
ROW_NUMBER()
+
PARTITION BY
+
ORDER BY date DESC
```

### 14. Previous/next row

```text
LAG()
LEAD()
```

### 15. Conditional classification

```sql
CASE
    WHEN ...
    THEN ...
END
```

---

# 📝 FINAL EXAM CHECKLIST

Before submitting a SQL answer, mentally check:

```text
☐ Did I select the required columns?
☐ Am I filtering rows or groups?
☐ Do I need WHERE or HAVING?
☐ Do I need another table?
☐ Should it be INNER JOIN or LEFT JOIN?
☐ Can NULL be involved?
☐ Do I need DISTINCT?
☐ Do I need GROUP BY?
☐ Do I need a subquery?
☐ Is this a ranking problem?
☐ Is the ranking overall or per group?
☐ Are ties important?
☐ Do I need ROW_NUMBER, RANK or DENSE_RANK?
☐ Does "top N" mean overall or per group?
☐ Does "second highest" mean distinct?
☐ Do I need ORDER BY?
☐ Do I need LIMIT?
☐ Could duplicate rows appear?
```

---

# 🧠 GOLDEN SQL RULE

Don't memorize:

```text
"Question 17 uses RANK."
```

Instead learn:

```text
"What does the English question require?"
                  ↓
        Identify the operation
                  ↓
        Choose SQL pattern
                  ↓
        Write syntax
                  ↓
        Check edge cases
```

For example:

```text
"Find the top 2 highest-paid employees
from EACH department."

             ↓

"each department"
             ↓
PARTITION BY department

"top 2"
             ↓
ROW_NUMBER()

"highest-paid"
             ↓
ORDER BY salary DESC

Therefore:

ROW_NUMBER()
OVER (
    PARTITION BY dept_id
    ORDER BY salary DESC
)
```

That is the level of understanding you should aim for in placement SQL.
