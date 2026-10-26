# SQL for Data Science with Python (IBM)

*Remark.* The snippets in these notes come from several SQL dialects:
- **MySQL:** the course uses MySQL, so most snippets are written in MySQL.
- **IBM Db2:** some course material uses Db2 syntax, e.g. `TRUNCATE TABLE ... IMMEDIATE`.
- **SQLite:** used in the Python part via the module `sqlite3`, e.g. the table `sqlite_master`.
- **PostgreSQL:** the database system used in the rest of this repository.

If a snippet does not work in PostgreSQL,
this is said before the snippet together with the dialect in which it works,
and the PostgreSQL version is shown right after it.
All differences are summarized in [Differences to PostgreSQL](#differences-to-postgresql).

## Basics

**SQL** = Structured Query Language.
It is a
- language for relational databases,
- query language.

**Data** = collection of facts, words, numbers, pictures.

**Database** = repository of data.
It should be
- secure,
- quickly accessible,
- able to store data in different types (tables, text, ...).

**DBMS** = Database Management System.

**RDBMS** = Relational Database Management System.

### Entity-Relationship Model

An RDBMS is based on the *Entity-Relationship Model*:
- *Entities* are objects, e.g. a table.
- *Attributes* tell us more about an entity.

**Example.**
`BOOK` is an entity
and might have the following attributes:
- Title,
- Editor,
- Year,
- Price.

### Primary and foreign keys

- Each table has a *primary key* to uniquely identify each row of the table.
- A *foreign key* is a primary key of a different table of the relational database.

## Types of SQL statements

1) **DDL** = Data Definition Language statements.
   Used to define, change and drop data (structures):
   `CREATE`, `ALTER`, `TRUNCATE`, `DROP`.
2) **DML** = Data Manipulation Language statements.
   Used to modify data, i.e. the **CRUD** operations (create, read, update and delete rows):
   `INSERT`, `SELECT`, `UPDATE`, `DELETE`.

**SQL scripts** = series of commands that can be executed on an SQL server.

## DDL: Creating and changing tables

### `CREATE TABLE`

```sql
CREATE TABLE table_name (
    column_name_1 datatype optional_parameter,
    column_name_2 datatype optional_parameter,
    ...
);
```

- *Data types*, e.g. `CHAR(2)`, `VARCHAR(24)`, `BIGINT`.
- *Optional parameters* (= constraints), e.g.
  - `PRIMARY KEY`,
  - `NOT NULL`,
  - `UNIQUE`: no duplicates exist.

### `ALTER`, `DROP`, `TRUNCATE`

`ALTER TABLE` adds, modifies or removes columns:
```sql
-- add a column
ALTER TABLE table_name ADD COLUMN column_name datatype;
-- or shorter
ALTER TABLE table_name ADD column_name datatype;

-- remove a column
ALTER TABLE table_name DROP COLUMN column_name;
```

Modify a column (e.g. change its data type).
*Works only in MySQL, not in PostgreSQL:*
```sql
ALTER TABLE table_name MODIFY column_name datatype;
```
In PostgreSQL:
```sql
ALTER TABLE table_name ALTER COLUMN column_name TYPE datatype;
```

Delete a table from the database:
```sql
DROP TABLE table_name;
-- or
DROP TABLE IF EXISTS table_name;
```
If the table does not exist, `DROP TABLE table_name;` raises an *error*
(PostgreSQL: `ERROR: table "table_name" does not exist`).
In many tools an error stops the whole script
(e.g. a `%%sql` cell in Jupyter or `psql -v ON_ERROR_STOP=1 -f script.sql`),
and inside a transaction it aborts the transaction: all following statements are ignored until `ROLLBACK`.
With `IF EXISTS` the database only gives a *notice* (`table "table_name" does not exist, skipping`)
and continues with the next statement.

*Advantage.* Scripts which start with `DROP TABLE IF EXISTS ...; CREATE TABLE ...;` can be run again and again,
no matter whether the table already exists or not.
This is what we do in the notebooks of [leet-challenges](leet-challenges/).
`IF EXISTS` works in PostgreSQL and MySQL.

