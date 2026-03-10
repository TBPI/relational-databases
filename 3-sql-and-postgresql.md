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
You can delete a database using `DROP database_name`.

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
ALTER TABLE table_name ADD COLUMN column1 DATATYPE;
```
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

*Remark.* It is also possible to remove a row from a table using `DELETE FROM table_name WHERE condition`.

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

## Primary and foreign keys

## Different types of relationships in a relational database

## Different Ways to join tables

