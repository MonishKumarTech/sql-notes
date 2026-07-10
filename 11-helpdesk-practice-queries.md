# Helpdesk practice queries

## Purpose

This file contains practical SQL queries based on a helpdesk and application-support database.

The exercises simulate common workplace tasks such as:

* Finding open tickets
* Identifying unassigned incidents
* Checking locked user accounts
* Investigating failed logins
* Measuring technician workload
* Calculating resolution time
* Finding missing or inconsistent data
* Preparing operational reports
* Safely updating support records

---

## Practice environment

* Database system: MySQL
* Query language: SQL
* Scenario: IT helpdesk and application support
* Focus: Retrieval, reporting, troubleshooting, and safe data modification

---

## Database structure

The practice database contains the following tables:

```text
departments
application_users
technicians
tickets
ticket_comments
login_attempts
```

Relationships:

```text
departments.department_id
        ↑
application_users.department_id

departments.department_id
        ↑
technicians.department_id

application_users.user_id
        ↑
tickets.user_id

technicians.technician_id
        ↑
tickets.assigned_to

tickets.ticket_id
        ↑
ticket_comments.ticket_id

application_users.user_id
        ↑
login_attempts.user_id
```

---

## Create the practice database

```sql
CREATE DATABASE helpdesk_practice;
```

Select the database:

```sql
USE helpdesk_practice;
```

---

## Create the departments table

```sql
CREATE TABLE departments (
    department_id INT AUTO_INCREMENT PRIMARY KEY,
    department_name VARCHAR(100) NOT NULL UNIQUE,
    location VARCHAR(100)
);
```

---

## Create the application users table

```sql
CREATE TABLE application_users (
    user_id INT AUTO_INCREMENT PRIMARY KEY,
    username VARCHAR(100) NOT NULL UNIQUE,
    full_name VARCHAR(100) NOT NULL,
    email VARCHAR(150) UNIQUE,
    department_id INT,
    account_status VARCHAR(20) DEFAULT 'Active',
    failed_login_count INT DEFAULT 0,
    last_login DATETIME,
    created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (department_id)
        REFERENCES departments(department_id)
        ON DELETE SET NULL
);
```

---

## Create the technicians table

```sql
CREATE TABLE technicians (
    technician_id INT AUTO_INCREMENT PRIMARY KEY,
    technician_name VARCHAR(100) NOT NULL,
    email VARCHAR(150) UNIQUE,
    department_id INT,
    technician_status VARCHAR(20) DEFAULT 'Available',
    FOREIGN KEY (department_id)
        REFERENCES departments(department_id)
        ON DELETE SET NULL
);
```

---

## Create the tickets table

```sql
CREATE TABLE tickets (
    ticket_id INT AUTO_INCREMENT PRIMARY KEY,
    user_id INT NOT NULL,
    department_id INT,
    assigned_to INT,
    issue VARCHAR(255) NOT NULL,
    category VARCHAR(50),
    priority VARCHAR(20) DEFAULT 'Medium',
    status VARCHAR(30) DEFAULT 'Open',
    created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
    updated_at DATETIME DEFAULT CURRENT_TIMESTAMP,
    closed_at DATETIME,
    resolution_minutes INT,
    FOREIGN KEY (user_id)
        REFERENCES application_users(user_id),
    FOREIGN KEY (department_id)
        REFERENCES departments(department_id)
        ON DELETE SET NULL,
    FOREIGN KEY (assigned_to)
        REFERENCES technicians(technician_id)
        ON DELETE SET NULL
);
```

---

## Create the ticket comments table

```sql
CREATE TABLE ticket_comments (
    comment_id INT AUTO_INCREMENT PRIMARY KEY,
    ticket_id INT NOT NULL,
    comment_text VARCHAR(500) NOT NULL,
    commented_by VARCHAR(100),
    created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (ticket_id)
        REFERENCES tickets(ticket_id)
        ON DELETE CASCADE
);
```

---

## Create the login attempts table

```sql
CREATE TABLE login_attempts (
    login_id INT AUTO_INCREMENT PRIMARY KEY,
    user_id INT NOT NULL,
    login_status VARCHAR(20) NOT NULL,
    ip_address VARCHAR(45),
    attempted_at DATETIME DEFAULT CURRENT_TIMESTAMP,
    failure_reason VARCHAR(255),
    FOREIGN KEY (user_id)
        REFERENCES application_users(user_id)
        ON DELETE CASCADE
);
```

---

## Insert sample departments

```sql
INSERT INTO departments (
    department_name,
    location
)
VALUES
    ('IT Support', 'Chennai'),
    ('Finance', 'Bengaluru'),
    ('Operations', 'Vellore'),
    ('Human Resources', 'Chennai');
```

---

## Insert sample users

```sql
INSERT INTO application_users (
    username,
    full_name,
    email,
    department_id,
    account_status,
    failed_login_count,
    last_login
)
VALUES
    (
        'ravi.k',
        'Ravi Kumar',
        'ravi@example.com',
        1,
        'Active',
        0,
        '2026-07-09 09:15:00'
    ),
    (
        'priya.s',
        'Priya S',
        'priya@example.com',
        2,
        'Locked',
        5,
        '2026-07-07 11:30:00'
    ),
    (
        'arun.m',
        'Arun M',
        NULL,
        3,
        'Active',
        2,
        '2026-06-25 14:20:00'
    ),
    (
        'meena.r',
        'Meena R',
        'meena@example.com',
        1,
        'Inactive',
        0,
        NULL
    ),
    (
        'divya.p',
        'Divya P',
        'divya@example.com',
        2,
        'Active',
        1,
        '2026-07-10 08:45:00'
    );
```

---

## Insert sample technicians

