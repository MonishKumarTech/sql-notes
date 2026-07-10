# Subqueries

## Purpose

A subquery is a query written inside another SQL query.

Subqueries are used when one query needs the result of another query before it can complete its work.

They are useful for:

* Finding values above or below an average
* Comparing records with summary values
* Finding related or missing records
* Filtering data using another table
* Finding highest or lowest values
* Application support investigations
* Reporting and troubleshooting

---

## Concepts covered

* Scalar subqueries
* Single-row subqueries
* Multiple-row subqueries
* Subqueries with `WHERE`
* Subqueries with `IN`
* Subqueries with `EXISTS`
* Subqueries with `NOT EXISTS`
* Correlated subqueries
* Subqueries in `SELECT`
* Subqueries in `FROM`
* Nested subqueries
* Subqueries compared with joins

---

## Example tables

The examples in this file use the following tables.

### Employees table

| employee_id | employee_name | department_id | salary | status   |
| ----------: | ------------- | ------------: | -----: | -------- |
|         101 | Ravi          |             1 |  28000 | Active   |
|         102 | Priya         |             2 |  32000 | Active   |
|         103 | Arun          |             3 |  26000 | Inactive |
|         104 | Meena         |             1 |  40000 | Active   |
|         105 | Kumar         |             2 |  42000 | Active   |
|         106 | Divya         |             1 |  35000 | Active   |
|         107 | Suresh        |          NULL |  29000 | Inactive |

### Departments table

| department_id | department_name |
| ------------: | --------------- |
|             1 | IT Support      |
|             2 | Finance         |
|             3 | Operations      |
|             4 | Human Resources |

---

## What is a subquery?

A subquery is an SQL query placed inside another SQL statement.

Example:

```sql
SELECT employee_name, salary
FROM employees
WHERE salary > (
    SELECT AVG(salary)
    FROM employees
);
```

The inner query calculates the average salary.

```sql
SELECT AVG(salary)
FROM employees;
```

The outer query returns employees earning more than that average.

Quick recall:

```text
Inner query produces a value or result.
Outer query uses that result.
```

---

## Basic structure

```sql
SELECT column_name
FROM table_name
WHERE column_name operator (
    SELECT column_name
    FROM another_table
);
```

The subquery is written inside parentheses.

---

## Query execution order

In a normal non-correlated subquery:

```text
1. The inner query runs.
2. The inner query returns a result.
3. The outer query uses that result.
4. The outer query returns the final rows.
```

Example:

```sql
SELECT employee_name, salary
FROM employees
WHERE salary > (
    SELECT AVG(salary)
    FROM employees
);
```

Conceptual execution:

```text
1. Calculate average salary.
2. Compare each employee salary with the average.
3. Return employees earning above average.
```

---

## Scalar subquery

A scalar subquery returns exactly one value.

Example:

```sql
SELECT AVG(salary)
FROM employees;
```

This returns one value.

It can be used inside another query:

```sql
SELECT employee_name, salary
FROM employees
WHERE salary > (
    SELECT AVG(salary)
    FROM employees
);
```

Quick recall:

```text
Scalar subquery = one value.
```

---

## Single-row subquery

A single-row subquery returns one complete row or one value.

It commonly works with comparison operators such as:

| Operator | Meaning               |
| -------- | --------------------- |
| `=`      | Equal                 |
| `>`      | Greater than          |
| `<`      | Less than             |
| `>=`     | Greater than or equal |
| `<=`     | Less than or equal    |
| `<>`     | Not equal             |

---

## Find employees earning above average

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

The subquery returns one average salary value.

The outer query compares every employee salary with it.

---

## Find employees earning below average

```sql
SELECT
    employee_name,
    salary
FROM employees
WHERE salary < (
    SELECT AVG(salary)
    FROM employees
);
```

---

## Find the highest-paid employee

```sql
SELECT
    employee_name,
    salary
FROM employees
WHERE salary = (
    SELECT MAX(salary)
    FROM employees
);
```

This returns every employee whose salary equals the highest salary.

Unlike:

