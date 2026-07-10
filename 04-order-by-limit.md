# ORDER BY and LIMIT

## Purpose

The `ORDER BY` clause sorts query results.

The `LIMIT` clause restricts the number of rows returned.

These clauses are useful for reports, dashboards, support investigations, recent-record checks, and finding the highest or lowest values.

---

## Commands covered

* `ORDER BY`
* `ASC`
* `DESC`
* `LIMIT`
* `OFFSET`

---

## Example table

The examples in this file use the following `employees` table:

| employee_id | employee_name | department        | salary | status   | joining_date |
| ----------: | ------------- | ----------------- | -----: | -------- | ------------ |
|         101 | Ravi          | IT Support        |  28000 | Active   | 2024-06-10   |
|         102 | Priya         | Finance           |  32000 | Active   | 2023-11-15   |
|         103 | Arun          | Operations        |  26000 | Inactive | 2025-01-20   |
|         104 | Meena         | IT Support        |  30000 | Active   | 2024-03-05   |
|         105 | Kumar         | Technical Support |  25000 | Inactive | 2025-05-12   |
|         106 | Divya         | IT Support        |  35000 | Active   | 2023-08-18   |

---

## ORDER BY

`ORDER BY` sorts the result based on one or more columns.

## Basic syntax

```sql
SELECT column_name
FROM table_name
ORDER BY column_name;
```

Example:

```sql
SELECT employee_name, salary
FROM employees
ORDER BY salary;
```

By default, the result is sorted in ascending order.

Quick recall:

```text
ORDER BY controls the result order.
```

---

## ASC

`ASC` means ascending order.

Ascending order means:

* Numbers move from smallest to largest
* Text moves from A to Z
* Dates move from oldest to newest

Example:

```sql
SELECT employee_name, salary
FROM employees
ORDER BY salary ASC;
```

Result:

| employee_name | salary |
| ------------- | -----: |
| Kumar         |  25000 |
| Arun          |  26000 |
| Ravi          |  28000 |
| Meena         |  30000 |
| Priya         |  32000 |
| Divya         |  35000 |

Quick recall:

```text
ASC = low to high
ASC = A to Z
ASC = old to new
```

---

## DESC

`DESC` means descending order.

Descending order means:

* Numbers move from largest to smallest
* Text moves from Z to A
* Dates move from newest to oldest

Example:

```sql
SELECT employee_name, salary
FROM employees
ORDER BY salary DESC;
```

Result:

| employee_name | salary |
| ------------- | -----: |
| Divya         |  35000 |
| Priya         |  32000 |
| Meena         |  30000 |
| Ravi          |  28000 |
| Arun          |  26000 |
| Kumar         |  25000 |

Quick recall:

```text
DESC = high to low
DESC = Z to A
DESC = new to old
```

---

## Default sorting order

When `ASC` or `DESC` is not written, SQL normally uses ascending order.

These queries are equivalent:

```sql
SELECT employee_name
FROM employees
ORDER BY employee_name;
```

```sql
SELECT employee_name
FROM employees
ORDER BY employee_name ASC;
```

Writing `ASC` explicitly can make the query easier to understand.

---

## Sorting text values

```sql
SELECT employee_name
FROM employees
ORDER BY employee_name ASC;
```

Result:

| employee_name |
| ------------- |
| Arun          |
| Divya         |
| Kumar         |
| Meena         |
| Priya         |
| Ravi          |

Descending order:

```sql
SELECT employee_name
FROM employees
ORDER BY employee_name DESC;
```

---

## Sorting numeric values

```sql
SELECT employee_name, salary
FROM employees
ORDER BY salary ASC;
```

This sorts employees from the lowest salary to the highest salary.

---

## Sorting dates

```sql
SELECT employee_name, joining_date
FROM employees
ORDER BY joining_date ASC;
```

This shows the oldest joining date first.

To show the newest employees first:

```sql
SELECT employee_name, joining_date
FROM employees
ORDER BY joining_date DESC;
```

---

## Sorting by multiple columns

SQL can sort using more than one column.

Example:

```sql
SELECT employee_name, department, salary
FROM employees
ORDER BY department ASC, salary DESC;
```

The database first sorts by `department`.