```sql
INSERT INTO technicians (
    technician_name,
    email,
    department_id,
    technician_status
)
VALUES
    ('Kumar', 'kumar.support@example.com', 1, 'Available'),
    ('Anitha', 'anitha.support@example.com', 1, 'Busy'),
    ('Suresh', 'suresh.support@example.com', 1, 'Available'),
    ('Lakshmi', 'lakshmi.support@example.com', 1, 'Leave');
```

---

## Insert sample tickets

```sql
INSERT INTO tickets (
    user_id,
    department_id,
    assigned_to,
    issue,
    category,
    priority,
    status,
    created_at,
    updated_at,
    closed_at,
    resolution_minutes
)
VALUES
    (
        1,
        1,
        1,
        'Unable to connect to VPN',
        'Network',
        'High',
        'Open',
        '2026-07-08 09:00:00',
        '2026-07-08 09:30:00',
        NULL,
        NULL
    ),
    (
        2,
        2,
        2,
        'Account locked after failed login attempts',
        'Access',
        'High',
        'In Progress',
        '2026-07-08 10:00:00',
        '2026-07-09 12:00:00',
        NULL,
        NULL
    ),
    (
        3,
        3,
        NULL,
        'Printer not responding',
        'Hardware',
        'Medium',
        'Open',
        '2026-07-09 11:00:00',
        '2026-07-09 11:00:00',
        NULL,
        NULL
    ),
    (
        1,
        1,
        3,
        'Email application not synchronizing',
        'Application',
        'Medium',
        'Closed',
        '2026-07-01 09:00:00',
        '2026-07-01 11:00:00',
        '2026-07-01 11:00:00',
        120
    ),
    (
        5,
        2,
        1,
        'Report export failed',
        'Application',
        'High',
        'Closed',
        '2026-07-03 13:00:00',
        '2026-07-03 15:30:00',
        '2026-07-03 15:30:00',
        150
    ),
    (
        4,
        1,
        NULL,
        'Password reset required',
        'Access',
        'Low',
        'Open',
        '2026-07-05 16:00:00',
        '2026-07-05 16:00:00',
        NULL,
        NULL
    ),
    (
        3,
        3,
        2,
        'Application displays server error',
        'Application',
        'Critical',
        'In Progress',
        '2026-07-10 07:30:00',
        '2026-07-10 08:00:00',
        NULL,
        NULL
    );
```

---

## Insert sample ticket comments

```sql
INSERT INTO ticket_comments (
    ticket_id,
    comment_text,
    commented_by,
    created_at
)
VALUES
    (
        1,
        'User confirmed that internet connection is working.',
        'Kumar',
        '2026-07-08 09:15:00'
    ),
    (
        1,
        'VPN configuration is being checked.',
        'Kumar',
        '2026-07-08 09:30:00'
    ),
    (
        2,
        'Account lock confirmed in application.',
        'Anitha',
        '2026-07-08 10:30:00'
    ),
    (
        4,
        'Email profile recreated and synchronization restored.',
        'Suresh',
        '2026-07-01 11:00:00'
    ),
    (
        7,
        'Application log shows database connection timeout.',
        'Anitha',
        '2026-07-10 08:00:00'
    );
```

---

## Insert sample login attempts

```sql
INSERT INTO login_attempts (
    user_id,
    login_status,
    ip_address,
    attempted_at,
    failure_reason
)
VALUES
    (
        2,
        'Failed',
        '192.168.1.20',
        '2026-07-08 09:40:00',
        'Incorrect password'
    ),
    (
        2,
        'Failed',
        '192.168.1.20',
        '2026-07-08 09:42:00',
        'Incorrect password'
    ),
    (
        2,
        'Failed',
        '192.168.1.20',
        '2026-07-08 09:44:00',
        'Incorrect password'
    ),
    (
        2,
        'Failed',
        '192.168.1.20',
        '2026-07-08 09:46:00',
        'Account locked'
    ),
    (
        1,
        'Successful',
        '192.168.1.10',
        '2026-07-09 09:15:00',
        NULL
    ),
    (
        3,
        'Failed',
        '192.168.1.30',
        '2026-07-10 07:00:00',
        'Incorrect password'
    ),
    (
        3,
        'Successful',
        '192.168.1.30',
        '2026-07-10 07:05:00',
        NULL
    );
```

---

# Basic ticket queries

## 1. Display all tickets

```sql
SELECT *
FROM tickets;
```

Use this for quick inspection.

In workplace reports, retrieve only the required columns.

---

## 2. Display important ticket columns

```sql
SELECT
    ticket_id,
    issue,
    priority,
    status,
    created_at
FROM tickets;
```

---

## 3. Find open tickets

```sql
SELECT
    ticket_id,
    issue,
    priority,
    created_at
FROM tickets
WHERE status = 'Open';
```

---

## 4. Find high-priority tickets

```sql
SELECT
    ticket_id,
    issue,
    status,
    created_at
FROM tickets
WHERE priority = 'High';
```

---

## 5. Find open high-priority tickets

```sql
SELECT
    ticket_id,
    issue,
    priority,
    status,
    created_at
FROM tickets
WHERE priority = 'High'
  AND status = 'Open';
```

---

## 6. Find unresolved tickets

```sql
SELECT
    ticket_id,
    issue,
    priority,
    status
FROM tickets
WHERE status <> 'Closed';
```

---

## 7. Find tickets created on a specific date

```sql
SELECT
    ticket_id,
    issue,
    created_at
FROM tickets
WHERE created_at >= '2026-07-10'
  AND created_at < '2026-07-11';
```

This includes every ticket created on July 10, even when the column contains time.

---

## 8. Find tickets created within a date range

```sql
SELECT
    ticket_id,
    issue,
    status,
    created_at
FROM tickets
WHERE created_at >= '2026-07-01'
  AND created_at < '2026-07-11';
```

---

## 9. Find application-related tickets

```sql
SELECT
    ticket_id,
    issue,
    priority,
    status
FROM tickets
WHERE category = 'Application';
```

---

## 10. Find tickets containing the word password

