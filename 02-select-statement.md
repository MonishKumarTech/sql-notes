# SELECT statement

## Purpose

The `SELECT` statement is used to retrieve data from one or more database tables.

It is the most frequently used SQL command in reporting, application support, troubleshooting, and data verification tasks.

---

## Basic syntax

```sql
SELECT column_name
FROM table_name;
```

Example:

```sql
SELECT employee_name
FROM employees;
```

This query retrieves the `employee_name` column from the `employees` table.

Quick recall:

```text
SELECT chooses the data.
FROM chooses the table.
```

---

## Example table

The examples in this file use the following `employees` table:

| employee_id | employee_name | department | salary | status   |
| ----------: | ------------- | ---------- | -----: | -------- |
|         101 | Ravi          | IT Support |  28000 | Active   |
|         102 | Priya         | Finance    |  32000 | Active   |
|         103 | Arun          | Operations |  26000 | Inactive |
|         104 | Meena         | IT Support |  30000 | Active   |

---

## Select one column

Use `SELECT` followed by the column name.

```sql
SELECT employee_name
FROM employees;
```

Result:

| employee_name |
| ------------- |
| Ravi          |
| Priya         |
| Arun          |
| Meena         |

Quick recall:

```text
One column name returns one column.
```

---

## Select multiple columns

Separate multiple column names with commas.

```sql
SELECT employee_name, department
FROM employees;
```

Result:

| employee_name | department |
| ------------- | ---------- |
| Ravi          | IT Support |
| Priya         | Finance    |
| Arun          | Operations |
| Meena         | IT Support |

Important:

The order of columns in the result follows the order written in the query.

Example:

```sql
SELECT department, employee_name
FROM employees;
```

This displays `department` first and `employee_name` second.

---

## Select all columns

Use the asterisk `*` to retrieve every column.

```sql
SELECT *
FROM employees;
```

Result:

| employee_id | employee_name | department | salary | status   |
| ----------: | ------------- | ---------- | -----: | -------- |
|         101 | Ravi          | IT Support |  28000 | Active   |
|         102 | Priya         | Finance    |  32000 | Active   |
|         103 | Arun          | Operations |  26000 | Inactive |
|         104 | Meena         | IT Support |  30000 | Active   |

Quick recall:

```text
* means all columns.
```

---

## Why SELECT * should be used carefully

`SELECT *` is useful for learning and quick checks.

However, it should not be used unnecessarily in workplace queries because it may:

* Return columns that are not needed
* Increase data transfer
* Make reports harder to read
* Reduce query performance on large tables
* Expose sensitive information
* Break applications if table structures change

Better:

```sql
SELECT employee_id, employee_name, status
FROM employees;
```

Less precise:

```sql
SELECT *
FROM employees;
```

Use `SELECT *` for exploration. Use specific column names for reports and production work.

---

## SELECT without FROM

Some database systems allow `SELECT` without a table.

Example:

```sql
SELECT 10 + 5;
```

Result:

```text
15
```

Another example:

```sql
SELECT 'SQL Practice';
```

Result:

```text
SQL Practice
```

This is useful for testing expressions and database functions.

---

## Column aliases

An alias gives a temporary name to a result column.

Use the `AS` keyword.

```sql
SELECT employee_name AS name
FROM employees;
```

Result:

| name  |
| ----- |
| Ravi  |
| Priya |
| Arun  |
| Meena |

The table structure is not changed. Only the query result heading changes.

Quick recall:

```text
Alias means temporary display name.
```

---

## Alias without AS

The `AS` keyword is optional in many SQL systems.

```sql
SELECT employee_name name
FROM employees;
```

This may work, but using `AS` is clearer.

Recommended:

```sql
SELECT employee_name AS employee
FROM employees;
```

---

## Alias with spaces

Use backticks in MySQL when an alias contains spaces.

```sql
SELECT employee_name AS `Employee Name`
FROM employees;
```

Result:

| Employee Name |
| ------------- |
| Ravi          |
| Priya         |
| Arun          |
| Meena         |

For portable SQL, aliases without spaces are often simpler:

```sql
SELECT employee_name AS employee_name
FROM employees;
```

---

## Table aliases

A table alias gives a temporary short name to a table.

```sql
SELECT e.employee_name, e.department
FROM employees AS e;
```

Here:

```text
e = employees
```

Table aliases are especially useful in joins and long queries.

Quick recall:

```text
Column alias renames a result heading.
Table alias shortens a table name.
```

---

## DISTINCT

`DISTINCT` removes duplicate values from the result.

Example:

```sql
SELECT department
FROM employees;
```

Result:

| department |
| ---------- |
| IT Support |
| Finance    |
| Operations |
| IT Support |

Using `DISTINCT`:

```sql
SELECT DISTINCT department
FROM employees;
```

Result:

| department |
| ---------- |
| IT Support |
| Finance    |
| Operations |

Quick recall:

```text
DISTINCT returns unique results.
```

---

## DISTINCT with multiple columns

When multiple columns are selected, `DISTINCT` removes duplicate combinations.