Delete all rows of a table (the table itself remains):
```sql
TRUNCATE TABLE table_name;
```
*The course uses IBM Db2 syntax here, which does not work in PostgreSQL (nor in MySQL):*
```sql
TRUNCATE TABLE table_name IMMEDIATE;
```
In Db2, `IMMEDIATE` means that this cannot be undone.
In PostgreSQL `TRUNCATE TABLE table_name;` (as above) is enough.
It can even be undone with `ROLLBACK` if it is run inside a transaction (see [ACID transactions](#acid-transactions)).

### Data integrity

- *Entity integrity:* every table needs a primary key identifying each row uniquely.
- *Referential integrity:* a foreign key in one table maps to a primary key in another table.
- *Domain integrity:* well-defined data type, format, value and nullability,
  ensured e.g. by the constraints `CHECK` and `NOT NULL`.

**Example.** (similar to [SQL and Bash](5-sql-and-bash.md), see also [Primary and foreign keys](3-sql-and-postgresql.md#primary-and-foreign-keys))
```sql
CREATE TABLE majors (
    major_id SERIAL PRIMARY KEY,
    major VARCHAR(50) NOT NULL UNIQUE
);

CREATE TABLE students (
    student_id SERIAL PRIMARY KEY,
    first_name VARCHAR(50) NOT NULL,
    last_name VARCHAR(50) NOT NULL,
    major_id INTEGER REFERENCES majors(major_id),
    gpa NUMERIC(2,1) CHECK (gpa BETWEEN 0 AND 4)
);
```
- *Primary key:* `PRIMARY KEY` after a column makes it the primary key.
  The values must be unique and cannot be `NULL`.
  `SERIAL` lets PostgreSQL number the rows automatically (MySQL: `INT AUTO_INCREMENT`).
- *Foreign key:* `REFERENCES majors(major_id)` makes `students.major_id` a foreign key.
  Every value must be an existing `major_id` of `majors` or `NULL`.
- *`CHECK`:* the condition in brackets must hold for every row, here the GPA lies between 0 and 4.
  A row for which the condition is `NULL` (e.g. `gpa` is `NULL`) is accepted.

The database rejects every row which violates a constraint:
```sql
INSERT INTO majors (major) VALUES ('Data Science'), ('Mathematics');

INSERT INTO students (first_name, last_name, major_id, gpa) VALUES ('Ada', 'Lovelace', 2, 3.9);    -- ok
INSERT INTO students (first_name, last_name, major_id, gpa) VALUES ('Alan', 'Turing', 7, 3.5);     -- error: major_id 7 does not exist
INSERT INTO students (first_name, last_name, major_id, gpa) VALUES ('Grace', 'Hopper', 1, 4.5);    -- error: violates CHECK
INSERT INTO majors (major) VALUES ('Mathematics');                                                 -- error: violates UNIQUE
```
The foreign key also protects the referenced table:
`DELETE FROM majors WHERE major_id = 2;` and `DROP TABLE majors;` fail as long as a student refers to it
(`DROP TABLE majors CASCADE;` would also remove the foreign key constraint).

Primary keys consisting of several columns, foreign keys and `CHECK` constraints can also be written
after the columns as *table constraints*.
This is necessary if the constraint involves several columns.
With `CONSTRAINT name` you can give the constraint a name which then appears in the error messages:
```sql
CREATE TABLE majors_courses (
    major_id INTEGER REFERENCES majors(major_id),
    course_id INTEGER,
    PRIMARY KEY (major_id, course_id),
    FOREIGN KEY (course_id) REFERENCES courses(course_id)
);

CREATE TABLE loans (
    loan_id SERIAL PRIMARY KEY,
    loan_date DATE NOT NULL,
    return_date DATE,
    CONSTRAINT valid_period CHECK (return_date >= loan_date)
);
```
(`majors_courses` needs a table `courses` with primary key `course_id`.)
Constraints can be added to an existing table, too:
```sql
ALTER TABLE students ADD CONSTRAINT gpa_positive CHECK (gpa > 0);
```

## DML: Working with data

Recall: **DML** = Data Manipulation Language.
These statements work with the rows (the data) of the tables, i.e. the CRUD operations:
`INSERT` (create), `SELECT` (read), `UPDATE` and `DELETE`.

### `INSERT`

Populates a table:
```sql
INSERT INTO table_name (column_name_1, column_name_2, ...)
VALUES (value_1, value_2, ...), (value_1, value_2, ...), ...;
```

### `SELECT`

- `SELECT` statement = *query*.
- The output of a query = *result set* or *result table*.

```sql
SELECT * FROM table_name;
SELECT column_name_1, column_name_2, ... FROM table_name;
```

**`WHERE`**

`WHERE` + predicate filters rows, e.g.
```sql
SELECT * FROM table_name WHERE name = 'John';
```
Comparison operators: `=`, `>`, `<`, `<=`, `>=`, `<>` (= not equal to).

**`COUNT`**

Built-in database function which retrieves the number of rows:
```sql
SELECT COUNT(COUNTRY) FROM MEDALS;
```
The output is a number.

**`DISTINCT`**

Removes duplicate values, e.g. `SELECT DISTINCT country FROM MEDALS;`.

**`LIMIT`**

Restricts the number of rows to display:
```sql
SELECT * FROM table_name LIMIT 10;
```
To query within a list with `LIMIT` set use `OFFSET`:
```sql
SELECT * FROM table_name LIMIT 3 OFFSET 5;
```
This skips the first 5 rows and returns rows 6, 7, 8.

### `UPDATE`

Alters rows of a table:
```sql
UPDATE table_name SET column_name = value WHERE condition;
```

### `DELETE`

```sql
DELETE FROM table_name WHERE condition;
```
If you don't specify a `WHERE` condition, *all* rows will be removed!

## Refining query results

### String patterns, ranges and sets

Make it easier to retrieve data.

**`LIKE`** is a predicate which allows you to look for a pattern.
The `%` is a *wildcard character* replacing missing letters:
```sql
SELECT * FROM table_name WHERE firstname LIKE 'R%';
```
This returns all rows where the first name starts with `R`.
- `_` replaces exactly one character, e.g. `LIKE 'R_v'` matches `Rav` but not `Raul`.
- In PostgreSQL `LIKE` is case-sensitive, `ILIKE` is the case-insensitive version (in MySQL `LIKE` is case-insensitive by default).
  `NOT LIKE` and `NOT ILIKE` return the opposite (see also [SQL and Bash](5-sql-and-bash.md)).

**Working with strings.**
In [Fix Names in a Table](leet-challenges/7-advanced-regex/1667-fix-names-in-a-table.ipynb)
strings are cut into pieces and concatenated again with `||`:
```sql
SELECT user_id, UPPER(LEFT(name, 1)) || LOWER(RIGHT(name, LENGTH(name) - 1)) AS name
FROM Users ORDER BY user_id;
```
- `LEFT(name, n)` / `RIGHT(name, n)`: first / last `n` characters,
  `SUBSTRING(name, 2)`: everything from the second character on.
- `UPPER`, `LOWER`: upper- and lowercase, `LENGTH`: number of characters.
- `||` concatenates strings, e.g. `'mAriE ANN'` becomes `'M' || 'arie ann' = 'Marie ann'`.
- `CONCAT('a', 'b', ...)` concatenates, too.
  Difference: `'Hello' || NULL` is `NULL`, whereas `CONCAT('Hello', NULL)` is `'Hello'`.

*`||` works in PostgreSQL, SQLite and Db2, but in MySQL `||` means logical `OR` by default.*
In MySQL use `CONCAT`, which also works in PostgreSQL:
```sql
SELECT user_id, CONCAT(UPPER(LEFT(name, 1)), LOWER(SUBSTRING(name, 2))) AS name
FROM Users ORDER BY user_id;
```
*Remark.* PostgreSQL also has `INITCAP(name)`, but it capitalizes *every* word: `'mAriE ANN'` becomes `'Marie Ann'`.

**Regular expressions.**
`LIKE` only knows the wildcards `%` and `_`.
For more complex patterns PostgreSQL has regular expressions (POSIX):
- `~` matches a regular expression (case-sensitive), `~*` case-insensitive,
  `!~` and `!~*` are the negations.
- `SIMILAR TO` is a mixture of `LIKE` and regular expressions, e.g. `firstname SIMILAR TO '(R|H)%'`.
- `REGEXP_REPLACE(string, pattern, replacement, 'g')` replaces all (`'g'` = global) matches.

```sql
-- first names starting with R
SELECT * FROM table_name WHERE firstname ~ '^R';
-- first names with exactly three letters, starting with r or R
SELECT * FROM table_name WHERE firstname ~* '^r[a-z]{2}$';
-- valid e-mail addresses: start with a letter, then letters, digits, _ . -
SELECT * FROM table_name WHERE mail ~ '^[A-Za-z][A-Za-z0-9_.-]*@leetcode\.com$';
-- replace multiple spaces by a single one
SELECT REGEXP_REPLACE(name, '\s+', ' ', 'g') FROM table_name;
```
*The operators `~`, `~*`, `!~`, `!~*` and `SIMILAR TO` only work in PostgreSQL.*
MySQL uses `REGEXP` (or `REGEXP_LIKE(string, pattern)`), e.g. `WHERE firstname REGEXP '^R'`.

**Ranges**
```sql
SELECT * FROM table_name WHERE pages >= 290 AND pages <= 300;
-- equivalent
SELECT * FROM table_name WHERE pages BETWEEN 290 AND 300;
```

**`IN`-operator**
```sql
SELECT * FROM table_name WHERE country IN ('...', '...', ...);
```

### Sorting

Use the `ORDER BY` clause:
- `ORDER BY title`: sorted by title, ascending by default.
- `ORDER BY title DESC`: sorted descending.
- `ORDER BY 2`: sorted by the second column; `2` is the column sequence number.

### Grouping results

**Example.** Display each country and the number of authors for each country:
```sql
SELECT country, COUNT(country) FROM Author GROUP BY country;
```
You can name the result column with `AS`: `COUNT(country) AS column_name`.

`HAVING` adds a condition to grouped results, i.e. it works like `WHERE` but only for `GROUP BY`.
- `WHERE` filters the *rows before* grouping,
- `HAVING` filters the *groups after* grouping and can therefore use aggregate functions.

**Example.** Countries with more than one author:
```sql
SELECT country, COUNT(country) AS num_authors
FROM Author
GROUP BY country
HAVING COUNT(country) > 1;
```
With `WHERE COUNT(country) > 1` instead you get `ERROR: aggregate functions are not allowed in WHERE`.

**Example (`WHERE` and `HAVING` together).** Countries with at least two authors born in 1970 or later:
```sql
SELECT country, COUNT(*) AS num_authors
FROM Author
WHERE birth_date >= '1970-01-01'
GROUP BY country
HAVING COUNT(*) >= 2
ORDER BY num_authors DESC;
```
The order of the clauses is `WHERE` -> `GROUP BY` -> `HAVING` -> `ORDER BY` -> `LIMIT`.

*MySQL also allows the alias in `HAVING`, e.g. `HAVING num_authors > 1`; this does not work in PostgreSQL*
(`ERROR: column "num_authors" does not exist`).
In PostgreSQL repeat the aggregate function: `HAVING COUNT(country) > 1`.

**Example from the leet-challenges.** In
[The Number of Employees Which Report to Each Employee](leet-challenges/5-advanced-select-and-joins/1731-the-number-of-employees-which-report-to-each-employee.ipynb)
the employees are grouped by their manager and the group of employees without a manager is removed:
```sql
SELECT reports_to AS manager_id, COUNT(*) AS reports_count, ROUND(AVG(age), 0) AS average_age
FROM Employees
GROUP BY reports_to
HAVING reports_to IS NOT NULL;
```
Since the condition `reports_to IS NOT NULL` contains no aggregate function,
`WHERE reports_to IS NOT NULL` (before `GROUP BY`) gives the same result here.

## Built-in database functions

*Goal.* Reduce network traffic and save bandwidth,
since the data is processed in the database instead of being sent to the application first.

There are aggregate and scalar functions.

### Aggregate functions

- *Input:* collection of values.
- *Output:* single value.

E.g. `SUM()`, `MIN()`, `MAX()`, `AVG()` (= mean).

```sql
SELECT SUM(column_name) FROM table_name;
SELECT MIN(ID) FROM table_name WHERE column_name = '...';
```

You can perform operations between columns:
```sql
SELECT AVG(COST / QUANTITY) FROM table_name WHERE ...;
```

**Integer division.**
*In PostgreSQL (and SQLite) `/` returns an integer if both inputs are integers:*
the result is truncated towards zero, e.g. `7 / 2 = 3` and `-7 / 2 = -3`
(the remainder is `7 % 2 = 1`).
So if `COST` and `QUANTITY` are `INTEGER` columns, the query above averages the truncated quotients:
for the rows `(7, 2)` and `(10, 4)` it returns `(3 + 2) / 2 = 2.5` instead of `(3.5 + 2.5) / 2 = 3.0`.
In MySQL `/` always returns a decimal number (`7 / 2 = 3.5000`), integer division is `DIV`.

To get the exact quotient in PostgreSQL, convert one of the inputs with `::numeric`:
```sql
SELECT 7::numeric / 2;                                           -- 3.5000000000000000
SELECT AVG(COST::numeric / QUANTITY) FROM table_name WHERE ...;
```
- `x::numeric` converts `x` to the data type `NUMERIC`, a decimal number with exact arithmetic.
  Then the division returns a decimal number instead of an integer.
- `::type` is PostgreSQL syntax. The standard SQL version `CAST(x AS NUMERIC)` works in all dialects.
- Other possibilities: `x * 1.0 / y` or `x::float / y` (floating point number, `DOUBLE PRECISION`).
  But `ROUND(value, 2)` with two arguments only exists for `NUMERIC` in PostgreSQL,
  `ROUND(7::float / 3, 2)` gives an error. Therefore `::numeric` is the best choice together with `ROUND`.

**Example from the leet-challenges.** In
[Average Selling Price](leet-challenges/3-basic-aggregate-functions/1251-average-selling-price.ipynb)
the columns `units` and `price` are `INTEGER`, so `SUM(total)` and `SUM(units)` are integers, too.
Without `::NUMERIC` the average price would be truncated to an integer:
```sql
ROUND(SUM(total)::NUMERIC / SUM(units), 2)
```
In [Average Time of Process per Machine](leet-challenges/2-basic-joins/1661-average-time-of-process-per-machine.ipynb)
the timestamps are floating point numbers and `::numeric` is needed so that `ROUND( _ , 3)` works.

### Scalar functions

`ROUND`, `LENGTH` (length of a string), `UCASE` (uppercase), `LCASE` (lowercase).

*`UCASE` and `LCASE` work in MySQL and Db2, but not in PostgreSQL.*
In PostgreSQL use `UPPER` and `LOWER` (they also work in MySQL, Db2 and SQLite).

Upper- and lowercase functions are good to use in the `WHERE` clause.
*MySQL:* `WHERE LCASE(name) = 'john'`, *PostgreSQL:* `WHERE LOWER(name) = 'john'`
matches independent of capitalization.

### Date and time functions

The database has the data types `DATE`, `TIME`, `TIMESTAMP`
and functions such as `YEAR()`, `MONTH()`, `DAY()`.
*These functions work in MySQL and Db2, but not in PostgreSQL:*
```sql
SELECT DAY(date_column) FROM table_name;
```
In PostgreSQL use `EXTRACT` (also works in MySQL):
```sql
SELECT EXTRACT(DAY FROM date_column) FROM table_name;
-- similarly EXTRACT(YEAR FROM ...), EXTRACT(MONTH FROM ...)
```

You can perform operations with dates and get the current date and time,
e.g. return the difference between today and a date column in years, months, days.
*`FROM_DAYS` and `DATEDIFF` only work in MySQL:*
```sql
SELECT FROM_DAYS(DATEDIFF(CURRENT_DATE, date_column)) FROM table_name;
```
In PostgreSQL:
```sql
SELECT AGE(CURRENT_DATE, date_column) FROM table_name;   -- e.g. 51 years 9 mons 24 days
SELECT CURRENT_DATE - date_column FROM table_name;       -- difference in days, e.g. 18925
```

## Working with multiple tables

There are three ways:
1) sub-queries,
2) implicit `JOIN`,
3) `INNER` / `OUTER JOIN`.

### Sub-queries

**Sub-query** = query nested inside another query.
It can be used with one table, e.g.
```sql
SELECT column_name_1 FROM table_name
WHERE column_name_2 = (SELECT MAX(column_name_2) FROM table_name);
```
Here a sub-query is needed because an aggregate function cannot be directly evaluated in the `WHERE` clause
(`WHERE column_name_2 = MAX(column_name_2)` gives an error).

With several tables: access the `employees` table and use a sub-query to filter by the department id in another table:
```sql
SELECT * FROM employees
WHERE DEP_ID IN (SELECT DEPT_ID_DEP FROM departments);
```

Sub-queries can also be used in the `FROM` clause, e.g. for `JOIN`s.
Such a sub-query is called a *derived table*:
the outer query uses the result of the sub-query as its data source, like a temporary table.
```sql
SELECT * FROM (SELECT column_name_1, column_name_2 FROM table_name) AS derived_table;
```

**Why sub-queries?**
- more powerful queries,
- aggregate functions in conditions (see above),
- derived tables,
- access to data of several tables.

### Implicit `JOIN`

You can specify multiple tables after `FROM`.
This is called a *full join*.
```sql
SELECT * FROM employees E, departments D
WHERE E.DEP_ID = D.DEPT_ID_DEP;
```
`E` and `D` are aliases for the table names.

### `JOIN` types

- `INNER JOIN`: rows of two tables which have a common value in a column.
- `OUTER JOIN`:
  - `LEFT OUTER JOIN`: all rows of the left table and the matching rows of the right table.
  - `RIGHT OUTER JOIN`: all rows of the right table and the matching rows of the left table.
  - `FULL OUTER JOIN`: all rows of both tables.

```
 LEFT OUTER JOIN      RIGHT OUTER JOIN     FULL OUTER JOIN
   ____   ____          ____   ____          ____   ____
  /####\ /    \        /    \ /####\        /####\ /####\
 | #### X##    |      |    ##X #### |      | #### X#### |
  \####/ \____/        \____/ \####/        \####/ \####/
   left   right         left   right         left   right
```

**Example (`INNER JOIN`).**
```sql
SELECT B.Borrower_ID, B.Lastname, B.Country, L.Borrower_ID, L.Loan_Date
FROM Borrower B INNER JOIN Loan L
ON B.Borrower_ID = L.Borrower_ID;
```

**Example (`LEFT OUTER JOIN`).** Shows all rows of `Borrower B`:
```sql
SELECT ...
FROM Borrower B LEFT JOIN Loan L
ON B.Borrower_ID = L.Borrower_ID;
```

## Advanced SQL

### Views

**View** = alternative way of representing data of one or different tables.
- The definition of the view is stored.
- A view can be queried like a table.
- Views are dynamic: only the definition is stored, not the data.
- You can use aliases for the column names.

**Why are views useful?**
- *Simplify queries:* a complex query (e.g. with joins and filters) is written once and saved under a name.
  Afterwards you query the view like a normal table instead of repeating the complex query.
- *Restrict access:* a view can show only some columns or rows of a table,
  e.g. the names of the employees but not their salaries.
  Users can get access to the view without getting access to the underlying table.
- *Always up to date:* since only the definition is stored,
  every query of the view runs on the current data of the underlying tables.
- *Readable names:* with aliases the view can rename cryptic columns, e.g. `F_NAME` to `first_name`.
- *No extra storage:* the data is not copied.

```sql
CREATE VIEW view_name (column_name_1, ...)
AS SELECT column_name_1, ...
FROM table_name
WHERE ...;
```
**Example.** The column list after the view name gives the selected columns new names (aliases):
```sql
CREATE VIEW emp_info (id, first_name, last_name)
AS SELECT EMP_ID, F_NAME, L_NAME
FROM employees;

SELECT first_name FROM emp_info;
```
The view `emp_info` shows `EMP_ID` as `id`, `F_NAME` as `first_name` and `L_NAME` as `last_name`.
Other columns of `employees` such as `SALARY` are hidden.

Drop a view:
```sql
DROP VIEW view_name;
```

### Common table expressions (`WITH ... AS`)

**Common table expression (CTE)** = temporary, named result table defined with `WITH ... AS` at the beginning of a query.
It only exists while this one query runs.

```sql
WITH cte_name AS (
    SELECT ...
    FROM table_name
    WHERE ...
)
SELECT ... FROM cte_name;
```

**Example.** Find all employees who earn more than the average salary of their department:
```sql
WITH avg_salary AS (
    SELECT DEP_ID, AVG(SALARY) AS avg_sal
    FROM employees
    GROUP BY DEP_ID
)
SELECT E.F_NAME, E.SALARY, A.avg_sal
FROM employees E INNER JOIN avg_salary A
ON E.DEP_ID = A.DEP_ID
WHERE E.SALARY > A.avg_sal;
```
1) The CTE `avg_salary` computes the average salary of each department.
2) The main query joins `employees` with `avg_salary` like with a normal table and keeps the employees above the average.

