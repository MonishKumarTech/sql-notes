# SQL interview questions

## Purpose

This file contains common SQL interview questions and concise answers for entry-level roles such as:

* Application Support Engineer
* IT Support Engineer
* Technical Support Engineer
* MIS Executive
* Reporting Executive
* Operations Support Executive
* Junior Database Support Engineer

The focus is on practical understanding rather than memorizing definitions without knowing how to use them.

---

## Database fundamentals

### 1. What is data?

Data is a collection of facts or information.

Examples include:

* Employee names
* Ticket statuses
* Transaction amounts
* User account details
* Product prices

---

### 2. What is a database?

A database is an organized collection of related data that can be stored, retrieved, updated, and managed.

---

### 3. What is a DBMS?

DBMS means Database Management System.

It is software used to create, manage, retrieve, and modify databases.

Examples include:

* MySQL
* PostgreSQL
* Microsoft SQL Server
* Oracle Database
* SQLite

---

### 4. What is SQL?

SQL means Structured Query Language.

It is used to communicate with relational databases.

SQL can:

* Retrieve data
* Insert records
* Update records
* Delete records
* Create tables
* Manage permissions
* Control transactions

---

### 5. What is the difference between SQL and MySQL?

| SQL                                | MySQL                        |
| ---------------------------------- | ---------------------------- |
| Query language                     | Database management system   |
| Used to communicate with databases | Uses SQL to manage databases |
| Defines commands such as `SELECT`  | Stores and processes data    |
| Language                           | Software                     |

Quick answer:

```text
SQL is a language. MySQL is database software that uses SQL.
```

---

### 6. What is a relational database?

A relational database stores data in tables containing rows and columns.

Tables can be connected using keys and relationships.

---

### 7. What is a table?

A table stores related data using rows and columns.

Example:

| employee_id | employee_name | department |
| ----------: | ------------- | ---------- |
|         101 | Ravi          | IT Support |
|         102 | Priya         | Finance    |

---

### 8. What is a row?

A row represents one complete record.

Example:

```text
101 | Ravi | IT Support
```

A row is also called a record or tuple.

---

### 9. What is a column?

A column represents one type of information.

Examples:

* `employee_id`
* `employee_name`
* `department`
* `salary`

A column may also be called a field or attribute.

---

### 10. What is a schema?

A schema defines the structure of a database.

It includes:

* Tables
* Columns
* Data types
* Relationships
* Keys
* Constraints

---

### 11. What is a data type?

A data type defines what kind of value a column can store.

Examples:

| Data type  | Purpose              |
| ---------- | -------------------- |
| `INT`      | Whole numbers        |
| `DECIMAL`  | Decimal numbers      |
| `VARCHAR`  | Variable-length text |
| `DATE`     | Date values          |
| `DATETIME` | Date and time        |
| `BOOLEAN`  | True or false        |
| `TEXT`     | Long text            |

---

### 12. What is NULL?

`NULL` means a value is missing, unknown, or unavailable.

It is not the same as:

* Zero
* Empty text
* Space
* False

---

## SQL command categories

### 13. What are the main SQL command categories?

| Category | Purpose                    | Common commands                       |
| -------- | -------------------------- | ------------------------------------- |
| DQL      | Retrieve data              | `SELECT`                              |
| DDL      | Define database structures | `CREATE`, `ALTER`, `DROP`, `TRUNCATE` |
| DML      | Modify records             | `INSERT`, `UPDATE`, `DELETE`          |
| DCL      | Manage permissions         | `GRANT`, `REVOKE`                     |
| TCL      | Manage transactions        | `COMMIT`, `ROLLBACK`, `SAVEPOINT`     |

---

### 14. What is DDL?

DDL means Data Definition Language.

It is used to create or modify database structures.

Examples:

```sql
CREATE
ALTER
DROP
TRUNCATE
```

---

### 15. What is DML?

DML means Data Manipulation Language.

It is used to insert, update, and delete records.

