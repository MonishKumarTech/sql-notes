# Keys and normalization

## Purpose

Keys identify records, connect tables, and protect data consistency.

Normalization organizes database tables to reduce duplication and prevent incorrect data.

These concepts are important for:

* Database design
* Application support
* Data troubleshooting
* Reporting
* User and role management
* Ticketing systems
* Transaction systems
* Maintaining reliable application data

---

## Concepts covered

* Super key
* Candidate key
* Primary key
* Alternate key
* Composite key
* Foreign key
* Unique key
* Natural key
* Surrogate key
* Referential integrity
* Database anomalies
* First normal form
* Second normal form
* Third normal form
* Boyce-Codd normal form
* Denormalization

---

## What is a key?

A key is a column or combination of columns used to identify records or create relationships between tables.

Keys help databases:

* Identify rows
* Prevent duplicate records
* Connect related tables
* Enforce data integrity
* Improve data retrieval
* Maintain reliable relationships

Example:

| employee_id | employee_name | email                                         |
| ----------: | ------------- | --------------------------------------------- |
|         101 | Ravi          | [ravi@example.com](mailto:ravi@example.com)   |
|         102 | Priya         | [priya@example.com](mailto:priya@example.com) |
|         103 | Arun          | [arun@example.com](mailto:arun@example.com)   |

The `employee_id` column can identify each employee.

Quick recall:

```text
A key identifies records or connects tables.
```

---

## Super key

A super key is any column or combination of columns that uniquely identifies a row.

Using the employee table, possible super keys include:

```text
employee_id
email
employee_id + employee_name
employee_id + email
```

Some super keys contain unnecessary columns.

For example:

```text
employee_id + employee_name
```

If `employee_id` already uniquely identifies the row, adding `employee_name` is unnecessary.

Quick recall:

```text
A super key uniquely identifies a row.
It may contain extra columns.
```

---

## Candidate key

A candidate key is a minimal super key.

It uniquely identifies a row without unnecessary columns.

Possible candidate keys:

```text
employee_id
email
```

Both can uniquely identify an employee.

However:

```text
employee_id + email
```

is not a candidate key if either column alone is already unique.

Quick recall:

```text
Candidate key = minimal unique identifier.
```

---

## Super key and candidate key difference

| Super key                      | Candidate key                   |
| ------------------------------ | ------------------------------- |
| Uniquely identifies a row      | Uniquely identifies a row       |
| May contain extra columns      | Contains no unnecessary columns |
| Several combinations may exist | Minimal valid combinations      |
| Example: ID and email together | Example: ID alone               |

---

## Primary key

A primary key is the candidate key selected as the main identifier for a table.

Example:

```sql
CREATE TABLE employees (
    employee_id INT PRIMARY KEY,
    employee_name VARCHAR(100),
    email VARCHAR(100)
);
```

A primary key:

* Must be unique
* Cannot contain `NULL`
* Should remain stable
* Identifies one row
* Can be referenced by foreign keys

Quick recall:

```text
Primary key = chosen main identifier.
```

---

## Primary-key example

| employee_id | employee_name |
| ----------: | ------------- |
|         101 | Ravi          |
|         102 | Priya         |
|         103 | Arun          |

Valid:

```text
101
102
103
```

Invalid duplicate:

```text
101
101
```

Invalid missing key:

```text
NULL
```

---

## Only one primary key

A table can have only one primary-key constraint.

However, that primary key may contain:

* One column
* Multiple columns

Example using one column:

```sql
PRIMARY KEY (employee_id)
```

Example using multiple columns:

```sql
PRIMARY KEY (student_id, course_id)
```

---

## Alternate key

An alternate key is a candidate key that was not selected as the primary key.

Example:

| employee_id | email                                         |
| ----------: | --------------------------------------------- |
|         101 | [ravi@example.com](mailto:ravi@example.com)   |
|         102 | [priya@example.com](mailto:priya@example.com) |

Both columns may uniquely identify employees.

If `employee_id` is selected as the primary key, `email` becomes an alternate key.

It can be protected using a unique constraint:

```sql
CREATE TABLE employees (
    employee_id INT PRIMARY KEY,
    employee_name VARCHAR(100),
    email VARCHAR(100) UNIQUE
);
```

Quick recall:

```text
Alternate key = unused candidate key.
```

---

## Unique key

A unique constraint prevents duplicate values in a column or column combination.

```sql
CREATE TABLE employees (
    employee_id INT PRIMARY KEY,
    email VARCHAR(100) UNIQUE
);
```

Invalid:

```text
ravi@example.com
ravi@example.com
```

A unique constraint is commonly used for:

* Email addresses
* Usernames
* Employee codes
* Serial numbers
* Registration numbers

---

## Primary key and unique key difference

| Primary key                               | Unique key                                  |
| ----------------------------------------- | ------------------------------------------- |
| Main row identifier                       | Prevents duplicate values                   |
| Only one primary-key constraint per table | Several unique constraints can exist        |
| Does not allow `NULL`                     | `NULL` behaviour depends on the DBMS        |
| Commonly referenced by foreign keys       | Can also be referenced in supported designs |
| Identifies the main record                | Protects alternate identifiers              |

In MySQL, a unique column can normally contain multiple `NULL` values because `NULL` represents an unknown value.

Do not assume that behaviour is identical in every database system.