- You can define several CTEs separated by commas: `WITH a AS (...), b AS (...) SELECT ...`.
  A later CTE can use an earlier one.
- *CTE vs. sub-query:* a CTE does the same as a derived table (sub-query in the `FROM` clause),
  but it is easier to read: the temporary table gets a name, is defined before it is used
  and can be referenced several times in the query.
- *CTE vs. view:* a view is stored in the database and can be used by many queries.
  A CTE is not stored and only exists for a single query.

### Stored procedures

**Stored procedure** = SQL statements stored and executed on the database server.
They can be written in many databases.

*Advantages:*
- reduction in network traffic,
- improvement in performance,
- reuse of code,
- increase in security.

*The following syntax only works in MySQL:*
```sql
DELIMITER $$
CREATE PROCEDURE UPDATE_SAL (...)
BEGIN
    ...
END $$
DELIMITER ;
```
You need to change the `DELIMITER`, e.g. to `$$`,
so that the `;` inside the procedure does not end the `CREATE PROCEDURE` statement.

PostgreSQL does not need `DELIMITER`.
The body of the procedure is put between `$$ ... $$` and you have to specify the language `plpgsql`:
```sql
CREATE PROCEDURE UPDATE_SAL (IN p_emp_id CHAR(9), IN p_raise DECIMAL)
LANGUAGE plpgsql
AS $$
BEGIN
    UPDATE employees SET SALARY = SALARY + p_raise WHERE EMP_ID = p_emp_id;
END;
$$;

CALL UPDATE_SAL('E1', 500);
```

