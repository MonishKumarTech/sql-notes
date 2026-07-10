# GROUP BY and HAVING

## Purpose

The `GROUP BY` clause combines rows with matching values into groups.

Aggregate functions can then calculate a separate result for each group.

The `HAVING` clause filters grouped results after aggregation.

These clauses are commonly used for:

* Department-wise reports
* Ticket status summaries
* Transaction reports
* Employee counts
* Salary summaries
* Application support dashboards
* Operational reporting

---

## Concepts covered

* `GROUP BY`
* Aggregate functions with groups
* Grouping by one column
* Grouping by multiple columns
* `WHERE` with `GROUP BY`
* `HAVING`
* `WHERE` and `HAVING` differences
* Sorting grouped results

---

## Example table

The examples in this file use the following `employees` table:

| employee_id | employee_name | department        | salary | status   | city      |
| ----------: | ------------- | ----------------- | -----: | -------- | --------- |
|         101 | Ravi          | IT Support        |  28000 | Active   | Chennai   |
|         102 | Priya         | Finance           |  32000 | Active   | Bengaluru |
|         103 | Arun          | Operations        |  26000 | Inactive | Chennai   |
|         104 | Meena         | IT Support        |  30000 | Active   | Vellore   |
|         105 | Kumar         | Technical Support |  25000 | Inactive | Bengaluru |
|         106 | Divya         | IT Support        |  35000 | Active   | Chennai   |
|         107 | Suresh        | Finance           |  29000 | Active   | Chennai   |
|         108 | Anitha        | Operations        |  27000 | Active   | Vellore   |

---

## What is GROUP BY?

`GROUP BY` combines rows that contain the same value in one or more columns.

Aggregate functions then calculate results separately for each group.

## Basic syntax

```sql
SELECT
    group_column,
    aggregate_function(column_name)
FROM table_name
GROUP BY group_column;
```

Example:

```sql
SELECT
    department,
    COUNT(*) AS employee_count
FROM employees
GROUP BY department;
```

Result:

| department        | employee_count |
| ----------------- | -------------: |
| Finance           |              2 |
| IT Support        |              3 |
| Operations        |              2 |
| Technical Support |              1 |

Quick recall:

```text
GROUP BY creates categories.
Aggregate functions calculate one result for each category.
```

---

## GROUP BY without aggregation

This query may work:

```sql
SELECT department
FROM employees
GROUP BY department;
```

It returns one row for each department.

However, if the goal is only to remove duplicate values, use `DISTINCT`.

Better:

```sql
SELECT DISTINCT department
FROM employees;
```

Use `GROUP BY` when calculating summaries.

Use `DISTINCT` when retrieving unique values.

---

## Count rows in each group

Count employees in each department:

```sql
SELECT
    department,
    COUNT(*) AS employee_count
FROM employees
GROUP BY department;
```

Processing logic:

```text
1. Read all employee rows
2. Group matching departments
3. Count rows inside each department
4. Return one row per department
```

---

## SUM with GROUP BY

Calculate the total salary for each department:

```sql
SELECT
    department,
    SUM(salary) AS total_salary
FROM employees
GROUP BY department;
```

Result:

| department        | total_salary |
| ----------------- | -----------: |
| Finance           |        61000 |
| IT Support        |        93000 |
| Operations        |        53000 |
| Technical Support |        25000 |

Quick recall:

```text
SUM with GROUP BY calculates one total for each group.
```

---

## AVG with GROUP BY

Calculate the average salary for each department:

```sql
SELECT
    department,
    ROUND(AVG(salary), 2) AS average_salary
FROM employees
GROUP BY department;
```

Result:

| department        | average_salary |
| ----------------- | -------------: |
| Finance           |       30500.00 |
| IT Support        |       31000.00 |
| Operations        |       26500.00 |
| Technical Support |       25000.00 |

---

## MIN and MAX with GROUP BY

Find the lowest and highest salary in each department:

```sql
SELECT
    department,
    MIN(salary) AS lowest_salary,
    MAX(salary) AS highest_salary
FROM employees
GROUP BY department;
```

Result:

| department        | lowest_salary | highest_salary |
| ----------------- | ------------: | -------------: |
| Finance           |         29000 |          32000 |
| IT Support        |         28000 |          35000 |
| Operations        |         26000 |          27000 |
| Technical Support |         25000 |          25000 |