```sql
SELECT employee_name, salary
FROM employees
ORDER BY salary DESC
LIMIT 1;
```

The subquery version returns all employees tied for the highest salary.

---

## Find the lowest-paid employee

```sql
SELECT
    employee_name,
    salary
FROM employees
WHERE salary = (
    SELECT MIN(salary)
    FROM employees
);
```

---

## Find employees in a specific department

```sql
SELECT
    employee_name,
    salary
FROM employees
WHERE department_id = (
    SELECT department_id
    FROM departments
    WHERE department_name = 'IT Support'
);
```

The subquery first finds the department ID for IT Support.

The outer query then finds employees using that department ID.

---

## Multiple-row subquery

A multiple-row subquery returns more than one value.

Example:

```sql
SELECT department_id
FROM departments
WHERE department_name IN ('IT Support', 'Finance');
```

Possible result:

```text
1
2
```

Because the subquery returns multiple values, use operators such as:

* `IN`
* `NOT IN`
* `ANY`
* `ALL`

Do not use `=` unless the subquery is guaranteed to return exactly one value.

Quick recall:

```text
One result uses =.
Many results usually use IN.
```

---

## Subquery with IN

Find employees in departments located in a selected list:

```sql
SELECT
    employee_name,
    department_id
FROM employees
WHERE department_id IN (
    SELECT department_id
    FROM departments
    WHERE department_name IN ('IT Support', 'Finance')
);
```

The inner query returns department IDs `1` and `2`.

The outer query returns employees from those departments.

---

## Find employees in existing departments

```sql
SELECT
    employee_id,
    employee_name
FROM employees
WHERE department_id IN (
    SELECT department_id
    FROM departments
);
```

Employees with missing or invalid department IDs are excluded.

---

## Subquery with NOT IN

Find employees whose department is not in a selected list:

```sql
SELECT
    employee_name,
    department_id
FROM employees
WHERE department_id NOT IN (
    SELECT department_id
    FROM departments
    WHERE department_name IN ('Finance', 'Operations')
);
```

---

## NOT IN and NULL warning

`NOT IN` can produce unexpected results when the subquery returns `NULL`.

Example:

```sql
SELECT employee_name
FROM employees
WHERE department_id NOT IN (
    SELECT department_id
    FROM departments
);
```

If the subquery contains a `NULL`, the comparison can become unknown and return no rows.

Safer approaches include:

```sql
SELECT employee_name
FROM employees
WHERE department_id NOT IN (
    SELECT department_id
    FROM departments
    WHERE department_id IS NOT NULL
);
```

Or use `NOT EXISTS`.

Quick recall:

```text
NOT IN is risky when NULL values are possible.
NOT EXISTS is often safer.
```

---

## EXISTS

`EXISTS` checks whether a subquery returns at least one row.

It returns:

* `TRUE` when the subquery finds a row
* `FALSE` when the subquery finds no rows

Example:

```sql
SELECT
    d.department_id,
    d.department_name
FROM departments AS d
WHERE EXISTS (
    SELECT 1
    FROM employees AS e
    WHERE e.department_id = d.department_id
);
```

This returns departments that contain at least one employee.

Quick recall:

```text
EXISTS asks: Does at least one matching row exist?
```

---

## Why SELECT 1 is used with EXISTS

Inside `EXISTS`, the actual selected value does not matter.

These are logically equivalent:

```sql
SELECT 1
```

```sql
SELECT *
```

```sql
SELECT employee_id
```

`SELECT 1` clearly shows that only the existence of a row matters.

---

## Find departments with employees

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

Possible result:

| department_name |
| --------------- |
| IT Support      |
| Finance         |
| Operations      |

Human Resources is excluded because it has no employees.

---

## NOT EXISTS

`NOT EXISTS` checks that the subquery returns no matching rows.

Find departments without employees:

```sql
SELECT
    d.department_id,
    d.department_name
FROM departments AS d
WHERE NOT EXISTS (
    SELECT 1
    FROM employees AS e
    WHERE e.department_id = d.department_id
);
```

Result:

| department_id | department_name |
| ------------: | --------------- |
|             4 | Human Resources |

