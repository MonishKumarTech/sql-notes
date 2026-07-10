# Joins

## Purpose

SQL joins combine related data from two or more tables.

Joins are important because relational databases usually store information in separate tables to reduce duplication and improve data consistency.

They are commonly used in:

* Application support
* Reporting
* Ticket analysis
* Employee and department reports
* Customer and order reports
* User account troubleshooting
* Transaction investigations

---

## Joins covered

* `INNER JOIN`
* `LEFT JOIN`
* `RIGHT JOIN`
* `FULL OUTER JOIN`
* `CROSS JOIN`
* Self join
* Multiple-table joins

---

## Example tables

The examples in this file use the following tables.

### Employees table

| employee_id | employee_name | department_id | manager_id | salary |
| ----------: | ------------- | ------------: | ---------: | -----: |
|         101 | Ravi          |             1 |        104 |  28000 |
|         102 | Priya         |             2 |        105 |  32000 |
|         103 | Arun          |             3 |        104 |  26000 |
|         104 | Meena         |             1 |       NULL |  40000 |
|         105 | Kumar         |             2 |       NULL |  42000 |
|         106 | Divya         |             1 |        104 |  35000 |
|         107 | Suresh        |          NULL |        105 |  29000 |

### Departments table

| department_id | department_name | location  |
| ------------: | --------------- | --------- |
|             1 | IT Support      | Chennai   |
|             2 | Finance         | Bengaluru |
|             3 | Operations      | Vellore   |
|             4 | Human Resources | Chennai   |

The relationship is:

```text
employees.department_id
        ↓
departments.department_id
```

`departments.department_id` is the primary key.

`employees.department_id` is the foreign key.

---

## Why joins are required

The `employees` table stores the department ID:

```text
department_id = 1
```

The `departments` table stores the corresponding department name:

```text
1 = IT Support
```

To display the employee name together with the department name, the two tables must be joined.

```sql
SELECT
    employees.employee_name,
    departments.department_name
FROM employees
INNER JOIN departments
    ON employees.department_id = departments.department_id;
```

Quick recall:

```text
A join connects related rows from different tables.
```

---

## Basic join structure

```sql
SELECT
    table1.column_name,
    table2.column_name
FROM table1
JOIN table2
    ON table1.related_column = table2.related_column;
```

The `ON` clause defines how the tables are related.

---

## Table aliases

Table aliases make join queries shorter and easier to read.

Without aliases:

```sql
SELECT
    employees.employee_name,
    departments.department_name
FROM employees
INNER JOIN departments
    ON employees.department_id = departments.department_id;
```

With aliases:

```sql
SELECT
    e.employee_name,
    d.department_name
FROM employees AS e
INNER JOIN departments AS d
    ON e.department_id = d.department_id;
```

Here:

```text
e = employees
d = departments
```

Quick recall:

```text
Aliases shorten table names inside a query.
```

---

## INNER JOIN

`INNER JOIN` returns only rows with matching values in both tables.

## Syntax

```sql
SELECT
    columns
FROM table1
INNER JOIN table2
    ON table1.column = table2.column;
```

## Example

```sql
SELECT
    e.employee_id,
    e.employee_name,
    d.department_name
FROM employees AS e
INNER JOIN departments AS d
    ON e.department_id = d.department_id;
```

Result:

| employee_id | employee_name | department_name |
| ----------: | ------------- | --------------- |
|         101 | Ravi          | IT Support      |
|         102 | Priya         | Finance         |
|         103 | Arun          | Operations      |
|         104 | Meena         | IT Support      |
|         105 | Kumar         | Finance         |
|         106 | Divya         | IT Support      |

Suresh is excluded because his `department_id` is `NULL`.

Human Resources is excluded because no employee belongs to that department.

Quick recall:

```text
INNER JOIN returns matching rows from both tables.
```

---

## JOIN and INNER JOIN

In most SQL systems, these are equivalent:

```sql
SELECT
    e.employee_name,
    d.department_name
FROM employees AS e
JOIN departments AS d
    ON e.department_id = d.department_id;
```

```sql
SELECT
    e.employee_name,
    d.department_name
FROM employees AS e
INNER JOIN departments AS d
    ON e.department_id = d.department_id;
```

