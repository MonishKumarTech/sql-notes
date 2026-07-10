# Aggregate functions

## Purpose

Aggregate functions calculate a single result from multiple rows.

They are commonly used for:

* Counting records
* Calculating totals
* Finding averages
* Finding minimum values
* Finding maximum values
* Preparing reports
* Summarizing support and business data

---

## Functions covered

* `COUNT()`
* `SUM()`
* `AVG()`
* `MIN()`
* `MAX()`

---

## Example table

The examples in this file use the following `employees` table:

| employee_id | employee_name | department        | salary | status   | bonus |
| ----------: | ------------- | ----------------- | -----: | -------- | ----: |
|         101 | Ravi          | IT Support        |  28000 | Active   |  3000 |
|         102 | Priya         | Finance           |  32000 | Active   |  4000 |
|         103 | Arun          | Operations        |  26000 | Inactive |  NULL |
|         104 | Meena         | IT Support        |  30000 | Active   |  3500 |
|         105 | Kumar         | Technical Support |  25000 | Inactive |  NULL |
|         106 | Divya         | IT Support        |  35000 | Active   |  5000 |

---

## What is an aggregate function?

An aggregate function performs a calculation on multiple rows and returns one summarized value.

Example:

```sql
SELECT COUNT(*)
FROM employees;
```

Result:

```text
6
```

The table contains six employee records.

Quick recall:

```text
Aggregate functions summarize multiple rows.
```

---

## COUNT()

`COUNT()` counts records or non-`NULL` values.

### Count all rows

```sql
SELECT COUNT(*)
FROM employees;
```

Result:

```text
6
```

`COUNT(*)` counts every row, including rows containing `NULL` values.

Quick recall:

```text
COUNT(*) counts rows.
```

---

## Count a specific column

```sql
SELECT COUNT(bonus)
FROM employees;
```

Result:

```text
4
```

Only four employees have a bonus value.

`COUNT(column_name)` ignores `NULL` values.

---

## COUNT(*) and COUNT(column) difference

| Expression               | Meaning                                 |
| ------------------------ | --------------------------------------- |
| `COUNT(*)`               | Counts all rows                         |
| `COUNT(column)`          | Counts non-`NULL` values in that column |
| `COUNT(DISTINCT column)` | Counts unique non-`NULL` values         |

Example:

```sql
SELECT
    COUNT(*) AS total_employees,
    COUNT(bonus) AS employees_with_bonus
FROM employees;
```

Result:

| total_employees | employees_with_bonus |
| --------------: | -------------------: |
|               6 |                    4 |

---

## Count unique values

Use `DISTINCT` to count unique values.

```sql
SELECT COUNT(DISTINCT department) AS department_count
FROM employees;
```

Result:

```text
4
```

The unique departments are:

* IT Support
* Finance
* Operations
* Technical Support

Quick recall:

```text
COUNT(DISTINCT column) counts unique values.
```

---

## COUNT() with WHERE

Count active employees:

```sql
SELECT COUNT(*) AS active_employee_count
FROM employees
WHERE status = 'Active';
```

Result:

```text
4
```

Count employees in IT Support:

```sql
SELECT COUNT(*) AS it_support_count
FROM employees
WHERE department = 'IT Support';
```

Result:

```text
3
```

`WHERE` filters rows before the aggregate function is calculated.

---

## SUM()

`SUM()` calculates the total of a numeric column.

### Basic syntax

```sql
SELECT SUM(column_name)
FROM table_name;
```

Example:

```sql
SELECT SUM(salary) AS total_salary
FROM employees;
```

Result:

```text
176000
```

Quick recall:

```text
SUM adds numeric values.
```

---

## SUM() with WHERE

Calculate the total salary of active employees:

```sql
SELECT SUM(salary) AS active_salary_total
FROM employees
WHERE status = 'Active';
```

Result:

```text
125000
```

Calculate the total salary for IT Support:

```sql
SELECT SUM(salary) AS it_support_salary_total
FROM employees
WHERE department = 'IT Support';
```