---

## Composite key

A composite key contains two or more columns.

It is used when one column alone cannot uniquely identify a row.

Example:

### Student courses table

| student_id | course_id | enrolled_date |
| ---------: | --------: | ------------- |
|          1 |       101 | 2026-06-01    |
|          1 |       102 | 2026-06-02    |
|          2 |       101 | 2026-06-03    |

Neither `student_id` nor `course_id` is unique alone.

The combination is unique:

```text
student_id + course_id
```

SQL definition:

```sql
CREATE TABLE student_courses (
    student_id INT,
    course_id INT,
    enrolled_date DATE,
    PRIMARY KEY (student_id, course_id)
);
```

Quick recall:

```text
Composite key = key made from multiple columns.
```

---

## Composite unique constraint

A table may have a separate primary key while also preventing duplicate combinations.

```sql
CREATE TABLE ticket_assignments (
    assignment_id INT AUTO_INCREMENT PRIMARY KEY,
    ticket_id INT NOT NULL,
    technician_id INT NOT NULL,
    assigned_date DATETIME NOT NULL,
    UNIQUE (ticket_id, technician_id)
);
```

This prevents the same technician from being assigned to the same ticket twice.

---

## Natural key

A natural key uses real-world data that already exists.

Examples:

* Email address
* Passport number
* Vehicle registration number
* Employee code
* Product serial number

Example:

```sql
CREATE TABLE users (
    email VARCHAR(100) PRIMARY KEY,
    user_name VARCHAR(100)
);
```

Advantages:

* Has business meaning
* May already be unique
* Reduces the need for an extra ID

Disadvantages:

* May change
* May be long
* May contain sensitive information
* May be difficult to use in relationships
* Business rules may later allow duplicates

---

## Surrogate key

A surrogate key is an artificial identifier created only for the database.

Examples:

* Auto-increment integer
* Generated UUID
* System-created ID

Example:

```sql
CREATE TABLE users (
    user_id INT AUTO_INCREMENT PRIMARY KEY,
    email VARCHAR(100) UNIQUE,
    user_name VARCHAR(100)
);
```

Here:

```text
user_id = surrogate key
email = natural alternate key
```

Advantages:

* Simple
* Usually stable
* Short
* Efficient for relationships
* Independent of business data

Disadvantages:

* Has no business meaning
* Does not automatically prevent duplicate business records

Quick recall:

```text
Natural key comes from real data.
Surrogate key is generated by the system.
```

---

## Natural key and surrogate key difference

| Natural key              | Surrogate key                          |
| ------------------------ | -------------------------------------- |
| Comes from business data | Created by the database or application |
| Has real-world meaning   | Has no business meaning                |
| May change               | Usually remains stable                 |
| May be long              | Usually short                          |
| Example: email           | Example: user ID                       |

A common design uses both:

```sql
CREATE TABLE application_users (
    user_id INT AUTO_INCREMENT PRIMARY KEY,
    username VARCHAR(100) NOT NULL UNIQUE,
    email VARCHAR(100) UNIQUE
);
```

---

## Foreign key

A foreign key creates a relationship between tables.

It refers to a key in another table.

Example:

### Departments table

```sql
CREATE TABLE departments (
    department_id INT PRIMARY KEY,
    department_name VARCHAR(100) NOT NULL
);
```

### Employees table

```sql
CREATE TABLE employees (
    employee_id INT PRIMARY KEY,
    employee_name VARCHAR(100) NOT NULL,
    department_id INT,
    FOREIGN KEY (department_id)
        REFERENCES departments(department_id)
);
```

Relationship:

```text
employees.department_id
        ↓
departments.department_id
```

Quick recall:

```text
Foreign key connects related tables.
```

---

## Parent and child tables

In a foreign-key relationship:

* The referenced table is the parent table
* The table containing the foreign key is the child table

Example:

```text
departments = parent table
employees = child table
```

The department must normally exist before an employee can refer to it.

---

## Valid foreign-key value

Departments:

| department_id | department_name |
| ------------: | --------------- |
|             1 | IT Support      |
|             2 | Finance         |

Valid employee:

```text
department_id = 1
```

Invalid employee:

```text
department_id = 999
```

If department `999` does not exist, the database may reject the record.

---

## Referential integrity

Referential integrity ensures that relationships between tables remain valid.

It prevents problems such as:

* Employees referring to nonexistent departments
* Tickets referring to nonexistent users
* Transactions referring to nonexistent accounts
* Role assignments referring to deleted roles

Quick recall:

```text
Referential integrity keeps table relationships valid.
```

---

## Foreign-key actions

Foreign keys can define what happens when a parent record is updated or deleted.

Common actions:

* `RESTRICT`
* `NO ACTION`
* `CASCADE`
* `SET NULL`

---

## RESTRICT

`RESTRICT` prevents deleting or updating a parent record while related child records exist.

```sql
FOREIGN KEY (department_id)
    REFERENCES departments(department_id)
    ON DELETE RESTRICT
```

Example:

A department cannot be deleted while employees still belong to it.

---

## NO ACTION

`NO ACTION` prevents invalid relationships.

Its exact timing and behaviour may differ between database systems.

In MySQL, it normally behaves similarly to `RESTRICT`.

---

## CASCADE

`CASCADE` automatically applies the parent change to related child records.