Writing `INNER JOIN` makes the join type explicit.

---

## LEFT JOIN

`LEFT JOIN` returns:

* Every row from the left table
* Matching rows from the right table
* `NULL` when no matching right-side row exists

## Syntax

```sql
SELECT
    columns
FROM table1
LEFT JOIN table2
    ON table1.column = table2.column;
```

## Example

```sql
SELECT
    e.employee_id,
    e.employee_name,
    d.department_name
FROM employees AS e
LEFT JOIN departments AS d
    ON e.department_id = d.department_id;
```

Result:

| employee_id | employee_name | department_name |
| ----------: | ------------- | --------------- |
|         101 | Ravi          | IT Support      |
|         102 | Priya         | Finance         |
|         103 | Arun          | Operations      |
|         104 | Meena         | IT Support      |
|         105 | Kumar         | Finance         |
|         106 | Divya         | IT Support      |
|         107 | Suresh        | NULL            |

Suresh remains in the result because every row from the left table is preserved.

Quick recall:

```text
LEFT JOIN keeps every row from the left table.
```

---

## Find unmatched rows using LEFT JOIN

A `LEFT JOIN` can find records without a related match.

Find employees without a department:

```sql
SELECT
    e.employee_id,
    e.employee_name
FROM employees AS e
LEFT JOIN departments AS d
    ON e.department_id = d.department_id
WHERE d.department_id IS NULL;
```

Result:

| employee_id | employee_name |
| ----------: | ------------- |
|         107 | Suresh        |

This pattern is useful for identifying:

* Users without roles
* Tickets without assigned technicians
* Orders without customers
* Employees without departments
* Transactions without matching accounts

Quick recall:

```text
LEFT JOIN plus IS NULL finds missing relationships.
```

---

## Find departments without employees

Place `departments` on the left side:

```sql
SELECT
    d.department_id,
    d.department_name
FROM departments AS d
LEFT JOIN employees AS e
    ON d.department_id = e.department_id
WHERE e.employee_id IS NULL;
```

Result:

| department_id | department_name |
| ------------: | --------------- |
|             4 | Human Resources |

The left table determines which records are preserved.

---

## RIGHT JOIN

`RIGHT JOIN` returns:

* Every row from the right table
* Matching rows from the left table
* `NULL` when no matching left-side row exists

## Syntax

```sql
SELECT
    columns
FROM table1
RIGHT JOIN table2
    ON table1.column = table2.column;
```

## Example

```sql
SELECT
    e.employee_name,
    d.department_name
FROM employees AS e
RIGHT JOIN departments AS d
    ON e.department_id = d.department_id;
```

Result:

| employee_name | department_name |
| ------------- | --------------- |
| Ravi          | IT Support      |
| Meena         | IT Support      |
| Divya         | IT Support      |
| Priya         | Finance         |
| Kumar         | Finance         |
| Arun          | Operations      |
| NULL          | Human Resources |

Human Resources appears even though it has no employees.

Quick recall:

```text
RIGHT JOIN keeps every row from the right table.
```

---

## LEFT JOIN and RIGHT JOIN difference

| LEFT JOIN                                                 | RIGHT JOIN                              |
| --------------------------------------------------------- | --------------------------------------- |
| Keeps all rows from the left table                        | Keeps all rows from the right table     |
| Matching rows are added from the right                    | Matching rows are added from the left   |
| More commonly used                                        | Less commonly used                      |
| Can usually replace a right join by reversing table order | Can usually be rewritten as a left join |

These queries return equivalent results:

```sql
SELECT
    d.department_name,
    e.employee_name
FROM departments AS d
LEFT JOIN employees AS e
    ON d.department_id = e.department_id;
```

```sql
SELECT
    d.department_name,
    e.employee_name
FROM employees AS e
RIGHT JOIN departments AS d
    ON e.department_id = d.department_id;
```

`LEFT JOIN` is generally easier to read because the preserved table appears first.

---

## FULL OUTER JOIN

`FULL OUTER JOIN` returns:

* Matching rows
* Unmatched rows from the left table
* Unmatched rows from the right table

Conceptually:

```sql
SELECT
    columns
FROM table1
FULL OUTER JOIN table2
    ON table1.column = table2.column;
```

This would include:

