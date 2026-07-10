# CRUD operations

## Purpose

CRUD represents the four basic operations performed on database records:

* Create
* Read
* Update
* Delete

These operations are used in nearly every database-backed application.

They are important for:

* Application support
* User account management
* Ticket management
* Data correction
* Reporting
* Transaction investigation
* Database troubleshooting

---

## CRUD commands

| CRUD operation | Purpose                 | SQL command |
| -------------- | ----------------------- | ----------- |
| Create         | Add new records         | `INSERT`    |
| Read           | Retrieve records        | `SELECT`    |
| Update         | Modify existing records | `UPDATE`    |
| Delete         | Remove records          | `DELETE`    |

Quick recall:

```text
Create = INSERT
Read = SELECT
Update = UPDATE
Delete = DELETE
```

---

## Example table

The examples in this file use the following `employees` table:

| employee_id | employee_name | department | salary | status   |
| ----------: | ------------- | ---------- | -----: | -------- |
|         101 | Ravi          | IT Support |  28000 | Active   |
|         102 | Priya         | Finance    |  32000 | Active   |
|         103 | Arun          | Operations |  26000 | Inactive |

Example table structure:

```sql
CREATE TABLE employees (
    employee_id INT PRIMARY KEY,
    employee_name VARCHAR(100) NOT NULL,
    department VARCHAR(100),
    salary DECIMAL(10, 2),
    status VARCHAR(20) DEFAULT 'Active'
);
```

---

# Create records using INSERT

## What is INSERT?

`INSERT` adds new records to a table.

## Basic syntax

```sql
INSERT INTO table_name (
    column1,
    column2,
    column3
)
VALUES (
    value1,
    value2,
    value3
);
```

Example:

```sql
INSERT INTO employees (
    employee_id,
    employee_name,
    department,
    salary,
    status
)
VALUES (
    104,
    'Meena',
    'IT Support',
    30000,
    'Active'
);
```

Quick recall:

```text
INSERT INTO chooses the table and columns.
VALUES provides the data.
```

---

## Insert using selected columns

You do not always need to provide every column.

```sql
INSERT INTO employees (
    employee_id,
    employee_name,
    department
)
VALUES (
    105,
    'Kumar',
    'Technical Support'
);
```

Columns not supplied may receive:

* A default value
* `NULL`
* An automatically generated value

This depends on the table definition.

---

## Insert using all columns

When values are provided for every column in the exact table order:

```sql
INSERT INTO employees
VALUES (
    106,
    'Divya',
    'IT Support',
    35000,
    'Active'
);
```

This works, but explicitly listing column names is safer.

Recommended:

```sql
INSERT INTO employees (
    employee_id,
    employee_name,
    department,
    salary,
    status
)
VALUES (
    106,
    'Divya',
    'IT Support',
    35000,
    'Active'
);
```

Why explicit column names are better:

* Easier to understand
* Safer when table structure changes
* Reduces column-order mistakes
* Easier to maintain

---

## Insert multiple rows

Several records can be inserted using one statement.

```sql
INSERT INTO employees (
    employee_id,
    employee_name,
    department,
    salary,
    status
)
VALUES
    (107, 'Suresh', 'Finance', 29000, 'Active'),
    (108, 'Anitha', 'Operations', 27000, 'Active'),
    (109, 'Ramesh', 'IT Support', 31000, 'Inactive');
```

Quick recall:

```text
One INSERT statement can contain multiple value groups.
```

---

## Insert default values

If a column has a default value, it can be omitted.

Example table definition:

```sql
status VARCHAR(20) DEFAULT 'Active'
```

Insert:

```sql
INSERT INTO employees (
    employee_id,
    employee_name,
    department,
    salary
)
VALUES (
    110,
    'Lakshmi',
    'Finance',
    33000
);
```

The `status` column receives:

```text
Active
```

---

## Insert NULL values

Use `NULL` when a value is unknown or unavailable.

```sql
INSERT INTO employees (
    employee_id,
    employee_name,
    department,
    salary,
    status
)
VALUES (
    111,
    'Manoj',
    NULL,
    25000,
    'Active'
);
```