Within each department, it sorts by `salary` in descending order.

Quick recall:

```text
SQL sorts by the first column first.
The next column resolves matching values.
```

---

## Multiple-column example

```sql
SELECT employee_name, status, salary
FROM employees
ORDER BY status ASC, salary DESC;
```

Possible result:

| employee_name | status   | salary |
| ------------- | -------- | -----: |
| Divya         | Active   |  35000 |
| Priya         | Active   |  32000 |
| Meena         | Active   |  30000 |
| Ravi          | Active   |  28000 |
| Arun          | Inactive |  26000 |
| Kumar         | Inactive |  25000 |

Rows are grouped by status, then sorted by salary within each status.

---

## Sorting by a selected column

The sorting column does not always need to appear in the result.

```sql
SELECT employee_name
FROM employees
ORDER BY salary DESC;
```

This returns employee names sorted by salary, even though salary is not displayed.

This works in many database systems, but including the sorting column usually makes the result clearer.

Better:

```sql
SELECT employee_name, salary
FROM employees
ORDER BY salary DESC;
```

---

## Sorting using an alias

A column alias can normally be used in `ORDER BY`.

```sql
SELECT
    employee_name,
    salary * 12 AS annual_salary
FROM employees
ORDER BY annual_salary DESC;
```

This works because `ORDER BY` is evaluated after the `SELECT` result is created.

Quick recall:

```text
SELECT aliases can usually be used in ORDER BY.
They usually cannot be used in WHERE.
```

---

## Sorting calculated values

```sql
SELECT
    employee_name,
    salary,
    salary * 12 AS annual_salary
FROM employees
ORDER BY salary * 12 DESC;
```

Using the alias is clearer:

```sql
SELECT
    employee_name,
    salary,
    salary * 12 AS annual_salary
FROM employees
ORDER BY annual_salary DESC;
```

---

## Sorting by column position

Some database systems allow sorting by the position of a selected column.

```sql
SELECT employee_name, department, salary
FROM employees
ORDER BY 3 DESC;
```

Here, `3` refers to the third selected column, which is `salary`.

This works, but it is weaker practice because:

* The meaning is not obvious
* Changing the column order can break the sorting
* Maintenance becomes harder

Better:

```sql
SELECT employee_name, department, salary
FROM employees
ORDER BY salary DESC;
```

Use column names instead of column positions.

---

## Sorting NULL values

The placement of `NULL` values may differ between database systems.

Depending on the DBMS and sort direction, `NULL` values may appear:

* First
* Last

Example:

```sql
SELECT employee_name, phone_number
FROM employees
ORDER BY phone_number ASC;
```

Do not assume where `NULL` will appear without testing the database behaviour.

In MySQL, a useful method is:

```sql
SELECT employee_name, phone_number
FROM employees
ORDER BY phone_number IS NULL, phone_number ASC;
```

This places non-`NULL` values first and `NULL` values last.

---

## ORDER BY with WHERE

`WHERE` filters rows before `ORDER BY` sorts them.

```sql
SELECT employee_name, salary
FROM employees
WHERE status = 'Active'
ORDER BY salary DESC;
```

Processing logic:

```text
1. Filter active employees
2. Sort them by salary
3. Return the result
```

---

## ORDER BY with DISTINCT

```sql
SELECT DISTINCT department
FROM employees
ORDER BY department ASC;
```

This returns unique departments in alphabetical order.

---

## LIMIT

`LIMIT` restricts the number of rows returned.

## Basic syntax

```sql
SELECT column_name
FROM table_name
LIMIT number;
```

Example:

```sql
SELECT employee_name, salary
FROM employees
LIMIT 3;
```

This returns only three rows.

Quick recall:

```text
LIMIT controls how many rows are returned.
```

---

## LIMIT without ORDER BY

```sql
SELECT employee_name
FROM employees
LIMIT 3;
```

This returns three rows, but not necessarily the three rows you logically want.

Without `ORDER BY`, row order is not guaranteed.

Weak:

```sql
SELECT employee_name, salary
FROM employees
LIMIT 3;
```

Better:

```sql
SELECT employee_name, salary
FROM employees
ORDER BY salary DESC
LIMIT 3;
```

This returns the three highest salaries.

---