Result:

```text
93000
```

---

## SUM() and NULL values

`SUM()` ignores `NULL` values.

```sql
SELECT SUM(bonus) AS total_bonus
FROM employees;
```

Result:

```text
15500
```

The two `NULL` bonus values are ignored.

If every value is `NULL`, `SUM()` may return `NULL`.

---

## AVG()

`AVG()` calculates the average of a numeric column.

### Basic syntax

```sql
SELECT AVG(column_name)
FROM table_name;
```

Example:

```sql
SELECT AVG(salary) AS average_salary
FROM employees;
```

Result:

```text
29333.3333
```

The number of decimal places depends on the database system and column type.

Quick recall:

```text
AVG calculates the arithmetic average.
```

---

## AVG() with WHERE

Calculate the average salary of active employees:

```sql
SELECT AVG(salary) AS active_employee_average
FROM employees
WHERE status = 'Active';
```

Calculate the average salary in IT Support:

```sql
SELECT AVG(salary) AS it_support_average
FROM employees
WHERE department = 'IT Support';
```

Result:

```text
31000
```

---

## AVG() and NULL values

`AVG()` ignores `NULL` values.

Example:

```sql
SELECT AVG(bonus) AS average_bonus
FROM employees;
```

The calculation uses only the four non-`NULL` bonus values.

It does not treat `NULL` as zero.

This matters because:

```text
NULL = unknown or missing
0 = known value of zero
```

Those are not interchangeable, despite the heroic efforts of bad spreadsheets everywhere.

---

## MIN()

`MIN()` returns the lowest value.

### Find the lowest salary

```sql
SELECT MIN(salary) AS lowest_salary
FROM employees;
```

Result:

```text
25000
```

Quick recall:

```text
MIN returns the smallest value.
```

---

## MIN() with WHERE

Find the lowest active employee salary:

```sql
SELECT MIN(salary) AS lowest_active_salary
FROM employees
WHERE status = 'Active';
```

Result:

```text
28000
```

Find the earliest joining date:

```sql
SELECT MIN(joining_date) AS earliest_joining_date
FROM employees;
```

`MIN()` can work with:

* Numbers
* Dates
* Text values

---

## MIN() with text

```sql
SELECT MIN(employee_name) AS first_name_alphabetically
FROM employees;
```

Possible result:

```text
Arun
```

For text, `MIN()` returns the value that comes first according to the database collation.

---

## MAX()

`MAX()` returns the highest value.

### Find the highest salary

```sql
SELECT MAX(salary) AS highest_salary
FROM employees;
```

Result:

```text
35000
```

Quick recall:

```text
MAX returns the largest value.
```

---

## MAX() with WHERE

Find the highest salary in IT Support:

```sql
SELECT MAX(salary) AS highest_it_support_salary
FROM employees
WHERE department = 'IT Support';
```

Result:

```text
35000
```

Find the latest joining date:

```sql
SELECT MAX(joining_date) AS latest_joining_date
FROM employees;
```

---

## Using multiple aggregate functions

Multiple aggregate functions can be used in one query.

```sql
SELECT
    COUNT(*) AS employee_count,
    SUM(salary) AS total_salary,
    AVG(salary) AS average_salary,
    MIN(salary) AS lowest_salary,
    MAX(salary) AS highest_salary
FROM employees;
```

Result:

| employee_count | total_salary | average_salary | lowest_salary | highest_salary |
| -------------: | -----------: | -------------: | ------------: | -------------: |
|              6 |       176000 |     29333.3333 |         25000 |          35000 |

This creates a complete salary summary.

---

## Aggregate function aliases

Aliases make report headings clearer.

Weak:

```sql
SELECT COUNT(*), AVG(salary)
FROM employees;
```

Better:

```sql
SELECT
    COUNT(*) AS employee_count,
    AVG(salary) AS average_salary
FROM employees;
```

Aliases do not change the original table or column names.

They only change the displayed result headings.

---

## Rounding aggregate results

Use `ROUND()` to control decimal places.