Do not use quotation marks around `NULL`.

Correct:

```sql
NULL
```

Wrong:

```sql
'NULL'
```

`'NULL'` is text. `NULL` means no known value.

---

## Insert with auto-increment

A table may automatically generate its primary key.

Example:

```sql
CREATE TABLE tickets (
    ticket_id INT AUTO_INCREMENT PRIMARY KEY,
    issue VARCHAR(255) NOT NULL,
    priority VARCHAR(20),
    status VARCHAR(20) DEFAULT 'Open'
);
```

Insert without providing `ticket_id`:

```sql
INSERT INTO tickets (
    issue,
    priority
)
VALUES (
    'Unable to log in',
    'High'
);
```

The database automatically assigns the next ticket ID.

---

## INSERT with SELECT

Data can be copied from one table to another.

```sql
INSERT INTO inactive_employees (
    employee_id,
    employee_name,
    department
)
SELECT
    employee_id,
    employee_name,
    department
FROM employees
WHERE status = 'Inactive';
```

This inserts the result of the `SELECT` query into another table.

Important:

The selected columns must match the destination columns in:

* Number
* Order
* Compatible data types

---

# Read records using SELECT

## What is SELECT?

`SELECT` retrieves records from a table.

## Basic syntax

```sql
SELECT column_name
FROM table_name;
```

Example:

```sql
SELECT
    employee_id,
    employee_name,
    department
FROM employees;
```

---

## Read all columns

```sql
SELECT *
FROM employees;
```

Use `SELECT *` mainly for quick inspection.

For reports and application queries, select only the required columns.

```sql
SELECT
    employee_id,
    employee_name,
    status
FROM employees;
```

---

## Read filtered records

```sql
SELECT
    employee_id,
    employee_name
FROM employees
WHERE status = 'Active';
```

---

## Read one specific record

```sql
SELECT
    employee_id,
    employee_name,
    department,
    salary,
    status
FROM employees
WHERE employee_id = 101;
```

Using a primary key normally identifies one record.

---

## Read sorted records

```sql
SELECT
    employee_name,
    salary
FROM employees
ORDER BY salary DESC;
```

---

## Read limited records

```sql
SELECT
    employee_name,
    salary
FROM employees
ORDER BY salary DESC
LIMIT 3;
```

---

# Update records using UPDATE

## What is UPDATE?

`UPDATE` modifies existing records.

## Basic syntax

```sql
UPDATE table_name
SET column_name = new_value
WHERE condition;
```

Example:

```sql
UPDATE employees
SET salary = 30000
WHERE employee_id = 101;
```

Quick recall:

```text
UPDATE chooses the table.
SET provides the new value.
WHERE chooses the records.
```

---

## Update one column

```sql
UPDATE employees
SET status = 'Inactive'
WHERE employee_id = 103;
```

---

## Update multiple columns

Separate column assignments using commas.

```sql
UPDATE employees
SET
    department = 'Technical Support',
    salary = 29000,
    status = 'Active'
WHERE employee_id = 103;
```

---

## Update multiple rows

```sql
UPDATE employees
SET status = 'Review'
WHERE department = 'Operations';
```

Every matching Operations record is updated.

Before running it, inspect the affected rows:

```sql
SELECT *
FROM employees
WHERE department = 'Operations';
```

Then run the update only after confirming the result.

---

## Update using the current value

Increase salary by `2000`:

```sql
UPDATE employees
SET salary = salary + 2000
WHERE department = 'IT Support';
```

The existing salary value is used in the calculation.

---

## Percentage update

Increase salary by 10 percent:

```sql
UPDATE employees
SET salary = salary * 1.10
WHERE department = 'IT Support';
```

This affects every matching employee.

Financial updates should be tested carefully because databases are very literal and have no interest in your intentions.

---

## Update NULL values

```sql
UPDATE employees
SET department = 'Unassigned'
WHERE department IS NULL;
```

Use `IS NULL`, not `= NULL`.

---

## Update using multiple conditions