### ACID transactions

**Transaction** = indivisible unit of work, s.t. either all statements happen or none (atomic for the database).

**ACID:**
- **A**tomic: changes must be performed successfully or not at all.
- **C**onsistent: data must be consistent before and after the transaction.
- **I**solated: no other process can change the data while the transaction is running.
- **D**urable: the changes made by the transaction must persist.

Statements:
- `BEGIN`: starts the transaction.
- `COMMIT`: all statements must persist.
- `ROLLBACK`: undoes all statements of the transaction.

## Accessing databases using Python

### API calls

The Python program talks to the database via API calls:
```
User <--> Jupyter Notebook / Python program <--API calls--> DBMS
```
Calls: `CONNECT`, `SEND`, `EXECUTE`, `STATUS_CHECK` (OK comes back), `DISCONNECT`.

Each database system has its own DB API:

| Database               | DB API                         |
|------------------------|--------------------------------|
| MySQL                  | MySQL C API (low-level access) |
| PostgreSQL             | `psycopg2`                     |
| IBM Db2                | `ibm_db`                       |
| any database (in Java) | JDBC                           |

### DB-API

**DB-API** = Python's standard API for accessing relational databases.
- easy to implement and understand,
- consistent (same code for different databases),
- broader reach of database connectivity from Python:
  DB-API is a common standard which many database modules (e.g. `sqlite3`, `psycopg2`, `ibm_db`) follow.
  So with the same Python code pattern (connect, cursor, execute, fetch, close)
  you can reach many different databases, and switching the database mostly means changing the import and the connection.