* All employees
* All departments
* Employees without departments
* Departments without employees

However, MySQL does not directly support `FULL OUTER JOIN`.

---

## Simulating FULL OUTER JOIN in MySQL

A full outer join can be simulated using `LEFT JOIN`, `RIGHT JOIN`, and `UNION`.

```sql
SELECT
    e.employee_name,
    d.department_name
FROM employees AS e
LEFT JOIN departments AS d
    ON e.department_id = d.department_id

UNION

SELECT
    e.employee_name,
    d.department_name
FROM employees AS e
RIGHT JOIN departments AS d
    ON e.department_id = d.department_id;
```

`UNION` removes duplicate result rows.

A more controlled version is:

```sql
SELECT
    e.employee_name,
    d.department_name
FROM employees AS e
LEFT JOIN departments AS d
    ON e.department_id = d.department_id

UNION ALL

SELECT
    e.employee_name,
    d.department_name
FROM employees AS e
RIGHT JOIN departments AS d
    ON e.department_id = d.department_id
WHERE e.employee_id IS NULL;
```

The second query includes only unmatched right-side rows, preventing matched rows from being duplicated.

Quick recall:

```text
MySQL has no direct FULL OUTER JOIN.
It can be simulated using joins and UNION.
```

---

## CROSS JOIN

`CROSS JOIN` returns every possible combination of rows from two tables.

## Syntax

```sql
SELECT
    columns
FROM table1
CROSS JOIN table2;
```

Example tables:

### Shifts

| shift_name |
| ---------- |
| Morning    |
| Evening    |

### Locations

| location_name |
| ------------- |
| Chennai       |
| Bengaluru     |
| Vellore       |

Query:

```sql
SELECT
    s.shift_name,
    l.location_name
FROM shifts AS s
CROSS JOIN locations AS l;
```

Result:

| shift_name | location_name |
| ---------- | ------------- |
| Morning    | Chennai       |
| Morning    | Bengaluru     |
| Morning    | Vellore       |
| Evening    | Chennai       |
| Evening    | Bengaluru     |
| Evening    | Vellore       |

If one table has 2 rows and another has 3 rows:

```text
2 × 3 = 6 result rows
```

Quick recall:

```text
CROSS JOIN creates every possible row combination.
```

---

## CROSS JOIN risk

A cross join can generate a very large result.

If one table contains 10,000 rows and another contains 5,000 rows:

```text
10,000 × 5,000 = 50,000,000 rows
```

Use `CROSS JOIN` only when every combination is actually required.

Accidentally omitting a join condition can create similar chaos, because databases are obedient in precisely the least forgiving way.

---

## Self join

A self join joins a table to itself.

It is useful when rows in the same table are related.

In the `employees` table:

```text
manager_id refers to another employee_id.
```

## Example

```sql
SELECT
    e.employee_name AS employee,
    m.employee_name AS manager
FROM employees AS e
LEFT JOIN employees AS m
    ON e.manager_id = m.employee_id;
```

Result:

| employee | manager |
| -------- | ------- |
| Ravi     | Meena   |
| Priya    | Kumar   |
| Arun     | Meena   |
| Meena    | NULL    |
| Kumar    | NULL    |
| Divya    | Meena   |
| Suresh   | Kumar   |

The same table is used twice:

```text
e = employee
m = manager
```

Quick recall:

```text
A self join connects rows within the same table.
```

---

## Why aliases are required in self joins

Without aliases, the database cannot clearly distinguish between the two uses of the same table.

Clear:

```sql
FROM employees AS e
LEFT JOIN employees AS m
    ON e.manager_id = m.employee_id
```

Here:

* `e` represents the employee row
* `m` represents the manager row

---

## Joining more than two tables

A query can join several related tables.

Example tables:

```text
tickets
users
technicians
departments
```

Relationships:

```text
tickets.user_id → users.user_id
tickets.assigned_to → technicians.technician_id
tickets.department_id → departments.department_id
```

Query:

```sql
SELECT
    t.ticket_id,
    t.issue,
    u.username,
    tech.technician_name,
    d.department_name
FROM tickets AS t
INNER JOIN users AS u
    ON t.user_id = u.user_id
LEFT JOIN technicians AS tech
    ON t.assigned_to = tech.technician_id
INNER JOIN departments AS d
    ON t.department_id = d.department_id;
```

