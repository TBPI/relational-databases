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

*Goal.* Choose data types as accurately as possible.

The standard naming convention for a table and its columns is *snake case*, i.e. writing words in lowercase and separating them with an underscore.

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
