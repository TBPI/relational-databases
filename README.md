# Learning SQL

We want to perform actions on a database.
Most of a *database* consists of one or more tables.

## Statements

### SELECT

Select data from a database

```
SELECT column1, column2, column3, ... FROM table_name;
```

Use * if you want to select all.

### SELECT DISTINCT

Select different values from a database.
Command is the same as for `SELECT`.

With `SELECT COUNT (DISTINCT ...) FROM` you can count the number of different entries in a given column. 