```sql
SELECT DISTINCT department, status
FROM employees;
```

The database checks the combination of both columns.

It does not apply uniqueness separately to each column.

---

## Select calculated values

SQL can perform calculations inside a `SELECT` statement.

```sql
SELECT employee_name, salary, salary * 12 AS annual_salary
FROM employees;
```

Result:

| employee_name | salary | annual_salary |
| ------------- | -----: | ------------: |
| Ravi          |  28000 |        336000 |
| Priya         |  32000 |        384000 |
| Arun          |  26000 |        312000 |
| Meena         |  30000 |        360000 |

Quick recall:

```text
SELECT can return stored values and calculated values.
```

---

## Arithmetic operators

Common arithmetic operators:

| Operator | Purpose        |
| -------- | -------------- |
| `+`      | Addition       |
| `-`      | Subtraction    |
| `*`      | Multiplication |
| `/`      | Division       |
| `%`      | Remainder      |

Examples:

```sql
SELECT salary + 2000 AS revised_salary
FROM employees;
```

```sql
SELECT salary * 12 AS annual_salary
FROM employees;
```

```sql
SELECT salary / 30 AS daily_salary
FROM employees;
```

---

## Select fixed text

Fixed text can be included in a query result.

```sql
SELECT employee_name, 'Employee' AS record_type
FROM employees;
```

Result:

| employee_name | record_type |
| ------------- | ----------- |
| Ravi          | Employee    |
| Priya         | Employee    |
| Arun          | Employee    |
| Meena         | Employee    |

Text values must be written inside single quotation marks.

---

## Combine text using CONCAT

In MySQL, `CONCAT` combines multiple text values.

```sql
SELECT CONCAT(employee_name, ' - ', department) AS employee_details
FROM employees;
```

Result:

| employee_details   |
| ------------------ |
| Ravi - IT Support  |
| Priya - Finance    |
| Arun - Operations  |
| Meena - IT Support |

Quick recall:

```text
CONCAT joins text values.
```

---

## Select current database

In MySQL, use:

```sql
SELECT DATABASE();
```

This shows the currently selected database.

Possible result:

```text
company_database
```

This is useful when working with multiple databases and trying to avoid editing the wrong one, a surprisingly popular human tradition.

---

## Select current user

In MySQL, use:

```sql
SELECT USER();
```

This shows the connected database user.

Example result:

```text
root@localhost
```

---

## Select current date and time

```sql
SELECT CURRENT_DATE;
```

```sql
SELECT CURRENT_TIME;
```

```sql
SELECT CURRENT_TIMESTAMP;
```

These return the database server's current date and time.

---

## Fully qualified column names

A column can be written with its table name.

```sql
SELECT employees.employee_name
FROM employees;
```

This is called a fully qualified column name.

Format:

```text
table_name.column_name
```

It is useful when multiple tables contain columns with the same name.

---

## Fully qualified table names

A table can also be written with its database name.

```sql
SELECT employee_name
FROM company_database.employees;
```

Format:

```text
database_name.table_name
```

This is useful when working across multiple databases.

---

## SQL statement formatting

This query works:

```sql
SELECT employee_name,department FROM employees;
```

This version is easier to read:

```sql
SELECT
    employee_name,
    department
FROM employees;
```

Recommended formatting:

* Write SQL keywords in uppercase
* Place each major clause on a new line
* Separate columns using commas
* Indent long column lists
* End the query with a semicolon

Clear formatting matters because unreadable SQL becomes expensive once other people have to fix it.

---

## SQL keywords and case sensitivity

SQL keywords are usually not case-sensitive.

These are generally equivalent:

```sql
SELECT employee_name
FROM employees;
```

```sql
select employee_name
from employees;
```

Uppercase keywords are recommended because they improve readability.

Table and column name case sensitivity may vary depending on the DBMS and operating system.

Use consistent naming.

---

## Semicolon

A semicolon marks the end of an SQL statement.

```sql
SELECT employee_name
FROM employees;
```

Some tools allow a single query without a semicolon, but using it consistently is better practice.

Multiple statements require clear separation:

```sql
SELECT employee_name
FROM employees;

SELECT department
FROM employees;
```

---

## Practical support examples

### Retrieve ticket details

```sql
SELECT ticket_id, issue, status
FROM tickets;
```

### Retrieve employee contact details

```sql
SELECT employee_name, email, phone_number
FROM employees;
```

### Retrieve application user accounts

```sql
SELECT user_id, username, account_status
FROM application_users;
```

### Retrieve unique ticket priorities

```sql
SELECT DISTINCT priority
FROM tickets;
```

### Calculate annual salary

```sql
SELECT employee_name, salary * 12 AS annual_salary
FROM employees;
```

---

## Common mistakes

### Mistake 1: Missing comma

Wrong:

```sql
SELECT employee_name department
FROM employees;
```

The database may treat `department` as an alias instead of a second column.

Correct:

```sql
SELECT employee_name, department
FROM employees;
```

---

### Mistake 2: Misspelling a column name

Wrong:

```sql
SELECT employe_name
FROM employees;
```