```sql
FOREIGN KEY (department_id)
    REFERENCES departments(department_id)
    ON DELETE CASCADE
```

If a department is deleted, related employee records may also be deleted.

This can be useful but dangerous.

Use cascade deletion only when child records should not exist independently.

---

## SET NULL

`SET NULL` replaces the child foreign-key value with `NULL` when the parent record is deleted.

```sql
FOREIGN KEY (department_id)
    REFERENCES departments(department_id)
    ON DELETE SET NULL
```

The foreign-key column must allow `NULL`.

Example:

```text
Before deletion: department_id = 3
After deletion:  department_id = NULL
```

---

## Foreign-key action comparison

| Action      | Result                                   |
| ----------- | ---------------------------------------- |
| `RESTRICT`  | Prevents parent deletion                 |
| `NO ACTION` | Rejects invalid relationship             |
| `CASCADE`   | Deletes or updates related child records |
| `SET NULL`  | Replaces child foreign key with `NULL`   |

---

## Example foreign-key definition

```sql
CREATE TABLE employees (
    employee_id INT AUTO_INCREMENT PRIMARY KEY,
    employee_name VARCHAR(100) NOT NULL,
    department_id INT,
    CONSTRAINT fk_employee_department
        FOREIGN KEY (department_id)
        REFERENCES departments(department_id)
        ON UPDATE CASCADE
        ON DELETE SET NULL
);
```

Naming constraints makes errors and maintenance easier to understand.

---

## Junction table

A junction table connects two tables in a many-to-many relationship.

Example:

```text
Users can have many roles.
Roles can belong to many users.
```

Tables:

```text
users
roles
user_roles
```

SQL:

```sql
CREATE TABLE user_roles (
    user_id INT,
    role_id INT,
    assigned_date DATETIME,
    PRIMARY KEY (user_id, role_id),
    FOREIGN KEY (user_id)
        REFERENCES users(user_id),
    FOREIGN KEY (role_id)
        REFERENCES roles(role_id)
);
```

The composite primary key prevents duplicate user-role assignments.

---

## What is normalization?

Normalization is the process of organizing database tables to reduce duplication and improve data integrity.

Normalization usually involves:

* Separating unrelated data
* Removing repeated groups
* Creating relationships
* Assigning appropriate keys
* Ensuring columns depend on the correct key

Quick recall:

```text
Normalization organizes data and reduces duplication.
```

---

## Why normalization is used

Normalization helps prevent:

* Repeated data
* Inconsistent values
* Difficult updates
* Accidental data loss
* Invalid relationships
* Large and confusing tables

---

## Example of repeated data

Poor table:

| employee_id | employee_name | department_name | department_location |
| ----------: | ------------- | --------------- | ------------------- |
|         101 | Ravi          | IT Support      | Chennai             |
|         102 | Meena         | IT Support      | Chennai             |
|         103 | Priya         | Finance         | Bengaluru           |
|         104 | Kumar         | Finance         | Bengaluru           |

Problems:

* Department names repeat
* Department locations repeat
* Updating a department location requires changing several rows
* Different rows may contain conflicting locations

Better design:

### Employees

| employee_id | employee_name | department_id |
| ----------: | ------------- | ------------: |
|         101 | Ravi          |             1 |
|         102 | Meena         |             1 |
|         103 | Priya         |             2 |
|         104 | Kumar         |             2 |

### Departments

| department_id | department_name | location  |
| ------------: | --------------- | --------- |
|             1 | IT Support      | Chennai   |
|             2 | Finance         | Bengaluru |

The department information is stored once.

---

## Database anomalies

Poorly designed tables create anomalies.

The main types are:

* Insertion anomaly
* Update anomaly
* Deletion anomaly

---

## Insertion anomaly

An insertion anomaly occurs when one fact cannot be stored without another unrelated fact.

Example table:

| employee_id | employee_name | department_name | location |
| ----------: | ------------- | --------------- | -------- |

Suppose a new department is created but has no employees.

The department cannot be stored because the table requires employee information.

Quick recall:

```text
Insertion anomaly = cannot add one fact independently.
```

---

## Update anomaly

An update anomaly occurs when the same value exists in several rows and must be updated repeatedly.

Example:

```text
IT Support | Chennai
IT Support | Chennai
IT Support | Chennai
```

If the department moves to Bengaluru, every matching row must be updated.

If one row remains unchanged, the data becomes inconsistent.

Quick recall:

```text
Update anomaly = repeated data must be changed in many places.
```

---

## Deletion anomaly

A deletion anomaly occurs when deleting one record unintentionally removes another important fact.

Example:

If the final IT Support employee is deleted, the database may also lose:

* Department name
* Department location
* Department details

Quick recall:

```text
Deletion anomaly = deleting one fact removes another fact.
```

---

## Normal forms

Common normalization levels include:

* First normal form
* Second normal form
* Third normal form
* Boyce-Codd normal form

Most basic application databases aim for at least third normal form.

---

## First normal form

A table is in first normal form when:

* Each column contains atomic values
* Each cell contains one value
* There are no repeating column groups
* Rows can be uniquely identified

Atomic means the value cannot be usefully divided for the current database purpose.

Quick recall:

```text
1NF = one value per cell.
```

---

## First-normal-form violation

Poor table:

| employee_id | employee_name | phone_numbers          |
| ----------: | ------------- | ---------------------- |
|         101 | Ravi          | 9876543210, 9123456780 |
|         102 | Priya         | 9000011111             |

The `phone_numbers` column contains multiple values.

This makes it difficult to:

* Search one phone number
* Update one phone number
* Validate phone numbers
* Join phone records
* Prevent duplicates

---

## Converting to first normal form

### Employees

| employee_id | employee_name |
| ----------: | ------------- |
|         101 | Ravi          |
|         102 | Priya         |

### Employee phones

| employee_id | phone_number |
| ----------: | ------------ |
|         101 | 9876543210   |
|         101 | 9123456780   |
|         102 | 9000011111   |

SQL:

```sql
CREATE TABLE employee_phones (
    employee_id INT,
    phone_number VARCHAR(15),
    PRIMARY KEY (employee_id, phone_number),
    FOREIGN KEY (employee_id)
        REFERENCES employees(employee_id)
);
```

---

## Repeating-column violation

Poor design:

| employee_id | phone1     | phone2     | phone3 |
| ----------: | ---------- | ---------- | ------ |
|         101 | 9876543210 | 9123456780 | NULL   |

Problems:

* Limited number of phone numbers
* Many `NULL` values
* Difficult querying
* Repeated column structure

Better:

| employee_id | phone_number |
| ----------: | ------------ |
|         101 | 9876543210   |
|         101 | 9123456780   |

---

## First-normal-form checklist

A table should have:

* One value per cell
* Consistent column meaning
* No repeated column groups
* A way to identify rows
* Separate rows for repeated values

---

## Second normal form

A table is in second normal form when:

* It is already in first normal form
* Every non-key column depends on the entire primary key
* There are no partial dependencies

Second normal form mainly matters when the primary key is composite.

Quick recall:

```text
2NF = 1NF plus no partial dependency.
```

---

## What is partial dependency?

A partial dependency occurs when a non-key column depends on only part of a composite key.

Example table:

### Student course results

| student_id | course_id | student_name | course_name | marks |
| ---------: | --------: | ------------ | ----------- | ----: |
|          1 |       101 | Ravi         | SQL         |    85 |
|          1 |       102 | Ravi         | Linux       |    80 |
|          2 |       101 | Priya        | SQL         |    90 |

Composite primary key:

```text
student_id + course_id
```

Dependencies:

```text
student_name depends only on student_id
course_name depends only on course_id
marks depends on student_id and course_id
```

Therefore, the table violates second normal form.

---

## Converting to second normal form

Separate the data into three tables.

### Students

| student_id | student_name |
| ---------: | ------------ |
|          1 | Ravi         |
|          2 | Priya        |

### Courses

| course_id | course_name |
| --------: | ----------- |
|       101 | SQL         |
|       102 | Linux       |

### Student results

| student_id | course_id | marks |
| ---------: | --------: | ----: |
|          1 |       101 |    85 |
|          1 |       102 |    80 |
|          2 |       101 |    90 |

SQL:

```sql
CREATE TABLE students (
    student_id INT PRIMARY KEY,
    student_name VARCHAR(100) NOT NULL
);
```

```sql
CREATE TABLE courses (
    course_id INT PRIMARY KEY,
    course_name VARCHAR(100) NOT NULL
);
```

```sql
CREATE TABLE student_results (
    student_id INT,
    course_id INT,
    marks DECIMAL(5, 2),
    PRIMARY KEY (student_id, course_id),
    FOREIGN KEY (student_id)
        REFERENCES students(student_id),
    FOREIGN KEY (course_id)
        REFERENCES courses(course_id)
);
```

Now:

* Student name depends on `student_id`
* Course name depends on `course_id`
* Marks depend on the complete composite key

---

## Second-normal-form note

A table with a single-column primary key cannot normally have partial dependency on part of that key.

Partial dependency requires a composite candidate key.

However, such a table may still violate third normal form.

---

## Third normal form

A table is in third normal form when:

* It is already in second normal form
* Non-key columns depend only on a key
* There are no transitive dependencies

Quick recall:

```text
3NF = 2NF plus no transitive dependency.
```

---

## What is transitive dependency?

A transitive dependency occurs when:

```text
Primary key → non-key column → another non-key column
```

Example:

| employee_id | employee_name | department_id | department_name | department_location |
| ----------: | ------------- | ------------: | --------------- | ------------------- |
|         101 | Ravi          |             1 | IT Support      | Chennai             |
|         102 | Meena         |             1 | IT Support      | Chennai             |
|         103 | Priya         |             2 | Finance         | Bengaluru           |

Dependencies:

```text
employee_id → department_id
department_id → department_name
department_id → department_location
```

The department details depend on `department_id`, not directly on `employee_id`.

This violates third normal form.

---

## Converting to third normal form

Separate employee and department data.

### Employees

| employee_id | employee_name | department_id |
| ----------: | ------------- | ------------: |
|         101 | Ravi          |             1 |
|         102 | Meena         |             1 |
|         103 | Priya         |             2 |

### Departments

| department_id | department_name | department_location |
| ------------: | --------------- | ------------------- |
|             1 | IT Support      | Chennai             |
|             2 | Finance         | Bengaluru           |

SQL:

```sql
CREATE TABLE departments (
    department_id INT PRIMARY KEY,
    department_name VARCHAR(100) NOT NULL,
    department_location VARCHAR(100)
);
```

```sql
CREATE TABLE employees (
    employee_id INT PRIMARY KEY,
    employee_name VARCHAR(100) NOT NULL,
    department_id INT,
    FOREIGN KEY (department_id)
        REFERENCES departments(department_id)
);
```

Now:

* Employee details depend on `employee_id`
* Department details depend on `department_id`
* Repeated department data is removed

---

## Normal-form summary

| Normal form | Main rule                                  |
| ----------- | ------------------------------------------ |
| 1NF         | One value per cell and no repeating groups |
| 2NF         | No dependency on part of a composite key   |
| 3NF         | No dependency between non-key columns      |
| BCNF        | Every determinant must be a candidate key  |

---

## Boyce-Codd normal form

Boyce-Codd normal form is a stricter version of third normal form.

A table is in BCNF when:

```text
Every determinant is a candidate key.
```

A determinant is a column or set of columns that determines another column.

Quick recall:

```text
BCNF = every determining column must be a candidate key.
```

---

## BCNF example

Suppose:

| student_id | subject | instructor |
| ---------: | ------- | ---------- |
|          1 | SQL     | Kumar      |
|          2 | SQL     | Kumar      |
|          1 | Linux   | Ravi       |

Business rules:

* Each instructor teaches only one subject
* A subject may have several instructors
* A student can study several subjects

Possible candidate key:

```text
student_id + subject
```

But:

```text
instructor → subject
```

The instructor determines the subject, but the instructor is not a candidate key for the table.

This may satisfy some interpretations of third normal form but violates BCNF.

A better design separates instructor assignments.

### Instructor subjects

| instructor | subject |
| ---------- | ------- |
| Kumar      | SQL     |
| Ravi       | Linux   |

### Student instructors

| student_id | instructor |
| ---------: | ---------- |
|          1 | Kumar      |
|          2 | Kumar      |
|          1 | Ravi       |

BCNF handles uncommon dependency patterns that third normal form may not fully remove.

For beginner and support-level work, understanding first, second, and third normal forms is the priority.

---

## Functional dependency

A functional dependency means one column determines another column.

Notation:

```text
employee_id → employee_name
```

This means one employee ID determines one employee name.

Other examples:

```text
department_id → department_name
ticket_id → ticket_status
user_id → username
```

Functional dependencies help identify:

* Candidate keys
* Partial dependencies
* Transitive dependencies
* Normalization problems

---

## Full functional dependency

A full functional dependency means a column depends on the entire composite key.

Example:

```text
student_id + course_id → marks
```

Neither `student_id` nor `course_id` alone determines the marks.

The complete key is required.

This satisfies the dependency requirement for second normal form.

---

## Partial functional dependency

Example:

```text
student_id + course_id → student_name
```

But in reality:

```text
student_id → student_name
```

The student name depends on only part of the composite key.

This violates second normal form.

---

## Transitive functional dependency

Example:

```text
employee_id → department_id
department_id → department_name
```

Therefore:

```text
employee_id → department_name
```

The department name depends indirectly on the employee ID.

This violates third normal form.

---

## Practical normalization example

Poor ticket table:

| ticket_id | user_name | user_email                                    | department_name | technician_name | issue          | status |
| --------: | --------- | --------------------------------------------- | --------------- | --------------- | -------------- | ------ |
|      1001 | Ravi      | [ravi@example.com](mailto:ravi@example.com)   | IT Support      | Kumar           | Login failure  | Open   |
|      1002 | Ravi      | [ravi@example.com](mailto:ravi@example.com)   | IT Support      | Meena           | Printer issue  | Closed |
|      1003 | Priya     | [priya@example.com](mailto:priya@example.com) | Finance         | Kumar           | Account locked | Open   |

Problems:

* User information repeats
* Department information repeats
* Technician names repeat
* Email updates require several changes
* Deleting a user's final ticket may remove the only stored user details

---

## Normalized ticket design

### Users

| user_id | username | email                                         |
| ------: | -------- | --------------------------------------------- |
|     201 | Ravi     | [ravi@example.com](mailto:ravi@example.com)   |
|     202 | Priya    | [priya@example.com](mailto:priya@example.com) |

### Departments

| department_id | department_name |
| ------------: | --------------- |
|             1 | IT Support      |
|             2 | Finance         |

### Technicians

| technician_id | technician_name |
| ------------: | --------------- |
|           301 | Kumar           |
|           302 | Meena           |

### Tickets

| ticket_id | user_id | department_id | assigned_to | issue          | status |
| --------: | ------: | ------------: | ----------: | -------------- | ------ |
|      1001 |     201 |             1 |         301 | Login failure  | Open   |
|      1002 |     201 |             1 |         302 | Printer issue  | Closed |
|      1003 |     202 |             2 |         301 | Account locked | Open   |

Now each fact is stored in the correct table.

---

## SQL for normalized ticket tables

```sql
CREATE TABLE application_users (
    user_id INT AUTO_INCREMENT PRIMARY KEY,
    username VARCHAR(100) NOT NULL,
    email VARCHAR(100) UNIQUE
);
```

```sql
CREATE TABLE departments (
    department_id INT AUTO_INCREMENT PRIMARY KEY,
    department_name VARCHAR(100) NOT NULL UNIQUE
);
```

