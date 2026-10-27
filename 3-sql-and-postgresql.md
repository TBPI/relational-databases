# SQL and PostgreSQL

**Relational database** = collection of data organized into tables.

- *Rows* represent individual records (e.g. data of a user).
- *Columns* represent the attributes or fields describing each record (e.g. ID number, name).
- *Relational* means that we can link data from different tables via common attributes.
- *Schema:* relational databases require a schema which defines the overall structure such as tables, columns, data types, relationships, constraints.

All tables in a relational database have a *primary key*, which is a unique identifier for each row.

The idea is to store data as efficient as possible linking it through relationships and not storing everything in one huge table.

A single *query* allows you to get the data efficiently.

Non-relational databases = **NoSQL** are more flexible.
They store data in individual files that are not connected.

Relational databases have a wide range application: web development, inventory management, healthcare systems, e-commerce, etc.

**Common relational databases:**
- MySQL,
- PostgreSQL,
- SQLite,
- Microsoft SQL Server. 

## What is SQL?

**SQL = Structured Query Language.**
*Programming language used for storing and managing data in relational databases.*

To open the psql shell in the command prompt/terminal you need to enter
```
psql -U <username> -d <database_name>
```
or
```
psql --username=<username> --dbname=<database_name>
```

You can create a database via
```
CREATE DATABASE my_database;
```
You can delete a database using `DROP DATABASE database_name`.

In the command line you can connect to a database via `\c database_name`.
You see to which database you are connected via
```
<database_name>=#
```
For creating a table you need the `CREATE TABLE` keyword:
```
CREATE TABLE table_name (
    column1 data_type column_constraint,
    column2 data_type column_constraint,
    column3 data_type column_constraint,
    ... etc
);
```
you specify the number of columns separated with commas and the data type for each column.

In order to delete a table you can use the `DROP TABLE` statement.

It is also possible to create a empty table:
```
CREATE TABLE table_name();
```
Afterwards, it is possible to add columns to the table using `ALTER TABLE`:
```
ALTER TABLE table_name ADD COLUMN column1 DATATYPE CONSTRAINTS;
```
You can add any constraints (such as `NOT NULL`, `UNIQUE`, `PRIMARY KEY`) after fixing the data type.
- In order to remove a column, you use the `DROP COLUMN` statement.
- In order to rename a column, you use `RENAME COLUMN ... TO ...`.

*Goal.* Choose data types as accurately as possible.

The standard naming convention for a table and its columns is *snake case*, i.e. writing words in lowercase and separating them with an underscore.

*Remark.* There are shortcuts in the psql-terminal to view the current status of your work:
- `\l` = lists all databases.
- `\d` = lists all tables in the current database.
- `\d table_name` = lists the contents of the table.

### Data types in SQL

