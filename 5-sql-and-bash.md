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
You can specify the order of the results by adding `ORDER BY column_name`.
By default it will be ordered in ascending order (`ORDER BY column_name ASC`).
If you want to order in descending order,
you need to add the keyword `DESC`. 

In order to limit the number of results returned, you can use the keyword `LIMIT number`.

*Caution.* You cannot put `LIMIT` before `ORDER BY`.
The order is `WHERE` -> `ORDER BY` -> `LIMIT`.

## Mathematical operations with numerical columns
- `MIN`: Selects the minimum from a given column
```
SELECT MIN(column_name) FROM table_name;
```
- `MAX`: Selects the maximum from a given column
```
SELECT MAX(column_name) FROM table_name;
```
- `SUM`: Returns sum of all entries in a column
```
SELECT SUM(column_name) FROM table_name;
```
- `AVG`: Returns the average of all entries from a column
```
SELECT AVG(column_name) FROM table_name;
```
In order to round decimals, you can use `CEIL`, `FLOOR` or `ROUND(number_to_round, number_of_decimals)`.
- `COUNT`: Counts the number of entries in a column.
- `DISTINCT`: Shows only unique values.
The same can be done by `GROUP BY` as follows
```
SELECT column_name FROM table_name GROUP BY column_name
```
The advantage of `GROUP BY` is that other aggregate functions can be added. For example, in the [workshop](sql_and_bash/workshop1), we can select all major_id and count how often they appear in the students table as follows:
```
SELECT major_id, COUNT(*) FROM students GROUP BY major_id;
```
If you want the unique major ids with minimal and maximal gpas, the command is
```
SELECT major_id, MIN(gpa), MAX(gpa) FROM students GROUP BY major_id;
```
This leads to three columns of all unique `major_id`s together with the minimal and the maximal gpa.

In addition, you can add `HAVING condition` after the `GROUP BY` keyword, e.g.
```
SELECT major_id, MIN(gpa), MAX(gpa) FROM students GROUP BY major_id HAVING MAX(gpa) = 4.0;
```
- Using `SELECT column_name AS new_column_name...` returns the column with a newly selected column name.

In order ot avoid showing numeric keys, you need to use the `JOIN` keyword.
Recall that we have 4 keywords:
- `table_1 FULL JOIN table_2 ON ...` joins all rows of the two tables with on the columns specified after the keyword `ON`,
- `table_1 LEFT JOIN table_2 ON ...` joins all the rows from `table_1` with the rows from `table_2`, but leaves out every row in `table_2` whose entry after the `ON` keyword does not appear in `table_1`.
`table_1 RIGHT JOIN table_2 ON ...` joins all the rows from `table_2` with the rows from `table_1`, but leaves out every row in `table_1` whose entry after the `ON` keyword does not appear in `table_2`.
- `INNER JOIN` takes the *intersection* of the two tables.

They become more effective when combining them with the numeric keys.