---

## Multiple aggregate functions

Several aggregate functions can be used in the same grouped query.

```sql
SELECT
    department,
    COUNT(*) AS employee_count,
    SUM(salary) AS total_salary,
    ROUND(AVG(salary), 2) AS average_salary,
    MIN(salary) AS lowest_salary,
    MAX(salary) AS highest_salary
FROM employees
GROUP BY department;
```

This produces a complete department-wise salary summary.

---

## Group by status

Count employees by status:

```sql
SELECT
    status,
    COUNT(*) AS employee_count
FROM employees
GROUP BY status;
```

Result:

| status   | employee_count |
| -------- | -------------: |
| Active   |              6 |
| Inactive |              2 |

---

## Group by city

Count employees in each city:

```sql
SELECT
    city,
    COUNT(*) AS employee_count
FROM employees
GROUP BY city;
```

Result:

| city      | employee_count |
| --------- | -------------: |
| Bengaluru |              2 |
| Chennai   |              4 |
| Vellore   |              2 |

---

## Grouping by multiple columns

SQL can group using more than one column.

```sql
SELECT
    department,
    status,
    COUNT(*) AS employee_count
FROM employees
GROUP BY department, status;
```

The database creates a separate group for each unique combination of department and status.

Possible result:

| department        | status   | employee_count |
| ----------------- | -------- | -------------: |
| Finance           | Active   |              2 |
| IT Support        | Active   |              3 |
| Operations        | Active   |              1 |
| Operations        | Inactive |              1 |
| Technical Support | Inactive |              1 |

Quick recall:

```text
Multiple-column grouping creates groups from combinations.
```

---

## Grouping by city and status

```sql
SELECT
    city,
    status,
    COUNT(*) AS employee_count
FROM employees
GROUP BY city, status;
```

This answers questions such as:

* How many active employees are in Chennai?
* How many inactive employees are in Bengaluru?
* How many active employees are in Vellore?

---

## Selected columns and GROUP BY

In a grouped query, every selected column should normally be:

* Included in `GROUP BY`, or
* Used inside an aggregate function

Correct:

```sql
SELECT
    department,
    COUNT(*) AS employee_count
FROM employees
GROUP BY department;
```

Problematic:

```sql
SELECT
    employee_name,
    department,
    COUNT(*) AS employee_count
FROM employees
GROUP BY department;
```

The database does not know which employee name should represent the entire department.

In strict SQL modes, this produces an error.

Correct alternatives:

```sql
SELECT
    department,
    COUNT(*) AS employee_count
FROM employees
GROUP BY department;
```

Or group by both columns:

```sql
SELECT
    employee_name,
    department,
    COUNT(*) AS employee_count
FROM employees
GROUP BY employee_name, department;
```

The second query usually has little reporting value because each employee may form a separate group.

---

## WHERE with GROUP BY

`WHERE` filters individual rows before grouping.

Count only active employees in each department:

```sql
SELECT
    department,
    COUNT(*) AS active_employee_count
FROM employees
WHERE status = 'Active'
GROUP BY department;
```

Processing logic:

```text
1. Filter active employees
2. Group them by department
3. Count employees in each department
```

Quick recall:

```text
WHERE filters rows before grouping.
```

---

## Salary summary for active employees

```sql
SELECT
    department,
    COUNT(*) AS active_employee_count,
    SUM(salary) AS active_salary_total,
    ROUND(AVG(salary), 2) AS active_average_salary
FROM employees
WHERE status = 'Active'
GROUP BY department;
```

Inactive employees are removed before aggregation.

---

## Filter rows by city before grouping

```sql
SELECT
    department,
    COUNT(*) AS employee_count
FROM employees
WHERE city = 'Chennai'
GROUP BY department;
```

This counts Chennai employees within each department.

---

## What is HAVING?

`HAVING` filters grouped results.

It is usually used with aggregate functions.

## Basic syntax

```sql
SELECT
    group_column,
    aggregate_function(column_name)
FROM table_name
GROUP BY group_column
HAVING aggregate_condition;
```

Example:

```sql
SELECT
    department,
    COUNT(*) AS employee_count
FROM employees
GROUP BY department
HAVING COUNT(*) >= 2;
```

Result:

| department | employee_count |
| ---------- | -------------: |
| Finance    |              2 |
| IT Support |              3 |
| Operations |              2 |

The Technical Support department is excluded because it contains only one employee.

Quick recall:

```text
HAVING filters groups after aggregation.
```

---

## HAVING with COUNT

Find departments containing more than one employee:

```sql
SELECT
    department,
    COUNT(*) AS employee_count
FROM employees
GROUP BY department
HAVING COUNT(*) > 1;
```

---

## HAVING with SUM

Find departments with a total salary above `50000`:

```sql
SELECT
    department,
    SUM(salary) AS total_salary
FROM employees
GROUP BY department
HAVING SUM(salary) > 50000;
```

Result:

| department | total_salary |
| ---------- | -----------: |
| Finance    |        61000 |
| IT Support |        93000 |
| Operations |        53000 |

---

## HAVING with AVG

Find departments with an average salary of at least `30000`:

```sql
SELECT
    department,
    ROUND(AVG(salary), 2) AS average_salary
FROM employees
GROUP BY department
HAVING AVG(salary) >= 30000;
```

Result:

| department | average_salary |
| ---------- | -------------: |
| Finance    |       30500.00 |
| IT Support |       31000.00 |

---

## HAVING with MIN and MAX

Find departments where the highest salary exceeds `30000`:

```sql
SELECT
    department,
    MAX(salary) AS highest_salary
FROM employees
GROUP BY department
HAVING MAX(salary) > 30000;
```

Find departments where the lowest salary is at least `27000`:

```sql
SELECT
    department,
    MIN(salary) AS lowest_salary
FROM employees
GROUP BY department
HAVING MIN(salary) >= 27000;
```

---

## WHERE and HAVING together

`WHERE` and `HAVING` can be used in the same query.

```sql
SELECT
    department,
    COUNT(*) AS active_employee_count,
    AVG(salary) AS average_salary
FROM employees
WHERE status = 'Active'
GROUP BY department
HAVING COUNT(*) >= 2;
```

Processing logic:

```text
1. WHERE keeps only active employees
2. GROUP BY creates department groups
3. Aggregate functions calculate results
4. HAVING keeps groups with at least two employees
```

---

## WHERE and HAVING difference

| WHERE                                    | HAVING                            |
| ---------------------------------------- | --------------------------------- |
| Filters individual rows                  | Filters grouped results           |
| Runs before `GROUP BY`                   | Runs after `GROUP BY`             |
| Usually does not use aggregate functions | Commonly uses aggregate functions |
| Reduces data before grouping             | Removes groups after aggregation  |

Example using `WHERE`:

```sql
SELECT
    department,
    COUNT(*) AS employee_count
FROM employees
WHERE status = 'Active'
GROUP BY department;
```

Example using `HAVING`:

```sql
SELECT
    department,
    COUNT(*) AS employee_count
FROM employees
GROUP BY department
HAVING COUNT(*) >= 2;
```

Quick recall:

```text
WHERE filters rows.
HAVING filters groups.
```

---

## Use WHERE when possible

This query may work:

```sql
SELECT
    department,
    COUNT(*) AS employee_count
FROM employees
GROUP BY department
HAVING department = 'IT Support';
```

But the condition does not depend on an aggregate result.

Better:

```sql
SELECT
    department,
    COUNT(*) AS employee_count
FROM employees
WHERE department = 'IT Support'
GROUP BY department;
```

`WHERE` filters data earlier and is usually clearer and more efficient.

Use `HAVING` when filtering aggregate results.

---

## Using aliases in HAVING

MySQL usually allows aggregate aliases in `HAVING`.

```sql
SELECT
    department,
    COUNT(*) AS employee_count
FROM employees
GROUP BY department
HAVING employee_count >= 2;
```

The more portable version is:

```sql
SELECT
    department,
    COUNT(*) AS employee_count
FROM employees
GROUP BY department
HAVING COUNT(*) >= 2;
```

Different database systems may handle aliases differently.

---

## ORDER BY with GROUP BY

Grouped results can be sorted using `ORDER BY`.

Sort departments by employee count:

```sql
SELECT
    department,
    COUNT(*) AS employee_count
FROM employees
GROUP BY department
ORDER BY employee_count DESC;
```

Result:

| department        | employee_count |
| ----------------- | -------------: |
| IT Support        |              3 |
| Finance           |              2 |
| Operations        |              2 |
| Technical Support |              1 |

---

## Sort by total salary

```sql
SELECT
    department,
    SUM(salary) AS total_salary
FROM employees
GROUP BY department
ORDER BY total_salary DESC;
```

---

## ORDER BY with HAVING

```sql
SELECT
    department,
    COUNT(*) AS employee_count,
    AVG(salary) AS average_salary
FROM employees
GROUP BY department
HAVING COUNT(*) >= 2
ORDER BY average_salary DESC;
```

This:

1. Groups employees by department
2. Keeps departments with at least two employees
3. Sorts the remaining groups by average salary

---

## LIMIT with grouped results

Return the department with the highest total salary:

```sql
SELECT
    department,
    SUM(salary) AS total_salary
FROM employees
GROUP BY department
ORDER BY total_salary DESC
LIMIT 1;
```

Result:

| department | total_salary |
| ---------- | -----------: |
| IT Support |        93000 |

---

## Top groups

Find the two departments with the highest average salary:

```sql
SELECT
    department,
    ROUND(AVG(salary), 2) AS average_salary
FROM employees
GROUP BY department
ORDER BY average_salary DESC
LIMIT 2;
```

---

## COUNT with conditional grouping

Count active and inactive employees by department:

```sql
SELECT
    department,
    status,
    COUNT(*) AS employee_count
FROM employees
GROUP BY department, status
ORDER BY department, status;
```

This creates a separate row for each department and status combination.

---

## Conditional aggregation

Conditional aggregation calculates multiple category counts in one grouped query.

MySQL example:

```sql
SELECT
    department,
    COUNT(*) AS total_employees,
    SUM(status = 'Active') AS active_employees,
    SUM(status = 'Inactive') AS inactive_employees
FROM employees
GROUP BY department;
```

A more portable version uses `CASE`.

```sql
SELECT
    department,
    COUNT(*) AS total_employees,
    SUM(
        CASE
            WHEN status = 'Active' THEN 1
            ELSE 0
        END
    ) AS active_employees,
    SUM(
        CASE
            WHEN status = 'Inactive' THEN 1
            ELSE 0
        END
    ) AS inactive_employees
FROM employees
GROUP BY department;
```

`CASE` expressions are covered more deeply in a later advanced topic.

---

## Grouping NULL values

If the grouping column contains `NULL`, all `NULL` values normally form one group.

Example table:

| employee_name | department |
| ------------- | ---------- |
| Ravi          | IT Support |
| Priya         | Finance    |
| Arun          | NULL       |
| Kumar         | NULL       |

Query:

```sql
SELECT
    department,
    COUNT(*) AS employee_count
FROM employees
GROUP BY department;
```

Possible result:

| department | employee_count |
| ---------- | -------------: |
| NULL       |              2 |
| Finance    |              1 |
| IT Support |              1 |

Use `COALESCE()` to display a readable label:

```sql
SELECT
    COALESCE(department, 'Unassigned') AS department_name,
    COUNT(*) AS employee_count
FROM employees
GROUP BY COALESCE(department, 'Unassigned');
```

---

## Grouping dates

Dates can be grouped by complete date.

```sql
SELECT
    created_date,
    COUNT(*) AS ticket_count
FROM tickets
GROUP BY created_date
ORDER BY created_date;
```

For date-time columns, grouping by the full value may create separate groups for every timestamp.

In MySQL, group by date only:

```sql
SELECT
    DATE(created_at) AS created_date,
    COUNT(*) AS ticket_count
FROM tickets
GROUP BY DATE(created_at)
ORDER BY created_date;
```

---

## Grouping by month

MySQL example:

```sql
SELECT
    YEAR(created_at) AS created_year,
    MONTH(created_at) AS created_month,
    COUNT(*) AS ticket_count
FROM tickets
GROUP BY
    YEAR(created_at),
    MONTH(created_at)
ORDER BY
    created_year,
    created_month;
```

This produces a monthly ticket summary.

---

## Practical support examples

## Count tickets by status

```sql
SELECT
    status,
    COUNT(*) AS ticket_count
FROM tickets
GROUP BY status;
```

---

## Count tickets by priority

```sql
SELECT
    priority,
    COUNT(*) AS ticket_count
FROM tickets
GROUP BY priority;
```

