# Advanced SQL

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

We use `jupysql` (successor of `ipython-sql`) and `psycopg`:
```bash
uv add jupysql "psycopg[binary]"
```
In the notebook:
```python
%load_ext sql
%sql postgresql+psycopg://username:password@localhost:5432/database_name
```