```sql
UPDATE employees
SET status = 'Inactive'
WHERE department = 'Operations'
  AND salary < 30000;
```

---

## Update using IN

```sql
UPDATE employees
SET status = 'Review'
WHERE employee_id IN (103, 105, 109);
```

---

## Update using a subquery

```sql
UPDATE employees
SET status = 'Review'
WHERE department IN (
    SELECT department_name
    FROM departments
    WHERE location = 'Vellore'
);
```

Database-specific restrictions may apply when a table is updated while also being read inside a subquery.

Always test the selection first.

---

## Update every row

```sql
UPDATE employees
SET status = 'Inactive';
```

Because there is no `WHERE` clause, every row is updated.

This may be intentional, but usually it is a mistake.

Safer workflow:

```sql
SELECT *
FROM employees;
```

Then confirm whether every row really should be changed.

---

# Delete records using DELETE

## What is DELETE?

`DELETE` removes records from a table.

## Basic syntax

```sql
DELETE FROM table_name
WHERE condition;
```

Example:

```sql
DELETE FROM employees
WHERE employee_id = 103;
```

Quick recall:

```text
DELETE FROM chooses the table.
WHERE chooses the records to remove.
```

---

## Delete one record

```sql
DELETE FROM employees
WHERE employee_id = 111;
```

Using a primary key is the safest way to target one specific row.

---

## Delete multiple records

```sql
DELETE FROM employees
WHERE status = 'Inactive';
```

Every inactive employee is deleted.

Check first:

```sql
SELECT *
FROM employees
WHERE status = 'Inactive';
```

Then delete only after verifying the records.

---

## Delete using multiple conditions

```sql
DELETE FROM employees
WHERE status = 'Inactive'
  AND department = 'Operations';
```

---

## Delete using IN

```sql
DELETE FROM employees
WHERE employee_id IN (107, 108, 109);
```

---

## Delete records containing NULL

```sql
DELETE FROM employees
WHERE department IS NULL;
```

---

## Delete all rows

```sql
DELETE FROM employees;
```

This removes every record but keeps the table structure.

The table still exists with:

* Column definitions
* Keys
* Constraints
* Indexes

This command should be used only when deleting every record is intentional.

---

# DELETE, TRUNCATE, and DROP

These commands are not identical.

| Command    |  Removes rows | Keeps table structure | Can use WHERE |
| ---------- | ------------: | --------------------: | ------------: |
| `DELETE`   |           Yes |                   Yes |           Yes |
| `TRUNCATE` | Yes, all rows |                   Yes |            No |
| `DROP`     |           Yes |                    No |            No |

---

## DELETE

```sql
DELETE FROM employees
WHERE status = 'Inactive';
```

Characteristics:

* Can remove selected rows
* Supports `WHERE`
* Keeps the table
* Usually logs row-level changes
* May be rolled back inside a transaction, depending on the DBMS and storage engine

---

## TRUNCATE

```sql
TRUNCATE TABLE employees;
```

Characteristics:

* Removes every row
* Does not support `WHERE`
* Keeps the table structure
* Often faster than deleting every row
* May reset auto-increment values
* Transaction behaviour depends on the database system

---

## DROP

```sql
DROP TABLE employees;
```

Characteristics:

* Removes all rows
* Removes the table structure
* Removes indexes and constraints
* The table no longer exists

Quick recall:

```text
DELETE removes selected records.
TRUNCATE empties the table.
DROP removes the table itself.
```

---

# Transactions

## What is a transaction?

A transaction is a group of database operations treated as one unit.

Transactions help ensure that related changes either:

* Complete successfully
* Or are cancelled together

Common commands:

* `START TRANSACTION`
* `COMMIT`
* `ROLLBACK`
* `SAVEPOINT`

---

## Start a transaction

```sql
START TRANSACTION;
```

---

## Commit changes

`COMMIT` permanently saves the transaction.

```sql
COMMIT;
```

---

## Roll back changes

`ROLLBACK` cancels uncommitted changes.

```sql
ROLLBACK;
```

