# Database basics

## Purpose

This topic explains the basic concepts required before writing SQL queries.

Understanding databases, tables, rows, columns, keys, and database management systems is important for Application Support, IT Support, Reporting, and System Administration roles.

---

## What is data?

Data is a collection of facts or information.

Examples:

* Employee name
* Customer phone number
* Product price
* Ticket status
* Order date
* Department name

Example data:

```text
Employee ID: 101
Employee name: Ravi
Department: IT Support
Status: Active
```

Quick recall:

```text
Data means stored facts or information.
```

---

## What is a database?

A database is an organized collection of related data.

It allows users and applications to:

* Store data
* Retrieve data
* Update data
* Delete data
* Organize data
* Generate reports

Examples of databases:

* Employee database
* Customer database
* Helpdesk ticket database
* College student database
* Inventory database
* Banking database

Quick recall:

```text
A database stores related data in an organized form.
```

---

## What is a DBMS?

DBMS means Database Management System.

A DBMS is software used to create, store, manage, retrieve, and modify data in a database.

Examples:

* MySQL
* PostgreSQL
* Microsoft SQL Server
* Oracle Database
* SQLite
* MariaDB

Quick recall:

```text
Database = stored data
DBMS = software used to manage the data
```

---

## Database and DBMS difference

| Database                          | DBMS                                    |
| --------------------------------- | --------------------------------------- |
| Collection of organized data      | Software that manages databases         |
| Stores records                    | Provides tools to access records        |
| Contains tables and relationships | Controls storage, security, and queries |
| Example: employee database        | Example: MySQL                          |

---

## What is SQL?

SQL means Structured Query Language.

SQL is used to communicate with relational databases.

SQL can be used to:

* Create databases and tables
* Retrieve data
* Filter records
* Sort results
* Insert new records
* Update existing records
* Delete records
* Control access
* Create reports

Example:

```sql
SELECT *
FROM employees;
```

This query retrieves all columns and rows from the `employees` table.

Quick recall:

```text
SQL is the language used to work with relational databases.
```

---

## SQL and MySQL difference

SQL and MySQL are not the same.

| SQL                                        | MySQL                              |
| ------------------------------------------ | ---------------------------------- |
| A query language                           | A database management system       |
| Used to communicate with databases         | Uses SQL to manage databases       |
| Defines commands such as SELECT and UPDATE | Stores and processes database data |
| It is a language                           | It is software                     |

Quick recall:

```text
SQL = language
MySQL = database software
```

---

## What is a relational database?

A relational database stores data in tables.

Tables can be connected using relationships.

Example:

An `employees` table can be connected to a `departments` table using a department ID.

### Employees table

| employee_id | employee_name | department_id |
| ----------: | ------------- | ------------: |
|         101 | Ravi          |             1 |
|         102 | Priya         |             2 |
|         103 | Arun          |             1 |

### Departments table

| department_id | department_name |
| ------------: | --------------- |
|             1 | IT Support      |
|             2 | Finance         |

The `department_id` column connects the two tables.

Quick recall:

```text
A relational database stores connected data in tables.
```

---

## What is a table?

A table stores related data using rows and columns.

Example table named `employees`:

| employee_id | employee_name | department | status   |
| ----------: | ------------- | ---------- | -------- |
|         101 | Ravi          | IT Support | Active   |
|         102 | Priya         | Finance    | Active   |
|         103 | Arun          | Operations | Inactive |

Quick recall:

```text
A table stores data in rows and columns.
```

---

## What is a row?

A row represents one complete record.

Example:

```text
101 | Ravi | IT Support | Active
```

This row contains the complete record of one employee.

Rows are also called:

* Records
* Tuples

Quick recall:

```text
One row means one complete record.
```

---

## What is a column?

A column represents one type of information.

Examples:

* `employee_id`
* `employee_name`
* `department`
* `status`

Columns are also called:

* Fields
* Attributes

Quick recall:

```text
A column defines one type of data.
```

---

## Row and column difference

| Row                   | Column                             |
| --------------------- | ---------------------------------- |
| Represents one record | Represents one type of information |
| Moves horizontally    | Appears vertically                 |
| Example: one employee | Example: employee name             |
| Also called record    | Also called field or attribute     |

---

## What is a schema?

A schema defines the structure of a database.

It describes:

* Tables
* Columns
* Data types
* Relationships
* Keys
* Constraints

Example schema:

```text
employees
├── employee_id
├── employee_name
├── department
└── status
```

Quick recall:

```text
Schema means database structure or design.
```

---

## What is a data type?

A data type defines what kind of value a column can store.

Common SQL data types:

| Data type  | Purpose              | Example                 |
| ---------- | -------------------- | ----------------------- |
| `INT`      | Whole numbers        | `101`                   |
| `DECIMAL`  | Decimal numbers      | `25000.50`              |
| `VARCHAR`  | Variable-length text | `'Ravi'`                |
| `CHAR`     | Fixed-length text    | `'Y'`                   |
| `DATE`     | Date values          | `'2026-07-10'`          |
| `DATETIME` | Date and time        | `'2026-07-10 10:30:00'` |
| `BOOLEAN`  | True or false values | `TRUE`                  |
| `TEXT`     | Long text            | Ticket description      |

Example:

```sql
employee_id INT
```

This means the `employee_id` column stores whole numbers.

Quick recall:

```text
A data type controls what kind of value a column can store.
```

---

## What is a primary key?

A primary key uniquely identifies each row in a table.

Example:

| employee_id | employee_name |
| ----------: | ------------- |
|         101 | Ravi          |
|         102 | Priya         |
|         103 | Arun          |

Here, `employee_id` can be used as the primary key because every employee has a unique ID.

A primary key:

* Must contain unique values
* Cannot contain `NULL`
* Should identify one row

Example definition:

```sql
employee_id INT PRIMARY KEY
```

Quick recall:

```text
A primary key uniquely identifies each record.
```

---

## What is a foreign key?

A foreign key connects one table to another table.

Example:

The `department_id` column in the `employees` table can refer to the `department_id` column in the `departments` table.

```text
employees.department_id
        ↓
departments.department_id
```

Quick recall:

```text
A foreign key creates a relationship between tables.
```

---

## Primary key and foreign key difference

| Primary key               | Foreign key                      |
| ------------------------- | -------------------------------- |
| Uniquely identifies a row | Connects one table to another    |
| Values must be unique     | Values may repeat                |
| Cannot normally be `NULL` | May allow `NULL`                 |
| Defined in the main table | Refers to a key in another table |

---

## What is NULL?

`NULL` means a value is missing, unknown, or not provided.

It is not the same as:

* Zero
* Empty text
* Space
* False

Example:

| employee_id | phone_number |
| ----------: | ------------ |
|         101 | 9876543210   |
|         102 | NULL         |

The second employee does not have a recorded phone number.

Quick recall:

```text
NULL means no known value.
```

---

## What is a constraint?

A constraint is a rule applied to a column or table.

Constraints help maintain correct and reliable data.

Common constraints:

| Constraint    | Purpose                               |
| ------------- | ------------------------------------- |
| `PRIMARY KEY` | Uniquely identifies each row          |
| `FOREIGN KEY` | Creates a relationship between tables |
| `NOT NULL`    | Prevents missing values               |
| `UNIQUE`      | Prevents duplicate values             |
| `DEFAULT`     | Adds a default value                  |
| `CHECK`       | Validates data using a condition      |

Example:

```sql
email VARCHAR(100) UNIQUE
```

This prevents two records from using the same email address.

---

## What is a query?

A query is a request sent to a database.

Queries can be used to:

* Retrieve data
* Add data
* Modify data
* Delete data
* Create database objects

Example:

```sql
SELECT employee_name
FROM employees;
```

This asks the database to return employee names.

Quick recall:

```text
A query is an instruction sent to a database.
```

---

## Main SQL command categories

SQL commands are grouped by purpose.

### Data Query Language

DQL is used to retrieve data.

```sql
SELECT
```

### Data Definition Language

DDL is used to define database structures.

```sql
CREATE
ALTER
DROP
TRUNCATE
```

### Data Manipulation Language

DML is used to modify data.

```sql
INSERT
UPDATE
DELETE
```

### Data Control Language

DCL is used to manage permissions.

```sql
GRANT
REVOKE
```

### Transaction Control Language

TCL is used to manage database transactions.

```sql
COMMIT
ROLLBACK
SAVEPOINT
```

---

## SQL command category summary

| Category | Full form                    | Purpose             | Commands                     |
| -------- | ---------------------------- | ------------------- | ---------------------------- |
| DQL      | Data Query Language          | Retrieve data       | `SELECT`                     |
| DDL      | Data Definition Language     | Define structures   | `CREATE`, `ALTER`, `DROP`    |
| DML      | Data Manipulation Language   | Modify records      | `INSERT`, `UPDATE`, `DELETE` |
| DCL      | Data Control Language        | Control permissions | `GRANT`, `REVOKE`            |
| TCL      | Transaction Control Language | Manage transactions | `COMMIT`, `ROLLBACK`         |

---

## What is CRUD?

CRUD represents four common database operations.

| Letter | Operation | SQL command |
| ------ | --------- | ----------- |
| C      | Create    | `INSERT`    |
| R      | Read      | `SELECT`    |
| U      | Update    | `UPDATE`    |
| D      | Delete    | `DELETE`    |