```sql
SELECT
    ticket_id,
    issue,
    status
FROM tickets
WHERE issue LIKE '%password%';
```

---

# Sorting and limiting queries

## 11. Display newest tickets first

```sql
SELECT
    ticket_id,
    issue,
    status,
    created_at
FROM tickets
ORDER BY created_at DESC;
```

---

## 12. Display the five latest tickets

```sql
SELECT
    ticket_id,
    issue,
    priority,
    status,
    created_at
FROM tickets
ORDER BY created_at DESC
LIMIT 5;
```

---

## 13. Display the oldest unresolved tickets

```sql
SELECT
    ticket_id,
    issue,
    priority,
    status,
    created_at
FROM tickets
WHERE status <> 'Closed'
ORDER BY created_at ASC;
```

---

## 14. Display the oldest three open tickets

```sql
SELECT
    ticket_id,
    issue,
    priority,
    created_at
FROM tickets
WHERE status = 'Open'
ORDER BY created_at ASC
LIMIT 3;
```

---

## 15. Sort priorities in business order

Alphabetical sorting does not produce the correct priority order.

Use:

```sql
SELECT
    ticket_id,
    issue,
    priority,
    status
FROM tickets
ORDER BY
    FIELD(
        priority,
        'Critical',
        'High',
        'Medium',
        'Low'
    ),
    created_at ASC;
```

This returns Critical tickets first, followed by High, Medium, and Low.

---

# User-account queries

## 16. Display active users

```sql
SELECT
    user_id,
    username,
    full_name,
    email
FROM application_users
WHERE account_status = 'Active';
```

---

## 17. Find locked accounts

```sql
SELECT
    user_id,
    username,
    full_name,
    failed_login_count,
    last_login
FROM application_users
WHERE account_status = 'Locked';
```

---

## 18. Find users with failed login counts

```sql
SELECT
    user_id,
    username,
    failed_login_count
FROM application_users
WHERE failed_login_count > 0
ORDER BY failed_login_count DESC;
```

---

## 19. Find users without email addresses

```sql
SELECT
    user_id,
    username,
    full_name
FROM application_users
WHERE email IS NULL;
```

---

## 20. Find users who never logged in

```sql
SELECT
    user_id,
    username,
    full_name,
    account_status
FROM application_users
WHERE last_login IS NULL;
```

---

## 21. Find inactive accounts

```sql
SELECT
    user_id,
    username,
    full_name,
    last_login
FROM application_users
WHERE account_status = 'Inactive';
```

---

## 22. Find accounts inactive for a long period

```sql
SELECT
    user_id,
    username,
    full_name,
    last_login
FROM application_users
WHERE account_status = 'Active'
  AND last_login < '2026-06-01';
```

The exact inactivity period should come from company policy, not whatever date looked dramatic while writing the query.

---

# Join-based support queries

## 23. Display tickets with user details

```sql
SELECT
    t.ticket_id,
    t.issue,
    t.priority,
    t.status,
    u.username,
    u.full_name,
    u.email
FROM tickets AS t
INNER JOIN application_users AS u
    ON t.user_id = u.user_id;
```

---

## 24. Display tickets with technician names

```sql
SELECT
    t.ticket_id,
    t.issue,
    t.status,
    tech.technician_name
FROM tickets AS t
LEFT JOIN technicians AS tech
    ON t.assigned_to = tech.technician_id;
```

A `LEFT JOIN` keeps tickets that have not yet been assigned.

---

## 25. Display complete ticket details

```sql
SELECT
    t.ticket_id,
    t.issue,
    t.category,
    t.priority,
    t.status,
    u.username,
    u.full_name,
    d.department_name,
    tech.technician_name,
    t.created_at
FROM tickets AS t
INNER JOIN application_users AS u
    ON t.user_id = u.user_id
LEFT JOIN departments AS d
    ON t.department_id = d.department_id
LEFT JOIN technicians AS tech
    ON t.assigned_to = tech.technician_id
ORDER BY t.created_at DESC;
```

---

## 26. Find unassigned tickets

```sql
SELECT
    t.ticket_id,
    t.issue,
    t.priority,
    t.status,
    t.created_at
FROM tickets AS t
LEFT JOIN technicians AS tech
    ON t.assigned_to = tech.technician_id
WHERE tech.technician_id IS NULL;
```

---

## 27. Find unassigned open tickets

```sql
SELECT
    t.ticket_id,
    t.issue,
    t.priority,
    t.created_at
FROM tickets AS t
LEFT JOIN technicians AS tech
    ON t.assigned_to = tech.technician_id
WHERE tech.technician_id IS NULL
  AND t.status = 'Open'
ORDER BY
    FIELD(
        t.priority,
        'Critical',
        'High',
        'Medium',
        'Low'
    ),
    t.created_at ASC;
```

---

## 28. Find tickets belonging to locked users

```sql
SELECT
    t.ticket_id,
    t.issue,
    t.status,
    u.username,
    u.account_status
FROM tickets AS t
INNER JOIN application_users AS u
    ON t.user_id = u.user_id
WHERE u.account_status = 'Locked';
```

---

## 29. Find users without tickets

```sql
SELECT
    u.user_id,
    u.username,
    u.full_name
FROM application_users AS u
LEFT JOIN tickets AS t
    ON u.user_id = t.user_id
WHERE t.ticket_id IS NULL;
```

---

## 30. Find technicians without assigned tickets

```sql
SELECT
    tech.technician_id,
    tech.technician_name,
    tech.technician_status
FROM technicians AS tech
LEFT JOIN tickets AS t
    ON tech.technician_id = t.assigned_to
WHERE t.ticket_id IS NULL;
```

---

# Aggregate and reporting queries

## 31. Count all tickets

```sql
SELECT
    COUNT(*) AS total_ticket_count
FROM tickets;
```

---

## 32. Count open tickets