This query combines ticket, user, technician, and department information.

---

## Choosing join types in multi-table queries

Each join can use a different type.

```sql
SELECT
    t.ticket_id,
    u.username,
    tech.technician_name
FROM tickets AS t
INNER JOIN users AS u
    ON t.user_id = u.user_id
LEFT JOIN technicians AS tech
    ON t.assigned_to = tech.technician_id;
```

This means:

* A ticket must have a matching user
* A ticket may or may not have an assigned technician

If the technician is missing, the ticket still appears with a `NULL` technician name.

---

## Join conditions using multiple columns

Sometimes one column is not enough to define the relationship.

Example:

```sql
SELECT
    a.account_id,
    a.region_code,
    b.balance
FROM accounts AS a
INNER JOIN account_balances AS b
    ON a.account_id = b.account_id
   AND a.region_code = b.region_code;
```

Both conditions must match.

Quick recall:

```text
A join condition may contain multiple matching columns.
```

---

## INNER JOIN with WHERE

`ON` defines the relationship between tables.

`WHERE` filters the joined result.

```sql
SELECT
    e.employee_name,
    d.department_name,
    e.salary
FROM employees AS e
INNER JOIN departments AS d
    ON e.department_id = d.department_id
WHERE e.salary >= 30000;
```

This:

1. Joins employees and departments
2. Keeps only employees earning at least `30000`

---

## Filtering joined tables

Find employees in Chennai departments:

```sql
SELECT
    e.employee_name,
    d.department_name,
    d.location
FROM employees AS e
INNER JOIN departments AS d
    ON e.department_id = d.department_id
WHERE d.location = 'Chennai';
```

Columns from either table can be used in `WHERE`.

---

## ON and WHERE with INNER JOIN

For an inner join, these may produce equivalent results:

```sql
SELECT
    e.employee_name,
    d.department_name
FROM employees AS e
INNER JOIN departments AS d
    ON e.department_id = d.department_id
   AND d.location = 'Chennai';
```

```sql
SELECT
    e.employee_name,
    d.department_name
FROM employees AS e
INNER JOIN departments AS d
    ON e.department_id = d.department_id
WHERE d.location = 'Chennai';
```

However:

* `ON` should normally describe how tables relate
* `WHERE` should normally filter the result

This separation improves readability.

---

## ON and WHERE with LEFT JOIN

With a left join, placing a condition in `ON` or `WHERE` can change the result.

### Condition inside ON

```sql
SELECT
    e.employee_name,
    d.department_name
FROM employees AS e
LEFT JOIN departments AS d
    ON e.department_id = d.department_id
   AND d.location = 'Chennai';
```

This keeps every employee.

Employees without a Chennai department receive `NULL` for department details.

### Condition inside WHERE

```sql
SELECT
    e.employee_name,
    d.department_name
FROM employees AS e
LEFT JOIN departments AS d
    ON e.department_id = d.department_id
WHERE d.location = 'Chennai';
```

This removes rows where the department did not match.

The query behaves more like an inner join for that condition.

Quick recall:

```text
In a LEFT JOIN, right-table filters in WHERE may remove unmatched rows.
```

---

## Aggregates with joins

Joins can be combined with aggregate functions.

Count employees in each department:

```sql
SELECT
    d.department_name,
    COUNT(e.employee_id) AS employee_count
FROM departments AS d
LEFT JOIN employees AS e
    ON d.department_id = e.department_id
GROUP BY d.department_id, d.department_name;
```

Result:

| department_name | employee_count |
| --------------- | -------------: |
| IT Support      |              3 |
| Finance         |              2 |
| Operations      |              1 |
| Human Resources |              0 |

---

## COUNT(*) and LEFT JOIN warning

Consider:

```sql
SELECT
    d.department_name,
    COUNT(*) AS employee_count
FROM departments AS d
LEFT JOIN employees AS e
    ON d.department_id = e.department_id
GROUP BY d.department_id, d.department_name;
```

Human Resources may show a count of `1` because the left join still creates one result row for the department.

Better:

```sql
SELECT
    d.department_name,
    COUNT(e.employee_id) AS employee_count
FROM departments AS d
LEFT JOIN employees AS e
    ON d.department_id = e.department_id
GROUP BY d.department_id, d.department_name;
```