Example:

```sql
INSERT INTO employees
VALUES (101, 'Ravi', 'IT Support');
```

```sql
SELECT *
FROM employees;
```

```sql
UPDATE employees
SET department = 'Technical Support'
WHERE employee_id = 101;
```

```sql
DELETE FROM employees
WHERE employee_id = 101;
```

Quick recall:

```text
CRUD means create, read, update, and delete data.
```

---

## Database server, database, and table

The basic hierarchy is:

```text
Database server
└── Database
    └── Table
        ├── Columns
        └── Rows
```

Example:

```text
MySQL Server
└── company_database
    └── employees
        ├── employee_id
        ├── employee_name
        └── department
```

---

## Example workplace database

A helpdesk application may use tables such as:

```text
users
tickets
departments
technicians
ticket_status
ticket_comments
```

Example `tickets` table:

| ticket_id | user_id | issue               | priority | status      |
| --------: | ------: | ------------------- | -------- | ----------- |
|      1001 |     201 | Unable to log in    | High     | Open        |
|      1002 |     202 | Printer not working | Medium   | In progress |
|      1003 |     203 | Password reset      | Low      | Closed      |

SQL can be used to find open tickets:

```sql
SELECT *
FROM tickets
WHERE status = 'Open';
```

---

## Basic SQL syntax rules

* SQL keywords are usually written in uppercase.
* Table and column names should be meaningful.
* Text values must be placed inside single quotation marks.
* SQL statements usually end with a semicolon.
* SQL is usually not case-sensitive for keywords.
* Database names may behave differently depending on the operating system and DBMS.

Example:

```sql
SELECT employee_name
FROM employees
WHERE status = 'Active';
```

---

## Naming recommendations

Use clear names:

```text
employee_id
employee_name
ticket_status
created_date
```

Avoid unclear names:

```text
id1
name2
value
data
x
```

Use lowercase names with underscores for consistency:

```text
employee_name
ticket_priority
department_id
```

---

## Quick recall notes

* Data means facts or information.
* A database is an organized collection of related data.
* A DBMS is software used to manage databases.
* SQL is a language used to communicate with relational databases.
* MySQL is a DBMS that uses SQL.
* A relational database stores data in connected tables.
* A table contains rows and columns.
* A row represents one complete record.
* A column represents one type of information.
* A schema defines the database structure.
* A data type controls what a column can store.
* A primary key uniquely identifies a row.
* A foreign key connects tables.
* `NULL` means missing or unknown data.
* A constraint is a rule applied to data.
* CRUD means create, read, update, and delete.
* DQL retrieves data.
* DDL defines database structures.
* DML modifies records.
* DCL controls permissions.
* TCL manages transactions.

---

## Interview questions

### 1. What is a database?

A database is an organized collection of related data that can be stored, retrieved, and managed.

### 2. What is a DBMS?

A DBMS is software used to create, store, retrieve, update, and manage databases.

### 3. What is SQL?

SQL is a language used to communicate with relational databases.

### 4. What is the difference between SQL and MySQL?

SQL is a query language. MySQL is a database management system that uses SQL.

### 5. What is a relational database?

A relational database stores data in tables that can be connected using relationships.

### 6. What is a table?

A table is a database object that stores related data in rows and columns.

### 7. What is a row?

A row represents one complete record in a table.

### 8. What is a column?

A column represents one type of information stored for every record.

### 9. What is a primary key?

A primary key is a column or combination of columns that uniquely identifies each row.

### 10. What is a foreign key?

A foreign key is a column that connects one table to another table.

### 11. What is NULL?

`NULL` represents a missing, unknown, or unavailable value.

### 12. What is a constraint?

A constraint is a rule used to control and validate data in a table.

### 13. What is CRUD?

CRUD means create, read, update, and delete.

### 14. What is the difference between a database and a table?

A database contains multiple related objects. A table is one object inside a database that stores data in rows and columns.

### 15. What is a schema?

A schema defines the structure of a database, including tables, columns, relationships, and constraints.

### 16. What is the difference between a primary key and a foreign key?

A primary key uniquely identifies a record. A foreign key connects one table to another.

### 17. What is DDL?

DDL means Data Definition Language. It includes commands used to define database structures, such as `CREATE`, `ALTER`, and `DROP`.

### 18. What is DML?

DML means Data Manipulation Language. It includes commands used to insert, update, and delete records.

### 19. What is DQL?

DQL means Data Query Language. It is used to retrieve data using `SELECT`.

### 20. Why is SQL useful for application support?

SQL helps support professionals retrieve records, verify application data, investigate errors, prepare reports, and troubleshoot database-backed applications.