```sql
SELECT
    COUNT(*) AS open_ticket_count
FROM tickets
WHERE status = 'Open';
```

---

## 33. Count unresolved tickets

```sql
SELECT
    COUNT(*) AS unresolved_ticket_count
FROM tickets
WHERE status <> 'Closed';
```

---

## 34. Count tickets by status

```sql
SELECT
    status,
    COUNT(*) AS ticket_count
FROM tickets
GROUP BY status
ORDER BY ticket_count DESC;
```

---

## 35. Count tickets by priority

```sql
SELECT
    priority,
    COUNT(*) AS ticket_count
FROM tickets
GROUP BY priority
ORDER BY
    FIELD(
        priority,
        'Critical',
        'High',
        'Medium',
        'Low'
    );
```

---

## 36. Count tickets by category

```sql
SELECT
    category,
    COUNT(*) AS ticket_count
FROM tickets
GROUP BY category
ORDER BY ticket_count DESC;
```

---

## 37. Count tickets by department

```sql
SELECT
    d.department_name,
    COUNT(t.ticket_id) AS ticket_count
FROM departments AS d
LEFT JOIN tickets AS t
    ON d.department_id = t.department_id
GROUP BY
    d.department_id,
    d.department_name
ORDER BY ticket_count DESC;
```

`COUNT(t.ticket_id)` correctly returns zero for departments without tickets.

---

## 38. Count open tickets by department

```sql
SELECT
    d.department_name,
    COUNT(t.ticket_id) AS open_ticket_count
FROM departments AS d
LEFT JOIN tickets AS t
    ON d.department_id = t.department_id
   AND t.status = 'Open'
GROUP BY
    d.department_id,
    d.department_name
ORDER BY open_ticket_count DESC;
```

The status condition is placed inside `ON` so departments with zero open tickets remain visible.

---

## 39. Count tickets assigned to each technician

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

---

## 40. Count unresolved tickets per technician

```sql
SELECT
    tech.technician_name,
    COUNT(t.ticket_id) AS unresolved_ticket_count
FROM technicians AS tech
LEFT JOIN tickets AS t
    ON tech.technician_id = t.assigned_to
   AND t.status <> 'Closed'
GROUP BY
    tech.technician_id,
    tech.technician_name
ORDER BY unresolved_ticket_count DESC;
```

---

## 41. Calculate average resolution time

```sql
SELECT
    ROUND(AVG(resolution_minutes), 2)
        AS average_resolution_minutes
FROM tickets
WHERE status = 'Closed';
```

---

## 42. Calculate resolution time by technician

```sql
SELECT
    tech.technician_name,
    COUNT(t.ticket_id) AS closed_ticket_count,
    ROUND(AVG(t.resolution_minutes), 2)
        AS average_resolution_minutes
FROM technicians AS tech
LEFT JOIN tickets AS t
    ON tech.technician_id = t.assigned_to
   AND t.status = 'Closed'
GROUP BY
    tech.technician_id,
    tech.technician_name
ORDER BY average_resolution_minutes ASC;
```

---

## 43. Find the fastest resolved ticket

```sql
SELECT
    ticket_id,
    issue,
    resolution_minutes
FROM tickets
WHERE status = 'Closed'
ORDER BY resolution_minutes ASC
LIMIT 1;
```

---

## 44. Find the longest resolved ticket

```sql
SELECT
    ticket_id,
    issue,
    resolution_minutes
FROM tickets
WHERE status = 'Closed'
ORDER BY resolution_minutes DESC
LIMIT 1;
```

---

## 45. Find technicians with more than one assigned ticket

```sql
SELECT
    tech.technician_name,
    COUNT(t.ticket_id) AS assigned_ticket_count
FROM technicians AS tech
INNER JOIN tickets AS t
    ON tech.technician_id = t.assigned_to
GROUP BY
    tech.technician_id,
    tech.technician_name
HAVING COUNT(t.ticket_id) > 1;
```

---

# Login investigation queries

## 46. Display failed login attempts

```sql
SELECT
    login_id,
    user_id,
    ip_address,
    attempted_at,
    failure_reason
FROM login_attempts
WHERE login_status = 'Failed'
ORDER BY attempted_at DESC;
```

---

## 47. Display failed logins with usernames

```sql
SELECT
    l.login_id,
    u.username,
    l.ip_address,
    l.attempted_at,
    l.failure_reason
FROM login_attempts AS l
INNER JOIN application_users AS u
    ON l.user_id = u.user_id
WHERE l.login_status = 'Failed'
ORDER BY l.attempted_at DESC;
```

---

## 48. Count failed logins by user

```sql
SELECT
    u.user_id,
    u.username,
    COUNT(l.login_id) AS failed_login_count
FROM application_users AS u
INNER JOIN login_attempts AS l
    ON u.user_id = l.user_id
WHERE l.login_status = 'Failed'
GROUP BY
    u.user_id,
    u.username
ORDER BY failed_login_count DESC;
```

---

## 49. Find users with at least three failed logins

```sql
SELECT
    u.user_id,
    u.username,
    COUNT(l.login_id) AS failed_login_count
FROM application_users AS u
INNER JOIN login_attempts AS l
    ON u.user_id = l.user_id
WHERE l.login_status = 'Failed'
GROUP BY
    u.user_id,
    u.username
HAVING COUNT(l.login_id) >= 3;
```

---

## 50. Count failed logins by IP address

```sql
SELECT
    ip_address,
    COUNT(*) AS failed_attempt_count
FROM login_attempts
WHERE login_status = 'Failed'
GROUP BY ip_address
ORDER BY failed_attempt_count DESC;
```

---

## 51. Find suspicious IP addresses

```sql
SELECT
    ip_address,
    COUNT(*) AS failed_attempt_count
FROM login_attempts
WHERE login_status = 'Failed'
GROUP BY ip_address
HAVING COUNT(*) >= 3
ORDER BY failed_attempt_count DESC;
```