```sql
SELECT ROUND(AVG(salary), 2) AS average_salary
FROM employees;
```

Possible result:

```text
29333.33
```

Syntax:

```sql
ROUND(value, decimal_places)
```

Example:

```sql
SELECT ROUND(AVG(bonus), 0) AS rounded_average_bonus
FROM employees;
```

---

## Aggregate functions with expressions

Aggregate functions can calculate expressions.

Calculate total annual salary:

```sql
SELECT SUM(salary * 12) AS total_annual_salary
FROM employees;
```

Calculate average annual salary:

```sql
SELECT AVG(salary * 12) AS average_annual_salary
FROM employees;
```

Calculate total salary after a fixed allowance:

```sql
SELECT SUM(salary + 2000) AS total_revised_salary
FROM employees;
```

---

## Aggregate functions and DISTINCT

`DISTINCT` removes duplicate values before calculation.

Example table values:

```text
28000
32000
26000
30000
25000
35000
```

Calculate the average of all salaries:

```sql
SELECT AVG(salary)
FROM employees;
```

Calculate the average of unique salary values:

```sql
SELECT AVG(DISTINCT salary)
FROM employees;
```

These produce the same result only when every salary value is unique.

Use `DISTINCT` only when the business requirement specifically needs unique values.

---

## Aggregate functions and WHERE

`WHERE` filters individual rows before aggregation.

```sql
SELECT COUNT(*) AS active_employee_count
FROM employees
WHERE status = 'Active';
```

Processing logic:

```text
1. Read the employees table
2. Keep only active employees
3. Count the remaining rows
```

---

## Aggregate functions and ORDER BY

A single aggregate result normally produces one row.

```sql
SELECT MAX(salary) AS highest_salary
FROM employees
ORDER BY highest_salary;
```

Sorting one row has no practical value.

`ORDER BY` becomes useful when aggregation produces multiple rows using `GROUP BY`.

That is covered in the next topic.

---

## Finding the record with the highest value

This query returns only the highest salary value:

```sql
SELECT MAX(salary) AS highest_salary
FROM employees;
```

Result:

```text
35000
```

It does not automatically return the employee name.

To return the employee with the highest salary:

```sql
SELECT employee_name, salary
FROM employees
ORDER BY salary DESC
LIMIT 1;
```

Result:

| employee_name | salary |
| ------------- | -----: |
| Divya         |  35000 |

Quick recall:

```text
MAX returns the highest value.
ORDER BY and LIMIT can return the full matching row.
```

---

## Find all employees with the highest salary

If multiple employees share the highest salary, `LIMIT 1` returns only one row.

To return every employee with the highest salary:

```sql
SELECT employee_name, salary
FROM employees
WHERE salary = (
    SELECT MAX(salary)
    FROM employees
);
```

The inner query finds the maximum salary.

The outer query finds all employees with that salary.

Subqueries are covered in a later topic.

---

## Working with an empty result

Example:

```sql
SELECT
    COUNT(*) AS employee_count,
    SUM(salary) AS total_salary,
    AVG(salary) AS average_salary,
    MIN(salary) AS lowest_salary,
    MAX(salary) AS highest_salary
FROM employees
WHERE department = 'Legal';
```

If no rows match:

| employee_count | total_salary | average_salary | lowest_salary | highest_salary |
| -------------: | -----------: | -------------: | ------------: | -------------: |
|              0 |         NULL |           NULL |          NULL |           NULL |

Important:

* `COUNT()` returns `0`
* `SUM()` may return `NULL`
* `AVG()` may return `NULL`
* `MIN()` may return `NULL`
* `MAX()` may return `NULL`

---

## Handling NULL aggregate results

Use `COALESCE()` to replace `NULL` with another value.

```sql
SELECT COALESCE(SUM(bonus), 0) AS total_bonus
FROM employees
WHERE department = 'Legal';
```

Result:

```text
0
```

`COALESCE()` returns the first non-`NULL` value.

Syntax:

```sql
COALESCE(value, replacement)
```