```sql
CREATE TABLE technicians (
    technician_id INT AUTO_INCREMENT PRIMARY KEY,
    technician_name VARCHAR(100) NOT NULL
);
```

```sql
CREATE TABLE tickets (
    ticket_id INT AUTO_INCREMENT PRIMARY KEY,
    user_id INT NOT NULL,
    department_id INT NOT NULL,
    assigned_to INT,
    issue VARCHAR(255) NOT NULL,
    status VARCHAR(20) DEFAULT 'Open',
    FOREIGN KEY (user_id)
        REFERENCES application_users(user_id),
    FOREIGN KEY (department_id)
        REFERENCES departments(department_id),
    FOREIGN KEY (assigned_to)
        REFERENCES technicians(technician_id)
);
```

---

## Benefits of normalization

Normalization provides:

* Less duplicate data
* More consistent values
* Easier updates
* Safer deletion
* Better data integrity
* Clear table responsibilities
* Easier relationship management
* Reduced storage waste

---

## Disadvantages of excessive normalization

Highly normalized databases may require:

* More tables
* More joins
* More complex queries
* Additional design effort
* More difficult reporting
* Possible performance costs in some workloads

Normalization improves correctness, but excessive splitting can make simple systems unnecessarily complicated.

Database design is supposed to reduce problems, not create seventeen tables for storing someone’s phone number.

---

## Denormalization

Denormalization intentionally stores repeated or pre-calculated data to improve read performance or simplify reporting.

Examples:

* Storing order total in the orders table
* Storing ticket comment count
* Storing monthly summary records
* Keeping a reporting table
* Maintaining cached calculated values

Quick recall:

```text
Denormalization adds controlled duplication for performance or reporting.
```

---

## Normalization and denormalization difference

| Normalization                        | Denormalization                   |
| ------------------------------------ | --------------------------------- |
| Reduces duplication                  | Intentionally adds duplication    |
| Improves consistency                 | May improve read speed            |
| Creates more related tables          | May combine or copy data          |
| Requires more joins                  | Can reduce joins                  |
| Best for reliable transactional data | Common in reporting and analytics |

---

## When denormalization may be used

Denormalization may be considered when:

* Read performance is too slow
* Reports require repeated complex joins
* Data changes infrequently
* Summary values are calculated repeatedly
* A warehouse or reporting database is used
* Performance testing proves it is necessary

Do not denormalize because joins look inconvenient.

First ensure:

* Queries are correct
* Indexes exist
* Table relationships are correct
* Execution plans have been reviewed
* Performance has been measured

---

## Index and key difference

A key defines data rules and relationships.

An index helps the database find rows faster.

| Key                               | Index                         |
| --------------------------------- | ----------------------------- |
| Enforces identity or relationship | Improves search performance   |
| Protects data integrity           | Speeds up retrieval           |
| Includes primary and foreign keys | Created on searchable columns |
| Logical database rule             | Physical access structure     |

A primary key normally creates an index automatically.

A unique constraint normally creates a unique index.

Foreign-key indexing behaviour depends on the database system.

---

## Key-selection recommendations

A good primary key should usually be:

* Unique
* Not null
* Stable
* Short
* Easy to reference
* Unrelated to changing business values

Common recommendation:

```sql
user_id INT AUTO_INCREMENT PRIMARY KEY
```

Then protect business identifiers separately:

```sql
username VARCHAR(100) NOT NULL UNIQUE
```

---

## Poor primary-key choices

Weak choices may include:

* Employee name
* Phone number
* Address
* Department name
* Job title

These values may:

* Change
* Repeat
* Contain spelling differences
* Be missing
* Be difficult to reference

---

## Composite-key considerations

Composite keys are useful when the combination naturally identifies the relationship.

Examples:

```text
student_id + course_id
user_id + role_id
order_id + product_id
ticket_id + technician_id
```

However, large composite keys can make:

* Foreign keys longer
* Joins more complex
* Indexes larger
* Application code harder to maintain

Some designs use:

* A surrogate primary key
* A separate composite unique constraint

Example:

```sql
CREATE TABLE order_items (
    order_item_id INT AUTO_INCREMENT PRIMARY KEY,
    order_id INT NOT NULL,
    product_id INT NOT NULL,
    quantity INT NOT NULL,
    UNIQUE (order_id, product_id)
);
```

---

## Common mistakes

### Mistake 1: Using names as primary keys

Weak:

```sql
PRIMARY KEY (employee_name)
```

Names can repeat and change.

Better:

```sql
PRIMARY KEY (employee_id)
```

---

### Mistake 2: Allowing duplicate business identifiers

A surrogate primary key does not automatically prevent duplicate emails.

Weak:

```sql
CREATE TABLE users (
    user_id INT AUTO_INCREMENT PRIMARY KEY,
    email VARCHAR(100)
);
```

Better:

```sql
CREATE TABLE users (
    user_id INT AUTO_INCREMENT PRIMARY KEY,
    email VARCHAR(100) NOT NULL UNIQUE
);
```

---

### Mistake 3: Confusing primary and foreign keys

Primary key:

```text
Identifies a row in its own table.
```

Foreign key:

```text
Refers to a related row in another table.
```

---

### Mistake 4: Using CASCADE without understanding it

