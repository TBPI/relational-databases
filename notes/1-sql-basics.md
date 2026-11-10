# Basics of SQL

**SQL** = **S**tructured **Q**uery **L**anguage.
It is a
*programming language used for storing and managing data in relational databases.*

**Data** = collection of facts, words, numbers, pictures.

**Database** = repository of data.
It should be
- secure,
- quickly accessible,
- able to store data in different types (tables, text, ...).

**DBMS** = Database Management System.

**RDBMS** = Relational Database Management System.

**Relational database** = collection of data organized into tables.

- *Rows* represent individual records (e.g. data of a user).
- *Columns* represent the attributes or fields describing each record (e.g. ID number, name).
- *Relational* means that data from different tables can be linked via common attributes.
- *Schema:* relational databases require a schema which defines the overall structure such as tables, columns, data types, relationships, constraints.

All tables in a relational database *should* have a *primary key*,
which is a unique identifier for each row.

*Remark.*
Avoid creating tables without a primary key.
A convenient choice for a primary key is a column of type `SERIAL`
(see [Data types in SQL](#data-types-in-sql)).
There are rare instances (e.g. in the exercises)
where a primary key can be omitted, see [Exercise 19](../leet-challenges/3-basic-aggregate-functions/1211-queries-quality-and-percentage.ipynb) for example.

The idea is to store data as efficiently as possible by linking it through relationships and not storing everything in one huge table.

A single *query* retrieves the data efficiently.

Non-relational databases = **NoSQL** are more flexible.
They store data without a fixed schema,
e.g. as documents, key-value pairs, graphs etc.

Relational databases have a wide range of applications: web development, inventory management, healthcare systems, e-commerce, etc.

**Common relational databases:**
- MySQL,
- PostgreSQL,
- SQLite,
- Microsoft SQL Server.

## Entity-Relationship Model

An RDBMS is based on the *Entity-Relationship Model*:
- *Entities* are objects, e.g. a table.
- *Attributes* tell us more about an entity.

**Example.**
Consider a movie rating webpage,
where users have to sign in.
A blueprint of the database could be [Exercise 39](../leet-challenges/6-subqueries/1341-movie-rating.ipynb).
It has the following entities:
- `Movies` with attributes `movie_id` (primary key), `title`,
- `Users` with attributes `user_id` (primary key), `name`, and
- `MovieRating` with attributes `movie_id` (refers to `Movies`), `user_id` (refers to `Users`), `rating` and `created_at`. The combination of two columns `(movie_id, user_id)` is a composite primary key. This means that every user can rate each movie only once.

## Primary and foreign keys

- Each table has a *primary key* to uniquely identify each row of the table.
- A *foreign key* references the primary key of a different table of the relational database.

A *primary key* is a column in a table such that the value of the column uniquely identifies each row.
Therefore values in the primary key column cannot be `NULL`.

Primary keys are essential for
- ensuring data uniqueness and integrity,
- optimizing data retrieval,
- establishing relationships with other tables in a relational database.

A *foreign key* is a column (or a set of columns) in a table
that references (usually) the primary key of another table.
A table can have multiple foreign keys.

Foreign keys prevent orphaned records, i.e. records referring to a non-existent record in a table.

**Example.** Foreign keys are present in the example of the movie rating database [Exercise 39](../leet-challenges/6-subqueries/1341-movie-rating.ipynb) above. The entity `MovieRating` has two foreign keys, namely `user_id` pointing to the entity `Users` and `movie_id` pointing to `Movies`.

## Types of SQL statements

1) **DDL** = **D**ata **D**efinition **L**anguage statements.
   Used to define, change and drop database objects (e.g. tables):
   `CREATE`, `ALTER`, `TRUNCATE`, `DROP`.
2) **DML** = **D**ata **M**anipulation **L**anguage statements.
   Used to modify data, i.e. the **CRUD** operations (create, read, update and delete rows):
   `INSERT`, `SELECT`, `UPDATE`, `DELETE`.

**SQL scripts** = series of commands that can be executed on a database server.

## DDL: Creating and changing tables

To open the psql shell in the command prompt/terminal, type
```bash
psql -U <username> -d <database_name>
```
or
```bash
psql --username=<username> --dbname=<database_name>
```

*Remark.*
Connecting to PostgreSQL in Python can be done via the DB API `pyscopg2`, see [Python and SQL](5-python-and-sql.md).

### `CREATE DATABASE`