---

## COUNT examples

### Count all employees

```sql
SELECT COUNT(*) AS total_employees
FROM employees;
```

### Count active employees

```sql
SELECT COUNT(*) AS active_employees
FROM employees
WHERE status = 'Active';
```

### Count employees with bonus values

```sql
SELECT COUNT(bonus) AS employees_with_bonus
FROM employees;
```

### Count unique departments

```sql
SELECT COUNT(DISTINCT department) AS total_departments
FROM employees;
```

---

## SUM examples

### Calculate total salaries

```sql
SELECT SUM(salary) AS total_salary
FROM employees;
```

### Calculate active employee salaries

```sql
SELECT SUM(salary) AS active_salary_total
FROM employees
WHERE status = 'Active';
```

### Calculate total bonuses

```sql
SELECT SUM(bonus) AS total_bonus
FROM employees;
```

---

## AVG examples

### Calculate average salary

```sql
SELECT ROUND(AVG(salary), 2) AS average_salary
FROM employees;
```

### Calculate average IT Support salary

```sql
SELECT ROUND(AVG(salary), 2) AS it_support_average
FROM employees
WHERE department = 'IT Support';
```

### Calculate average bonus

```sql
SELECT ROUND(AVG(bonus), 2) AS average_bonus
FROM employees;
```

---

## MIN and MAX examples

### Find salary range

```sql
SELECT
    MIN(salary) AS lowest_salary,
    MAX(salary) AS highest_salary
FROM employees;
```

### Find earliest and latest joining dates

```sql
SELECT
    MIN(joining_date) AS earliest_joining_date,
    MAX(joining_date) AS latest_joining_date
FROM employees;
```

---

## Practical support examples

### Count total tickets

```sql
SELECT COUNT(*) AS total_tickets
FROM tickets;
```

### Count open tickets

```sql
SELECT COUNT(*) AS open_ticket_count
FROM tickets
WHERE status = 'Open';
```

### Count failed transactions

```sql
SELECT COUNT(*) AS failed_transaction_count
FROM transactions
WHERE status = 'Failed';
```

### Calculate total successful transaction value

```sql
SELECT SUM(amount) AS successful_transaction_total
FROM transactions
WHERE status = 'Successful';
```

### Find average ticket resolution time

```sql
SELECT AVG(resolution_minutes) AS average_resolution_minutes
FROM tickets
WHERE status = 'Closed';
```

### Find oldest unresolved ticket date

```sql
SELECT MIN(created_at) AS oldest_unresolved_ticket
FROM tickets
WHERE status <> 'Closed';
```

### Find latest user login

```sql
SELECT MAX(last_login) AS latest_login
FROM application_users;
```

### Count locked accounts

```sql
SELECT COUNT(*) AS locked_account_count
FROM application_users
WHERE account_status = 'Locked';
```

### Count users with missing email addresses

```sql
SELECT COUNT(*) AS users_without_email
FROM application_users
WHERE email IS NULL;
```

### Count unique ticket priorities

```sql
SELECT COUNT(DISTINCT priority) AS priority_count
FROM tickets;
```

---

## Aggregate functions and data types

| Function  | Common data type       |
| --------- | ---------------------- |
| `COUNT()` | Any data type          |
| `SUM()`   | Numeric                |
| `AVG()`   | Numeric                |
| `MIN()`   | Numeric, date, or text |
| `MAX()`   | Numeric, date, or text |

`SUM()` and `AVG()` should be used with numeric columns.

Trying to calculate a total of employee names would be nonsense. Databases are powerful, but they remain tragically unable to rescue bad requirements.

---

## Common mistakes

### Mistake 1: Assuming COUNT(column) counts every row

```sql
SELECT COUNT(bonus)
FROM employees;
```

This counts only non-`NULL` bonus values.

To count every row:

```sql
SELECT COUNT(*)
FROM employees;
```

---

### Mistake 2: Treating NULL as zero

`AVG(bonus)` ignores `NULL` values.

It does not include them as zero.