## Find top values

Find the three highest-paid employees:

```sql
SELECT employee_name, salary
FROM employees
ORDER BY salary DESC
LIMIT 3;
```

Result:

| employee_name | salary |
| ------------- | -----: |
| Divya         |  35000 |
| Priya         |  32000 |
| Meena         |  30000 |

---

## Find lowest values

Find the two lowest-paid employees:

```sql
SELECT employee_name, salary
FROM employees
ORDER BY salary ASC
LIMIT 2;
```

Result:

| employee_name | salary |
| ------------- | -----: |
| Kumar         |  25000 |
| Arun          |  26000 |

---

## Find the most recent record

```sql
SELECT employee_name, joining_date
FROM employees
ORDER BY joining_date DESC
LIMIT 1;
```

This returns the employee with the newest joining date.

---

## Find the oldest record

```sql
SELECT employee_name, joining_date
FROM employees
ORDER BY joining_date ASC
LIMIT 1;
```

This returns the employee with the oldest joining date.

---

## LIMIT with OFFSET

`OFFSET` skips a specified number of rows.

## Syntax

```sql
SELECT column_name
FROM table_name
LIMIT number
OFFSET number_to_skip;
```

Example:

```sql
SELECT employee_name, salary
FROM employees
ORDER BY salary DESC
LIMIT 2
OFFSET 2;
```

This skips the first two rows and returns the next two rows.

Quick recall:

```text
LIMIT = number of rows to return
OFFSET = number of rows to skip
```

---

## MySQL alternative LIMIT syntax

MySQL also supports:

```sql
LIMIT offset, row_count;
```

Example:

```sql
SELECT employee_name, salary
FROM employees
ORDER BY salary DESC
LIMIT 2, 2;
```

This means:

```text
Skip 2 rows and return 2 rows.
```

The clearer version is:

```sql
LIMIT 2
OFFSET 2;
```

It is easier to read and harder to misinterpret.

---

## Pagination

Pagination divides query results into pages.

Example:

```text
Page size: 10 rows
```

### Page 1

```sql
SELECT ticket_id, issue, status
FROM tickets
ORDER BY ticket_id
LIMIT 10
OFFSET 0;
```

### Page 2

```sql
SELECT ticket_id, issue, status
FROM tickets
ORDER BY ticket_id
LIMIT 10
OFFSET 10;
```

### Page 3

```sql
SELECT ticket_id, issue, status
FROM tickets
ORDER BY ticket_id
LIMIT 10
OFFSET 20;
```

Formula:

```text
OFFSET = page size × (page number - 1)
```

For page 4 with 10 rows per page:

```text
OFFSET = 10 × (4 - 1)
OFFSET = 30
```

---

## Stable sorting

When multiple rows have the same sorting value, their internal order may not be predictable.

Example:

```sql
SELECT employee_name, salary
FROM employees
ORDER BY salary DESC;
```

If two employees have the same salary, their relative order may vary.

Use a second column for stable sorting:

```sql
SELECT employee_id, employee_name, salary
FROM employees
ORDER BY salary DESC, employee_id ASC;
```

This sorts by salary first, then employee ID.

Quick recall:

```text
Add a unique column to make sorting predictable.
```

---

## Practical support examples

### Find the latest five tickets

```sql
SELECT ticket_id, issue, created_at
FROM tickets
ORDER BY created_at DESC
LIMIT 5;
```

### Find the three oldest unresolved tickets

```sql
SELECT ticket_id, issue, created_at
FROM tickets
WHERE status <> 'Closed'
ORDER BY created_at ASC
LIMIT 3;
```

### Find the highest-priority recent tickets

```sql
SELECT ticket_id, priority, status, created_at
FROM tickets
WHERE status = 'Open'
ORDER BY priority DESC, created_at ASC;
```

Note:

Text priority values such as `High`, `Medium`, and `Low` may not sort in the required business order alphabetically.

A custom sorting method may be required.

Example in MySQL:

```sql
SELECT ticket_id, priority, status
FROM tickets
ORDER BY FIELD(priority, 'High', 'Medium', 'Low');
```

---

## Find latest user logins

```sql
SELECT username, last_login
FROM application_users
WHERE last_login IS NOT NULL
ORDER BY last_login DESC
LIMIT 10;
```