Examples:

```sql
INSERT
UPDATE
DELETE
```

---

### 16. What is DQL?

DQL means Data Query Language.

It is used to retrieve data.

```sql
SELECT
```

---

### 17. What is CRUD?

CRUD represents four basic database operations.

| Letter | Operation | SQL command |
| ------ | --------- | ----------- |
| C      | Create    | `INSERT`    |
| R      | Read      | `SELECT`    |
| U      | Update    | `UPDATE`    |
| D      | Delete    | `DELETE`    |

---

## SELECT questions

### 18. What does SELECT do?

`SELECT` retrieves data from a table.

```sql
SELECT employee_name
FROM employees;
```

---

### 19. How do you select multiple columns?

Separate column names with commas.

```sql
SELECT
    employee_name,
    department,
    salary
FROM employees;
```

---

### 20. What does SELECT * mean?

`SELECT *` retrieves every column from a table.

```sql
SELECT *
FROM employees;
```

It should be used carefully because it may return unnecessary or sensitive columns.

---

### 21. Why should SELECT * be avoided in production queries?

It may:

* Retrieve unnecessary data
* Reduce readability
* Expose sensitive columns
* Increase data transfer
* Create problems when table structures change

Better:

```sql
SELECT
    employee_id,
    employee_name,
    status
FROM employees;
```

---

### 22. What is DISTINCT?

`DISTINCT` removes duplicate result rows.

```sql
SELECT DISTINCT department
FROM employees;
```

---

### 23. What is an alias?

An alias is a temporary name given to a column or table.

Column alias:

```sql
SELECT employee_name AS name
FROM employees;
```

Table alias:

```sql
SELECT e.employee_name
FROM employees AS e;
```

Aliases do not permanently rename database objects.

---

## WHERE questions

### 24. What does WHERE do?

`WHERE` filters rows based on a condition.

```sql
SELECT employee_name
FROM employees
WHERE status = 'Active';
```

---

### 25. What are common comparison operators?

| Operator | Meaning               |
| -------- | --------------------- |
| `=`      | Equal                 |
| `<>`     | Not equal             |
| `!=`     | Not equal             |
| `>`      | Greater than          |
| `<`      | Less than             |
| `>=`     | Greater than or equal |
| `<=`     | Less than or equal    |

---

### 26. What is the difference between AND and OR?

`AND` requires every condition to be true.

```sql
SELECT *
FROM employees
WHERE department = 'IT Support'
  AND status = 'Active';
```

`OR` requires at least one condition to be true.

```sql
SELECT *
FROM employees
WHERE city = 'Chennai'
   OR city = 'Vellore';
```

---

### 27. Why are parentheses important with AND and OR?

SQL evaluates `AND` before `OR`.

Parentheses make the intended logic clear.

```sql
SELECT *
FROM employees
WHERE status = 'Active'
  AND (
      city = 'Chennai'
      OR city = 'Vellore'
  );
```

---

### 28. What does IN do?

`IN` checks whether a value matches any value in a list.

```sql
SELECT *
FROM employees
WHERE city IN ('Chennai', 'Vellore');
```

---

### 29. What does BETWEEN do?

`BETWEEN` checks whether a value falls inside an inclusive range.

```sql
SELECT *
FROM employees
WHERE salary BETWEEN 25000 AND 35000;
```

Both boundary values are included.

---

### 30. What does LIKE do?

`LIKE` performs pattern matching.

```sql
SELECT employee_name
FROM employees
WHERE employee_name LIKE 'R%';
```

Wildcards:

| Wildcard | Meaning                 |
| -------- | ----------------------- |
| `%`      | Zero or more characters |
| `_`      | Exactly one character   |

---

### 31. How do you find values containing a word?

```sql
SELECT *
FROM tickets
WHERE issue LIKE '%password%';
```

---

### 32. How do you check for NULL?

Use `IS NULL`.

```sql
SELECT *
FROM employees
WHERE phone_number IS NULL;
```