---

## Safe update example

```sql
START TRANSACTION;

UPDATE employees
SET salary = salary + 2000
WHERE department = 'IT Support';

SELECT *
FROM employees
WHERE department = 'IT Support';
```

If the result is correct:

```sql
COMMIT;
```

If the result is wrong:

```sql
ROLLBACK;
```

Quick recall:

```text
COMMIT saves.
ROLLBACK cancels.
```

---

## Safe delete example

```sql
START TRANSACTION;

DELETE FROM employees
WHERE status = 'Inactive';

SELECT *
FROM employees;
```

If the deletion is correct:

```sql
COMMIT;
```

If the wrong rows were deleted:

```sql
ROLLBACK;
```

The ability to roll back depends on:

* Database system
* Table storage engine
* Whether the change has already been committed
* Whether the command supports transaction rollback

---

## SAVEPOINT

A savepoint creates a temporary checkpoint inside a transaction.

```sql
START TRANSACTION;

UPDATE employees
SET status = 'Review'
WHERE department = 'Operations';

SAVEPOINT operations_updated;

DELETE FROM employees
WHERE employee_id = 109;
```

Cancel only the changes after the savepoint:

```sql
ROLLBACK TO operations_updated;
```

Save the remaining transaction:

```sql
COMMIT;
```

---

# Safe modification workflow

Use this workflow before running `UPDATE` or `DELETE`.

## Step 1: Write the condition using SELECT

```sql
SELECT *
FROM employees
WHERE status = 'Inactive';
```

## Step 2: Verify the rows

Check:

* Number of rows
* Record IDs
* Names
* Status values
* Conditions

## Step 3: Start a transaction

```sql
START TRANSACTION;
```

## Step 4: Run the modification

```sql
UPDATE employees
SET status = 'Review'
WHERE status = 'Inactive';
```

## Step 5: Verify again

```sql
SELECT *
FROM employees
WHERE status = 'Review';
```

## Step 6: Commit or roll back

```sql
COMMIT;
```

or:

```sql
ROLLBACK;
```

This process is slower than blindly running commands, but considerably faster than explaining to management why the production table is now empty.

---

# Affected rows

After `INSERT`, `UPDATE`, or `DELETE`, the database tool often reports the number of affected rows.

Examples:

```text
1 row affected
```

```text
25 rows affected
```

Always compare the affected-row count with what you expected.

If you intended to update one user and the result says:

```text
14,582 rows affected
```

Stop. Do not continue the transaction.

---

# Constraints and CRUD operations

Database constraints may prevent unsafe changes.

## Primary key

A duplicate primary key cannot normally be inserted.

```sql
INSERT INTO employees (
    employee_id,
    employee_name
)
VALUES (
    101,
    'New Employee'
);
```

Possible error:

```text
Duplicate entry for primary key
```

---

## NOT NULL

A required column cannot receive `NULL`.

```sql
INSERT INTO employees (
    employee_id,
    employee_name
)
VALUES (
    112,
    NULL
);
```

Possible error:

```text
Column cannot be null
```

---

## UNIQUE

A unique column cannot contain duplicate values.

Example:

```sql
email VARCHAR(100) UNIQUE
```

Trying to insert an existing email may fail.

---

## FOREIGN KEY

A foreign key may prevent inserting a value that does not exist in the related table.

Example:

```sql
INSERT INTO employees (
    employee_id,
    employee_name,
    department_id
)
VALUES (
    113,
    'Vijay',
    999
);
```

If department `999` does not exist, the database may reject the record.

---

## Foreign keys and DELETE

Deleting a parent record may fail when child records still reference it.

Example:

```sql
DELETE FROM departments
WHERE department_id = 1;
```

If employees still belong to department `1`, the database may prevent the deletion.

Possible behaviours include:

* Reject the deletion
* Delete related records automatically
* Set related foreign keys to `NULL`

The behaviour depends on the foreign-key rule:

* `RESTRICT`
* `CASCADE`
* `SET NULL`
* `NO ACTION`

---

# MySQL duplicate-key handling