This is a basic investigation query, not a complete security-detection system. One query does not make a Security Operations Centre, despite what certain course advertisements imply.

---

## 52. Find users with no successful login

```sql
SELECT
    u.user_id,
    u.username,
    u.full_name
FROM application_users AS u
WHERE NOT EXISTS (
    SELECT 1
    FROM login_attempts AS l
    WHERE l.user_id = u.user_id
      AND l.login_status = 'Successful'
);
```

---

## 53. Find the latest login attempt for each user

```sql
SELECT
    l.user_id,
    u.username,
    l.login_status,
    l.ip_address,
    l.attempted_at
FROM login_attempts AS l
INNER JOIN application_users AS u
    ON l.user_id = u.user_id
WHERE l.attempted_at = (
    SELECT MAX(l2.attempted_at)
    FROM login_attempts AS l2
    WHERE l2.user_id = l.user_id
);
```

---

# Ticket-comment queries

## 54. Display ticket comments

```sql
SELECT
    ticket_id,
    comment_text,
    commented_by,
    created_at
FROM ticket_comments
ORDER BY created_at;
```

---

## 55. Display comments for one ticket

```sql
SELECT
    comment_id,
    comment_text,
    commented_by,
    created_at
FROM ticket_comments
WHERE ticket_id = 1
ORDER BY created_at;
```

---

## 56. Count comments per ticket

```sql
SELECT
    t.ticket_id,
    t.issue,
    COUNT(c.comment_id) AS comment_count
FROM tickets AS t
LEFT JOIN ticket_comments AS c
    ON t.ticket_id = c.ticket_id
GROUP BY
    t.ticket_id,
    t.issue
ORDER BY comment_count DESC;
```

---

## 57. Find tickets without comments

```sql
SELECT
    t.ticket_id,
    t.issue,
    t.status,
    t.created_at
FROM tickets AS t
LEFT JOIN ticket_comments AS c
    ON t.ticket_id = c.ticket_id
WHERE c.comment_id IS NULL;
```

---

## 58. Find unresolved tickets without comments

```sql
SELECT
    t.ticket_id,
    t.issue,
    t.priority,
    t.status,
    t.created_at
FROM tickets AS t
LEFT JOIN ticket_comments AS c
    ON t.ticket_id = c.ticket_id
WHERE c.comment_id IS NULL
  AND t.status <> 'Closed';
```

These tickets may require follow-up because no support activity has been documented.

---

## 59. Find the latest comment for each ticket

```sql
SELECT
    c.ticket_id,
    c.comment_text,
    c.commented_by,
    c.created_at
FROM ticket_comments AS c
WHERE c.created_at = (
    SELECT MAX(c2.created_at)
    FROM ticket_comments AS c2
    WHERE c2.ticket_id = c.ticket_id
);
```

---

# Subquery-based investigations

## 60. Find users who created more tickets than average

```sql
SELECT
    user_id,
    COUNT(*) AS ticket_count
FROM tickets
GROUP BY user_id
HAVING COUNT(*) > (
    SELECT AVG(user_ticket_count)
    FROM (
        SELECT
            COUNT(*) AS user_ticket_count
        FROM tickets
        GROUP BY user_id
    ) AS ticket_counts
);
```

---

## 61. Find tickets with above-average resolution time

```sql
SELECT
    ticket_id,
    issue,
    resolution_minutes
FROM tickets
WHERE resolution_minutes > (
    SELECT AVG(resolution_minutes)
    FROM tickets
    WHERE resolution_minutes IS NOT NULL
);
```

---

## 62. Find the technician with the most assigned tickets

```sql
SELECT
    tech.technician_name,
    COUNT(t.ticket_id) AS assigned_ticket_count
FROM technicians AS tech
INNER JOIN tickets AS t
    ON tech.technician_id = t.assigned_to
GROUP BY
    tech.technician_id,
    tech.technician_name
ORDER BY assigned_ticket_count DESC
LIMIT 1;
```

---

## 63. Find all technicians tied for the highest ticket count

```sql
SELECT
    technician_id,
    assigned_ticket_count
FROM (
    SELECT
        assigned_to AS technician_id,
        COUNT(*) AS assigned_ticket_count
    FROM tickets
    WHERE assigned_to IS NOT NULL
    GROUP BY assigned_to
) AS technician_counts
WHERE assigned_ticket_count = (
    SELECT MAX(ticket_total)
    FROM (
        SELECT
            COUNT(*) AS ticket_total
        FROM tickets
        WHERE assigned_to IS NOT NULL
        GROUP BY assigned_to
    ) AS totals
);
```

---

## 64. Find users with open tickets

```sql
SELECT
    u.user_id,
    u.username,
    u.full_name
FROM application_users AS u
WHERE EXISTS (
    SELECT 1
    FROM tickets AS t
    WHERE t.user_id = u.user_id
      AND t.status = 'Open'
);
```

---

## 65. Find users without unresolved tickets

```sql
SELECT
    u.user_id,
    u.username,
    u.full_name
FROM application_users AS u
WHERE NOT EXISTS (
    SELECT 1
    FROM tickets AS t
    WHERE t.user_id = u.user_id
      AND t.status <> 'Closed'
);
```

---

# Data-quality queries

## 66. Find tickets without departments

```sql
SELECT
    ticket_id,
    issue,
    status
FROM tickets
WHERE department_id IS NULL;
```

---

## 67. Find users without departments

```sql
SELECT
    user_id,
    username,
    full_name
FROM application_users
WHERE department_id IS NULL;
```

---

## 68. Find tickets marked closed without a closing date

```sql
SELECT
    ticket_id,
    issue,
    status,
    closed_at
FROM tickets
WHERE status = 'Closed'
  AND closed_at IS NULL;
```

---

## 69. Find open tickets with a closing date

```sql
SELECT
    ticket_id,
    issue,
    status,
    closed_at
FROM tickets
WHERE status <> 'Closed'
  AND closed_at IS NOT NULL;
```