Do not use:

```sql
WHERE phone_number = NULL;
```

---

### 33. What is the difference between IS NULL and IS NOT NULL?

`IS NULL` finds missing values.

```sql
WHERE email IS NULL
```

`IS NOT NULL` finds available values.

```sql
WHERE email IS NOT NULL
```

---

## Sorting and limiting

### 34. What does ORDER BY do?

`ORDER BY` sorts query results.

```sql
SELECT employee_name, salary
FROM employees
ORDER BY salary DESC;
```

---

### 35. What is the difference between ASC and DESC?

| Keyword | Meaning    |
| ------- | ---------- |
| `ASC`   | Ascending  |
| `DESC`  | Descending |

Examples:

```text
ASC numbers: lowest to highest
DESC numbers: highest to lowest
ASC dates: oldest to newest
DESC dates: newest to oldest
```

---

### 36. What is the default sorting order?

The default order is usually ascending.

These are generally equivalent:

```sql
ORDER BY employee_name;
```

```sql
ORDER BY employee_name ASC;
```

---

### 37. What does LIMIT do?

`LIMIT` restricts the number of rows returned.

```sql
SELECT employee_name, salary
FROM employees
ORDER BY salary DESC
LIMIT 3;
```

---

### 38. Why should LIMIT normally be used with ORDER BY?

Without `ORDER BY`, the database does not guarantee which rows will be returned.

Weak:

```sql
SELECT *
FROM employees
LIMIT 3;
```

Meaningful:

```sql
SELECT *
FROM employees
ORDER BY joining_date DESC
LIMIT 3;
```

---

### 39. What does OFFSET do?

`OFFSET` skips rows before returning results.

```sql
SELECT *
FROM tickets
ORDER BY ticket_id
LIMIT 10
OFFSET 20;
```

This skips the first 20 rows and returns the next 10.

---

## Aggregate functions

### 40. What is an aggregate function?

An aggregate function summarizes several rows and returns one result.

Common aggregate functions:

* `COUNT()`
* `SUM()`
* `AVG()`
* `MIN()`
* `MAX()`

---

### 41. What does COUNT(*) do?

`COUNT(*)` counts every row.

```sql
SELECT COUNT(*) AS employee_count
FROM employees;
```

---

### 42. What is the difference between COUNT(*) and COUNT(column)?

`COUNT(*)` counts all rows.

`COUNT(column)` counts only non-`NULL` values in that column.

```sql
SELECT
    COUNT(*) AS total_users,
    COUNT(email) AS users_with_email
FROM application_users;
```

---

### 43. What does COUNT(DISTINCT column) do?

It counts unique non-`NULL` values.

```sql
SELECT COUNT(DISTINCT department)
FROM employees;
```

---

### 44. What does SUM do?

`SUM()` calculates the total of a numeric column.

```sql
SELECT SUM(salary) AS total_salary
FROM employees;
```

---

### 45. What does AVG do?

`AVG()` calculates the average of a numeric column.

```sql
SELECT ROUND(AVG(salary), 2) AS average_salary
FROM employees;
```

---

### 46. What do MIN and MAX do?

`MIN()` returns the lowest value.

`MAX()` returns the highest value.

```sql
SELECT
    MIN(salary) AS lowest_salary,
    MAX(salary) AS highest_salary
FROM employees;
```

---

### 47. Do aggregate functions include NULL values?

Most aggregate functions ignore `NULL`.

`COUNT(*)` still counts the row.

`AVG(column)` does not treat `NULL` as zero.

---

### 48. Does MAX(salary) return the employee name?

No.

```sql
SELECT MAX(salary)
FROM employees;
```

returns only the highest salary value.

To retrieve the employee record:

```sql
SELECT
    employee_name,
    salary
FROM employees
ORDER BY salary DESC
LIMIT 1;
```

---

## GROUP BY and HAVING

### 49. What does GROUP BY do?