## INSERT IGNORE

MySQL can ignore certain insert errors.

```sql
INSERT IGNORE INTO employees (
    employee_id,
    employee_name,
    department,
    salary,
    status
)
VALUES (
    101,
    'Ravi',
    'IT Support',
    28000,
    'Active'
);
```

Use this carefully because ignored errors may hide data-quality problems.

---

## ON DUPLICATE KEY UPDATE

MySQL can update a record when an insert conflicts with a primary or unique key.

```sql
INSERT INTO employees (
    employee_id,
    employee_name,
    department,
    salary,
    status
)
VALUES (
    101,
    'Ravi',
    'IT Support',
    30000,
    'Active'
)
ON DUPLICATE KEY UPDATE
    salary = 30000,
    status = 'Active';
```

This operation is sometimes called an upsert.

Quick recall:

```text
Upsert = insert a new row or update an existing row.
```

---

# Practical application support examples

## Create a support ticket

```sql
INSERT INTO tickets (
    user_id,
    issue,
    priority,
    status,
    created_at
)
VALUES (
    201,
    'Unable to log in',
    'High',
    'Open',
    CURRENT_TIMESTAMP
);
```

---

## Read open tickets

```sql
SELECT
    ticket_id,
    user_id,
    issue,
    priority,
    created_at
FROM tickets
WHERE status = 'Open'
ORDER BY created_at ASC;
```

---

## Assign a technician

```sql
UPDATE tickets
SET
    assigned_to = 15,
    status = 'In Progress'
WHERE ticket_id = 1001;
```

---

## Close a ticket

```sql
UPDATE tickets
SET
    status = 'Closed',
    closed_at = CURRENT_TIMESTAMP
WHERE ticket_id = 1001;
```

---

## Delete a test ticket

```sql
DELETE FROM tickets
WHERE ticket_id = 9999
  AND issue = 'Test ticket';
```

Using more than one condition provides additional protection.

---

## Create an application user

```sql
INSERT INTO application_users (
    username,
    email,
    account_status
)
VALUES (
    'hari.prasad',
    'hari@example.com',
    'Active'
);
```

---

## Lock an application account

```sql
UPDATE application_users
SET account_status = 'Locked'
WHERE user_id = 501;
```

---

## Unlock an account

```sql
UPDATE application_users
SET
    account_status = 'Active',
    failed_login_count = 0
WHERE user_id = 501;
```

---

## Deactivate accounts with no recent login

First verify:

```sql
SELECT
    user_id,
    username,
    last_login
FROM application_users
WHERE last_login < '2025-01-01'
  AND account_status = 'Active';
```

Then update:

```sql
UPDATE application_users
SET account_status = 'Inactive'
WHERE last_login < '2025-01-01'
  AND account_status = 'Active';
```

---

## Remove expired sessions

First verify:

```sql
SELECT *
FROM user_sessions
WHERE expires_at < CURRENT_TIMESTAMP;
```

Then delete:

```sql
DELETE FROM user_sessions
WHERE expires_at < CURRENT_TIMESTAMP;
```

---

# Common mistakes

## Mistake 1: Omitting WHERE in UPDATE

Dangerous:

```sql
UPDATE employees
SET status = 'Inactive';
```

This updates every row.

Safer:

```sql
UPDATE employees
SET status = 'Inactive'
WHERE employee_id = 103;
```

---

## Mistake 2: Omitting WHERE in DELETE

Dangerous:

```sql
DELETE FROM employees;
```

This deletes every record.

Safer:

```sql
DELETE FROM employees
WHERE employee_id = 103;
```

---

## Mistake 3: Not testing the condition

Weak:

```sql
DELETE FROM employees
WHERE department = 'Operations';
```

Better workflow:

```sql
SELECT *
FROM employees
WHERE department = 'Operations';
```

Then delete after confirming the rows.

---

## Mistake 4: Inserting values in the wrong order

Risky:

```sql
INSERT INTO employees
VALUES (
    104,
    'IT Support',
    'Meena',
    30000,
    'Active'
);
```

