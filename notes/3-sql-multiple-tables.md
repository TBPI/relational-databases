# Multiple Tables

There are three ways:
1) sub-queries,
2) implicit `JOIN`,
3) `INNER` / `OUTER JOIN`.

### Sub-queries

**Sub-query** = query nested inside another query.
It can be used with one table, e.g.
```sql
SELECT column_name_1 FROM table_name
WHERE column_name_2 = (SELECT MAX(column_name_2) FROM table_name);
```
Here a sub-query is needed because an aggregate function cannot be directly evaluated in the `WHERE` clause
(`WHERE column_name_2 = MAX(column_name_2)` gives an error).

With several tables: access the `employees` table and use a sub-query to filter by the department id in another table:
```sql
SELECT * FROM employees
WHERE DEP_ID IN (SELECT DEPT_ID_DEP FROM departments);
```

Sub-queries can also be used in the `FROM` clause, e.g. for `JOIN`s.
Such a sub-query is called a *derived table*:
the outer query uses the result of the sub-query as its data source, like a temporary table.
```sql
SELECT * FROM (SELECT column_name_1, column_name_2 FROM table_name) AS derived_table;
```

**Why sub-queries?**
- more powerful queries,
- aggregate functions in conditions (see above),
- derived tables,
- access to data of several tables.

### Implicit `JOIN`

You can specify multiple tables after `FROM`.
This is called a *full join*.
```sql
SELECT * FROM employees E, departments D
WHERE E.DEP_ID = D.DEPT_ID_DEP;
```
`E` and `D` are aliases for the table names.

### `JOIN` types

- `INNER JOIN`: rows of two tables which have a common value in a column.
- `OUTER JOIN`:
  - `LEFT OUTER JOIN`: all rows of the left table and the matching rows of the right table.
  - `RIGHT OUTER JOIN`: all rows of the right table and the matching rows of the left table.
  - `FULL OUTER JOIN`: all rows of both tables.

```text
 LEFT OUTER JOIN      RIGHT OUTER JOIN     FULL OUTER JOIN
   ____   ____          ____   ____          ____   ____
  /####\ /    \        /    \ /####\        /####\ /####\
 | #### X##    |      |    ##X #### |      | #### X#### |
  \####/ \____/        \____/ \####/        \####/ \####/
   left   right         left   right         left   right
```

**Example (`INNER JOIN`).**
```sql
SELECT B.Borrower_ID, B.Lastname, B.Country, L.Borrower_ID, L.Loan_Date
FROM Borrower B INNER JOIN Loan L
ON B.Borrower_ID = L.Borrower_ID;
```

**Example (`LEFT OUTER JOIN`).** Shows all rows of `Borrower B`:
```sql
SELECT ...
FROM Borrower B LEFT JOIN Loan L
ON B.Borrower_ID = L.Borrower_ID;
```