---

## Find inactive accounts with the oldest login

```sql
SELECT username, account_status, last_login
FROM application_users
WHERE account_status = 'Inactive'
ORDER BY last_login ASC;
```

---

## Find highest salaries in IT Support

```sql
SELECT employee_name, salary
FROM employees
WHERE department = 'IT Support'
ORDER BY salary DESC
LIMIT 3;
```

---

## Find recently failed transactions

```sql
SELECT transaction_id, user_id, failure_reason, created_at
FROM transactions
WHERE status = 'Failed'
ORDER BY created_at DESC
LIMIT 20;
```

---

## Query processing order

A simplified logical order is:

```text
1. FROM
2. WHERE
3. SELECT
4. DISTINCT
5. ORDER BY
6. LIMIT
```

Example:

```sql
SELECT employee_name, salary
FROM employees
WHERE status = 'Active'
ORDER BY salary DESC
LIMIT 3;
```

The database conceptually:

1. Reads the `employees` table
2. Filters active employees
3. Selects the required columns
4. Sorts by salary
5. Returns three rows

---

## Common mistakes

## Mistake 1: Assuming table order is guaranteed

Weak:

```sql
SELECT employee_name
FROM employees;
```

A table does not guarantee a meaningful display order.

Correct:

```sql
SELECT employee_name
FROM employees
ORDER BY employee_name ASC;
```

---

## Mistake 2: Using LIMIT without ORDER BY

Weak:

```sql
SELECT employee_name, salary
FROM employees
LIMIT 3;
```

This returns any three rows based on the database execution plan.

Correct:

```sql
SELECT employee_name, salary
FROM employees
ORDER BY salary DESC
LIMIT 3;
```

---

## Mistake 3: Reversing ASC and DESC

```text
ASC = ascending
DESC = descending
```

Do not guess from memory during an interview. Recall:

```text
ASC = climb upward
DESC = descend downward
```

---

## Mistake 4: Sorting numeric data stored as text

If salary values are stored as text, sorting may produce incorrect results.

Example text sorting:

```text
10000
2000
30000
```

This happens because text is sorted character by character.

Numeric data should use a numeric data type such as:

```sql
INT
DECIMAL
```

Database design problems eventually become query problems. Humanity remains surprised by this every quarter.

---

## Mistake 5: Using column position

Weak:

```sql
ORDER BY 3 DESC;
```

Better:

```sql
ORDER BY salary DESC;
```

Column names are clearer and safer.

---

## Mistake 6: Forgetting a comma between sort columns

Wrong:

```sql
ORDER BY department ASC salary DESC;
```

Correct:

```sql
ORDER BY department ASC, salary DESC;
```

---

## Mistake 7: Assuming alphabetical priority order is correct

This query may sort priorities incorrectly:

```sql
ORDER BY priority ASC;
```

Alphabetical order may produce:

```text
High
Low
Medium
```

Business priority order is normally:

```text
High
Medium
Low
```

Use a custom sort expression when required.

---

## Mistake 8: Using large OFFSET values carelessly

```sql
LIMIT 20
OFFSET 100000;
```

Large offsets can become slow because the database may need to scan and skip many rows.

For very large datasets, keyset pagination may be more efficient.

Basic example:

```sql
SELECT ticket_id, issue
FROM tickets
WHERE ticket_id > 100000
ORDER BY ticket_id
LIMIT 20;
```

This is an advanced optimization, but it is worth knowing that large offsets are not magically free.

---

## Practice queries

## Query 1

Sort employees alphabetically.

```sql
SELECT employee_name
FROM employees
ORDER BY employee_name ASC;
```

## Query 2

Sort employees by highest salary.

```sql
SELECT employee_name, salary
FROM employees
ORDER BY salary DESC;
```

## Query 3

Sort employees by oldest joining date.

```sql
SELECT employee_name, joining_date
FROM employees
ORDER BY joining_date ASC;
```

## Query 4

Sort by department and then salary.

```sql
SELECT employee_name, department, salary
FROM employees
ORDER BY department ASC, salary DESC;
```

## Query 5

Find the three highest-paid employees.