Quick recall:

```text
NOT EXISTS asks: Is there no matching row?
```

---

## Find users without roles

```sql
SELECT
    u.user_id,
    u.username
FROM users AS u
WHERE NOT EXISTS (
    SELECT 1
    FROM user_roles AS ur
    WHERE ur.user_id = u.user_id
);
```

This is useful in application support for identifying incomplete user configurations.

---

## Correlated subquery

A correlated subquery refers to a value from the outer query.

It runs once for each row processed by the outer query.

Example:

```sql
SELECT
    e.employee_name,
    e.department_id,
    e.salary
FROM employees AS e
WHERE e.salary > (
    SELECT AVG(e2.salary)
    FROM employees AS e2
    WHERE e2.department_id = e.department_id
);
```

This finds employees earning more than the average salary of their own department.

The inner query depends on:

```sql
e.department_id
```

That value comes from the current outer employee row.

Quick recall:

```text
A normal subquery runs independently.
A correlated subquery depends on the outer row.
```

---

## Correlated subquery execution

For each employee:

```text
1. Read the employee's department.
2. Calculate the average salary for that department.
3. Compare the employee salary with the department average.
4. Keep the employee if the condition is true.
```

Because the inner query may run repeatedly, correlated subqueries can be slower on large datasets.

---

## Find employees above their department average

```sql
SELECT
    e.employee_name,
    e.department_id,
    e.salary
FROM employees AS e
WHERE e.salary > (
    SELECT AVG(e2.salary)
    FROM employees AS e2
    WHERE e2.department_id = e.department_id
);
```

---

## Find the highest-paid employee in each department

```sql
SELECT
    e.employee_name,
    e.department_id,
    e.salary
FROM employees AS e
WHERE e.salary = (
    SELECT MAX(e2.salary)
    FROM employees AS e2
    WHERE e2.department_id = e.department_id
);
```

This returns the highest-paid employee from each department.

If several employees share the maximum salary, all of them are returned.

---

## Correlated EXISTS subquery

Find departments with at least one active employee:

```sql
SELECT
    d.department_id,
    d.department_name
FROM departments AS d
WHERE EXISTS (
    SELECT 1
    FROM employees AS e
    WHERE e.department_id = d.department_id
      AND e.status = 'Active'
);
```

---

## Subquery in SELECT

A subquery can be placed in the `SELECT` list.

Example:

```sql
SELECT
    employee_name,
    salary,
    (
        SELECT AVG(salary)
        FROM employees
    ) AS company_average_salary
FROM employees;
```

Result:

| employee_name | salary | company_average_salary |
| ------------- | -----: | ---------------------: |
| Ravi          |  28000 |               34571.43 |
| Priya         |  32000 |               34571.43 |
| Arun          |  26000 |               34571.43 |
| Meena         |  40000 |               34571.43 |
| Kumar         |  42000 |               34571.43 |
| Divya         |  35000 |               34571.43 |
| Suresh        |  29000 |               34571.43 |

The same company average appears beside every employee.

---

## Compare salary with average in SELECT

```sql
SELECT
    employee_name,
    salary,
    (
        SELECT ROUND(AVG(salary), 2)
        FROM employees
    ) AS average_salary,
    salary - (
        SELECT AVG(salary)
        FROM employees
    ) AS difference_from_average
FROM employees;
```

This shows how far each salary is above or below the company average.

---

## Correlated subquery in SELECT

Count employees in each department:

```sql
SELECT
    d.department_name,
    (
        SELECT COUNT(*)
        FROM employees AS e
        WHERE e.department_id = d.department_id
    ) AS employee_count
FROM departments AS d;
```

This works, but a join with `GROUP BY` may be more efficient and easier to scale.

Equivalent join:

```sql
SELECT
    d.department_name,
    COUNT(e.employee_id) AS employee_count
FROM departments AS d
LEFT JOIN employees AS e
    ON d.department_id = e.department_id
GROUP BY
    d.department_id,
    d.department_name;
```

---

## Subquery in FROM

A subquery inside `FROM` creates a temporary result table.

This is also called:

* Derived table
* Inline view

Example:

```sql
SELECT
    department_id,
    average_salary
FROM (
    SELECT
        department_id,
        AVG(salary) AS average_salary
    FROM employees
    GROUP BY department_id
) AS department_summary;
```

The inner query creates a department salary summary.

The outer query reads from that temporary result.

Quick recall:

```text
A subquery in FROM behaves like a temporary table.
```

---

## Derived table alias

MySQL requires a subquery in `FROM` to have an alias.

Correct:

```sql
SELECT *
FROM (
    SELECT
        department_id,
        AVG(salary) AS average_salary
    FROM employees
    GROUP BY department_id
) AS department_summary;
```

Wrong:

```sql
SELECT *
FROM (
    SELECT
        department_id,
        AVG(salary) AS average_salary
    FROM employees
    GROUP BY department_id
);
```

Possible error:

```text
Every derived table must have its own alias
```

---

## Filter a derived table

Find departments with an average salary above `30000`:

```sql
SELECT
    department_id,
    average_salary
FROM (
    SELECT
        department_id,
        ROUND(AVG(salary), 2) AS average_salary
    FROM employees
    GROUP BY department_id
) AS department_summary
WHERE average_salary > 30000;
```

This can also be written using `HAVING`:

```sql
SELECT
    department_id,
    ROUND(AVG(salary), 2) AS average_salary
FROM employees
GROUP BY department_id
HAVING AVG(salary) > 30000;
```

The simpler direct query is usually preferable.

---

## Nested subqueries

A subquery can contain another subquery.

Example:

```sql
SELECT
    employee_name,
    salary
FROM employees
WHERE salary > (
    SELECT AVG(salary)
    FROM employees
    WHERE department_id = (
        SELECT department_id
        FROM departments
        WHERE department_name = 'IT Support'
    )
);
```

Execution:

```text
1. Find the IT Support department ID.
2. Calculate the average salary in IT Support.
3. Find employees earning more than that average.
```

Nested subqueries are valid but can become difficult to read.

Use joins or common table expressions when they produce clearer logic.

---

## ANY operator

`ANY` compares a value with any value returned by a subquery.

Example:

```sql
SELECT
    employee_name,
    salary
FROM employees
WHERE salary > ANY (
    SELECT salary
    FROM employees
    WHERE department_id = 1
);
```

This means:

```text
Salary must be greater than at least one salary in department 1.
```

If department 1 salaries are:

```text
28000
35000
40000
```

Then `salary > ANY` means salary greater than `28000`.

---

## ALL operator

`ALL` compares a value with every value returned by a subquery.

```sql
SELECT
    employee_name,
    salary
FROM employees
WHERE salary > ALL (
    SELECT salary
    FROM employees
    WHERE department_id = 1
);
```

This means:

```text
Salary must be greater than every salary in department 1.
```

Using the same values, salary must be greater than `40000`.

Quick recall:

```text
ANY = at least one comparison is true.
ALL = every comparison must be true.
```

---

## ANY and ALL comparison

| Expression | Meaning                                  |
| ---------- | ---------------------------------------- |
| `> ANY`    | Greater than at least one returned value |
| `> ALL`    | Greater than every returned value        |
| `< ANY`    | Less than at least one returned value    |
| `< ALL`    | Less than every returned value           |
| `= ANY`    | Similar to `IN`                          |

These operators are less common in beginner workplace queries but may appear in interviews.

---

## Subquery with INSERT

A subquery can supply data for an `INSERT`.

```sql
INSERT INTO active_employees_archive (
    employee_id,
    employee_name,
    salary
)
SELECT
    employee_id,
    employee_name,
    salary
FROM employees
WHERE status = 'Active';
```

This copies active employee records into another table.

---

## Subquery with UPDATE

A subquery can be used in an `UPDATE` condition.

```sql
UPDATE employees
SET status = 'Review'
WHERE department_id IN (
    SELECT department_id
    FROM departments
    WHERE department_name = 'Operations'
);
```

Before running an update, first test the condition using `SELECT`.

