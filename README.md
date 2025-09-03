# Learning SQL

We want to perform actions on a database.
Usually, a *database* consists of one or more tables.

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

### WHERE

Used to filter records (=rows) satisfying a certain condition.
```
SELECT column1, column2, ...
FROM table_name
WHERE condition;
```
A condition could for example be `Country = 'Mexico'` or `CustomerID = 1` or `CustomerID > 80`.

*Remark.* The SQL statement `WHERE` is also used in other statements that `SELECT`, e.g.`UPDATE` or `DELETE`.

### ORDER BY

Sort results in ascending or descending order.
```
SELECT column1, column2, ...
FROM table_name
ORDER BY column1, column2, ... ASC|DESC
```
*Remark.* For string values the ordering will be alphabetically (with `DESC` it will be reverse alphabetically).

*Remark.* If you select several columns for the ordering, then it will order by `column2` if the value of `column1` agrees for two rows.

### AND and OR

You can include `AND` or `OR` into a `WHERE` clause
```
SELECT column1, column2, ...
FROM table_name
WHERE columnx = conditionx AND|OR columny = conditiony
``` 

The `AND` operator displays a record if *all* the conditions are TRUE.\
The `OR` operator displays a record if *at least one* of the conditions is TRUE.

You can combine `AND` and `OR` using parentheses `(` `)`.

### NOT

There are several versions of how we can use `NOT`