**Main concepts**

1) *Connection objects:*
   - database connection,
   - manage transactions.

   Methods:
   - `.cursor()`: creates a cursor object,
   - `.commit()`: commits any pending transaction,
   - `.rollback()`: rolls back to the start of any pending transaction,
   - `.close()`: closes the database connection.
2) *Cursor objects:*
   - database queries,
   - scroll through the result set,
   - retrieve results.

   Methods: `.callproc()`, `.execute()`, ...

   You open a cursor to get access to the database and make queries.
   A cursor works like a file handle: you gain access to the query result.

Close the connection to avoid unused connections taking up resources.

**How to code**
```python
from dbmodule import connect
```
e.g. using `sqlite3`:
```python
import sqlite3
```

### Cheat sheet for SQLite

```python
import sqlite3

con = sqlite3.connect("INSTRUCTOR.db")  # connect to a database

# execute SQL statements -> need cursor object
cursor_obj = con.cursor()
cursor_obj.execute("...")
```

### SQL magic

Magic commands are not valid Python code but are used in notebooks.

1) *Line magics:* command prefixed with `%`, i.e. a command line in the shell.
   - `%pwd`,
   - `%ls`,
   - `%history` = command history,
   - `%lsmagic` = lists which magics exist.
2) *Cell magics:* prefixed with `%%`, apply to the whole cell.
   - `%%timeit` = time for executing the cell,
   - `%%HTML` = render the cell as HTML code,
   - `%%js` = run the cell as JavaScript.