`COUNT(e.employee_id)` ignores the `NULL` value and correctly returns `0`.

Quick recall:

```text
With LEFT JOIN summaries, count a matching right-table key instead of COUNT(*).
```

---

## Department salary summary

```sql
SELECT
    d.department_name,
    COUNT(e.employee_id) AS employee_count,
    COALESCE(SUM(e.salary), 0) AS total_salary,
    COALESCE(ROUND(AVG(e.salary), 2), 0) AS average_salary
FROM departments AS d
LEFT JOIN employees AS e
    ON d.department_id = e.department_id
GROUP BY d.department_id, d.department_name
ORDER BY total_salary DESC;
```

Departments without employees remain visible.

`COALESCE()` converts missing aggregate results to zero.

---

## Duplicate rows after joins

A join can return multiple rows when one record matches several records in another table.

Example:

### Tickets table

| ticket_id | issue         |
| --------: | ------------- |
|      1001 | Login failure |

### Ticket comments table

| comment_id | ticket_id | comment        |
| ---------: | --------: | -------------- |
|          1 |      1001 | User contacted |
|          2 |      1001 | Password reset |
|          3 |      1001 | Issue resolved |

Query:

```sql
SELECT
    t.ticket_id,
    t.issue,
    c.comment
FROM tickets AS t
INNER JOIN ticket_comments AS c
    ON t.ticket_id = c.ticket_id;
```

Result:

| ticket_id | issue         | comment        |
| --------: | ------------- | -------------- |
|      1001 | Login failure | User contacted |
|      1001 | Login failure | Password reset |
|      1001 | Login failure | Issue resolved |

The ticket is repeated because it has three related comments.

This is not necessarily a duplicate error. It is the correct result of a one-to-many relationship.

---

## Relationship types

### One-to-one

One row in the first table matches one row in the second table.

Example:

```text
user ↔ user_profile
```

### One-to-many

One row in the first table matches several rows in the second table.

Example:

```text
department → many employees
ticket → many comments
customer → many orders
```

### Many-to-many

Many rows in one table relate to many rows in another table.

Example:

```text
users ↔ roles
students ↔ courses
```

Many-to-many relationships normally require a junction table.

---

## Joining through a junction table

Example tables:

```text
users
roles
user_roles
```

The `user_roles` table contains:

| user_id | role_id |
| ------: | ------: |
|       1 |       2 |
|       1 |       3 |
|       2 |       1 |

Query:

```sql
SELECT
    u.username,
    r.role_name
FROM users AS u
INNER JOIN user_roles AS ur
    ON u.user_id = ur.user_id
INNER JOIN roles AS r
    ON ur.role_id = r.role_id;
```

This retrieves each user together with assigned roles.

---

## Practical application support examples

### Find tickets with user details

```sql
SELECT
    t.ticket_id,
    t.issue,
    t.status,
    u.username,
    u.email
FROM tickets AS t
INNER JOIN application_users AS u
    ON t.user_id = u.user_id;
```

---

### Find unassigned tickets

```sql
SELECT
    t.ticket_id,
    t.issue,
    t.priority
FROM tickets AS t
LEFT JOIN technicians AS tech
    ON t.assigned_to = tech.technician_id
WHERE tech.technician_id IS NULL;
```

---

### Find users without assigned roles

```sql
SELECT
    u.user_id,
    u.username
FROM users AS u
LEFT JOIN user_roles AS ur
    ON u.user_id = ur.user_id
WHERE ur.role_id IS NULL;
```

---

### Find departments without active employees

```sql
SELECT
    d.department_id,
    d.department_name
FROM departments AS d
LEFT JOIN employees AS e
    ON d.department_id = e.department_id
   AND e.status = 'Active'
WHERE e.employee_id IS NULL;
```

The active-status condition belongs in `ON` so departments are preserved before unmatched rows are checked.

---

### Find orders with customer details

```sql
SELECT
    o.order_id,
    o.order_date,
    o.order_status,
    c.customer_name,
    c.phone_number
FROM orders AS o
INNER JOIN customers AS c
    ON o.customer_id = c.customer_id;
```

---

### Find failed transactions with account details

