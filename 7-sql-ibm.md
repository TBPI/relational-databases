# SQL for Data Science with Python (IBM)

*Remark.* The course uses MySQL.
Commands which are different in PostgreSQL are collected in [MySQL vs. PostgreSQL](#mysql-vs-postgresql).

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

-- modify a column (e.g. change its data type)
ALTER TABLE table_name MODIFY column_name datatype;

-- remove a column
ALTER TABLE table_name DROP COLUMN column_name;
```

Delete a table from the database:
```sql
DROP TABLE table_name;
```

Delete all rows of a table (the table itself remains):
```sql
TRUNCATE TABLE table_name IMMEDIATE;
```
`IMMEDIATE` means that this cannot be undone.

*Remark.* The exact syntax depends on the database system:
`MODIFY` is MySQL syntax and `IMMEDIATE` is required in IBM Db2.
In other systems `TRUNCATE TABLE table_name;` is enough.
For PostgreSQL see [MySQL vs. PostgreSQL](#mysql-vs-postgresql).

### Data integrity

- *Entity integrity:* every table needs a primary key identifying each row uniquely.
- *Referential integrity:* a foreign key in one table maps to a primary key in another table.
- *Domain integrity:* well-defined data type, format, value and nullability,
  ensured e.g. by the constraints `CHECK` and `NOT NULL`.

## DML: Working with data

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

### Scalar functions

`ROUND`, `LENGTH` (length of a string), `UCASE` (uppercase), `LCASE` (lowercase).

`UCASE` and `LCASE` are good to use in the `WHERE` clause,
e.g. `WHERE LCASE(name) = 'john'` matches independent of capitalization.

### Date and time functions

The database has the data types `DATE`, `TIME`, `TIMESTAMP`
and functions such as `YEAR()`, `MONTH()`, `DAY()`:
```sql
SELECT DAY(date_column) FROM table_name;
```
You can perform operations with dates and get the current date and time,
e.g. return the difference between today and a date column in years, months, days:
```sql
SELECT FROM_DAYS(DATEDIFF(CURRENT_DATE, date_column)) FROM table_name;
```

## Sub-queries

**Sub-query** = query nested inside another query, e.g.
```sql
SELECT column_name_1 FROM table_name
WHERE column_name_2 = (SELECT MAX(column_name_2) FROM table_name);
```
Here a sub-query is needed because an aggregate function cannot be directly evaluated in the `WHERE` clause.

Sub-queries can also be used in the `FROM` clause, e.g. when joining or working with multiple tables.

**Why sub-queries?**
- more powerful queries,
- e.g. aggregate functions cannot be evaluated in the `WHERE` clause,
- derived tables, e.g. for `JOIN`s.
  A *derived table* is a sub-query in the `FROM` clause:
  the outer query uses the result of the sub-query as its data source, like a temporary table.
  ```sql
  SELECT * FROM (SELECT column_name_1, column_name_2 FROM table_name) AS derived_table;
  ```

## Working with multiple tables

There are three ways:
1) sub-queries,
2) implicit `JOIN`,
3) `INNER` / `OUTER JOIN`.

### Sub-queries

Access the `employees` table and use a sub-query to filter by the department id in another table:
```sql
SELECT * FROM employees
WHERE DEP_ID IN (SELECT DEPT_ID_DEP FROM departments);
```

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

**Getting table and column names** via `sqlite_master`, e.g.
```sql
SELECT name FROM sqlite_master WHERE type = 'table';
```

## MySQL vs. PostgreSQL

The course uses MySQL.
Most commands of these notes work the same in PostgreSQL:
`CREATE TABLE`, `ALTER TABLE ... ADD [COLUMN]`, `DROP COLUMN`, `DROP TABLE`, `INSERT`, `SELECT`, `WHERE`, `LIKE`, `BETWEEN`, `IN`,
`LIMIT ... OFFSET`, `ORDER BY`, `GROUP BY`, `HAVING`, `COUNT`, `SUM`, `MIN`, `MAX`, `AVG`, `ROUND`, `LENGTH`,
sub-queries, joins, `CREATE VIEW`, `WITH ... AS`, `BEGIN`, `COMMIT`, `ROLLBACK`.

The following commands are different:

| Task                        | MySQL (as in these notes)                         | PostgreSQL                                              |
|-----------------------------|---------------------------------------------------|---------------------------------------------------------|
| Change the data type        | `ALTER TABLE t MODIFY c datatype;`                | `ALTER TABLE t ALTER COLUMN c TYPE datatype;`           |
| Delete all rows             | `TRUNCATE TABLE t;` (`IMMEDIATE` is only for Db2) | `TRUNCATE TABLE t;`                                     |
| Upper- / lowercase          | `UCASE(c)`, `LCASE(c)`                            | `UPPER(c)`, `LOWER(c)` (also work in MySQL)             |
| Year, month, day of a date  | `YEAR(d)`, `MONTH(d)`, `DAY(d)`                   | `EXTRACT(YEAR FROM d)`, `EXTRACT(MONTH FROM d)`, `EXTRACT(DAY FROM d)` |
| Time since a date           | `FROM_DAYS(DATEDIFF(CURRENT_DATE, d))`            | `AGE(CURRENT_DATE, d)` or `CURRENT_DATE - d` (in days)  |
| Case-insensitive `LIKE`     | `LIKE` (case-insensitive by default)              | `ILIKE` (`LIKE` is case-sensitive)                      |
| Quote column names          | `` `column name` `` (backticks)                   | `"column name"` (double quotes)                         |
| List all tables             | `SHOW TABLES;`                                    | `\dt` in psql                                           |
| Python module               | `mysql-connector-python`                          | `psycopg` (or the older `psycopg2`)                     |

*Remarks.*
- PostgreSQL cannot change the data type of a column which is used by a view.
  You have to drop the view first, change the column and create the view again.
- `AGE(CURRENT_DATE, d)` returns the difference in years, months and days, e.g. `50 years 9 mons 17 days`.
- PostgreSQL converts unquoted names to lowercase: `SELECT F_NAME ...` looks for the column `f_name`.
  Therefore `"F_NAME"` (with double quotes) only works if the column was created with double quotes.
- In SQLite the tables are listed via `sqlite_master` (see [Real-world datasets](#real-world-datasets)).
  The standard way in PostgreSQL (and MySQL) is
  ```sql
  SELECT table_name FROM information_schema.tables WHERE table_schema = 'public';
  ```

### Stored procedures in PostgreSQL

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
