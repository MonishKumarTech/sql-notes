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
7. DIS