To use SQL magic:
1) Install it: `pip install --user ipython-sql`.
2) Load it: `%load_ext sql`, then you can use `%sql` (line) and `%%sql` (cell).
3) Establish a connection to the database.

### Analyzing data with Python

- `df.to_sql("table_name", con)`: turns a pandas DataFrame object into an SQL table.
- `df = pd.read_sql("SQL QUERY", ...)`: read the result of an SQL query into a pandas DataFrame.

### Real-world datasets

- Often `.csv` files (comma-separated values).
- Splitting a query over multiple lines with `%sql`: use `\` at the end of the line.
- E.g. you can use `pandas.read_sql(...)`.
  Use `"..."` (double quotes) for column names, e.g. if they contain spaces or mixed case.

**Getting table and column names.**
*The table `sqlite_master` only exists in SQLite:*
```sql
SELECT name FROM sqlite_master WHERE type = 'table';
```
The standard way in PostgreSQL (and MySQL) is
```sql
SELECT table_name FROM information_schema.tables WHERE table_schema = 'public';
```
In `psql` you can also use `\dt` (tables) and `\d table_name` (columns of a table).

## Differences to PostgreSQL

The course uses MySQL, some material uses IBM Db2 and the Python part uses SQLite.
Most commands of these notes work the same in PostgreSQL:
`CREATE TABLE`, `ALTER TABLE ... ADD [COLUMN]`, `DROP COLUMN`, `DROP TABLE [IF EXISTS]`, `PRIMARY KEY`, `REFERENCES`, `CHECK`,
`INSERT`, `SELECT`, `WHERE`, `LIKE`, `BETWEEN`, `IN`,
`LIMIT ... OFFSET`, `ORDER BY`, `GROUP BY`, `HAVING`, `COUNT`, `SUM`, `MIN`, `MAX`, `AVG`, `ROUND`, `LENGTH`,
sub-queries, joins, `CREATE VIEW`, `WITH ... AS`, `BEGIN`, `COMMIT`, `ROLLBACK`.

The following commands are different:

| Task                        | Other dialect (as in these notes)                          | PostgreSQL                                              |
|-----------------------------|------------------------------------------------------------|---------------------------------------------------------|
| Change the data type        | MySQL: `ALTER TABLE t MODIFY c datatype;`                  | `ALTER TABLE t ALTER COLUMN c TYPE datatype;`           |
| Delete all rows             | Db2: `TRUNCATE TABLE t IMMEDIATE;`                         | `TRUNCATE TABLE t;` (as in MySQL)                       |
| Automatic numbering         | MySQL: `INT AUTO_INCREMENT`                                | `SERIAL`                                                |
| Upper- / lowercase          | MySQL, Db2: `UCASE(c)`, `LCASE(c)`                         | `UPPER(c)`, `LOWER(c)` (also work in MySQL)             |
| Year, month, day of a date  | MySQL, Db2: `YEAR(d)`, `MONTH(d)`, `DAY(d)`                | `EXTRACT(YEAR FROM d)`, `EXTRACT(MONTH FROM d)`, `EXTRACT(DAY FROM d)` |
| Time since a date           | MySQL: `FROM_DAYS(DATEDIFF(CURRENT_DATE, d))`              | `AGE(CURRENT_DATE, d)` or `CURRENT_DATE - d` (in days)  |
| Division of integers        | MySQL: `7 / 2 = 3.5000`, integer division `7 DIV 2 = 3`    | `7 / 2 = 3`, `7::numeric / 2 = 3.5` (as in SQLite)      |
| Case-insensitive `LIKE`     | MySQL: `LIKE` (case-insensitive by default)                | `ILIKE` (`LIKE` is case-sensitive)                      |
| Concatenate strings         | MySQL: `CONCAT(a, b)` (`\|\|` means `OR`)                  | `a \|\| b` or `CONCAT(a, b)`                            |
| Regular expressions         | MySQL: `c REGEXP 'pattern'`                                | `c ~ 'pattern'`, `c ~* 'pattern'` (case-insensitive)    |
| Alias in `HAVING`           | MySQL: `HAVING alias > 1` allowed                          | not allowed, repeat the aggregate function              |
| Stored procedures           | MySQL: `DELIMITER $$ ... END $$`                           | `LANGUAGE plpgsql AS $$ ... $$;`                        |
| Quote column names          | MySQL: `` `column name` `` (backticks)                     | `"column name"` (double quotes)                         |
| List all tables             | MySQL: `SHOW TABLES;`, SQLite: `sqlite_master`             | `\dt` in psql or `information_schema.tables`            |
| Python module               | MySQL: `mysql-connector-python`, SQLite: `sqlite3`         | `psycopg` (or the older `psycopg2`)                     |

*Remarks.*
- PostgreSQL cannot change the data type of a column which is used by a view.
  You have to drop the view first, change the column and create the view again.
- `AGE(CURRENT_DATE, d)` returns the difference in years, months and days, e.g. `50 years 9 mons 17 days`.
- PostgreSQL converts unquoted names to lowercase: `SELECT F_NAME ...` looks for the column `f_name`.
  Therefore `"F_NAME"` (with double quotes) only works if the column was created with double quotes.

### SQL magic with PostgreSQL

As described in the [README](README.md) we use `jupysql` (successor of `ipython-sql`) and `psycopg`:
```
uv add jupysql "psycopg[binary]"
```
In the notebook:
```python
%load_ext sql
%sql postgresql+psycopg://username:password@localhost:5432/database_name
```