```sql
ON DELETE CASCADE
```

This may delete many related records automatically.

Use it only when child records should be removed with the parent.

---

### Mistake 5: Storing several values in one column

Weak:

```text
roles = Admin, Support, Reporter
```

Better:

```text
users
roles
user_roles
```

One cell should not contain several role values.

---

### Mistake 6: Creating repeated numbered columns

Weak:

```text
phone1
phone2
phone3
```

Better:

```text
employee_phones
```

with one phone number per row.

---

### Mistake 7: Repeating department details in every employee row

Weak:

| employee_name | department_name | department_location |
| ------------- | --------------- | ------------------- |
| Ravi          | IT Support      | Chennai             |
| Meena         | IT Support      | Chennai             |

Better:

* Store department details in `departments`
* Store only `department_id` in `employees`

---

### Mistake 8: Assuming every table requires heavy normalization

Small temporary tables, logs, imports, and reporting tables may have different requirements.

Normalization decisions depend on:

* Data purpose
* Update frequency
* Integrity requirements
* Query patterns
* Performance measurements

---

### Mistake 9: Splitting data without a useful reason

Creating separate tables for every small attribute can make the database unnecessarily complex.

Normalize based on actual dependencies, not because more tables look sophisticated.

---

### Mistake 10: Ignoring existing duplicate data

Adding a unique constraint may fail if duplicate records already exist.

Check first:

```sql
SELECT
    email,
    COUNT(*) AS duplicate_count
FROM users
WHERE email IS NOT NULL
GROUP BY email
HAVING COUNT(*) > 1;
```

Clean duplicate data before adding the constraint.

---

## Practical application-support examples

### Find duplicate usernames

```sql
SELECT
    username,
    COUNT(*) AS duplicate_count
FROM application_users
GROUP BY username
HAVING COUNT(*) > 1;
```

---

### Find users with invalid role relationships

```sql
SELECT
    ur.user_id,
    ur.role_id
FROM user_roles AS ur
LEFT JOIN users AS u
    ON ur.user_id = u.user_id
LEFT JOIN roles AS r
    ON ur.role_id = r.role_id
WHERE u.user_id IS NULL
   OR r.role_id IS NULL;
```

---

### Find tickets with missing users

```sql
SELECT
    t.ticket_id,
    t.user_id
FROM tickets AS t
LEFT JOIN application_users AS u
    ON t.user_id = u.user_id
WHERE u.user_id IS NULL;
```

---

### Find duplicate user-role assignments

```sql
SELECT
    user_id,
    role_id,
    COUNT(*) AS assignment_count
FROM user_roles
GROUP BY
    user_id,
    role_id
HAVING COUNT(*) > 1;
```

A composite primary key or unique constraint should prevent this problem.

---

### Add a unique constraint

```sql
ALTER TABLE application_users
ADD CONSTRAINT uq_application_users_username
UNIQUE (username);
```

---

### Add a foreign key

```sql
ALTER TABLE tickets
ADD CONSTRAINT fk_ticket_user
FOREIGN KEY (user_id)
REFERENCES application_users(user_id);
```

---

### Add a composite unique constraint

```sql
ALTER TABLE user_roles
ADD CONSTRAINT uq_user_role
UNIQUE (user_id, role_id);
```

---

## Practice questions

### Question 1

Create an employees table with:

* Auto-generated primary key
* Required employee name
* Unique email
* Foreign-key department ID

```sql
CREATE TABLE employees (
    employee_id INT AUTO_INCREMENT PRIMARY KEY,
    employee_name VARCHAR(100) NOT NULL,
    email VARCHAR(100) NOT NULL UNIQUE,
    department_id INT,
    FOREIGN KEY (department_id)
        REFERENCES departments(department_id)
);
```

---

### Question 2

Create a user-role junction table.

```sql
CREATE TABLE user_roles (
    user_id INT,
    role_id INT,
    assigned_at DATETIME DEFAULT CURRENT_TIMESTAMP,
    PRIMARY KEY (user_id, role_id),
    FOREIGN KEY (user_id)
        REFERENCES users(user_id),
    FOREIGN KEY (role_id)
        REFERENCES roles(role_id)
);
```

---

### Question 3

Find duplicate employee emails.

```sql
SELECT
    email,
    COUNT(*) AS duplicate_count
FROM employees
WHERE email IS NOT NULL
GROUP BY email
HAVING COUNT(*) > 1;
```

---

### Question 4

Find employees without valid departments.

```sql
SELECT
    e.employee_id,
    e.employee_name,
    e.department_id
FROM employees AS e
LEFT JOIN departments AS d
    ON e.department_id = d.department_id
WHERE e.department_id IS NOT NULL
  AND d.department_id IS NULL;
```

---

### Question 5

Prevent duplicate course enrolments.

```sql
ALTER TABLE student_courses
ADD CONSTRAINT uq_student_course
UNIQUE (student_id, course_id);
```

---

### Question 6

Create a foreign key that sets the employee department to `NULL` when a department is deleted.

```sql
ALTER TABLE employees
ADD CONSTRAINT fk_employee_department
FOREIGN KEY (department_id)
REFERENCES departments(department_id)
ON DELETE SET NULL;
```

---

### Question 7

Convert a repeated phone-number column into first normal form.

Create a separate table:

```sql
CREATE TABLE employee_phones (
    employee_id INT,
    phone_number VARCHAR(15),
    PRIMARY KEY (employee_id, phone_number),
    FOREIGN KEY (employee_id)
        REFERENCES employees(employee_id)
);
```

---

### Question 8

Separate student and course details from a result table.

Use:

```text
students
courses
student_results
```

The `student_results` table should contain:

```text
student_id
course_id
marks
```

---

### Question 9

Separate department details from employees.

Use:

```text
departments
employees
```

The employees table should store only the department ID.

---

### Question 10

Explain why this design is weak:

```text
employee_id
employee_name
department_name
department_location
```

Because department data repeats for every employee, creating update, insertion, and deletion anomalies.

---

## Quick recall notes

* A key identifies records or creates relationships.
* A super key uniquely identifies a row and may contain extra columns.
* A candidate key is a minimal super key.
* A primary key is the selected main candidate key.
* An alternate key is an unused candidate key.
* A unique key prevents duplicate values.
* A composite key contains multiple columns.
* A natural key comes from real-world data.
* A surrogate key is created by the system.
* A foreign key connects tables.
* Referential integrity keeps relationships valid.
* `RESTRICT` prevents parent deletion.
* `CASCADE` applies changes to child rows.
* `SET NULL` removes the child relationship without deleting the child.
* A junction table supports many-to-many relationships.
* Normalization reduces duplicate data.
* Insertion anomalies prevent independent data entry.
* Update anomalies create inconsistent repeated values.
* Deletion anomalies remove unintended information.
* First normal form requires one value per cell.
* Second normal form removes partial dependency.
* Third normal form removes transitive dependency.
* BCNF requires every determinant to be a candidate key.
* Denormalization adds controlled duplication for performance.
* Keys enforce rules.
* Indexes improve search speed.

---

## Interview questions

### 1. What is a key in SQL?

A key is a column or combination of columns used to identify records or create relationships between tables.

### 2. What is a super key?

A super key is any column combination that uniquely identifies a row.

### 3. What is a candidate key?

A candidate key is a minimal super key without unnecessary columns.

### 4. What is a primary key?

A primary key is the candidate key selected as the main identifier for a table.

### 5. Can a primary key contain NULL?

No.

### 6. Can a table have multiple primary keys?

A table has one primary-key constraint, but that key may contain multiple columns.

### 7. What is an alternate key?

An alternate key is a candidate key that was not selected as the primary key.

### 8. What is a composite key?

A composite key is a key containing two or more columns.

### 9. What is a unique key?

A unique key prevents duplicate values in a column or column combination.

### 10. What is the difference between a primary key and unique key?

A primary key is the main row identifier and cannot contain `NULL`. A table may contain several unique constraints.

### 11. What is a foreign key?

A foreign key is a column or column combination that refers to a key in another table.

### 12. What is referential integrity?

Referential integrity ensures that foreign-key values refer to valid parent records.

### 13. What is a parent table?

The parent table contains the key referenced by another table.

### 14. What is a child table?

The child table contains the foreign key.

### 15. What does ON DELETE CASCADE do?

It automatically deletes related child records when the parent record is deleted.

### 16. What does ON DELETE SET NULL do?

It changes the child foreign-key value to `NULL` when the parent record is deleted.

### 17. What is a natural key?

A natural key is a real-world value used to identify a record.

### 18. What is a surrogate key?

A surrogate key is an artificial identifier generated for database use.

### 19. What is a junction table?

A junction table connects two tables in a many-to-many relationship.

### 20. What is normalization?

Normalization is the process of organizing tables to reduce duplication and improve data integrity.

### 21. Why is normalization important?

It reduces repeated data and prevents insertion, update, and deletion anomalies.

### 22. What is an insertion anomaly?

It occurs when one fact cannot be added without another unrelated fact.

### 23. What is an update anomaly?

It occurs when repeated data must be changed in several rows.

### 24. What is a deletion anomaly?

It occurs when deleting one record unintentionally removes other useful information.

### 25. What is first normal form?

First normal form requires atomic values, one value per cell, and no repeating groups.

### 26. What is second normal form?

Second normal form requires first normal form and removes partial dependencies on composite keys.

### 27. What is partial dependency?

A partial dependency occurs when a non-key column depends on only part of a composite key.

### 28. What is third normal form?

Third normal form requires second normal form and removes transitive dependencies between non-key columns.

### 29. What is transitive dependency?

It occurs when one non-key column depends on another non-key column instead of directly on the key.

### 30. What is BCNF?

BCNF requires every determinant to be a candidate key.

### 31. What is denormalization?

Denormalization intentionally adds controlled duplication to improve performance or simplify reporting.

### 32. What is the difference between a key and an index?

A key enforces identity or relationships. An index improves data retrieval speed.

### 33. Why are names usually poor primary keys?

Names can repeat, change, contain spelling differences, or be missing.

### 34. Why use a surrogate key with a unique email?

The surrogate key provides a stable identifier, while the unique constraint prevents duplicate email addresses.

### 35. How are keys useful in application support?

They help locate exact records, verify relationships, detect duplicates, identify missing references, and protect database consistency.

### 36. How is normalization useful in application support?

It helps support teams understand where information belongs, trace relationships, avoid conflicting values, and troubleshoot data problems accurately.