```sql
SELECT *
FROM employees
WHERE department_id IN (
    SELECT department_id
    FROM departments
    WHERE department_name = 'Operations'
);
```

Databases do not accept “I assumed it would update only one row” as a recovery strategy.

---

## Subquery with DELETE

```sql
DELETE FROM employees
WHERE department_id IN (
    SELECT department_id
    FROM departments
    WHERE department_name = 'Closed Department'
);
```

Always verify with a `SELECT` first.

```sql
SELECT *
FROM employees
WHERE department_id IN (
    SELECT department_id
    FROM departments
    WHERE department_name = 'Closed Department'
);
```

Use transactions when modifying important data.

---

## Subqueries compared with joins

Many subqueries can be rewritten as joins.

### Subquery version

```sql
SELECT
    employee_name
FROM employees
WHERE department_id IN (
    SELECT department_id
    FROM departments
    WHERE department_name = 'IT Support'
);
```

### Join version

```sql
SELECT
    e.employee_name
FROM employees AS e
INNER JOIN departments AS d
    ON e.department_id = d.department_id
WHERE d.department_name = 'IT Support';
```

Both may return the same result.

---

## When to use a subquery

A subquery is useful when:

* One calculated value is needed
* A query depends on an aggregate result
* Existence must be checked
* The inner logic is easier to understand separately
* A temporary summarized result is required
* The query needs values from another query

---

## When a join may be better

A join may be better when:

* Columns from multiple tables must be displayed
* Several related tables are involved
* Performance matters on large datasets
* The relationship is naturally table-to-table
* A correlated subquery would run repeatedly

There is no universal rule that joins are always faster. Modern database optimizers may transform queries internally.

Choose the clearest correct query, then test performance using real data.

---

## Practical application support examples

### Find users with locked accounts

```sql
SELECT
    username,
    account_status
FROM application_users
WHERE user_id IN (
    SELECT user_id
    FROM login_attempts
    WHERE status = 'Failed'
    GROUP BY user_id
    HAVING COUNT(*) >= 3
);
```

---

## Find users with no successful login

```sql
SELECT
    u.user_id,
    u.username
FROM application_users AS u
WHERE NOT EXISTS (
    SELECT 1
    FROM login_attempts AS l
    WHERE l.user_id = u.user_id
      AND l.status = 'Successful'
);
```

---

## Find tickets older than the average ticket age

```sql
SELECT
    ticket_id,
    created_at
FROM tickets
WHERE created_at < (
    SELECT FROM_UNIXTIME(
        AVG(UNIX_TIMESTAMP(created_at))
    )
    FROM tickets
);
```

In real reporting, a simpler business rule such as “older than seven days” is often clearer than comparing average timestamps.

---

## Find tickets with more comments than average

```sql
SELECT
    ticket_id,
    COUNT(*) AS comment_count
FROM ticket_comments
GROUP BY ticket_id
HAVING COUNT(*) > (
    SELECT AVG(comment_total)
    FROM (
        SELECT
            COUNT(*) AS comment_total
        FROM ticket_comments
        GROUP BY ticket_id
    ) AS ticket_comment_counts
);
```

---

## Find technicians with above-average closed ticket counts

```sql
SELECT
    assigned_to,
    COUNT(*) AS closed_ticket_count
FROM tickets
WHERE status = 'Closed'
GROUP BY assigned_to
HAVING COUNT(*) > (
    SELECT AVG(ticket_count)
    FROM (
        SELECT
            COUNT(*) AS ticket_count
        FROM tickets
        WHERE status = 'Closed'
        GROUP BY assigned_to
    ) AS technician_totals
);
```

---

## Find users with failed transactions

```sql
SELECT
    user_id,
    username
FROM application_users
WHERE user_id IN (
    SELECT user_id
    FROM transactions
    WHERE status = 'Failed'
);
```

---

## Find users without transactions

```sql
SELECT
    u.user_id,
    u.username
FROM application_users AS u
WHERE NOT EXISTS (
    SELECT 1
    FROM transactions AS t
    WHERE t.user_id = u.user_id
);
```

---

## Find transactions above the average amount