To intentionally treat missing bonuses as zero:

```sql
SELECT AVG(COALESCE(bonus, 0)) AS average_bonus
FROM employees;
```

This changes the business meaning of the calculation, so use it only when required.

---

### Mistake 3: Using SUM() on text data

Wrong:

```sql
SELECT SUM(employee_name)
FROM employees;
```

Correct:

```sql
SELECT SUM(salary)
FROM employees;
```

`SUM()` requires numeric data.

---

### Mistake 4: Selecting normal columns with aggregate functions

Problematic:

```sql
SELECT employee_name, MAX(salary)
FROM employees;
```

The query mixes:

* A normal column
* An aggregate result

In strict SQL modes, this produces an error because the database does not know which employee name should be returned.

Correct for only the value:

```sql
SELECT MAX(salary) AS highest_salary
FROM employees;
```

Correct for one complete row:

```sql
SELECT employee_name, salary
FROM employees
ORDER BY salary DESC
LIMIT 1;
```

---

### Mistake 5: Assuming MAX() returns the complete row

```sql
SELECT MAX(salary)
FROM employees;
```

This returns only the salary value.

It does not return the employee name, department, or other columns.

---

### Mistake 6: Forgetting WHERE changes the calculation

```sql
SELECT AVG(salary)
FROM employees
WHERE status = 'Active';
```

This calculates the average only for active employees.

It does not calculate the average for the entire table.

---

### Mistake 7: Using DISTINCT unnecessarily

```sql
SELECT SUM(DISTINCT salary)
FROM employees;
```

This adds only unique salary values.

If two employees have the same salary, one value is ignored.

That is usually wrong for payroll totals.

Correct:

```sql
SELECT SUM(salary)
FROM employees;
```

---

### Mistake 8: Expecting SUM() to return zero for no rows

```sql
SELECT SUM(salary)
FROM employees
WHERE department = 'Legal';
```

This may return `NULL`.

Use:

```sql
SELECT COALESCE(SUM(salary), 0) AS total_salary
FROM employees
WHERE department = 'Legal';
```

---

### Mistake 9: Forgetting meaningful aliases

Weak result headings:

```sql
SELECT COUNT(*), AVG(salary), MAX(salary)
FROM employees;
```

Better:

```sql
SELECT
    COUNT(*) AS employee_count,
    ROUND(AVG(salary), 2) AS average_salary,
    MAX(salary) AS highest_salary
FROM employees;
```

---

### Mistake 10: Confusing COUNT(*) with SUM()

`COUNT(*)` counts records.

`SUM(column)` adds numeric values.

Example:

```sql
SELECT
    COUNT(*) AS employee_count,
    SUM(salary) AS total_salary
FROM employees;
```

These answer different questions.

---

## Practice queries

### Query 1

Count all employees.

```sql
SELECT COUNT(*) AS employee_count
FROM employees;
```

### Query 2

Count active employees.

```sql
SELECT COUNT(*) AS active_employee_count
FROM employees
WHERE status = 'Active';
```

### Query 3

Count unique departments.

```sql
SELECT COUNT(DISTINCT department) AS department_count
FROM employees;
```

### Query 4

Calculate the total salary.

```sql
SELECT SUM(salary) AS total_salary
FROM employees;
```

### Query 5

Calculate the average salary.

```sql
SELECT ROUND(AVG(salary), 2) AS average_salary
FROM employees;
```

### Query 6

Find the lowest salary.

```sql
SELECT MIN(salary) AS lowest_salary
FROM employees;
```

### Query 7

Find the highest salary.

```sql
SELECT MAX(salary) AS highest_salary
FROM employees;
```

### Query 8

Calculate the salary summary for active employees.

```sql
SELECT
    COUNT(*) AS active_employee_count,
    SUM(salary) AS total_active_salary,
    ROUND(AVG(salary), 2) AS average_active_salary,
    MIN(salary) AS lowest_active_salary,
    MAX(salary) AS highest_active_salary
FROM employees
WHERE status = 'Active';
```

