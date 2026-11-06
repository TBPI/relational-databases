# Basics of SQL

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

## Entity-Relationship Model

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

## Primary and foreign keys

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
This is what we do in the notebooks of [leet-challenges](../leet-challenges/).
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
It can even be undone with `ROLLBACK` if it is run inside a transaction (see [ACID transactions](4-advanced-sql.md#acid-transactions)).

### Data integrity

- *Entity integrity:* every table needs a primary key identifying each row uniquely.
- *Referential integrity:* a foreign key in one table maps to a primary key in another table.
- *Domain integrity:* well-defined data type, format, value and nullability,
  ensured e.g. by the constraints `CHECK` and `NOT NULL`.

**Example.** (similar to [SQL and Bash](B-sql-and-bash.md), see also [Primary and foreign keys](A-sql-and-postgresql.md#primary-and-foreign-keys))
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