```sql
SELECT
    transaction_id,
    user_id,
    amount
FROM transactions
WHERE amount > (
    SELECT AVG(amount)
    FROM transactions
);
```

---

## Find the latest transaction

```sql
SELECT
    transaction_id,
    user_id,
    amount,
    created_at
FROM transactions
WHERE created_at = (
    SELECT MAX(created_at)
    FROM transactions
);
```

---

## Find the latest transaction for each user

```sql
SELECT
    t.transaction_id,
    t.user_id,
    t.amount,
    t.created_at
FROM transactions AS t
WHERE t.created_at = (
    SELECT MAX(t2.created_at)
    FROM transactions AS t2
    WHERE t2.user_id = t.user_id
);
```

This is a correlated subquery.

---

## Find duplicate email addresses

```sql
SELECT
    user_id,
    username,
    email
FROM application_users
WHERE email IN (
    SELECT email
    FROM application_users
    WHERE email IS NOT NULL
    GROUP BY email
    HAVING COUNT(*) > 1
)
ORDER BY email;
```

This first identifies repeated email addresses, then returns the complete user records.

---

## Find employees without valid departments

```sql
SELECT
    employee_id,
    employee_name,
    department_id
FROM employees AS e
WHERE e.department_id IS NOT NULL
  AND NOT EXISTS (
      SELECT 1
      FROM departments AS d
      WHERE d.department_id = e.department_id
  );
```

This can identify broken foreign-key relationships in systems where constraints are missing or disabled.

---

## Common mistakes

### Mistake 1: Using = with a multi-row subquery

Wrong:

```sql
SELECT employee_name
FROM employees
WHERE department_id = (
    SELECT department_id
    FROM departments
);
```

The subquery returns several department IDs.

Possible error:

```text
Subquery returns more than 1 row
```

Correct:

```sql
SELECT employee_name
FROM employees
WHERE department_id IN (
    SELECT department_id
    FROM departments
);
```

---

### Mistake 2: Forgetting parentheses

Wrong:

```sql
SELECT employee_name
FROM employees
WHERE salary > SELECT AVG(salary) FROM employees;
```

Correct:

```sql
SELECT employee_name
FROM employees
WHERE salary > (
    SELECT AVG(salary)
    FROM employees
);
```

---

### Mistake 3: Returning too many columns

Wrong:

```sql
SELECT employee_name
FROM employees
WHERE department_id IN (
    SELECT department_id, department_name
    FROM departments
);
```

The outer condition expects one column.

Correct:

```sql
SELECT employee_name
FROM employees
WHERE department_id IN (
    SELECT department_id
    FROM departments
);
```

---

### Mistake 4: Ignoring NULL with NOT IN

Risky:

```sql
SELECT employee_name
FROM employees
WHERE department_id NOT IN (
    SELECT department_id
    FROM departments
);
```

Safer:

```sql
SELECT employee_name
FROM employees AS e
WHERE NOT EXISTS (
    SELECT 1
    FROM departments AS d
    WHERE d.department_id = e.department_id
);
```

---

### Mistake 5: Confusing normal and correlated subqueries

Normal subquery:

```sql
SELECT employee_name
FROM employees
WHERE salary > (
    SELECT AVG(salary)
    FROM employees
);
```

The inner query is independent.

Correlated subquery:

```sql
SELECT e.employee_name
FROM employees AS e
WHERE e.salary > (
    SELECT AVG(e2.salary)
    FROM employees AS e2
    WHERE e2.department_id = e.department_id
);
```

The inner query depends on the current outer row.

---

### Mistake 6: Forgetting an alias for a derived table

Wrong:

```sql
SELECT *
FROM (
    SELECT department_id, AVG(salary)
    FROM employees
    GROUP BY department_id
);
```

Correct:

```sql
SELECT *
FROM (
    SELECT
        department_id,
        AVG(salary) AS average_salary
    FROM employees
    GROUP BY department_id
) AS department_summary;
```

---

### Mistake 7: Using a complicated subquery when a direct query is simpler

Unnecessary:

```sql
SELECT *
FROM (
    SELECT employee_name, salary
    FROM employees
) AS employee_data;
```