---

## Count open tickets by department

```sql
SELECT
    department,
    COUNT(*) AS open_ticket_count
FROM tickets
WHERE status = 'Open'
GROUP BY department;
```

---

## Find departments with more than five open tickets

```sql
SELECT
    department,
    COUNT(*) AS open_ticket_count
FROM tickets
WHERE status = 'Open'
GROUP BY department
HAVING COUNT(*) > 5;
```

---

## Find users with repeated failed login attempts

```sql
SELECT
    user_id,
    COUNT(*) AS failed_attempt_count
FROM login_attempts
WHERE status = 'Failed'
GROUP BY user_id
HAVING COUNT(*) >= 3;
```

This can help identify locked accounts or suspicious login activity.

---

## Find repeated error codes

```sql
SELECT
    error_code,
    COUNT(*) AS occurrence_count
FROM application_logs
WHERE log_level = 'ERROR'
GROUP BY error_code
HAVING COUNT(*) > 10
ORDER BY occurrence_count DESC;
```

---

## Count transactions by status

```sql
SELECT
    status,
    COUNT(*) AS transaction_count
FROM transactions
GROUP BY status;
```

---

## Calculate transaction value by status

```sql
SELECT
    status,
    COUNT(*) AS transaction_count,
    SUM(amount) AS total_amount
FROM transactions
GROUP BY status;
```

---

## Find users with multiple failed transactions

```sql
SELECT
    user_id,
    COUNT(*) AS failed_transaction_count
FROM transactions
WHERE status = 'Failed'
GROUP BY user_id
HAVING COUNT(*) >= 2;
```

---

## Calculate average ticket resolution time by technician

```sql
SELECT
    assigned_to,
    COUNT(*) AS closed_ticket_count,
    ROUND(AVG(resolution_minutes), 2) AS average_resolution_minutes
FROM tickets
WHERE status = 'Closed'
GROUP BY assigned_to;
```

---

## Find technicians handling more than ten closed tickets

```sql
SELECT
    assigned_to,
    COUNT(*) AS closed_ticket_count
FROM tickets
WHERE status = 'Closed'
GROUP BY assigned_to
HAVING COUNT(*) > 10;
```

---

## Monthly failed transaction report

```sql
SELECT
    YEAR(created_at) AS transaction_year,
    MONTH(created_at) AS transaction_month,
    COUNT(*) AS failed_transaction_count,
    SUM(amount) AS failed_transaction_value
FROM transactions
WHERE status = 'Failed'
GROUP BY
    YEAR(created_at),
    MONTH(created_at)
ORDER BY
    transaction_year,
    transaction_month;
```

---

## Logical query processing order

A simplified processing order is:

```text
1. FROM
2. WHERE
3. GROUP BY
4. Aggregate functions
5. HAVING
6. SELECT
7. DISTINCT
8. ORDER BY
9. LIMIT
```

Example:

```sql
SELECT
    department,
    COUNT(*) AS employee_count
FROM employees
WHERE status = 'Active'
GROUP BY department
HAVING COUNT(*) >= 2
ORDER BY employee_count DESC
LIMIT 3;
```

Conceptual execution:

```text
1. Read employees
2. Keep active employees
3. Group by department
4. Count employees in each group
5. Keep groups with at least two employees
6. Sort by count
7. Return three groups
```

---

## Common mistakes

## Mistake 1: Selecting a column not included in GROUP BY

Wrong:

```sql
SELECT
    employee_name,
    department,
    COUNT(*)
FROM employees
GROUP BY department;
```

The database cannot determine which employee name represents the department.

Correct:

```sql
SELECT
    department,
    COUNT(*) AS employee_count
FROM employees
GROUP BY department;
```

---

## Mistake 2: Using WHERE with aggregate functions

Wrong:

```sql
SELECT
    department,
    COUNT(*) AS employee_count
FROM employees
WHERE COUNT(*) > 1
GROUP BY department;
```

Correct:

```sql
SELECT
    department,
    COUNT(*) AS employee_count
FROM employees
GROUP BY department
HAVING COUNT(*) > 1;
```

Aggregate conditions belong in `HAVING`.

---

## Mistake 3: Using HAVING for normal row filters

Weak:

```sql
SELECT
    department,
    COUNT(*) AS employee_count
FROM employees
GROUP BY department
HAVING department = 'IT Support';
```

Better:

```sql
SELECT
    department,
    COUNT(*) AS employee_count
FROM employees
WHERE department = 'IT Support'
GROUP BY department;
```

Use `WHERE` to filter normal rows before grouping.

---

## Mistake 4: Forgetting that WHERE runs before grouping

```sql
SELECT
    department,
    COUNT(*) AS employee_count
FROM employees
WHERE status = 'Active'
GROUP BY department;
```

This counts only active employees.

It does not count all employees and then label the result active.

---

## Mistake 5: Grouping by too many columns

```sql
SELECT
    employee_id,
    employee_name,
    department,
    COUNT(*) AS employee_count
FROM employees
GROUP BY employee_id, employee_name, department;
```

Because `employee_id` is unique, each employee becomes a separate group.

The count will usually be `1` for every row.

Group only by columns required by the report.

---

## Mistake 6: Grouping by too few columns

```sql
SELECT
    department,
    status,
    COUNT(*) AS employee_count
FROM employees
GROUP BY department;
```

`status` is selected but not grouped or aggregated.

Correct:

```sql
SELECT
    department,
    status,
    COUNT(*) AS employee_count
FROM employees
GROUP BY department, status;
```

---

## Mistake 7: Confusing DISTINCT and GROUP BY

Use `DISTINCT` for unique values:

```sql
SELECT DISTINCT department
FROM employees;
```

Use `GROUP BY` for summaries:

```sql
SELECT
    department,
    COUNT(*) AS employee_count
FROM employees
GROUP BY department;
```

---

## Mistake 8: Expecting one result from GROUP BY

Without grouping:

```sql
SELECT COUNT(*)
FROM employees;
```

Returns one total.

With grouping:

```sql
SELECT
    department,
    COUNT(*)
FROM employees
GROUP BY department;
```

Returns one total per department.

---

## Mistake 9: Forgetting NULL creates a group

Rows with `NULL` in the grouped column may appear as a separate group.

Use `COALESCE()` when a readable label is required.

```sql
SELECT
    COALESCE(department, 'Unassigned') AS department_name,
    COUNT(*) AS employee_count
FROM employees
GROUP BY COALESCE(department, 'Unassigned');
```

---

## Mistake 10: Grouping full timestamps

Weak:

```sql
SELECT
    created_at,
    COUNT(*)
FROM tickets
GROUP BY created_at;
```

Every timestamp may become a separate group.

Better:

```sql
SELECT
    DATE(created_at) AS created_date,
    COUNT(*) AS ticket_count
FROM tickets
GROUP BY DATE(created_at);
```

---

## Practice queries

## Query 1

Count employees in each department.

```sql
SELECT
    department,
    COUNT(*) AS employee_count
FROM employees
GROUP BY department;
```

## Query 2

Calculate total salary by department.

```sql
SELECT
    department,
    SUM(salary) AS total_salary
FROM employees
GROUP BY department;
```

## Query 3

Calculate average salary by department.

```sql
SELECT
    department,
    ROUND(AVG(salary), 2) AS average_salary
FROM employees
GROUP BY department;
```

## Query 4

Count active employees by department.

```sql
SELECT
    department,
    COUNT(*) AS active_employee_count
FROM employees
WHERE status = 'Active'
GROUP BY department;
```

## Query 5

Find departments with at least two employees.

```sql
SELECT
    department,
    COUNT(*) AS employee_count
FROM employees
GROUP BY department
HAVING COUNT(*) >= 2;
```

## Query 6

Find departments with an average salary above `28000`.

```sql
SELECT
    department,
    ROUND(AVG(salary), 2) AS average_salary
FROM employees
GROUP BY department
HAVING AVG(salary) > 28000;
```

## Query 7

Group employees by department and status.

```sql
SELECT
    department,
    status,
    COUNT(*) AS employee_count
FROM employees
GROUP BY department, status;
```

## Query 8

Sort departments by employee count.

```sql
SELECT
    department,
    COUNT(*) AS employee_count
FROM employees
GROUP BY department
ORDER BY employee_count DESC;
```

## Query 9

Find the department with the highest total salary.

```sql
SELECT
    department,
    SUM(salary) AS total_salary
FROM employees
GROUP BY department
ORDER BY total_salary DESC
LIMIT 1;
```