```sql
SELECT
    t.transaction_id,
    t.failure_reason,
    t.created_at,
    a.account_number,
    a.account_status
FROM transactions AS t
INNER JOIN accounts AS a
    ON t.account_id = a.account_id
WHERE t.status = 'Failed'
ORDER BY t.created_at DESC;
```

---

### Find users with no successful login

```sql
SELECT
    u.user_id,
    u.username
FROM users AS u
LEFT JOIN login_attempts AS l
    ON u.user_id = l.user_id
   AND l.status = 'Successful'
WHERE l.login_id IS NULL;
```

---

### Count tickets assigned to each technician

```sql
SELECT
    tech.technician_name,
    COUNT(t.ticket_id) AS assigned_ticket_count
FROM technicians AS tech
LEFT JOIN tickets AS t
    ON tech.technician_id = t.assigned_to
GROUP BY
    tech.technician_id,
    tech.technician_name
ORDER BY assigned_ticket_count DESC;
```

This includes technicians with zero assigned tickets.

---

### Calculate closed tickets by technician

```sql
SELECT
    tech.technician_name,
    COUNT(t.ticket_id) AS closed_ticket_count
FROM technicians AS tech
LEFT JOIN tickets AS t
    ON tech.technician_id = t.assigned_to
   AND t.status = 'Closed'
GROUP BY
    tech.technician_id,
    tech.technician_name;
```

---

## Common mistakes

### Mistake 1: Forgetting the ON condition

Dangerous:

```sql
SELECT
    e.employee_name,
    d.department_name
FROM employees AS e
JOIN departments AS d;
```

This may create every possible combination of employees and departments.

Correct:

```sql
SELECT
    e.employee_name,
    d.department_name
FROM employees AS e
INNER JOIN departments AS d
    ON e.department_id = d.department_id;
```

---

### Mistake 2: Joining unrelated columns

Wrong:

```sql
ON e.employee_id = d.department_id
```

Correct:

```sql
ON e.department_id = d.department_id
```

Join columns must represent the same relationship.

Matching data types alone does not make columns logically related. Two integers are not soulmates merely because both are integers.

---

### Mistake 3: Using ambiguous column names

Both tables may contain `department_id`.

Unclear:

```sql
SELECT department_id
FROM employees AS e
INNER JOIN departments AS d
    ON e.department_id = d.department_id;
```

Possible error:

```text
Column 'department_id' is ambiguous
```

Correct:

```sql
SELECT
    e.department_id,
    d.department_name
FROM employees AS e
INNER JOIN departments AS d
    ON e.department_id = d.department_id;
```

Use the table alias before shared column names.

---

### Mistake 4: Using INNER JOIN when unmatched rows are required

```sql
SELECT
    e.employee_name,
    d.department_name
FROM employees AS e
INNER JOIN departments AS d
    ON e.department_id = d.department_id;
```

This removes employees without departments.

Use:

```sql
SELECT
    e.employee_name,
    d.department_name
FROM employees AS e
LEFT JOIN departments AS d
    ON e.department_id = d.department_id;
```

Choose the join based on the required result, not whichever keyword remains in memory.

---

### Mistake 5: Filtering the right table in WHERE after LEFT JOIN

Problem:

```sql
SELECT
    e.employee_name,
    d.department_name
FROM employees AS e
LEFT JOIN departments AS d
    ON e.department_id = d.department_id
WHERE d.location = 'Chennai';
```

This removes unmatched employees.

To preserve every employee:

```sql
SELECT
    e.employee_name,
    d.department_name
FROM employees AS e
LEFT JOIN departments AS d
    ON e.department_id = d.department_id
   AND d.location = 'Chennai';
```

---

### Mistake 6: Using COUNT(*) after LEFT JOIN

Potentially incorrect:

```sql
SELECT
    d.department_name,
    COUNT(*) AS employee_count
FROM departments AS d
LEFT JOIN employees AS e
    ON d.department_id = e.department_id
GROUP BY d.department_name;
```

Better:

```sql
SELECT
    d.department_name,
    COUNT(e.employee_id) AS employee_count
FROM departments AS d
LEFT JOIN employees AS e
    ON d.department_id = e.department_id
GROUP BY d.department_id, d.department_name;
```

---