Simpler:

```sql
SELECT employee_name, salary
FROM employees;
```

Do not add subqueries merely to make the query look advanced. Complexity without purpose is not expertise. It is camouflage.

---

### Mistake 8: Using correlated subqueries on large tables without testing

A correlated subquery may run repeatedly.

Example:

```sql
SELECT e.employee_name
FROM employees AS e
WHERE e.salary > (
    SELECT AVG(e2.salary)
    FROM employees AS e2
    WHERE e2.department_id = e.department_id
);
```

On large datasets, consider:

* Indexes
* Joins
* Derived tables
* Common table expressions
* Execution-plan analysis

---

### Mistake 9: Updating data without testing the subquery

Dangerous:

```sql
UPDATE users
SET account_status = 'Inactive'
WHERE user_id IN (
    SELECT user_id
    FROM login_attempts
    WHERE status = 'Failed'
);
```

Test first:

```sql
SELECT *
FROM users
WHERE user_id IN (
    SELECT user_id
    FROM login_attempts
    WHERE status = 'Failed'
);
```

Then run the update only after confirming the result.

---

### Mistake 10: Assuming a subquery is always slower than a join

Performance depends on:

* Database optimizer
* Indexes
* Table size
* Data distribution
* Query structure
* Selected columns

Do not guess. Use the execution plan and real performance tests.

---

## Practice queries

### Query 1

Find employees earning above the company average.

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

### Query 2

Find every employee with the highest salary.

```sql
SELECT
    employee_name,
    salary
FROM employees
WHERE salary = (
    SELECT MAX(salary)
    FROM employees
);
```

### Query 3

Find employees working in IT Support.

```sql
SELECT
    employee_name
FROM employees
WHERE department_id = (
    SELECT department_id
    FROM departments
    WHERE department_name = 'IT Support'
);
```

### Query 4

Find employees in IT Support or Finance.

```sql
SELECT
    employee_name,
    department_id
FROM employees
WHERE department_id IN (
    SELECT department_id
    FROM departments
    WHERE department_name IN ('IT Support', 'Finance')
);
```

### Query 5

Find departments with employees.

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

### Query 6

Find departments without employees.

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

### Query 7

Find employees earning above their department average.

```sql
SELECT
    e.employee_name,
    e.department_id,
    e.salary
FROM employees AS e
WHERE e.salary > (
    SELECT AVG(e2.salary)
    FROM employees AS e2
    WHERE e2.department_id = e.department_id
);
```

### Query 8

Find the highest-paid employee in each department.

```sql
SELECT
    e.employee_name,
    e.department_id,
    e.salary
FROM employees AS e
WHERE e.salary = (
    SELECT MAX(e2.salary)
    FROM employees AS e2
    WHERE e2.department_id = e.department_id
);
```

### Query 9

Display each employee with the company average salary.

```sql
SELECT
    employee_name,
    salary,
    (
        SELECT ROUND(AVG(salary), 2)
        FROM employees
    ) AS company_average_salary
FROM employees;
```

### Query 10

Find users without transactions.

```sql
SELECT
    u.user_id,
    u.username
FROM application_users AS u
WHERE NOT EXISTS (
    SELECT 1
    FROM transactions AS t
    WHERE t.user_id = u.user_id
);
```

---

## Quick recall notes

* A subquery is a query inside another query.
* Subqueries are written inside parentheses.
* The inner query usually runs before the outer query.
* A scalar subquery returns one value.
* A single-row subquery works with operators such as `=`, `>`, and `<`.
* A multiple-row subquery commonly uses `IN`.
* `EXISTS` checks whether at least one matching row exists.
* `NOT EXISTS` checks whether no matching row exists.
* `NOT IN` can behave unexpectedly when `NULL` is returned.
* A correlated subquery depends on the current outer row.
* Correlated subqueries may run once for each outer row.
* A subquery can appear in `SELECT`, `FROM`, `WHERE`, `INSERT`, `UPDATE`, or `DELETE`.
* A subquery in `FROM` is called a derived table.
* MySQL requires a derived-table alias.
* `ANY` requires at least one comparison to be true.
* `ALL` requires every comparison to be true.
* Subqueries and joins can sometimes solve the same problem.
* Use the clearest correct approach and test performance.
* Test modifying queries with `SELECT` before using `UPDATE` or `DELETE`.