## Query 10

Find active departments with at least two employees.

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

## Quick recall notes

* `GROUP BY` combines rows with matching values.
* Aggregate functions calculate one result per group.
* `COUNT()` counts rows inside each group.
* `SUM()` calculates one total per group.
* `AVG()` calculates one average per group.
* `MIN()` finds the lowest value per group.
* `MAX()` finds the highest value per group.
* Multiple columns can be used in `GROUP BY`.
* Multiple-column grouping uses unique combinations.
* Selected normal columns should appear in `GROUP BY`.
* `WHERE` filters rows before grouping.
* `HAVING` filters groups after aggregation.
* Aggregate conditions normally belong in `HAVING`.
* Normal row conditions normally belong in `WHERE`.
* `ORDER BY` sorts grouped results.
* `LIMIT` can return the top grouped results.
* `NULL` values may form a separate group.
* `DISTINCT` returns unique values.
* `GROUP BY` creates summarized results.

---

## Interview questions

### 1. What does GROUP BY do?

`GROUP BY` combines rows with matching values and allows aggregate functions to calculate one result for each group.

### 2. Why is GROUP BY used?

It is used to create category-wise summaries such as department counts, ticket status totals, and monthly transaction reports.

### 3. How do you count employees by department?

```sql
SELECT
    department,
    COUNT(*) AS employee_count
FROM employees
GROUP BY department;
```

### 4. Can multiple columns be used in GROUP BY?

Yes.

```sql
GROUP BY department, status;
```

This creates one group for every unique department and status combination.

### 5. What does HAVING do?

`HAVING` filters grouped or aggregated results.

### 6. What is the difference between WHERE and HAVING?

`WHERE` filters rows before grouping. `HAVING` filters groups after aggregation.

### 7. Can aggregate functions be used in WHERE?

Normally, no. Aggregate conditions should be placed in `HAVING`.

### 8. How do you find departments with more than five employees?

```sql
SELECT
    department,
    COUNT(*) AS employee_count
FROM employees
GROUP BY department
HAVING COUNT(*) > 5;
```

### 9. Can WHERE and HAVING be used together?

Yes.

```sql
SELECT
    department,
    COUNT(*) AS active_employee_count
FROM employees
WHERE status = 'Active'
GROUP BY department
HAVING COUNT(*) >= 2;
```

### 10. What happens if a selected normal column is not included in GROUP BY?

The query may fail or return unreliable results because the database cannot determine which value should represent the group.

### 11. What is the difference between DISTINCT and GROUP BY?

`DISTINCT` removes duplicate result values. `GROUP BY` creates groups for aggregate calculations.

### 12. How do you calculate average salary by department?

```sql
SELECT
    department,
    AVG(salary) AS average_salary
FROM employees
GROUP BY department;
```

### 13. How do you sort grouped results?

Use `ORDER BY` after `GROUP BY` and `HAVING`.

```sql
SELECT
    department,
    COUNT(*) AS employee_count
FROM employees
GROUP BY department
ORDER BY employee_count DESC;
```

### 14. Can an aggregate alias be used in HAVING?

MySQL usually allows it, but using the aggregate expression directly is more portable across database systems.

### 15. What happens to NULL values in GROUP BY?

Rows containing `NULL` in the grouping column normally form one separate group.

### 16. How do you display NULL groups as Unassigned?

```sql
SELECT
    COALESCE(department, 'Unassigned') AS department_name,
    COUNT(*) AS employee_count
FROM employees
GROUP BY COALESCE(department, 'Unassigned');
```

### 17. How do you return the department with the highest total salary?

```sql
SELECT
    department,
    SUM(salary) AS total_salary
FROM employees
GROUP BY department
ORDER BY total_salary DESC
LIMIT 1;
```

### 18. What is the logical order of WHERE, GROUP BY, and HAVING?

```text
WHERE
GROUP BY
HAVING
```

### 19. Why should normal conditions be placed in WHERE instead of HAVING?

`WHERE` filters rows earlier, reduces the amount of data being grouped, and usually improves clarity and efficiency.

### 20. How are GROUP BY and HAVING useful in application support?

They help summarize tickets, count failed transactions, identify repeated errors, calculate technician workloads, and detect users with repeated login failures.
