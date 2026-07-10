# WHERE filtering

## Purpose

The `WHERE` clause filters rows based on one or more conditions.

It is commonly used in application support, reporting, troubleshooting, and data verification tasks to retrieve only the required records.

---

## Basic syntax

```sql
SELECT column_name
FROM table_name
WHERE condition;
```

Example:

```sql
SELECT employee_name, department
FROM employees
WHERE status = 'Active';
```

This query returns only employees whose status is `Active`.

Quick recall:

```text
SELECT chooses columns.
FROM chooses the table.
WHERE chooses rows.
```

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

---

## Comparison operators

Comparison operators are used to compare values.

| Operator | Meaning                  |
| -------- | ------------------------ |
| `=`      | Equal to                 |
| `!=`     | Not equal to             |
| `<>`     | Not equal to             |
| `>`      | Greater than             |
| `<`      | Less than                |
| `>=`     | Greater than or equal to |
| `<=`     | Less than or equal to    |

---

## Equal to

Use `=` to find rows with an exact value.

```sql
SELECT employee_name, department
FROM employees
WHERE department = 'IT Support';
```

Result:

| employee_name | department |
| ------------- | ---------- |
| Ravi          | IT Support |
| Meena         | IT Support |
| Divya         | IT Support |

Quick recall:

```text
= checks whether two values are equal.
```

---

## Not equal to

Use `!=` or `<>` to exclude a value.

```sql
SELECT employee_name, status
FROM employees
WHERE status != 'Active';
```

Equivalent query:

```sql
SELECT employee_name, status
FROM employees
WHERE status <> 'Active';
```

Result:

| employee_name | status   |
| ------------- | -------- |
| Arun          | Inactive |
| Kumar         | Inactive |

Both operators usually mean the same thing.

For standard SQL, `<>` is the traditional not-equal operator.

---

## Greater than

Use `>` to find values above a number.

```sql
SELECT employee_name, salary
FROM employees
WHERE salary > 30000;
```

Result:

| employee_name | salary |
| ------------- | -----: |
| Priya         |  32000 |
| Divya         |  35000 |

---

## Less than

Use `<` to find values below a number.

```sql
SELECT employee_name, salary
FROM employees
WHERE salary < 28000;
```

Result:

| employee_name | salary |
| ------------- | -----: |
| Arun          |  26000 |
| Kumar         |  25000 |

---

## Greater than or equal to

```sql
SELECT employee_name, salary
FROM employees
WHERE salary >= 30000;
```

This includes salaries equal to or greater than `30000`.

---

## Less than or equal to

```sql
SELECT employee_name, salary
FROM employees
WHERE salary <= 28000;
```

This includes salaries equal to or below `28000`.

---

## Filtering text values

Text values must be written inside single quotation marks.

Correct:

```sql
SELECT employee_name
FROM employees
WHERE city = 'Chennai';
```

Wrong:

```sql
SELECT employee_name
FROM employees
WHERE city = Chennai;
```

Without quotation marks, the database may treat `Chennai` as a column name.

---

## Filtering numeric values

Numbers are normally written without quotation marks.

```sql
SELECT employee_name
FROM employees
WHERE employee_id = 101;
```

Recommended:

```sql
WHERE salary > 25000
```

Avoid:

```sql
WHERE salary > '25000'
```

Some databases may automatically convert the text, but relying on conversion is weak practice.

---

## AND operator

`AND` requires every connected condition to be true.

```sql
SELECT employee_name, department, status
FROM employees
WHERE department = 'IT Support'
  AND status = 'Active';
```

Result:

| employee_name | department | status |
| ------------- | ---------- | ------ |
| Ravi          | IT Support | Active |
| Meena         | IT Support | Active |
| Divya         | IT Support | Active |

Quick recall:

```text
AND means all conditions must be true.
```

---

## Multiple AND conditions

```sql
SELECT employee_name, salary, city
FROM employees
WHERE department = 'IT Support'
  AND status = 'Active'
  AND salary >= 30000;
```

Result:

| employee_name | salary | city    |
| ------------- | -----: | ------- |
| Meena         |  30000 | Vellore |
| Divya         |  35000 | Chennai |

---