### Query 9

Count employees without bonus values.

```sql
SELECT COUNT(*) AS employees_without_bonus
FROM employees
WHERE bonus IS NULL;
```

### Query 10

Calculate total bonuses and return zero when no rows match.

```sql
SELECT COALESCE(SUM(bonus), 0) AS total_bonus
FROM employees;
```

---

## Quick recall notes

* Aggregate functions summarize multiple rows.
* `COUNT(*)` counts all rows.
* `COUNT(column)` counts non-`NULL` values.
* `COUNT(DISTINCT column)` counts unique non-`NULL` values.
* `SUM()` adds numeric values.
* `AVG()` calculates the average.
* `MIN()` returns the lowest value.
* `MAX()` returns the highest value.
* Aggregate functions usually ignore `NULL` values.
* `COUNT(*)` does not ignore rows containing `NULL`.
* `WHERE` filters rows before aggregation.
* `ROUND()` controls decimal places.
* `COALESCE()` replaces `NULL` results.
* `MAX()` returns a value, not the complete row.
* Use aliases to create clear report headings.
* Use `DISTINCT` only when unique values are actually required.

---

## Interview questions

### 1. What is an aggregate function?

An aggregate function calculates one summarized result from multiple rows.

### 2. What are the main aggregate functions?

* `COUNT()`
* `SUM()`
* `AVG()`
* `MIN()`
* `MAX()`

### 3. What does COUNT(*) do?

`COUNT(*)` counts every row in the result.

### 4. What does COUNT(column) do?

`COUNT(column)` counts only non-`NULL` values in that column.

### 5. What is the difference between COUNT(*) and COUNT(column)?

`COUNT(*)` counts rows. `COUNT(column)` counts non-`NULL` values in the selected column.

### 6. What does COUNT(DISTINCT column) do?

It counts unique non-`NULL` values.

### 7. What does SUM() do?

`SUM()` calculates the total of a numeric column.

### 8. What does AVG() do?

`AVG()` calculates the average of a numeric column.

### 9. What does MIN() do?

`MIN()` returns the lowest value.

### 10. What does MAX() do?

`MAX()` returns the highest value.

### 11. Do aggregate functions include NULL values?

Most aggregate functions ignore `NULL` values. `COUNT(*)` counts rows regardless of `NULL` values.

### 12. Does AVG() treat NULL as zero?

No. `AVG()` ignores `NULL` values.

### 13. How do you round an average to two decimal places?

```sql
SELECT ROUND(AVG(salary), 2) AS average_salary
FROM employees;
```

### 14. How do you count active employees?

```sql
SELECT COUNT(*) AS active_employee_count
FROM employees
WHERE status = 'Active';
```

### 15. How do you find the highest salary?

```sql
SELECT MAX(salary) AS highest_salary
FROM employees;
```

### 16. Does MAX(salary) return the employee name?

No. It returns only the highest salary value.

### 17. How do you return the employee with the highest salary?

```sql
SELECT employee_name, salary
FROM employees
ORDER BY salary DESC
LIMIT 1;
```

### 18. What happens when SUM() receives no matching rows?

It may return `NULL`.

### 19. How do you return zero instead of NULL?

```sql
SELECT COALESCE(SUM(salary), 0) AS total_salary
FROM employees;
```

### 20. Can MIN() and MAX() work with dates?

Yes. `MIN()` returns the earliest date, and `MAX()` returns the latest date.

### 21. Can MIN() and MAX() work with text?

Yes. They return values based on the database collation and sorting rules.

### 22. Why are aliases useful with aggregate functions?

Aliases create clear and readable result headings.

### 23. Can WHERE be used with aggregate functions?

Yes. `WHERE` filters rows before the aggregate calculation.

### 24. Why is SUM(DISTINCT salary) risky?

It ignores repeated salary values, even when those values belong to different employees.

### 25. How are aggregate functions useful in application support?

They help count errors, summarize transactions, calculate resolution times, find oldest or latest records, and prepare operational reports.