A database can be created via the following SQL command:
```sql
CREATE DATABASE my_database;
```
Note that PostgreSQL will return an error of type `duplicate_database`
if a database with the name `my_database` already exists.

One can delete a database using `DROP DATABASE database_name`.

In the command line one can connect to a database via `\c database_name`.
The prompt shows the database to which one is connected:
```text
<database_name>=#
```

*Remark.* There are shortcuts in the psql shell to view the current status of the work:
- `\l` = lists all databases.
- `\d` = lists all tables in the current database.
- `\d table_name` = shows the table's structure (columns, types, constraints).

### `CREATE TABLE`

```sql
CREATE TABLE table_name (
    column_name_1 datatype optional_parameter,
    column_name_2 datatype optional_parameter,
    ...
);
```
The columns are listed separated by commas, each with its data type and optional constraints.

It is also possible to create an empty table:
```sql
CREATE TABLE table_name();
```

- *Data types*, e.g. `CHAR(2)`, `VARCHAR(24)`, `BIGINT`.
- *Optional parameters* (= constraints), e.g.
  - `PRIMARY KEY`,
  - `NOT NULL`,
  - `UNIQUE`: no two rows may have the same value in this column.

*Goal.* Choose data types as accurately as possible.

The standard naming convention for a table and its columns is *snake case*, i.e. writing words in lowercase and separating them with an underscore.

*Remark.* As with `CREATE DATABASE`,
creating a second table with the same name (in the same database)
raises an error `relation "table_name" already exists` and no (second) table will be created.