The department and employee name values are reversed.

Better:

```sql
INSERT INTO employees (
    employee_id,
    employee_name,
    department,
    salary,
    status
)
VALUES (
    104,
    'Meena',
    'IT Support',
    30000,
    'Active'
);
```

---

## Mistake 5: Using quotes incorrectly

Correct text:

```sql
'Active'
```

Correct number:

```sql
30000
```

Correct missing value:

```sql
NULL
```

Incorrect missing value:

```sql
'NULL'
```

---

## Mistake 6: Updating a primary key unnecessarily

```sql
UPDATE employees
SET employee_id = 999
WHERE employee_id = 101;
```

Changing primary keys can break relationships with other tables.

Primary keys should normally remain stable.

---

## Mistake 7: Ignoring foreign-key relationships

Deleting a department may affect employees, tickets, or related records.

Check dependent tables before deleting parent records.

---

## Mistake 8: Committing before verification

Once a transaction is committed, normal rollback is no longer available.

Wrong order:

```sql
UPDATE employees
SET status = 'Inactive';

COMMIT;

SELECT *
FROM employees;
```

Better:

```sql
START TRANSACTION;

UPDATE employees
SET status = 'Inactive'
WHERE employee_id = 103;

SELECT *
FROM employees
WHERE employee_id = 103;

COMMIT;
```

---

## Mistake 9: Assuming affected rows are correct

Always inspect the reported count.

Expected:

```text
1 row affected
```

Suspicious:

```text
5000 rows affected
```

Large unexpected counts indicate a faulty condition.

---

## Mistake 10: Confusing DELETE and DROP

```sql
DELETE FROM employees;
```

removes records but keeps the table.

```sql
DROP TABLE employees;
```

removes the table itself.

These are not remotely equivalent, despite both producing regret when misused.

---

## Practice queries

### Query 1

Insert one employee.

```sql
INSERT INTO employees (
    employee_id,
    employee_name,
    department,
    salary,
    status
)
VALUES (
    104,
    'Meena',
    'IT Support',
    30000,
    'Active'
);
```

### Query 2

Insert two employees.

```sql
INSERT INTO employees (
    employee_id,
    employee_name,
    department,
    salary,
    status
)
VALUES
    (105, 'Kumar', 'Finance', 33000, 'Active'),
    (106, 'Divya', 'IT Support', 35000, 'Active');
```

### Query 3

Retrieve active employees.

```sql
SELECT
    employee_id,
    employee_name,
    department
FROM employees
WHERE status = 'Active';
```

### Query 4

Update one employee's salary.

```sql
UPDATE employees
SET salary = 31000
WHERE employee_id = 104;
```

### Query 5

Update multiple columns.

```sql
UPDATE employees
SET
    department = 'Technical Support',
    status = 'Active'
WHERE employee_id = 103;
```

### Query 6

Increase IT Support salaries by 5 percent.

```sql
UPDATE employees
SET salary = salary * 1.05
WHERE department = 'IT Support';
```

### Query 7

Delete one employee.

```sql
DELETE FROM employees
WHERE employee_id = 106;
```

### Query 8

Delete inactive employees safely.

```sql
START TRANSACTION;

SELECT *
FROM employees
WHERE status = 'Inactive';

DELETE FROM employees
WHERE status = 'Inactive';

COMMIT;
```

### Query 9

Copy inactive employees into an archive table.

```sql
INSERT INTO inactive_employees (
    employee_id,
    employee_name,
    department
)
SELECT
    employee_id,
    employee_name,
    department
FROM employees
WHERE status = 'Inactive';
```

### Query 10

Update missing departments.

```sql
UPDATE employees
SET department = 'Unassigned'
WHERE department IS NULL;
```

---

# Quick recall notes

