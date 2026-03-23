# SQL and Bash

## Normalization in SQL

In a relational database, you want to
- *reduce data redundancy*, and
- *improve data integrity*.

This is done via established **normal forms**. The process is called **normalization**.

There three increasingly stricter steps of normalization and, in addition, the Normal Form 3.5.

**Goal.** Reach the third normal form.

## SQL Injection


## The $N+1$ Problem

# Workshop : Building a Student Database: Part 1
As already observed, it is cumbersome to insert every row separately into the tables of your database.

Given the 'rough data', you can write a script which automates this process for you.

In the [workshop](sql_and_bash/workshop1), we are given `csv`-files
with rough data on students and their majors.
The `cat <filename>` command in bash prints the whole file
into the terminal.
You can use this in a `while` loop to run through the entries in the table.
Reading out the linebreaks in a file, and saving the content in variables works as follows:
```
cat courses.csv | while IFS="," read MAJOR COURSE
do
    STATEMENT
done
```
- The file with the rough data is `courses.csv`.
- `IFS=","` separates the entries in a line into variables `MAJOR` and `COURSE` whenever a comma appears. 

In order to run a script querying the database, you need to access PSQL.
This can conveniently be done by creating a `PSQL` variable, e.g.
```
    PSQL="psql -X --username=freecodecamp --dbname=students --no-align --tuples-only -c"
``` 

# Workshop : Building a Student Database: Part 2