### Mistake 7: Assuming repeated rows are always duplicates

A ticket can have several comments.

Joining the ticket to comments repeats the ticket once for each matching comment.

That is expected in a one-to-many relationship.

Understand the relationship before applying `DISTINCT` as a decorative bandage.

---

### Mistake 8: Using DISTINCT to hide a bad join

Weak:

```sql
SELECT DISTINCT
    e.employee_name,
    d.department_name
FROM employees AS e
JOIN departments AS d;
```

`DISTINCT` may hide duplicate rows but does not repair the missing join condition.

Correct the relationship:

```sql
SELECT
    e.employee_name,
    d.department_name
FROM employees AS e
INNER JOIN departments AS d
    ON e.department_id = d.department_id;
```

---

### Mistake 9: Joining too many tables without checking row counts

Each one-to-many join can multiply the number of result rows.

Before building a large query:

1. Join two tables
2. Check the result
3. Add the next table
4. Check again
5. Add filters and aggregation

This makes incorrect row multiplication easier to detect.

---

### Mistake 10: Using RIGHT JOIN when LEFT JOIN is clearer

This works:

```sql
SELECT
    d.department_name,
    e.employee_name
FROM employees AS e
RIGHT JOIN departments AS d
    ON e.department_id = d.department_id;
```

This is usually clearer:

```sql
SELECT
    d.department_name,
    e.employee_name
FROM departments AS d
LEFT JOIN employees AS e
    ON d.department_id = e.department_id;
```

Use a left join with the preserved table first when possible.

---

## Practice queries

### Query 1

Display employees with their department names.

```sql
SELECT
    e.employee_name,
    d.department_name
FROM employees AS e
INNER JOIN departments AS d
    ON e.department_id = d.department_id;
```

### Query 2

Display every employee, including employees without departments.

```sql
SELECT
    e.employee_name,
    d.department_name
FROM employees AS e
LEFT JOIN departments AS d
    ON e.department_id = d.department_id;
```

### Query 3

Display every department, including departments without employees.

```sql
SELECT
    d.department_name,
    e.employee_name
FROM departments AS d
LEFT JOIN employees AS e
    ON d.department_id = e.department_id;
```

### Query 4

Find employees without departments.

```sql
SELECT
    e.employee_id,
    e.employee_name
FROM employees AS e
LEFT JOIN departments AS d
    ON e.department_id = d.department_id
WHERE d.department_id IS NULL;
```

### Query 5

Find departments without employees.

```sql
SELECT
    d.department_id,
    d.department_name
FROM departments AS d
LEFT JOIN employees AS e
    ON d.department_id = e.department_id
WHERE e.employee_id IS NULL;
```

### Query 6

Display employees with their manager names.

```sql
SELECT
    e.employee_name AS employee,
    m.employee_name AS manager
FROM employees AS e
LEFT JOIN employees AS m
    ON e.manager_id = m.employee_id;
```

### Query 7

Count employees in every department.

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

### Query 8

Calculate total salary by department.

```sql
SELECT
    d.department_name,
    COALESCE(SUM(e.salary), 0) AS total_salary
FROM departments AS d
LEFT JOIN employees AS e
    ON d.department_id = e.department_id
GROUP BY
    d.department_id,
    d.department_name;
```

### Query 9

Find employees working in Chennai departments.

```sql
SELECT
    e.employee_name,
    d.department_name,
    d.location
FROM employees AS e
INNER JOIN departments AS d
    ON e.department_id = d.department_id
WHERE d.location = 'Chennai';
```

### Query 10

Find unassigned open tickets.

```sql
SELECT
    t.ticket_id,
    t.issue,
    t.priority
FROM tickets AS t
LEFT JOIN technicians AS tech
    ON t.assigned_to = tech.technician_id
WHERE tech.technician_id IS NULL
  AND t.status = 'Open';
```

---

## Quick recall notes