---

## 70. Find closed tickets without resolution time

```sql
SELECT
    ticket_id,
    issue,
    closed_at,
    resolution_minutes
FROM tickets
WHERE status = 'Closed'
  AND resolution_minutes IS NULL;
```

---

## 71. Find tickets assigned to unavailable technicians

```sql
SELECT
    t.ticket_id,
    t.issue,
    t.status,
    tech.technician_name,
    tech.technician_status
FROM tickets AS t
INNER JOIN technicians AS tech
    ON t.assigned_to = tech.technician_id
WHERE tech.technician_status IN ('Leave', 'Inactive');
```

---

## 72. Find duplicate user email addresses

```sql
SELECT
    email,
    COUNT(*) AS duplicate_count
FROM application_users
WHERE email IS NOT NULL
GROUP BY email
HAVING COUNT(*) > 1;
```

A unique constraint should normally prevent this.

---

## 73. Find duplicate usernames

```sql
SELECT
    username,
    COUNT(*) AS duplicate_count
FROM application_users
GROUP BY username
HAVING COUNT(*) > 1;
```

---

## 74. Find inconsistent locked accounts

```sql
SELECT
    user_id,
    username,
    account_status,
    failed_login_count
FROM application_users
WHERE account_status = 'Locked'
  AND failed_login_count = 0;
```

---

## 75. Find users with high failure counts but active accounts

```sql
SELECT
    user_id,
    username,
    account_status,
    failed_login_count
FROM application_users
WHERE account_status = 'Active'
  AND failed_login_count >= 3;
```

---

# Safe data-modification queries

## Safety rule

Before using `UPDATE` or `DELETE`:

1. Run the condition using `SELECT`.
2. Check the matching records.
3. Start a transaction.
4. Perform the modification.
5. Verify the result.
6. Commit or roll back.

---

## 76. Assign an unassigned ticket

Check the ticket:

```sql
SELECT *
FROM tickets
WHERE ticket_id = 3
  AND assigned_to IS NULL;
```

Update inside a transaction:

```sql
START TRANSACTION;

UPDATE tickets
SET
    assigned_to = 3,
    status = 'In Progress',
    updated_at = CURRENT_TIMESTAMP
WHERE ticket_id = 3
  AND assigned_to IS NULL;

SELECT *
FROM tickets
WHERE ticket_id = 3;
```

Save the change:

```sql
COMMIT;
```

Cancel the change:

```sql
ROLLBACK;
```

---

## 77. Unlock a user account

Check the user:

```sql
SELECT
    user_id,
    username,
    account_status,
    failed_login_count
FROM application_users
WHERE user_id = 2;
```

Update:

```sql
START TRANSACTION;

UPDATE application_users
SET
    account_status = 'Active',
    failed_login_count = 0
WHERE user_id = 2
  AND account_status = 'Locked';

SELECT
    user_id,
    username,
    account_status,
    failed_login_count
FROM application_users
WHERE user_id = 2;

COMMIT;
```

---

## 78. Close a resolved ticket

```sql
START TRANSACTION;

UPDATE tickets
SET
    status = 'Closed',
    closed_at = CURRENT_TIMESTAMP,
    updated_at = CURRENT_TIMESTAMP,
    resolution_minutes = TIMESTAMPDIFF(
        MINUTE,
        created_at,
        CURRENT_TIMESTAMP
    )
WHERE ticket_id = 1
  AND status <> 'Closed';

SELECT
    ticket_id,
    status,
    created_at,
    closed_at,
    resolution_minutes
FROM tickets
WHERE ticket_id = 1;

COMMIT;
```

---

## 79. Add a ticket comment

```sql
INSERT INTO ticket_comments (
    ticket_id,
    comment_text,
    commented_by
)
VALUES (
    1,
    'VPN configuration updated and user asked to test again.',
    'Kumar'
);
```

---

## 80. Delete an accidental test comment

Verify first:

```sql
SELECT *
FROM ticket_comments
WHERE comment_id = 999
  AND comment_text LIKE '%test%';
```

Delete inside a transaction:

```sql
START TRANSACTION;

DELETE FROM ticket_comments
WHERE comment_id = 999
  AND comment_text LIKE '%test%';

COMMIT;
```

Using both the ID and content condition reduces the chance of deleting the wrong record.

---

# Support report queries

## 81. Daily ticket summary

```sql
SELECT
    DATE(created_at) AS ticket_date,
    COUNT(*) AS total_tickets,
    SUM(status = 'Open') AS open_tickets,
    SUM(status = 'In Progress') AS in_progress_tickets,
    SUM(status = 'Closed') AS closed_tickets
FROM tickets
GROUP BY DATE(created_at)
ORDER BY ticket_date;
```

This uses MySQL Boolean expressions inside `SUM()`.

---

## 82. Portable daily summary using CASE

```sql
SELECT
    DATE(created_at) AS ticket_date,
    COUNT(*) AS total_tickets,
    SUM(
        CASE
            WHEN status = 'Open' THEN 1
            ELSE 0
        END
    ) AS open_tickets,
    SUM(
        CASE
            WHEN status = 'In Progress' THEN 1
            ELSE 0
        END
    ) AS in_progress_tickets,
    SUM(
        CASE
            WHEN status = 'Closed' THEN 1
            ELSE 0
        END
    ) AS closed_tickets
FROM tickets
GROUP BY DATE(created_at)
ORDER BY ticket_date;
```

---

## 83. Technician performance summary