```sql
SELECT employee_name, salary
FROM employees
ORDER BY salary DESC
LIMIT 3;
```

## Query 6

Find the newest employee.

```sql
SELECT employee_name, joining_date
FROM employees
ORDER BY joining_date DESC
LIMIT 1;
```

## Query 7

Find the two lowest-paid active employees.

```sql
SELECT employee_name, salary
FROM employees
WHERE status = 'Active'
ORDER BY salary ASC
LIMIT 2;
```

## Query 8

Skip the first two highest-paid employees and return the next two.

```sql
SELECT employee_name, salary
FROM employees
ORDER BY salary DESC
LIMIT 2
OFFSET 2;
```

## Query 9

Sort annual salary from highest to lowest.

```sql
SELECT
    employee_name,
    salary * 12 AS annual_salary
FROM employees
ORDER BY annual_salary DESC;
```

## Query 10

Return the latest five open tickets.

```sql
SELECT ticket_id, issue, created_at
FROM tickets
WHERE status = 'Open'
ORDER BY created_at DESC
LIMIT 5;
```

---

## Quick recall notes

* `ORDER BY` sorts query results.
* `ASC` means ascending order.
* `DESC` means descending order.
* Ascending numbers go from low to high.
* Descending numbers go from high to low.
* Ascending text goes from A to Z.
* Descending text goes from Z to A.
* Ascending dates go from oldest to newest.
* Descending dates go from newest to oldest.
* Multiple columns can be used for sorting.
* SQL sorts by the first column, then the next column.
* `LIMIT` controls how many rows are returned.
* `OFFSET` controls how many rows are skipped.
* Use `ORDER BY` with `LIMIT` for meaningful results.
* A result order is not guaranteed without `ORDER BY`.
* Add a unique secondary column for stable sorting.
* Aliases can normally be used in `ORDER BY`.
* Column names are better than column positions.

---

## Interview questions

## 1. What does ORDER BY do?

`ORDER BY` sorts the rows returned by a query.

## 2. What does ASC mean?

`ASC` means ascending order.

## 3. What does DESC mean?

`DESC` means descending order.

## 4. What is the default ORDER BY direction?

The default direction is normally ascending.

## 5. How do you sort salaries from highest to lowest?

```sql
SELECT employee_name, salary
FROM employees
ORDER BY salary DESC;
```

## 6. How do you sort names alphabetically?

```sql
SELECT employee_name
FROM employees
ORDER BY employee_name ASC;
```

## 7. Can multiple columns be used in ORDER BY?

Yes.

```sql
ORDER BY department ASC, salary DESC;
```

## 8. How does multiple-column sorting work?

SQL sorts by the first column. When values are equal, it uses the next column.

## 9. What does LIMIT do?

`LIMIT` restricts the number of rows returned.

## 10. What does OFFSET do?

`OFFSET` skips a specified number of rows before returning results.

## 11. How do you return the top three salaries?

```sql
SELECT employee_name, salary
FROM employees
ORDER BY salary DESC
LIMIT 3;
```

## 12. Why should LIMIT usually be used with ORDER BY?

Without `ORDER BY`, the returned rows may not have a predictable or meaningful order.

## 13. Is row order guaranteed without ORDER BY?

No. SQL does not guarantee result order without `ORDER BY`.

## 14. Can a SELECT alias be used in ORDER BY?

Yes, in most database systems.

```sql
SELECT salary * 12 AS annual_salary
FROM employees
ORDER BY annual_salary DESC;
```

## 15. What is stable sorting?

Stable sorting means using enough sort columns to produce a predictable row order.

Example:

```sql
ORDER BY salary DESC, employee_id ASC;
```

## 16. What is pagination?

Pagination divides a large result set into smaller pages using `LIMIT` and `OFFSET`.

## 17. How do you return page two with ten rows per page?

```sql
LIMIT 10
OFFSET 10;
```

## 18. What is wrong with ORDER BY 3?

It sorts by column position, which is less clear and can break when the selected columns change.

## 19. How do you return the most recent record?

```sql
ORDER BY created_at DESC
LIMIT 1;
```

## 20. How are ORDER BY and LIMIT useful in application support?

They help find recent errors, oldest unresolved tickets, latest logins, highest values, lowest values, and limited report