## OR operator

`OR` requires at least one connected condition to be true.

```sql
SELECT employee_name, city
FROM employees
WHERE city = 'Chennai'
   OR city = 'Vellore';
```

This returns employees from either Chennai or Vellore.

Quick recall:

```text
OR means any one condition can be true.
```

---

## Combining AND and OR

```sql
SELECT employee_name, department, city
FROM employees
WHERE department = 'IT Support'
  AND (city = 'Chennai' OR city = 'Vellore');
```

Parentheses make the intended logic clear.

Without parentheses, SQL operator precedence may produce a different result.

---

## Operator precedence

SQL normally evaluates conditions in this order:

1. Parentheses
2. `NOT`
3. `AND`
4. `OR`

Example:

```sql
WHERE status = 'Active'
  AND department = 'IT Support'
   OR city = 'Bengaluru'
```

This is interpreted approximately as:

```sql
WHERE (status = 'Active' AND department = 'IT Support')
   OR city = 'Bengaluru'
```

For safer and clearer logic, use parentheses:

```sql
WHERE status = 'Active'
  AND (department = 'IT Support' OR city = 'Bengaluru')
```

Do not make the database guess what you meant. Humans already do enough of that.

---

## NOT operator

`NOT` reverses a condition.

```sql
SELECT employee_name, status
FROM employees
WHERE NOT status = 'Active';
```

A clearer version is often:

```sql
SELECT employee_name, status
FROM employees
WHERE status <> 'Active';
```

`NOT` is especially useful with `IN`, `BETWEEN`, `LIKE`, and `NULL`.

---

## IN operator

`IN` checks whether a value matches any value in a list.

```sql
SELECT employee_name, city
FROM employees
WHERE city IN ('Chennai', 'Vellore');
```

This is shorter than:

```sql
SELECT employee_name, city
FROM employees
WHERE city = 'Chennai'
   OR city = 'Vellore';
```

Quick recall:

```text
IN checks whether a value exists in a list.
```

---

## NOT IN

`NOT IN` excludes values in a list.

```sql
SELECT employee_name, city
FROM employees
WHERE city NOT IN ('Chennai', 'Vellore');
```

This returns employees whose city is neither Chennai nor Vellore.

---

## BETWEEN operator

`BETWEEN` filters values inside an inclusive range.

```sql
SELECT employee_name, salary
FROM employees
WHERE salary BETWEEN 26000 AND 32000;
```

This includes both `26000` and `32000`.

Equivalent logic:

```sql
SELECT employee_name, salary
FROM employees
WHERE salary >= 26000
  AND salary <= 32000;
```

Quick recall:

```text
BETWEEN includes both boundary values.
```

---

## NOT BETWEEN

```sql
SELECT employee_name, salary
FROM employees
WHERE salary NOT BETWEEN 26000 AND 32000;
```

This returns salaries below `26000` or above `32000`.

---

## Filtering dates

Dates should normally use the `YYYY-MM-DD` format.

Example table:

| ticket_id | created_date | status      |
| --------: | ------------ | ----------- |
|      1001 | 2026-07-01   | Open        |
|      1002 | 2026-07-05   | Closed      |
|      1003 | 2026-07-10   | In progress |

Exact date:

```sql
SELECT ticket_id, status
FROM tickets
WHERE created_date = '2026-07-10';
```

Date range:

```sql
SELECT ticket_id, created_date
FROM tickets
WHERE created_date BETWEEN '2026-07-01' AND '2026-07-10';
```

For columns containing both date and time, range filtering needs extra care because the final date may not include the entire day.

Example:

```sql
SELECT ticket_id, created_at
FROM tickets
WHERE created_at >= '2026-07-01'
  AND created_at < '2026-07-11';
```

This includes all records from July 1 through July 10.

---

## LIKE operator

`LIKE` is used for pattern matching in text.

It commonly uses two wildcard characters:

| Wildcard | Meaning                 |
| -------- | ----------------------- |
| `%`      | Zero or more characters |
| `_`      | Exactly one character   |

---

## Starts with

Find names starting with `R`:

```sql
SELECT employee_name
FROM employees
WHERE employee_name LIKE 'R%';
```