* CRUD means create, read, update, and delete.
* `INSERT` adds records.
* `SELECT` retrieves records.
* `UPDATE` modifies records.
* `DELETE` removes records.
* List column names explicitly in `INSERT`.
* Multiple rows can be inserted in one statement.
* `NULL` is written without quotation marks.
* `UPDATE` uses `SET` to provide new values.
* `WHERE` controls which records are updated or deleted.
* Missing `WHERE` affects every row.
* Test `UPDATE` and `DELETE` conditions using `SELECT`.
* Use transactions for important modifications.
* `COMMIT` saves changes.
* `ROLLBACK` cancels uncommitted changes.
* Check the affected-row count.
* `DELETE` removes records.
* `TRUNCATE` removes every record and keeps the table.
* `DROP` removes the table itself.
* Constraints protect data quality.
* Foreign keys may prevent unsafe deletion.
* An upsert inserts or updates depending on whether the record exists.

---

# Interview questions

## 1. What does CRUD mean?

CRUD means create, read, update, and delete.

## 2. Which SQL command creates records?

`INSERT` adds new records.

## 3. Which SQL command reads records?

`SELECT` retrieves records.

## 4. Which SQL command modifies records?

`UPDATE` modifies existing records.

## 5. Which SQL command removes records?

`DELETE` removes records.

## 6. What does the SET clause do?

`SET` specifies the new values in an `UPDATE` statement.

## 7. Why is WHERE important in UPDATE?

`WHERE` controls which records are changed. Without it, every row may be updated.

## 8. Why is WHERE important in DELETE?

`WHERE` controls which records are removed. Without it, every row may be deleted.

## 9. What should be done before UPDATE or DELETE?

Run a `SELECT` query using the same condition and verify the affected records.

## 10. How do you insert multiple rows?

```sql
INSERT INTO employees (
    employee_id,
    employee_name
)
VALUES
    (101, 'Ravi'),
    (102, 'Priya');
```

## 11. Why should column names be listed in INSERT?

It improves clarity, prevents column-order mistakes, and protects the query from table-structure changes.

## 12. How do you insert NULL?

```sql
NULL
```

Do not place it inside quotation marks.

## 13. How do you update several columns?

```sql
UPDATE employees
SET
    department = 'IT Support',
    status = 'Active'
WHERE employee_id = 101;
```

## 14. How do you update a value using its current value?

```sql
UPDATE employees
SET salary = salary + 2000
WHERE employee_id = 101;
```

## 15. What happens when UPDATE has no WHERE clause?

Every row in the table is updated.

## 16. What happens when DELETE has no WHERE clause?

Every row in the table is deleted.

## 17. What is the difference between DELETE and TRUNCATE?

`DELETE` can remove selected rows using `WHERE`. `TRUNCATE` removes every row and does not support `WHERE`.

## 18. What is the difference between DELETE and DROP?

`DELETE` removes records but keeps the table. `DROP` removes the table itself.

## 19. What is a transaction?

A transaction is a group of database operations handled as one unit.

## 20. What does COMMIT do?

`COMMIT` permanently saves transaction changes.

## 21. What does ROLLBACK do?

`ROLLBACK` cancels uncommitted transaction changes.

## 22. What is a SAVEPOINT?

A savepoint is a checkpoint inside a transaction that allows partial rollback.

## 23. What does affected rows mean?

It shows how many rows were inserted, updated, or deleted.

## 24. Why should affected-row counts be checked?

They help detect incorrect conditions before further changes are committed.

## 25. What is INSERT INTO SELECT?

It inserts records into one table using the result of a `SELECT` query.

## 26. What is an upsert?

An upsert inserts a new record or updates an existing record when a key conflict occurs.

## 27. What can prevent an INSERT operation?

Constraints such as:

* Primary key
* Unique
* Not null
* Foreign key
* Check constraint

## 28. Why can DELETE fail because of a foreign key?

Another table may still contain records that reference the row being deleted.

## 29. How are CRUD operations used in application support?

They are used to retrieve user data, create tickets, correct records, update account status, assign technicians, close tickets, and remove invalid test data.

## 30. What is the safest workflow for modifying records?

```text
1. Test the condition with SELECT.
2. Start a transaction.
3. Run UPDATE or DELETE.
4. Verify the affected rows.
5. COMMIT if correct.
6. ROLLBACK if incorrect.
```