`GROUP BY` combines rows with matching values and calculates one result for each group.

```sql
SELECT
    department,
    COUNT(*) AS employee_count
FROM employees
GROUP BY department;
```

---

### 50. How do you calculate average salary by department?

```sql
SELECT
    department,
    ROUND(AVG(salary), 2) AS average_salary
FROM employees
GROUP BY department;
```

---

### 51. What does HAVING do?

`HAVING` filters grouped results.

```sql
SELECT
    department,
    COUNT(*) AS employee_count
FROM employees
GROUP BY department
HAVING COUNT(*) >= 5;
```

---

### 52. What is the difference between WHERE and HAVING?

| WHERE                       | HAVING                                  |
| --------------------------- | --------------------------------------- |
| Filters individual rows     | Filters grouped results                 |
| Runs before grouping        | Runs after grouping                     |
| Used with normal conditions | Commonly used with aggregate conditions |

Example:

```sql
SELECT
    department,
    COUNT(*) AS active_employee_count
FROM employees
WHERE status = 'Active'
GROUP BY department
HAVING COUNT(*) >= 2;
```

---

### 53. Can aggregate functions be used in WHERE?

Normally, no.

Wrong:

```sql
WHERE COUNT(*) > 5
```

Correct:

```sql
HAVING COUNT(*) > 5
```

---

### 54. What happens if a selected column is not in GROUP BY?

The query may fail or produce unreliable results because the database does not know which value should represent the group.

Problematic:

```sql
SELECT
    employee_name,
    department,
    COUNT(*)
FROM employees
GROUP BY department;
```

Correct:

```sql
SELECT
    department,
    COUNT(*) AS employee_count
FROM employees
GROUP BY department;
```

---

## Joins

### 55. What is a join?

A join combines related rows from two or more tables.

---

### 56. What does INNER JOIN return?

`INNER JOIN` returns only matching rows from both tables.

```sql
SELECT
    e.employee_name,
    d.department_name
FROM employees AS e
INNER JOIN departments AS d
    ON e.department_id = d.department_id;
```

---

### 57. What does LEFT JOIN return?

`LEFT JOIN` returns:

* Every row from the left table
* Matching rows from the right table
* `NULL` when no right-side match exists

```sql
SELECT
    e.employee_name,
    d.department_name
FROM employees AS e
LEFT JOIN departments AS d
    ON e.department_id = d.department_id;
```

---

### 58. What is the difference between INNER JOIN and LEFT JOIN?

`INNER JOIN` removes unmatched records.

`LEFT JOIN` preserves every record from the left table.

---

### 59. How do you find unmatched records?

Use `LEFT JOIN` with `IS NULL`.

```sql
SELECT
    e.employee_id,
    e.employee_name
FROM employees AS e
LEFT JOIN departments AS d
    ON e.department_id = d.department_id
WHERE d.department_id IS NULL;
```

---

### 60. What is a self join?

A self join connects a table to itself.

Example: employees and their managers.

```sql
SELECT
    e.employee_name AS employee,
    m.employee_name AS manager
FROM employees AS e
LEFT JOIN employees AS m
    ON e.manager_id = m.employee_id;
```

---

### 61. What is a CROSS JOIN?

A `CROSS JOIN` returns every possible combination of rows from two tables.

If one table has 5 rows and another has 4 rows:

```text
5 × 4 = 20 result rows
```

---

### 62. Does MySQL support FULL OUTER JOIN?

MySQL does not directly support `FULL OUTER JOIN`.

It can be simulated using:

* `LEFT JOIN`
* `RIGHT JOIN`
* `UNION`

---

### 63. What happens when a join condition is missing?

The query may create a Cartesian product and return every possible row combination.

This can produce an extremely large and incorrect result.

---

### 64. Why can joins produce repeated rows?

One row may match several rows in another table.

Example:

```text
One ticket can have many comments.
```

The ticket appears once for every matching comment.

This may be correct behaviour, not a duplicate-data error.

---