```sql
SELECT
    tech.technician_name,
    COUNT(t.ticket_id) AS total_assigned_tickets,
    SUM(
        CASE
            WHEN t.status = 'Closed' THEN 1
            ELSE 0
        END
    ) AS closed_tickets,
    SUM(
        CASE
            WHEN t.status <> 'Closed' THEN 1
            ELSE 0
        END
    ) AS unresolved_tickets,
    ROUND(
        AVG(
            CASE
                WHEN t.status = 'Closed'
                THEN t.resolution_minutes
            END
        ),
        2
    ) AS average_resolution_minutes
FROM technicians AS tech
LEFT JOIN tickets AS t
    ON tech.technician_id = t.assigned_to
GROUP BY
    tech.technician_id,
    tech.technician_name
ORDER BY closed_tickets DESC;
```

Ticket count alone does not prove technician quality. Complexity, severity, customer response time, and assignment differences also matter.

---

## 84. Department support summary

```sql
SELECT
    d.department_name,
    COUNT(t.ticket_id) AS total_tickets,
    SUM(
        CASE
            WHEN t.status <> 'Closed' THEN 1
            ELSE 0
        END
    ) AS unresolved_tickets,
    SUM(
        CASE
            WHEN t.priority IN ('Critical', 'High')
            THEN 1
            ELSE 0
        END
    ) AS urgent_tickets
FROM departments AS d
LEFT JOIN tickets AS t
    ON d.department_id = t.department_id
GROUP BY
    d.department_id,
    d.department_name
ORDER BY total_tickets DESC;
```

---

## 85. Current workload report

```sql
SELECT
    tech.technician_name,
    tech.technician_status,
    COUNT(t.ticket_id) AS unresolved_ticket_count
FROM technicians AS tech
LEFT JOIN tickets AS t
    ON tech.technician_id = t.assigned_to
   AND t.status <> 'Closed'
GROUP BY
    tech.technician_id,
    tech.technician_name,
    tech.technician_status
ORDER BY unresolved_ticket_count DESC;
```

---

## 86. Critical and high-priority backlog

```sql
SELECT
    t.ticket_id,
    t.issue,
    t.priority,
    t.status,
    tech.technician_name,
    t.created_at
FROM tickets AS t
LEFT JOIN technicians AS tech
    ON t.assigned_to = tech.technician_id
WHERE t.status <> 'Closed'
  AND t.priority IN ('Critical', 'High')
ORDER BY
    FIELD(t.priority, 'Critical', 'High'),
    t.created_at ASC;
```

---

## 87. Tickets with no update for more than one day

```sql
SELECT
    ticket_id,
    issue,
    priority,
    status,
    updated_at
FROM tickets
WHERE status <> 'Closed'
  AND updated_at < CURRENT_TIMESTAMP - INTERVAL 1 DAY
ORDER BY updated_at ASC;
```

---

## 88. Tickets open for more than two days

```sql
SELECT
    ticket_id,
    issue,
    priority,
    status,
    created_at,
    TIMESTAMPDIFF(
        HOUR,
        created_at,
        CURRENT_TIMESTAMP
    ) AS open_hours
FROM tickets
WHERE status <> 'Closed'
  AND created_at < CURRENT_TIMESTAMP - INTERVAL 2 DAY
ORDER BY created_at ASC;
```

---

## 89. Resolution compliance example

Assume the target resolution time is:

| Priority |       Target |
| -------- | -----------: |
| Critical |  120 minutes |
| High     |  240 minutes |
| Medium   |  480 minutes |
| Low      | 1440 minutes |

Query:

```sql
SELECT
    ticket_id,
    issue,
    priority,
    resolution_minutes,
    CASE
        WHEN priority = 'Critical'
             AND resolution_minutes <= 120
            THEN 'Within target'
        WHEN priority = 'High'
             AND resolution_minutes <= 240
            THEN 'Within target'
        WHEN priority = 'Medium'
             AND resolution_minutes <= 480
            THEN 'Within target'
        WHEN priority = 'Low'
             AND resolution_minutes <= 1440
            THEN 'Within target'
        ELSE 'Target missed'
    END AS resolution_result
FROM tickets
WHERE status = 'Closed';
```

This is a simplified service-level example.

Actual service-level rules may exclude:

* Waiting for customer time
* Non-working hours
* Holidays
* Paused tickets
* Vendor delays

---

# Common mistakes

## Mistake 1: Using SELECT * in every report

Weak:

```sql
SELECT *
FROM tickets;
```

Better:

```sql
SELECT
    ticket_id,
    issue,
    priority,
    status
FROM tickets;
```

Retrieve only the required columns.

---

## Mistake 2: Using INNER JOIN for unassigned tickets

Wrong:

```sql
SELECT
    t.ticket_id,
    tech.technician_name
FROM tickets AS t
INNER JOIN technicians AS tech
    ON t.assigned_to = tech.technician_id;
```

Unassigned tickets disappear.

Correct:

```sql
SELECT
    t.ticket_id,
    tech.technician_name
FROM tickets AS t
LEFT JOIN technicians AS tech
    ON t.assigned_to = tech.technician_id;
```

---

## Mistake 3: Using COUNT(*) after LEFT JOIN

Potentially misleading:

```sql
SELECT
    tech.technician_name,
    COUNT(*) AS ticket_count
FROM technicians AS tech
LEFT JOIN tickets AS t
    ON tech.technician_id = t.assigned_to
GROUP BY tech.technician_name;
```

Better:

```sql
SELECT
    tech.technician_name,
    COUNT(t.ticket_id) AS ticket_count
FROM technicians AS tech
LEFT JOIN tickets AS t
    ON tech.technician_id = t.assigned_to
GROUP BY
    tech.technician_id,
    tech.technician_name;
```

---

## Mistake 4: Filtering the right table in WHERE

This removes technicians with no tickets:

```sql
SELECT
    tech.technician_name,
    COUNT(t.ticket_id)
FROM technicians AS tech
LEFT JOIN tickets AS t
    ON tech.technician_id = t.assigned_to
WHERE t.status = 'Closed'
GROUP BY tech.technician_name;
```

Better:

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

## Mistake 5: Updating without verifying

Dangerous:

```sql
UPDATE tickets
SET status = 'Closed'
WHERE priority = 'Low';
```