* Joins combine related data from multiple tables.
* `ON` defines the relationship between tables.
* `INNER JOIN` returns only matching rows.
* `LEFT JOIN` keeps every row from the left table.
* `RIGHT JOIN` keeps every row from the right table.
* MySQL does not directly support `FULL OUTER JOIN`.
* `FULL OUTER JOIN` can be simulated using joins and `UNION`.
* `CROSS JOIN` returns every possible row combination.
* A self join connects rows within the same table.
* Table aliases make join queries shorter and clearer.
* `LEFT JOIN` with `IS NULL` finds missing relationships.
* The order of tables matters in outer joins.
* Filters in `ON` and `WHERE` can behave differently with outer joins.
* Use `COUNT(right_table.primary_key)` when counting matches after a left join.
* Repeated rows may be valid in one-to-many relationships.
* Do not use `DISTINCT` to hide an incorrect join.
* Verify each join condition before adding more tables.

---

## Interview questions

### 1. What is a join?

A join combines related rows from two or more database tables.

### 2. Why are joins required?

Relational databases store related information in separate tables. Joins combine that information when retrieving data.

### 3. What does INNER JOIN return?

`INNER JOIN` returns only rows with matching values in both tables.

### 4. What does LEFT JOIN return?

`LEFT JOIN` returns every row from the left table and matching rows from the right table.

### 5. What happens when a LEFT JOIN finds no match?

Columns from the right table contain `NULL`.

### 6. What does RIGHT JOIN return?

`RIGHT JOIN` returns every row from the right table and matching rows from the left table.

### 7. What is the difference between INNER JOIN and LEFT JOIN?

`INNER JOIN` removes unmatched rows. `LEFT JOIN` preserves all rows from the left table.

### 8. How do you find unmatched rows?

Use a `LEFT JOIN` and check the right-side key with `IS NULL`.

```sql
SELECT
    e.employee_name
FROM employees AS e
LEFT JOIN departments AS d
    ON e.department_id = d.department_id
WHERE d.department_id IS NULL;
```

### 9. What does the ON clause do?

The `ON` clause defines how rows from the joined tables are related.

### 10. What is a table alias?

A table alias is a temporary short name used for a table inside a query.

### 11. Why are aliases important in joins?

They improve readability and help distinguish columns with the same name.

### 12. What is an ambiguous column error?

It occurs when multiple joined tables contain a column with the same name and the query does not specify which table to use.

### 13. How do you prevent ambiguous column errors?

Prefix the column with its table name or alias.

```sql
e.department_id
```

### 14. What is a self join?

A self join joins a table to itself.

### 15. When is a self join useful?

It is useful for relationships within the same table, such as employees and their managers.

### 16. What is a CROSS JOIN?

A `CROSS JOIN` returns every possible combination of rows from two tables.

### 17. Does MySQL support FULL OUTER JOIN directly?

No. It must be simulated using joins and `UNION`.

### 18. Can more than two tables be joined?

Yes. Each additional table is joined using another join clause and relationship condition.

### 19. What is the difference between ON and WHERE?

`ON` defines the join relationship. `WHERE` filters the resulting rows.

### 20. Why can a WHERE condition change a LEFT JOIN result?

A condition on the right table inside `WHERE` can remove rows containing `NULL`, eliminating unmatched left-side rows.

### 21. Why can joins produce repeated rows?

One row may match several rows in another table because of a one-to-many relationship.

### 22. What is a one-to-many relationship?

One record in one table is related to several records in another table.

### 23. What is a many-to-many relationship?

Several rows in one table relate to several rows in another table, normally through a junction table.

### 24. Why should COUNT(*) be used carefully with LEFT JOIN?

The left join preserves a result row even without a match, so `COUNT(*)` may return `1` instead of `0`.

### 25. How do you correctly count matching right-side rows?

Count a non-`NULL` right-table key.

```sql
COUNT(e.employee_id)
```

### 26. What happens when a join condition is missing?

The query may return every possible combination of rows, producing a Cartesian product.

### 27. Should DISTINCT be used to fix duplicate rows from a bad join?

No. The join condition and table relationships should be corrected.

### 28. Which is generally clearer, LEFT JOIN or RIGHT JOIN?

`LEFT JOIN` is generally clearer because the preserved table appears first.

### 29. How are joins useful in application support?

They connect users, accounts, tickets, technicians, transactions, departments, and error records for investigation and reporting.

### 30. How do you find users without roles?

```sql
SELECT
    u.user_id,
    u.username
FROM users AS u
LEFT JOIN user_roles AS ur
    ON u.user_id = ur.user_id
WHERE ur.role_id IS NULL;
```