To circumvent this error, one can run
```sql
CREATE TABLE IF NOT EXISTS table_name;
```
See also the discussion in [`ALTER`, `DROP`, `TRUNCATE`](#alter-drop-truncate) below.

### Data types in SQL

There are six categories of data types in SQL:
- *Numeric*, e.g. `INTEGER`, `FLOAT`, `SERIAL`, `DECIMAL`
- *Date and time*, e.g. `TIMESTAMP`, `DATE`, `TIME`
- *Character and string*, e.g. `CHAR`, `VARCHAR`, `TEXT`
- *Unicode*, e.g. `NTEXT`, `NVARCHAR` (make sure that text will be stored and retrieved correctly, regardless of the characters' origin),
- *Binary* used to store non-textual data such as images, audio, video files
- *Miscellaneous data types*: e.g. `XML`, `TABLE`

Moreover, it is possible to create a custom data type `ENUM` with a list of fixed values:
```sql
CREATE TYPE my_type AS ENUM ('value_1', 'value_2', ...);
```
The order of the values defines the sort order (e.g. for `ORDER BY` and comparisons).

An example of such a type can be found in [Exercise 1](../leet-challenges/1-select/1757-recyclable-and-low-fat-products.ipynb).

#### Integers

`INTEGER` uses 4 bytes and can range from -2,147,483,648 to 2,147,483,647 in PostgreSQL.
`SMALLINT` (2 bytes) and `BIGINT` (8 bytes) cover a smaller and a larger range of numbers.

#### Serial

`SERIAL` is a very useful datatype for creating a unique identifier.
The column has an integer data type which
- does not allow `NULL`,
- increments automatically with every row added.

#### String

`VARCHAR` stores strings of variable length; `VARCHAR(max_length)` sets a maximal length.
For strings of any length use `TEXT`.

#### Time

`DATE` stores the date, `TIME` is a data type for the time, `TIMESTAMP` combines both of them, and `TIMESTAMP WITH TIME ZONE` (`TIMESTAMPTZ` for short) also takes the time zone into account.

#### Boolean

The `BOOLEAN` data type takes the values `TRUE` and `FALSE` (or `NULL`).

This information is already sufficient for the creation of a table in a database.
For example, one could create a users table in [Exercise 39](../leet-challenges/6-subqueries/1341-movie-rating.ipynb) as follows:
```sql
CREATE TABLE Users (
    user_id SERIAL,
    name VARCHAR
);
```
The `user_id` should be the primary key and this has to be made clear.
The `name` of a user should not be allowed to be the empty string.

Constraints (such as `NOT NULL`, `UNIQUE`, `PRIMARY KEY`) are written after the data type:
```sql
CREATE TABLE Users (
    user_id SERIAL PRIMARY KEY,
    name VARCHAR NOT NULL
);
```
For adding constraints later, one needs the `ALTER` command.

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
- In order to remove a column, use the `DROP COLUMN` statement.
- In order to rename a column, use `RENAME COLUMN ... TO ...`.

To add a `NOT NULL` constraint, type
```sql
ALTER TABLE table_name ALTER COLUMN column_name SET NOT NULL;
```
Other constraints (`UNIQUE`, `CHECK`, `PRIMARY KEY`, `FOREIGN KEY`) are added with `ADD`,
see [Constraints](#constraints).

**Example.**
Suppose that in [Exercise 39](../leet-challenges/6-subqueries/1341-movie-rating.ipynb) the `Users` entity was defined without constraints:
```sql
CREATE TABLE Users (
    user_id INTEGER,
    name VARCHAR
);
```
To add the constraints, perform
```sql
ALTER TABLE Users ALTER user_id SET NOT NULL;
ALTER TABLE Users ADD PRIMARY KEY (user_id);
ALTER TABLE Users ALTER name SET NOT NULL;
```
*Remark.* PostgreSQL checks all existing rows before adding a constraint.
`SET NOT NULL` fails if the column already contains `NULL` values,
and `ADD PRIMARY KEY` fails if the column contains duplicates or `NULL` values.
Hence it is easiest to set constraints directly in `CREATE TABLE`.

Modify a column (e.g. change its data type):
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
(e.g. a `%%sql` cell in Jupyter),
and inside a transaction it aborts the transaction: all following statements are ignored until `ROLLBACK`.
With `IF EXISTS` the database only gives a *notice* (`table "table_name" does not exist, skipping`)
and continues with the next statement.

*Advantage.* Scripts which start with `DROP TABLE IF EXISTS ...; CREATE TABLE ...;` can be run again and again,
no matter whether the table already exists or not.
This is what we do in the notebooks of [leet-challenges](../leet-challenges/).

Delete all rows of a table (the table itself remains):
```sql
TRUNCATE TABLE table_name;
```
When testing scripts, it might be useful to delete all entries from a table.
This can be done with the `TRUNCATE` keyword.

It can even be undone with `ROLLBACK` if it is run inside a transaction (see [ACID transactions](4-advanced-sql.md#acid-transactions)).

### Data integrity

- *Entity integrity:* every table needs a primary key identifying each row uniquely.
- *Referential integrity:* a foreign key in one table maps to a primary key in another table.
- *Domain integrity:* well-defined data type, format, value and nullability,
  ensured e.g. by the constraints `CHECK` and `NOT NULL`.

**Example.** (similar to the [student database workshop](6-bash-and-sql.md#workshop--building-a-student-database-part-1), see also [Primary and foreign keys](#primary-and-foreign-keys))
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

There is the possibility to have a *composite primary key* consisting of several columns identifying each row uniquely.
In this case the table creation is modified as follows:
```sql
CREATE TABLE table_name (
    column1 DATA TYPE column_constraint,
    column2 DATA TYPE column_constraint,
    column3 DATA TYPE column_constraint,
    ...
    PRIMARY KEY (column1, column2)
);
```

With `CONSTRAINT name` the constraint gets a name which then appears in the error messages:
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

### Constraints
To add a constraint after the table has been defined, use:
```sql
ALTER TABLE table_name ADD CONSTRAINT constraint_name UNIQUE (column_name);
ALTER TABLE table_name ADD FOREIGN KEY (column_name) REFERENCES foreign_table(foreign_table_key);
ALTER TABLE table_name ALTER COLUMN column_name SET NOT NULL;
```

To add a primary key to an existing table, use:
```sql
ALTER TABLE table_name ADD PRIMARY KEY (column_name);
```

### Normalization

In a relational database, the goal is to
- *reduce data redundancy*, and
- *improve data integrity*.

This is done via established **normal forms**. The process is called **normalization**.

There are three increasingly stricter steps of normalization and, in addition, the Normal Form 3.5.

**Goal.** Reach the third normal form.

*To be continued*.

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
Note that
- you have to leave out the first column if it is the unique identifier of data type `SERIAL` -> PostgreSQL will take care of it.
- you can leave out the specification `(column1, column2, column3, ...)` of the column names, but it is error-prone.
- you can add several rows at once by separating the sets of values with a comma.

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

The column `gpa` in the `students` table takes `NUMERIC` values.
We can use the operators `=`, `!=`, `<`, `<=`, `>`, `>=` for the conditions.
They work also with `VARCHAR` columns with the *alphabetic order*.

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
If you don't specify a `WHERE` condition, *all* rows will be removed,
but the table will exist as an empty table!