### 65. Why should COUNT(*) be used carefully after LEFT JOIN?

A left join preserves one result row even when no match exists.

Use a right-table key:

```sql
COUNT(t.ticket_id)
```

instead of:

```sql
COUNT(*)
```

when counting matching records.

---

## Subqueries

### 66. What is a subquery?

A subquery is a query written inside another query.

```sql
SELECT
    employee_name,
    salary
FROM employees
WHERE salary > (
    SELECT AVG(salary)
    FROM employees
);
```

---

### 67. What is a scalar subquery?

A scalar subquery returns exactly one value.

Example:

```sql
SELECT MAX(salary)
FROM employees;
```

---

### 68. What is a correlated subquery?

A correlated subquery refers to the current row from the outer query.

```sql
SELECT
    e.employee_name,
    e.salary
FROM employees AS e
WHERE e.salary > (
    SELECT AVG(e2.salary)
    FROM employees AS e2
    WHERE e2.department_id = e.department_id
);
```

It may run once for each outer row.

---

### 69. What is the difference between a normal and correlated subquery?

A normal subquery runs independently.

A correlated subquery depends on a value from the outer query.

---

### 70. What does EXISTS do?

`EXISTS` checks whether a subquery returns at least one row.

```sql
SELECT
    d.department_name
FROM departments AS d
WHERE EXISTS (
    SELECT 1
    FROM employees AS e
    WHERE e.department_id = d.department_id
);
```

---

### 71. What does NOT EXISTS do?

`NOT EXISTS` checks whether no matching row exists.

```sql
SELECT
    d.department_name
FROM departments AS d
WHERE NOT EXISTS (
    SELECT 1
    FROM employees AS e
    WHERE e.department_id = d.department_id
);
```

---

### 72. Why can NOT IN be dangerous with NULL?

If the subquery returns `NULL`, `NOT IN` may produce unexpected results.

`NOT EXISTS` is often safer when `NULL` values are possible.

---

### 73. What is the difference between IN and EXISTS?

`IN` compares a value against a returned list.

`EXISTS` checks whether matching rows exist.

---

### 74. When should a join be used instead of a subquery?

A join is often clearer when:

* Columns from several tables must be displayed
* Several related tables are involved
* Relationships are the main part of the query

A subquery is often clearer when:

* One calculated value is required
* Existence must be checked
* The outer query depends on a separate summary

---

## CRUD and transaction questions

### 75. How do you insert a record?

```sql
INSERT INTO employees (
    employee_id,
    employee_name,
    department,
    salary
)
VALUES (
    101,
    'Ravi',
    'IT Support',
    28000
);
```

---

### 76. Why should column names be included in INSERT?

It:

* Improves readability
* Prevents column-order mistakes
* Makes the query safer when table structures change

---

### 77. How do you insert multiple records?

```sql
INSERT INTO employees (
    employee_id,
    employee_name
)
VALUES
    (101, 'Ravi'),
    (102, 'Priya'),
    (103, 'Arun');
```

---

### 78. How do you update a record?

```sql
UPDATE employees
SET salary = 30000
WHERE employee_id = 101;
```

---

### 79. What happens if UPDATE has no WHERE clause?

Every row may be updated.

```sql
UPDATE employees
SET status = 'Inactive';
```

This changes the status of every employee.

---

### 80. How do you delete one record?

```sql
DELETE FROM employees
WHERE employee_id = 101;
```

---

### 81. What happens if DELETE has no WHERE clause?

Every record is deleted.

The table structure remains.

---

### 82. What is the difference between DELETE, TRUNCATE, and DROP?

| Command    | Result                                           |
| ---------- | ------------------------------------------------ |
| `DELETE`   | Removes selected or all rows and keeps the table |
| `TRUNCATE` | Removes all rows and keeps the table             |
| `DROP`     | Removes the table itself                         |

---

### 83. What should be done before UPDATE or DELETE?

Use this workflow:

```text
1. Run the condition using SELECT.
2. Check the matching records.
3. Start a transaction.
4. Run UPDATE or DELETE.
5. Verify the result.
6. Commit or roll back.
```

---

### 84. What is a transaction?

A transaction is a group of database operations treated as one unit.

The changes should either:

* Complete successfully
* Or be cancelled together

---

### 85. What does COMMIT do?

`COMMIT` permanently saves transaction changes.

```sql
COMMIT;
```

---

### 86. What does ROLLBACK do?

`ROLLBACK` cancels uncommitted transaction changes.

```sql
ROLLBACK;
```

---

### 87. What is a SAVEPOINT?

A savepoint creates a checkpoint inside a transaction.

```sql
SAVEPOINT before_delete;
```

Rollback to the checkpoint:

```sql
ROLLBACK TO before_delete;
```

---

### 88. What is an upsert?

An upsert inserts a new record or updates an existing record when a key conflict occurs.

MySQL example:

```sql
INSERT INTO application_users (
    user_id,
    username,
    account_status
)
VALUES (
    101,
    'ravi.k',
    'Active'
)
ON DUPLICATE KEY UPDATE
    account_status = 'Active';
```

---

## Keys and constraints

### 89. What is a primary key?

A primary key uniquely identifies each row.

It:

* Must be unique
* Cannot contain `NULL`
* Should remain stable

---

### 90. What is a foreign key?

A foreign key connects one table to another.

```sql
FOREIGN KEY (department_id)
    REFERENCES departments(department_id)
```

---

### 91. What is a composite key?

A composite key contains two or more columns.

```sql
PRIMARY KEY (student_id, course_id)
```

---

### 92. What is a candidate key?

A candidate key is a minimal column or column combination that uniquely identifies a row.

---

### 93. What is an alternate key?

An alternate key is a candidate key that was not selected as the primary key.

---

### 94. What is a surrogate key?

A surrogate key is an artificial identifier generated for database use.

Example:

```sql
user_id INT AUTO_INCREMENT PRIMARY KEY
```

---

### 95. What is a natural key?

A natural key comes from real-world data.

Examples:

* Email address
* Passport number
* Employee code
* Product serial number

---

### 96. What is referential integrity?

Referential integrity ensures that foreign-key values refer to valid parent records.

It prevents records from pointing to nonexistent related records.

---

### 97. What does ON DELETE CASCADE do?

It automatically deletes related child records when the parent record is deleted.

Use it carefully because one deletion may remove many related records.

---

### 98. What does ON DELETE SET NULL do?

It changes the child foreign-key value to `NULL` when the parent is deleted.

The foreign-key column must allow `NULL`.

---

### 99. What is a constraint?

A constraint is a rule applied to database data.

Common constraints:

* `PRIMARY KEY`
* `FOREIGN KEY`
* `UNIQUE`
* `NOT NULL`
* `DEFAULT`
* `CHECK`

---

### 100. What is the difference between a primary key and a unique key?

| Primary key                          | Unique key                           |
| ------------------------------------ | ------------------------------------ |
| Main row identifier                  | Prevents duplicate values            |
| Cannot contain `NULL`                | `NULL` behaviour depends on DBMS     |
| One primary-key constraint per table | Several unique constraints can exist |

---

## Normalization

### 101. What is normalization?

Normalization organizes database tables to reduce duplication and improve data consistency.

---

### 102. Why is normalization important?

It helps prevent:

* Duplicate data
* Inconsistent values
* Update problems
* Insertion problems
* Accidental information loss

---

### 103. What is an insertion anomaly?

An insertion anomaly occurs when one fact cannot be stored without another unrelated fact.

Example:

A department cannot be added until an employee joins it.

---

### 104. What is an update anomaly?

An update anomaly occurs when repeated data must be updated in several rows.

If one row is missed, the database becomes inconsistent.

---

### 105. What is a deletion anomaly?

A deletion anomaly occurs when deleting one record unintentionally removes other useful information.

