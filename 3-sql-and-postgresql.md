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
    id ---,
    name ---
);
```
you specify the number of columns separated with commas.

The standard naming convention for a table and its columns is *snake case*, i.e. writing words in lowercase and separating them with an underscore.