Possible result:

| employee_name |
| ------------- |
| Ravi          |

Quick recall:

```text
'R%' means starts with R.
```

---

## Ends with

Find names ending with `a`:

```sql
SELECT employee_name
FROM employees
WHERE employee_name LIKE '%a';
```

Possible result:

| employee_name |
| ------------- |
| Meena         |
| Divya         |

Quick recall:

```text
'%a' means ends with a.
```

---

## Contains

Find departments containing the word `Support`:

```sql
SELECT employee_name, department
FROM employees
WHERE department LIKE '%Support%';
```

This can match:

* IT Support
* Technical Support
* Application Support

Quick recall:

```text
'%Support%' means contains Support.
```

---

## Single-character wildcard

Find four-letter names beginning with `R`:

```sql
SELECT employee_name
FROM employees
WHERE employee_name LIKE 'R___';
```

`R___` means:

* Starts with `R`
* Followed by exactly three characters

---

## NOT LIKE

```sql
SELECT employee_name
FROM employees
WHERE employee_name NOT LIKE 'A%';
```

This excludes names starting with `A`.

---

## Case sensitivity with LIKE

Case sensitivity depends on:

* Database system
* Column collation
* Configuration

In many MySQL configurations, this may match both `Ravi` and `ravi`:

```sql
WHERE employee_name LIKE 'r%'
```

Do not assume case behaviour without checking the database configuration.

---

## IS NULL

`NULL` means missing, unknown, or unavailable data.

Use `IS NULL` to find missing values.

Example table:

| employee_id | employee_name | phone_number |
| ----------: | ------------- | ------------ |
|         101 | Ravi          | 9876543210   |
|         102 | Priya         | NULL         |
|         103 | Arun          | 9123456780   |

Query:

```sql
SELECT employee_name
FROM employees
WHERE phone_number IS NULL;
```

Result:

| employee_name |
| ------------- |
| Priya         |

Quick recall:

```text
Use IS NULL, not = NULL.
```

---

## IS NOT NULL

Use `IS NOT NULL` to find records with an available value.

```sql
SELECT employee_name, phone_number
FROM employees
WHERE phone_number IS NOT NULL;
```

---

## Why = NULL is wrong

Wrong:

```sql
SELECT employee_name
FROM employees
WHERE phone_number = NULL;
```

Correct:

```sql
SELECT employee_name
FROM employees
WHERE phone_number IS NULL;
```

`NULL` is not a normal value. It represents an unknown value, so it cannot be compared using `=`.

---

## Boolean filtering

Some databases support Boolean values.

Example:

```sql
SELECT username
FROM application_users
WHERE is_active = TRUE;
```

In MySQL, Boolean values are commonly stored internally as `1` and `0`.

Possible equivalent:

```sql
SELECT username
FROM application_users
WHERE is_active = 1;
```

The exact implementation depends on the table design and database system.

---

## Filtering using aliases

A column alias normally cannot be used in the `WHERE` clause of the same query.

Wrong in most systems:

```sql
SELECT salary * 12 AS annual_salary
FROM employees
WHERE annual_salary > 350000;
```

Correct:

```sql
SELECT salary * 12 AS annual_salary
FROM employees
WHERE salary * 12 > 350000;
```

Reason:

`WHERE` is evaluated before the `SELECT` alias is created.

---

## WHERE with calculated conditions

```sql
SELECT employee_name, salary
FROM employees
WHERE salary * 12 > 350000;
```

This filters employees whose calculated annual salary is above `350000`.

---

## WHERE with DISTINCT

```sql
SELECT DISTINCT department
FROM employees
WHERE status = 'Active';
```

The database first filters active employees, then removes duplicate departments from the result.

---

## Practical application support examples

### Find an application user by username

```sql
SELECT user_id, username, account_status
FROM application_users
WHERE username = 'hari.prasad';
```

### Find locked accounts

```sql
SELECT user_id, username, last_login
FROM application_users
WHERE account_status = 'Locked';
```

### Find active users without an email address

```sql
SELECT user_id, username
FROM application_users
WHERE account_status = 'Active'
  AND email IS NULL;
```

### Find high-priority open tickets