---

### 106. What is first normal form?

First normal form requires:

* One value per cell
* No repeating groups
* Atomic values
* Identifiable rows

Quick recall:

```text
1NF = one value per cell.
```

---

### 107. What is second normal form?

Second normal form requires:

* First normal form
* No partial dependency on part of a composite key

Quick recall:

```text
2NF = no partial dependency.
```

---

### 108. What is third normal form?

Third normal form requires:

* Second normal form
* No transitive dependency between non-key columns

Quick recall:

```text
3NF = non-key columns depend only on the key.
```

---

### 109. What is denormalization?

Denormalization intentionally adds controlled duplication to improve read performance or simplify reporting.

It should be used only when there is a measured requirement.

---

## Practical support scenarios

### 110. How do you find open tickets?

```sql
SELECT
    ticket_id,
    issue,
    priority,
    created_at
FROM tickets
WHERE status = 'Open';
```

---

### 111. How do you find unassigned tickets?

```sql
SELECT
    ticket_id,
    issue,
    priority
FROM tickets
WHERE assigned_to IS NULL;
```

---

### 112. How do you find the oldest unresolved ticket?

```sql
SELECT
    ticket_id,
    issue,
    created_at
FROM tickets
WHERE status <> 'Closed'
ORDER BY created_at ASC
LIMIT 1;
```

---

### 113. How do you count tickets by status?

```sql
SELECT
    status,
    COUNT(*) AS ticket_count
FROM tickets
GROUP BY status;
```

---

### 114. How do you count tickets assigned to each technician?

```sql
SELECT
    tech.technician_name,
    COUNT(t.ticket_id) AS ticket_count
FROM technicians AS tech
LEFT JOIN tickets AS t
    ON tech.technician_id = t.assigned_to
GROUP BY
    tech.technician_id,
    tech.technician_name;
```

---

### 115. How do you find technicians with no assigned tickets?

```sql
SELECT
    tech.technician_id,
    tech.technician_name
FROM technicians AS tech
LEFT JOIN tickets AS t
    ON tech.technician_id = t.assigned_to
WHERE t.ticket_id IS NULL;
```

---

### 116. How do you find locked user accounts?

```sql
SELECT
    user_id,
    username,
    failed_login_count
FROM application_users
WHERE account_status = 'Locked';
```

---

### 117. How do you find users with three or more failed logins?

```sql
SELECT
    user_id,
    COUNT(*) AS failed_login_count
FROM login_attempts
WHERE login_status = 'Failed'
GROUP BY user_id
HAVING COUNT(*) >= 3;
```

---

### 118. How do you find users without email addresses?

```sql
SELECT
    user_id,
    username
FROM application_users
WHERE email IS NULL;
```

---

### 119. How do you calculate average ticket resolution time?

```sql
SELECT
    ROUND(AVG(resolution_minutes), 2)
        AS average_resolution_minutes
FROM tickets
WHERE status = 'Closed';
```

---

### 120. How do you find closed tickets without resolution time?

```sql
SELECT
    ticket_id,
    issue,
    closed_at
FROM tickets
WHERE status = 'Closed'
  AND resolution_minutes IS NULL;
```

---

### 121. How do you find tickets without comments?

```sql
SELECT
    t.ticket_id,
    t.issue
FROM tickets AS t
LEFT JOIN ticket_comments AS c
    ON t.ticket_id = c.ticket_id
WHERE c.comment_id IS NULL;
```

---

### 122. How do you safely unlock a user account?

First verify:

```sql
SELECT
    user_id,
    username,
    account_status,
    failed_login_count
FROM application_users
WHERE user_id = 501;
```

Then update using a transaction:

```sql
START TRANSACTION;

UPDATE application_users
SET
    account_status = 'Active',
    failed_login_count = 0
WHERE user_id = 501
  AND account_status = 'Locked';

SELECT
    user_id,
    username,
    account_status,
    failed_login_count
FROM application_users
WHERE user_id = 501;

COMMIT;
```