---

## Interview questions

### 1. What is a subquery?

A subquery is an SQL query written inside another SQL query.

### 2. Why are subqueries used?

They allow one query to use values or result sets produced by another query.

### 3. Where can subqueries be used?

Subqueries can be used in:

* `SELECT`
* `FROM`
* `WHERE`
* `HAVING`
* `INSERT`
* `UPDATE`
* `DELETE`

### 4. What is a scalar subquery?

A scalar subquery returns exactly one value.

### 5. What is a single-row subquery?

A single-row subquery returns one row or one value and can be used with operators such as `=`, `>`, or `<`.

### 6. What is a multiple-row subquery?

A multiple-row subquery returns several values and commonly works with `IN`, `ANY`, or `ALL`.

### 7. Why can = fail with a subquery?

The equality operator expects one value. It fails when the subquery returns multiple rows.

### 8. When should IN be used?

Use `IN` when the subquery may return multiple values.

### 9. What does EXISTS do?

`EXISTS` returns true when the subquery returns at least one row.

### 10. What does NOT EXISTS do?

`NOT EXISTS` returns true when the subquery returns no rows.

### 11. What is a correlated subquery?

A correlated subquery refers to columns from the outer query and runs for each outer row.

### 12. What is the difference between a normal and correlated subquery?

A normal subquery runs independently. A correlated subquery depends on the current row of the outer query.

### 13. Why can correlated subqueries be slow?

The inner query may execute repeatedly for every outer row.

### 14. What is a derived table?

A derived table is a temporary result created by a subquery in the `FROM` clause.

### 15. Does a derived table require an alias in MySQL?

Yes.

### 16. What is the difference between IN and EXISTS?

`IN` compares a value against a returned list. `EXISTS` checks whether matching rows exist.

### 17. Why is NOT EXISTS often safer than NOT IN?

`NOT EXISTS` handles possible `NULL` values more predictably.

### 18. How do you find employees earning above average?

```sql
SELECT employee_name, salary
FROM employees
WHERE salary > (
    SELECT AVG(salary)
    FROM employees
);
```

### 19. How do you find all employees tied for the highest salary?

```sql
SELECT employee_name, salary
FROM employees
WHERE salary = (
    SELECT MAX(salary)
    FROM employees
);
```

### 20. How do you find departments without employees?

```sql
SELECT d.department_name
FROM departments AS d
WHERE NOT EXISTS (
    SELECT 1
    FROM employees AS e
    WHERE e.department_id = d.department_id
);
```

### 21. What does ANY mean?

`ANY` requires the comparison to be true for at least one value returned by the subquery.

### 22. What does ALL mean?

`ALL` requires the comparison to be true for every value returned by the subquery.

### 23. Can a subquery be used inside SELECT?

Yes. It can calculate a value displayed beside each result row.

### 24. Can subqueries modify data?

Yes. They can be used with `INSERT`, `UPDATE`, and `DELETE`.

### 25. What safety step should be used before UPDATE or DELETE?

Run the same condition with `SELECT` first and confirm the affected rows.

### 26. Are joins always faster than subqueries?

No. Performance depends on the query, database optimizer, indexes, table sizes, and data distribution.

### 27. When is a join usually clearer?

A join is often clearer when columns from multiple related tables must appear in the result.

### 28. When is a subquery usually clearer?

A subquery is often clearer when the outer query depends on one calculated value, a list, or an existence check.

### 29. How are subqueries useful in application support?

They help find abnormal transactions, users without roles, repeated failures, records above average, missing relationships, and latest activity.

### 30. How do you find users with no related transactions?

```sql
SELECT
    u.user_id,
    u.username
FROM application_users AS u
WHERE NOT EXISTS (
    SELECT 1
    FROM transactions AS t
    WHERE t.user_id = u.user_id
);
```