```sql
SELECT ticket_id, issue, assigned_to
FROM tickets
WHERE priority = 'High'
  AND status = 'Open';
```

### Find unresolved tickets from selected departments

```sql
SELECT ticket_id, department, status
FROM tickets
WHERE department IN ('IT Support', 'Application Support')
  AND status <> 'Closed';
```

### Find tickets created within a date range

```sql
SELECT ticket_id, created_date, status
FROM tickets
WHERE created_date BETWEEN '2026-07-01' AND '2026-07-10';
```

### Find users whose names contain a keyword

```sql
SELECT user_id, username
FROM application_users
WHERE username LIKE '%hari%';
```

### Find records with missing phone numbers

```sql
SELECT employee_id, employee_name
FROM employees
WHERE phone_number IS NULL;
```

---

## Common mistakes

### Mistake 1: Using double equals

Wrong:

```sql
SELECT *
FROM employees
WHERE status == 'Active';
```

Correct:

```sql
SELECT *
FROM employees
WHERE status = 'Active';
```

SQL uses one equals sign.

---

### Mistake 2: Missing quotation marks around text

Wrong:

```sql
WHERE city = Chennai
```

Correct:

```sql
WHERE city = 'Chennai'
```

Text values require single quotation marks.

---

### Mistake 3: Quoting numbers unnecessarily

Weak:

```sql
WHERE employee_id = '101'
```

Better:

```sql
WHERE employee_id = 101
```

Use values that match the column's data type.

---

### Mistake 4: Using = NULL

Wrong:

```sql
WHERE phone_number = NULL
```

Correct:

```sql
WHERE phone_number IS NULL
```

---

### Mistake 5: Confusing AND with OR

This requires both conditions:

```sql
WHERE city = 'Chennai'
  AND city = 'Vellore'
```

A single city value normally cannot be both Chennai and Vellore.

Correct:

```sql
WHERE city = 'Chennai'
   OR city = 'Vellore'
```

Better:

```sql
WHERE city IN ('Chennai', 'Vellore')
```

---

### Mistake 6: Ignoring operator precedence

Unclear:

```sql
WHERE department = 'IT Support'
   OR department = 'Technical Support'
  AND status = 'Active'
```

Clear:

```sql
WHERE (department = 'IT Support'
    OR department = 'Technical Support')
  AND status = 'Active'
```

Use parentheses when mixing `AND` and `OR`.

---

### Mistake 7: Forgetting that BETWEEN is inclusive

```sql
WHERE salary BETWEEN 25000 AND 30000
```

This includes both `25000` and `30000`.

---

### Mistake 8: Using LIKE when exact matching is required

Weak:

```sql
WHERE status LIKE 'Active'
```

Better:

```sql
WHERE status = 'Active'
```

Use `LIKE` for patterns. Use `=` for exact values.

---

### Mistake 9: Starting a wildcard search with %

```sql
WHERE username LIKE '%hari'
```

Leading wildcard searches may be slower on large tables because normal indexes may not be used efficiently.

Use them only when necessary.

---

### Mistake 10: Filtering a date-time column incorrectly

Potentially incomplete:

```sql
WHERE created_at BETWEEN '2026-07-01' AND '2026-07-10'
```

This may exclude records later on July 10 if the column includes time.

Safer:

```sql
WHERE created_at >= '2026-07-01'
  AND created_at < '2026-07-11'
```

---

## Practice queries

### Query 1

Find active employees.

```sql
SELECT employee_id, employee_name
FROM employees
WHERE status = 'Active';
```

### Query 2

Find employees earning more than `30000`.

```sql
SELECT employee_name, salary
FROM employees
WHERE salary > 30000;
```

### Query 3

Find active IT Support employees.

```sql
SELECT employee_name, department, status
FROM employees
WHERE department = 'IT Support'
  AND status = 'Active';
```

### Query 4

Find employees from Chennai or Vellore.

```sql
SELECT employee_name, city
FROM employees
WHERE city IN ('Chennai', 'Vellore');
```

### Query 5

Find employees earning between `26000` and `32000`.

```sql
SELECT employee_name, salary
FROM employees
WHERE salary BETWEEN 26000 AND 32000;
```