Use `ROLLBACK` instead of `COMMIT` if the result is incorrect.

---

## Common interview traps

### 123. Is NULL equal to zero?

No.

`NULL` means unknown or missing.

Zero is a known numeric value.

---

### 124. Is NULL equal to an empty string?

No.

```text
NULL = missing value
'' = known empty text value
```

---

### 125. Can you use = NULL?

No.

Wrong:

```sql
WHERE email = NULL
```

Correct:

```sql
WHERE email IS NULL
```

---

### 126. Does BETWEEN include both values?

Yes.

```sql
WHERE salary BETWEEN 25000 AND 30000
```

includes both `25000` and `30000`.

---

### 127. Is row order guaranteed without ORDER BY?

No.

Always use `ORDER BY` when result order matters.

---

### 128. Does DISTINCT apply separately to every column?

No.

With multiple columns, `DISTINCT` checks the complete column combination.

```sql
SELECT DISTINCT department, status
FROM employees;
```

---

### 129. Can a SELECT alias be used in WHERE?

Normally, no.

The `WHERE` clause is evaluated before the `SELECT` alias is created.

Wrong:

```sql
SELECT salary * 12 AS annual_salary
FROM employees
WHERE annual_salary > 300000;
```

Correct:

```sql
SELECT salary * 12 AS annual_salary
FROM employees
WHERE salary * 12 > 300000;
```

---

### 130. Can a SELECT alias be used in ORDER BY?

Yes, in most database systems.

```sql
SELECT
    salary * 12 AS annual_salary
FROM employees
ORDER BY annual_salary DESC;
```

---

### 131. Does INNER JOIN keep unmatched rows?

No.

Only matching rows are returned.

---

### 132. Does LEFT JOIN keep unmatched left-side rows?

Yes.

Right-side columns become `NULL` when no match exists.

---

### 133. Should DISTINCT be used to fix an incorrect join?

No.

`DISTINCT` may hide repeated rows without fixing the underlying join condition.

Correct the relationship first.

---

### 134. Does COUNT(column) include NULL?

No.

It counts only non-`NULL` values.

---

### 135. Does AVG treat NULL as zero?

No.

`AVG()` ignores `NULL`.

---

### 136. Is a high ticket count enough to judge technician performance?

No.

Ticket volume must be considered with:

* Ticket complexity
* Priority
* Shift duration
* Assignment method
* Resolution quality
* Escalations
* Waiting time

SQL produces numbers. It does not automatically produce intelligent conclusions, a limitation shared with many dashboards.

---

## Rapid recall

```text
SELECT = choose columns
FROM = choose table
WHERE = filter rows
GROUP BY = create groups
HAVING = filter groups
ORDER BY = sort results
LIMIT = restrict rows

INSERT = add records
UPDATE = modify records
DELETE = remove records

COUNT = count
SUM = total
AVG = average
MIN = lowest
MAX = highest

INNER JOIN = matching rows
LEFT JOIN = all left rows
RIGHT JOIN = all right rows
CROSS JOIN = every combination

PRIMARY KEY = identify a row
FOREIGN KEY = connect tables
UNIQUE = prevent duplicates
NOT NULL = require a value

COMMIT = save transaction
ROLLBACK = cancel transaction
```

---

## Query writing checklist

Before finalizing a query, check:

* Are the correct tables being used?
* Are the selected columns necessary?
* Are join conditions correct?
* Are text values inside single quotation marks?
* Are `NULL` values handled correctly?
* Are date boundaries correct?
* Is `WHERE` filtering the intended rows?
* Is `HAVING` used only for grouped conditions?
* Is `ORDER BY` included when order matters?
* Is `LIMIT` used with meaningful sorting?
* Are duplicate rows expected or caused by a bad join?
* Has an `UPDATE` or `DELETE` condition been tested with `SELECT`?
* Does the affected-row count match the expectation?
* Is the result supported by business context?