First inspect:

```sql
SELECT *
FROM tickets
WHERE priority = 'Low';
```

Priority alone does not prove that a ticket is resolved.

---

## Mistake 6: Deleting ticket history

Deleting old tickets may destroy:

* Audit history
* Performance data
* User issue history
* Compliance evidence
* Trend information

Closed tickets should normally be archived according to company policy rather than casually deleted.

---

## Mistake 7: Treating NULL as normal text

Wrong:

```sql
WHERE assigned_to = 'NULL'
```

Correct:

```sql
WHERE assigned_to IS NULL
```

---

## Mistake 8: Assuming high ticket count means poor performance

A technician may receive more tickets because they:

* Handle difficult incidents
* Work longer shifts
* Support more systems
* Have stronger skills
* Receive escalations

SQL provides numbers. Interpretation still requires business context, an inconvenience that dashboards frequently conceal.

---

## Quick recall notes

* Use `WHERE` to filter support records.
* Use `ORDER BY created_at DESC` for recent tickets.
* Use `ORDER BY created_at ASC` for oldest backlog.
* Use `LEFT JOIN` to preserve unassigned records.
* Use `LEFT JOIN` with `IS NULL` to find missing relationships.
* Use `COUNT(ticket_id)` after a left join.
* Use `GROUP BY` for status, category, and technician summaries.
* Use `HAVING` to filter aggregated results.
* Use `EXISTS` to check whether related records exist.
* Use `NOT EXISTS` to find records without activity.
* Use `CASE` for conditional reporting.
* Use `TIMESTAMPDIFF()` for time calculations in MySQL.
* Test every `UPDATE` and `DELETE` condition with `SELECT`.
* Use transactions for important modifications.
* Check the affected-row count.
* SQL results require business context before decisions are made.

---

## Interview questions

### 1. How do you find all open tickets?

```sql
SELECT *
FROM tickets
WHERE status = 'Open';
```

### 2. How do you find unassigned tickets?

```sql
SELECT
    t.ticket_id,
    t.issue
FROM tickets AS t
LEFT JOIN technicians AS tech
    ON t.assigned_to = tech.technician_id
WHERE tech.technician_id IS NULL;
```

### 3. How do you count tickets by status?

```sql
SELECT
    status,
    COUNT(*) AS ticket_count
FROM tickets
GROUP BY status;
```

### 4. How do you find the oldest open ticket?

```sql
SELECT
    ticket_id,
    issue,
    created_at
FROM tickets
WHERE status = 'Open'
ORDER BY created_at ASC
LIMIT 1;
```

### 5. How do you find users with locked accounts?

```sql
SELECT
    user_id,
    username,
    failed_login_count
FROM application_users
WHERE account_status = 'Locked';
```

### 6. How do you find users with three or more failed logins?

```sql
SELECT
    user_id,
    COUNT(*) AS failed_login_count
FROM login_attempts
WHERE login_status = 'Failed'
GROUP BY user_id
HAVING COUNT(*) >= 3;
```

### 7. How do you find tickets without comments?

```sql
SELECT
    t.ticket_id,
    t.issue
FROM tickets AS t
LEFT JOIN ticket_comments AS c
    ON t.ticket_id = c.ticket_id
WHERE c.comment_id IS NULL;
```

### 8. How do you calculate average ticket resolution time?

```sql
SELECT
    AVG(resolution_minutes)
FROM tickets
WHERE status = 'Closed';
```

### 9. How do you calculate technician workload?

```sql
SELECT
    tech.technician_name,
    COUNT(t.ticket_id) AS ticket_count
FROM technicians AS tech
LEFT JOIN tickets AS t
    ON tech.technician_id = t.assigned_to
   AND t.status <> 'Closed'
GROUP BY
    tech.technician_id,
    tech.technician_name;
```

### 10. Why is LEFT JOIN used for workload reports?

It preserves technicians who currently have zero matching tickets.

### 11. Why should COUNT(ticket_id) be used instead of COUNT(*) after a left join?

`COUNT(ticket_id)` ignores unmatched `NULL` rows and correctly returns zero.

### 12. How do you find users who never logged in successfully?

```sql
SELECT
    u.user_id,
    u.username
FROM application_users AS u
WHERE NOT EXISTS (
    SELECT 1
    FROM login_attempts AS l
    WHERE l.user_id = u.user_id
      AND l.login_status = 'Successful'
);
```

### 13. How do you find the latest ticket comment?

```sql
SELECT *
FROM ticket_comments
WHERE ticket_id = 1
ORDER BY created_at DESC
LIMIT 1;
```

### 14. How do you find closed tickets without resolution time?

```sql
SELECT *
FROM tickets
WHERE status = 'Closed'
  AND resolution_minutes IS NULL;
```

### 15. How do you safely update a ticket?

```text
1. Select and verify the ticket.
2. Start a transaction.
3. Run the update.
4. Verify the changed record.
5. Commit or roll back.
```

### 16. Why should old support tickets not be deleted casually?

They may be required for auditing, reporting, troubleshooting history, performance analysis, and compliance.

### 17. How can SQL help application-support teams?

SQL can help teams:

* Verify user records
* Investigate failed logins
* Locate tickets
* Find missing relationships
* Measure ticket workload
* Detect inconsistent data
* Prepare support reports
* Safely correct application records

### 18. What should be checked before trusting a support report?

Check:

* Join conditions
* Date filters
* Status definitions
* Missing values
* Duplicate records
* Business rules
* Row counts
* Data freshness

### 19. Does a high ticket count automatically mean poor service?

No. Ticket volume must be interpreted with ticket complexity, assignment method, priority, work hours, and resolution quality.

### 20. What is the main value of SQL in helpdesk work?

SQL allows support staff to retrieve exact records, investigate incidents, verify application data, identify patterns, and produce operational evidence instead of relying on guesses.
