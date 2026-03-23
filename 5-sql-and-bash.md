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
In the first workshop we saved our progress in an `sql`-file.
In order to use the database, we need to rebuild the database, this is done with
```
psql -U postgres < students.sql
```
## Pattern matching
We can use
```
SELECT * FROM table_name WHERE CONDITIONS
```
with `CONDITIONS` being for example arithmetic conditions.
The column `gpa` in the `students` table takes `NUMERIC` values.
We can use the operators `=`, `!=`, `<`, `<=`, `>`, `>=` for the conditions.
They work also with `VARCHAR` columns with the *alphabetic order*. 

Another useful condition is `LIKE` for patterns.
For example the condition `WHERE course LIKE '_lgortihm'` returns everything where `course` contains the pattern `lgorithm` and an arbitrary character before it. 
Here are some useful commands with to find patterns:
- `_` placeholder for a pattern that can have any character at this place, but must match everything else.
- `%` returns everything that matches the pattern after or before the `%`.
For example in the courses, table of the first [workshop](sql_and_bash/workshop1/), in order to view all courses which have an empty space,
you can run the SQL-command:
```
SELECT * FROM courses WHERE course = '% %';
```

There are also different keywords for the pattern check:
- `LIKE`,
- `NOT LIKE` = opposite of `LIKE`,
- `ILIKE` = as `LIKE` but without case matching,
- `NOT ILIKE` = opposite of `ILIKE`.

## Ordering results