### Query 6

Find employee names starting with `M`.

```sql
SELECT employee_name
FROM employees
WHERE employee_name LIKE 'M%';
```

### Query 7

Find departments containing `Support`.

```sql
SELECT DISTINCT department
FROM employees
WHERE department LIKE '%Support%';
```

### Query 8

Find employees without phone numbers.

```sql
SELECT employee_name
FROM employees
WHERE phone_number IS NULL;
```

### Query 9

Find employees who are not inactive.

```sql
SELECT employee_name, status
FROM employees
WHERE status <> 'Inactive';
```

### Query 10

Find active employees from Chennai with salaries above `30000`.

```sql
SELECT employee_name, salary, city
FROM employees
WHERE status = 'Active'
  AND city = 'Chennai'
  AND salary > 30000;
```

---

## Quick recall notes

* `WHERE` filters rows.
* `=` checks exact equality.
* `!=` and `<>` mean not equal.
* `>` means greater than.
* `<` means less than.
* `>=` and `<=` include the boundary value.
* Text values use single quotation marks.
* Numeric values normally do not need quotation marks.
* `AND` requires every condition to be true.
* `OR` requires at least one condition to be true.
* `NOT` reverses a condition.
* `IN` checks a list of values.
* `BETWEEN` checks an inclusive range.
* `LIKE` performs pattern matching.
* `%` matches zero or more characters.
* `_` matches exactly one character.
* `IS NULL` finds missing values.
* `IS NOT NULL` finds available values.
* Use parentheses when mixing `AND` and `OR`.
* Use `=` for exact values and `LIKE` for patterns.
* Date-time ranges should be checked carefully.

---

## Interview questions

### 1. What does the WHERE clause do?

The `WHERE` clause filters rows based on a condition.

### 2. What is the difference between SELECT and WHERE?

`SELECT` chooses the columns to return. `WHERE` chooses the rows to return.

### 3. Which operator checks equality?

```sql
=
```

### 4. Which operators mean not equal?

```sql
!=
<>
```

### 5. What does AND do?

`AND` returns a row only when all connected conditions are true.

### 6. What does OR do?

`OR` returns a row when at least one connected condition is true.

### 7. What is the purpose of parentheses in a WHERE clause?

Parentheses control condition evaluation and make combined `AND` and `OR` logic clear.

### 8. What does IN do?

`IN` checks whether a value matches any value in a provided list.

### 9. What does BETWEEN do?

`BETWEEN` checks whether a value is inside an inclusive range.

### 10. Does BETWEEN include the boundary values?

Yes. Both the starting and ending values are included.

### 11. What does LIKE do?

`LIKE` performs pattern matching on text values.

### 12. What does the % wildcard mean?

`%` matches zero or more characters.

### 13. What does the _ wildcard mean?

`_` matches exactly one character.

### 14. How do you find values beginning with A?

```sql
WHERE column_name LIKE 'A%'
```

### 15. How do you find values containing Support?

```sql
WHERE column_name LIKE '%Support%'
```

### 16. How do you check for NULL?

```sql
WHERE column_name IS NULL
```

### 17. Why is = NULL incorrect?

`NULL` represents an unknown value and cannot be compared using the normal equality operator.

### 18. What is the difference between IS NULL and IS NOT NULL?

`IS NULL` finds missing values. `IS NOT NULL` finds non-missing values.

### 19. Which is evaluated first, AND or OR?

`AND` is normally evaluated before `OR`.

### 20. Why should parentheses be used with AND and OR?

They prevent ambiguous logic and make the intended condition clear.

### 21. What is the difference between = and LIKE?

`=` checks an exact match. `LIKE` checks a text pattern.

### 22. How can WHERE help in application support?

It can locate user accounts, filter failed transactions, find open tickets, identify missing values, verify record status, and troubleshoot application data.

### 23. Can a SELECT alias normally be used in WHERE?

No. The `WHERE` clause is generally evaluated before the `SELECT` alias is created.

### 24. How do you filter a complete date range on a date-time column?

Use an inclusive starting value and an exclusive next-day ending value.

```sql
WHERE created_at >= '2026-07-01'
  AND created_at < '2026-07-11'
```