There are six popular data types in SQL:
- *Numeric*, e.g. `INTEGER`, `FLOAT`, `SERIAL`, `DECIMAL`
- *Date and time*, e.g. `TIMESTAMP`, `DATE`, `TIME`
- *Character and string*, e.g. `CHAR`, `VARCHAR`, `TEXT`
- *Unicode*, e.g. `NTEXT`, `NVARCHAR` (make surethat text will be stored and retrieved coorectly, regardless o the characters' origin),
- *Binary* used to story non-textual data such as images, audio, video files
- *Miscellaneous data types*: e.g. `XML`, `TABLE`

**Integers**

`INTEGER` types consume 4 bytes and can range from -2,147,483,648 to 2,147,483,648 in PostgreSQL.
`SMALLINT` and `BIGINT` allow smaller and bigger range of numbers.

**Serial**

`SERIAL` is a very useful datatype for creating a unique identifier.
The column has an integer data type which does
- not allow `NULL`,
- increment automatically with every column added.

<sub>
In MySQL the equivalent to `SERIAL` is `AUTO_INCREMENT` with the data type INT.
</sub>

**String**

Using `VARCHAR` you can characterize a string of variable length. You can set a maximal length of the string by calling `VARCHAR(max_length)`.
For strings of any length use `TEXT`.

**Time**

`DATE` stores the data, `TIME` is a data type for the time, `TIMESTAMP` combines both of them and you can make the time cone precise by `TIMESTAMP WITH TIME ZONE`.

**Boolean**

Called with `BOOLEAN` data type which can be set to `TRUE` or `FALSE`.

### Constraints
If you need to add a constraint after having defined the table, you can do as follows:
```
ALTER TABLE table_name ADD CONSTRAINT constraint_name UNIQUE (column_name);
ALTER TABLE table_name ADD FOREIGN KEY (column_name) REFERENCES foreign_table(foreign_table_key);
ALTER TABLE table_name ALTER COLUMN column_name SET NOT NULL;
```

## Inserting and viewing data in a table

### Inserting

We know already how to create a table.
To add a row to a table you call
```
INSERT INTO table_name (column1, column2, column3, ...)
VALUES (value1, value2, value 3, ...);
```
Note that
- you have to leave out the first column if it is the unique identifier of data type `SERIAL` -> PostgreSQL will take care of it.
- you can leave out the specification `(column1, column2, column3, ...)` of the column names, but it is error-prone.
- you can add several rows at ones by separating the sets of values with a comma.

In order to change data in a row, use:
```
UPDATE table_name SET column_name = new_value WHERE condition;
```

### Viewing

The `SELECT` statement is used for querying data from one or more table.
If you want to select all the information of a table you use an asterisk:
```
SELECT * FROM table_name;
```
You can also specify attributes to select only specific column
```
SELECT column_attribute1, column_attribute2 FROM table_name;
```
Moreover, you can specify constraints using the `WHERE` keyword:
```
SELECT column_attribute1, ... FROM table_name WHERE column_attribute3 < 5
```

**Additional attributes**

By adding `ORDER BY column_name`,
you can order the rows subject to the entries in `column_name`.

### Deleting entries

It is also possible to remove a row from a table using `DELETE FROM table_name WHERE condition`.

When testing scripts, it might be useful to delete all entries from a table.
This can be done with the `TRUNCATE` keyword.
```
TRUNCATE table_name;
```

## Primary and foreign keys

A *primary key* is a column in a table such that the value of the column uniquely identifies each row.
Therefore values in the primary key column cannot be `NULL`.

You can simply add the `PRIMARY KEY` statement when defining column to make it the primary key, i.e.
```
ALTER TABLE table_name ADD COLUMN column_name DATA TYPE PRIMARY KEY
```
There is the possibility to have a *composite primary key* consisting of several columns identifying each row uniquely.
In this case you can modify the table creation as follows:
```
CREATE TABLE table_name (
    column1 DATA TYPE column_constraint,
    column2 DATA TYPE column_constraint,
    column3 DATA TYPE column_constraint,
    ...
    PRIMARY KEY (column1, column2)
);
```
Primary keys are essential for ensuring
- data uniqueness,
- integrity,
- optimizing data retrieval,
- establishing relationships with other tables in a relational database.

In order to add a primary key to an existing table, you can use the command:
```
ALTER TABLE characters ADD PRIMARY KEY (column_name);
```
A *foreign key* is a column (or a set of columns) in a table
that references (usually) the primary key of another table.
A table can have multiple foreign keys.
You can add foreign keys as follows
```
CREATE TABLE table_name (
    column1 DATA TYPE column_constraint,
    column2 DATA TYPE column_constraint,
    column3 DATA TYPE column_constraint,
    ...
    FOREIGN KEY (primary_key) REFERENCES table_name(primary_key)
)
```
The values in a foreign key column must either match the primary key values in the referenced table
or be `NULL`.

Foreign keys prevent orphaned records, i.e. records referring to a non-existent record in a table.

## Different types of relationships in a relational database

There are five main types of relations:
1) *one-to-one:* each record in the first table can be associated with at most one record in the second table, and vice versa.
2) *one-to-many:* one record in a table can be related to one or more records in another table.
3) *many-to-one:* one or more records are are related to one record in another table.
4) *many-to-many:* one record in a table can be related to one or more records in another table, and vice versa.
5) *self-referencing:* records in the same table are related between each other.

Implementing many-to-many relationships is complex as this is not supported at the core.
Usually you create an intermediate table, called the *junction table*.
It creates essentially to one-to-many relationships resolving the many-to-many relationship.

**Example.**
If you have a `books` table with the columns `title`, `authors`
and a `authors` table with the columns `name`, `books`,
you want to see which books where written by whom and the books of the author.
Then, the junction table `books_authors` consists of the author_id, and book_id.

## Different Ways to join tables

The `JOIN` operations allow you to combine related information from tables to query data.

1) `INNER JOIN`: filters the results to include only rows where the values in the joining columns that you specify are equal in both tables, e.g.
```
SELECT * FROM table_1 INNER JOIN table_2 ON table_1.key = table_2.key;
```
2) `FULL OUTER JOIN`: returns all rows from both tables.
```
SELECT * FROM table_1 FULL OUTER JOIN ON table_1.key = table_2.key;
```
3) `LEFT OUTER JOIN`: all the records from the left table and the matching from the right table:
```
SELECT * FROM table_1 LEFT JOIN table_2 ON table_1.key = table_2.key;
```
In this case, if table_2 has a row where table_2.key does not match any key in table_1, this entry will *not* be displayed.
4) `RIGHT OUTER JOIN`: all the records from the right table and the matching one from the left table.
5) `SELF JOIN`: join the table with itself.
```
SELECT * FROM table_name t1 INNER JOIN table_name t2 ON ...
```
6) `CROSS JOIN`: as a Cartesian join, joins every entry of first table with every entry from second table.
```
SELECT * FROM table_1, table_2;
```
Alternatively the cross join can be performed as
```
SELECT * FROM table_1 CROSS JOIN table_2;
```

**Example.**
Given a table with student_id and student_names, you want to add the subjects `Math`, `Physics`, `Programming` from the Table `Subjects` to each of the students. You can do this via the `CROSS JOIN`, see [1280-students-and-examinations.ipynb](leet-challenges/2-basic-joins/1280-students-and-examinations.ipynb).