Possible error:

```text
Unknown column 'employe_name'
```

Correct:

```sql
SELECT employee_name
FROM employees;
```

Check the exact table structure before guessing names.

---

### Mistake 3: Using quotation marks around column names

Wrong:

```sql
SELECT 'employee_name'
FROM employees;
```

This returns the text `employee_name` for every row.

Correct:

```sql
SELECT employee_name
FROM employees;
```

Single quotation marks are for text values, not normal column names.

---

### Mistake 4: Forgetting FROM

Wrong:

```sql
SELECT employee_name;
```

This fails because `employee_name` belongs to a table.

Correct:

```sql
SELECT employee_name
FROM employees;
```

---

### Mistake 5: Using SELECT * for every query

Weak:

```sql
SELECT *
FROM employees;
```

Better:

```sql
SELECT employee_id, employee_name, status
FROM employees;
```

Retrieve only the data required for the task.

---

### Mistake 6: Using DISTINCT without understanding it

```sql
SELECT DISTINCT department, status
FROM employees;
```

This returns unique combinations of `department` and `status`.

It does not separately remove duplicates from each column.

---

### Mistake 7: Confusing aliases with permanent changes

```sql
SELECT employee_name AS name
FROM employees;
```

This does not rename the original column.

It only changes the heading in the query result.

---

## Practice queries

### Query 1

Retrieve all employee names.

```sql
SELECT employee_name
FROM employees;
```

### Query 2

Retrieve employee names and departments.

```sql
SELECT employee_name, department
FROM employees;
```

### Query 3

Retrieve all columns.

```sql
SELECT *
FROM employees;
```

### Query 4

Retrieve unique departments.

```sql
SELECT DISTINCT department
FROM employees;
```

### Query 5

Display `employee_name` as `name`.

```sql
SELECT employee_name AS name
FROM employees;
```

### Query 6

Calculate annual salary.

```sql
SELECT employee_name, salary * 12 AS annual_salary
FROM employees;
```

### Query 7

Combine employee name and department.

```sql
SELECT CONCAT(employee_name, ' - ', department) AS employee_details
FROM employees;
```

### Query 8

Show the current database.

```sql
SELECT DATABASE();
```

---

## Quick recall notes

* `SELECT` retrieves data.
* `FROM` identifies the table.
* Use commas to separate multiple columns.
* `*` means all columns.
* Specific columns are better than `SELECT *` for workplace queries.
* `DISTINCT` removes duplicate results.
* `AS` creates a temporary alias.
* Column aliases rename result headings.
* Table aliases shorten table names.
* SQL can perform calculations inside `SELECT`.
* `CONCAT` combines text values in MySQL.
* Single quotation marks are used for text values.
* SQL statements usually end with a semicolon.
* SQL keywords are commonly written in uppercase.
* The result column order follows the query column order.

---

## Interview questions

### 1. What does the SELECT statement do?

The `SELECT` statement retrieves data from a database table.

### 2. What does the FROM clause do?

The `FROM` clause identifies the table from which data should be retrieved.

### 3. How do you select one column?

```sql
SELECT column_name
FROM table_name;
```

### 4. How do you select multiple columns?

Separate the column names with commas.

```sql
SELECT column1, column2
FROM table_name;
```

### 5. What does SELECT * mean?

`SELECT *` retrieves all columns from a table.

### 6. Why should SELECT * be avoided in production queries?

It may retrieve unnecessary data, reduce readability, expose sensitive columns, and affect performance.

### 7. What does DISTINCT do?

`DISTINCT` removes duplicate rows from the query result.

### 8. What is a column alias?

A column alias is a temporary name given to a result column.

### 9. How do you create a column alias?

```sql
SELECT employee_name AS name
FROM employees;
```

### 10. Does an alias permanently rename a column?

No. It changes only the query result heading.

### 11. What is a table alias?

A table alias is a temporary short name given to a table.

```sql
SELECT e.employee_name
FROM employees AS e;
```

### 12. Can SELECT perform calculations?

Yes.

```sql
SELECT salary * 12 AS annual_salary
FROM employees;
```

### 13. What is the difference between SELECT and SELECT DISTINCT?

`SELECT` returns all matching rows. `SELECT DISTINCT` removes duplicate result rows.

### 14. Why are single quotation marks used in SQL?

Single quotation marks are used around text values.

### 15. What happens if a column name is placed inside single quotation marks?

The database treats it as fixed text instead of a column reference.

### 16. What is a fully qualified column name?

A fully qualified column name includes the table name.

```text
table_name.column_name
```

### 17. Why are table aliases useful?

They shorten queries and help distinguish columns when multiple tables are used.

### 18. Is SQL case-sensitive?

SQL keywords are usually not case-sensitive, but table and column name behavior may depend on the DBMS and operating system.

### 19. Why should SQL queries be formatted clearly?

Clear formatting makes queries easier to understand, review, troubleshoot, and maintain.

### 20. How is SELECT used in application support?

It is used to retrieve user records, verify application data, inspect ticket information, check account status, and prepare reports.
