# Refining Query Results


### String patterns, ranges and sets

Make it easier to retrieve data.

**`LIKE`** is a predicate which allows you to look for a pattern.
The `%` is a *wildcard character* replacing missing letters:
```sql
SELECT * FROM table_name WHERE firstname LIKE 'R%';
```
This returns all rows where the first name starts with `R`.
- `_` replaces exactly one character, e.g. `LIKE 'R_v'` matches `Rav` but not `Raul`.
- In PostgreSQL `LIKE` is case-sensitive, `ILIKE` is the case-insensitive version (in MySQL `LIKE` is case-insensitive by default).
  `NOT LIKE` and `NOT ILIKE` return the opposite (see also [SQL and Bash](B-sql-and-bash.md)).

**Working with strings.**
In [Fix Names in a Table](../leet-challenges/7-advanced-regex/1667-fix-names-in-a-table.ipynb)
strings are cut into pieces and concatenated again with `||`:
```sql
SELECT user_id, UPPER(LEFT(name, 1)) || LOWER(RIGHT(name, LENGTH(name) - 1)) AS name
FROM Users ORDER BY user_id;
```
- `LEFT(name, n)` / `RIGHT(name, n)`: first / last `n` characters,
  `SUBSTRING(name, 2)`: everything from the second character on.
- `UPPER`, `LOWER`: upper- and lowercase, `LENGTH`: number of characters.
- `||` concatenates strings, e.g. `'mAriE ANN'` becomes `'M' || 'arie ann' = 'Marie ann'`.
- `CONCAT('a', 'b', ...)` concatenates, too.
  Difference: `'Hello' || NULL` is `NULL`, whereas `CONCAT('Hello', NULL)` is `'Hello'`.

*`||` works in PostgreSQL, SQLite and Db2, but in MySQL `||` means logical `OR` by default.*
In MySQL use `CONCAT`, which also works in PostgreSQL:
```sql
SELECT user_id, CONCAT(UPPER(LEFT(name, 1)), LOWER(SUBSTRING(name, 2))) AS name
FROM Users ORDER BY user_id;
```
*Remark.* PostgreSQL also has `INITCAP(name)`, but it capitalizes *every* word: `'mAriE ANN'` becomes `'Marie Ann'`.

**Regular expressions.**
`LIKE` only knows the wildcards `%` and `_`.
For more complex patterns PostgreSQL has regular expressions (POSIX):
- `~` matches a regular expression (case-sensitive), `~*` case-insensitive,
  `!~` and `!~*` are the negations.
- `SIMILAR TO` is a mixture of `LIKE` and regular expressions, e.g. `firstname SIMILAR TO '(R|H)%'`.
- `REGEXP_REPLACE(string, pattern, replacement, 'g')` replaces all (`'g'` = global) matches.

```sql
-- first names starting with R
SELECT * FROM table_name WHERE firstname ~ '^R';
-- first names with exactly three letters, starting with r or R
SELECT * FROM table_name WHERE firstname ~* '^r[a-z]{2}$';
-- valid e-mail addresses: start with a letter, then letters, digits, _ . -
SELECT * FROM table_name WHERE mail ~ '^[A-Za-z][A-Za-z0-9_.-]*@leetcode\.com$';
-- replace multiple spaces by a single one
SELECT REGEXP_REPLACE(name, '\s+', ' ', 'g') FROM table_name;
```
*The operators `~`, `~*`, `!~`, `!~*` and `SIMILAR TO` only work in PostgreSQL.*
MySQL uses `REGEXP` (or `REGEXP_LIKE(string, pattern)`), e.g. `WHERE firstname REGEXP '^R'`.

**Ranges**
```sql
SELECT * FROM table_name WHERE pages >= 290 AND pages <= 300;
-- equivalent
SELECT * FROM table_name WHERE pages BETWEEN 290 AND 300;
```

**`IN`-operator**
```sql
SELECT * FROM table_name WHERE country IN ('...', '...', ...);
```

### Sorting

Use the `ORDER BY` clause:
- `ORDER BY title`: sorted by title, ascending by default.
- `ORDER BY title DESC`: sorted descending.
- `ORDER BY 2`: sorted by the second column; `2` is the column sequence number.

### Grouping results

**Example.** Display each country and the number of authors for each country:
```sql
SELECT country, COUNT(country) FROM Author GROUP BY country;
```
You can name the result column with `AS`: `COUNT(country) AS column_name`.

`HAVING` adds a condition to grouped results, i.e. it works like `WHERE` but only for `GROUP BY`.
- `WHERE` filters the *rows before* grouping,
- `HAVING` filters the *groups after* grouping and can therefore use aggregate functions.

**Example.** Countries with more than one author:
```sql
SELECT country, COUNT(country) AS num_authors
FROM Author
GROUP BY country
HAVING COUNT(country) > 1;
```
With `WHERE COUNT(country) > 1` instead you get `ERROR: aggregate functions are not allowed in WHERE`.

**Example (`WHERE` and `HAVING` together).** Countries with at least two authors born in 1970 or later:
```sql
SELECT country, COUNT(*) AS num_authors
FROM Author
WHERE birth_date >= '1970-01-01'
GROUP BY country
HAVING COUNT(*) >= 2
ORDER BY num_authors DESC;
```
The order of the clauses is `WHERE` -> `GROUP BY` -> `HAVING` -> `ORDER BY` -> `LIMIT`.

*MySQL also allows the alias in `HAVING`, e.g. `HAVING num_authors > 1`; this does not work in PostgreSQL*
(`ERROR: column "num_authors" does not exist`).
In PostgreSQL repeat the aggregate function: `HAVING COUNT(country) > 1`.

**Example from the leet-challenges.** In
[The Number of Employees Which Report to Each Employee](../leet-challenges/5-advanced-select-and-joins/1731-the-number-of-employees-which-report-to-each-employee.ipynb)
the employees are grouped by their manager and the group of employees without a manager is removed:
```sql
SELECT reports_to AS manager_id, COUNT(*) AS reports_count, ROUND(AVG(age), 0) AS average_age
FROM Employees
GROUP BY reports_to
HAVING reports_to IS NOT NULL;
```
Since the condition `reports_to IS NOT NULL` contains no aggregate function,
`WHERE reports_to IS NOT NULL` (before `GROUP BY`) gives the same result here.

## Built-in database functions

*Goal.* Reduce network traffic and save bandwidth,
since the data is processed in the database instead of being sent to the application first.

There are aggregate and scalar functions.

### Aggregate functions

- *Input:* collection of values.
- *Output:* single value.

E.g. `SUM()`, `MIN()`, `MAX()`, `AVG()` (= mean).

```sql
SELECT SUM(column_name) FROM table_name;
SELECT MIN(ID) FROM table_name WHERE column_name = '...';
```

You can perform operations between columns:
```sql
SELECT AVG(COST / QUANTITY) FROM table_name WHERE ...;
```

**Integer division.**
*In PostgreSQL (and SQLite) `/` returns an integer if both inputs are integers:*
the result is truncated towards zero, e.g. `7 / 2 = 3` and `-7 / 2 = -3`
(the remainder is `7 % 2 = 1`).
So if `COST` and `QUANTITY` are `INTEGER` columns, the query above averages the truncated quotients:
for the rows `(7, 2)` and `(10, 4)` it returns `(3 + 2) / 2 = 2.5` instead of `(3.5 + 2.5) / 2 = 3.0`.
In MySQL `/` always returns a decimal number (`7 / 2 = 3.5000`), integer division is `DIV`.

To get the exact quotient in PostgreSQL, convert one of the inputs with `::numeric`:
```sql
SELECT 7::numeric / 2;                                           -- 3.5000000000000000
SELECT AVG(COST::numeric / QUANTITY) FROM table_name WHERE ...;
```
- `x::numeric` converts `x` to the data type `NUMERIC`, a decimal number with exact arithmetic.
  Then the division returns a decimal number instead of an integer.
- `::type` is PostgreSQL syntax. The standard SQL version `CAST(x AS NUMERIC)` works in all dialects.
- Other possibilities: `x * 1.0 / y` or `x::float / y` (floating point number, `DOUBLE PRECISION`).
  But `ROUND(value, 2)` with two arguments only exists for `NUMERIC` in PostgreSQL,
  `ROUND(7::float / 3, 2)` gives an error. Therefore `::numeric` is the best choice together with `ROUND`.

**Example from the leet-challenges.** In
[Average Selling Price](../leet-challenges/3-basic-aggregate-functions/1251-average-selling-price.ipynb)
the columns `units` and `price` are `INTEGER`, so `SUM(total)` and `SUM(units)` are integers, too.
Without `::NUMERIC` the average price would be truncated to an integer:
```sql
ROUND(SUM(total)::NUMERIC / SUM(units), 2)
```
In [Average Time of Process per Machine](../leet-challenges/2-basic-joins/1661-average-time-of-process-per-machine.ipynb)
the timestamps are floating point numbers and `::numeric` is needed so that `ROUND( _ , 3)` works.

### Scalar functions

`ROUND`, `LENGTH` (length of a string), `UCASE` (uppercase), `LCASE` (lowercase).

*`UCASE` and `LCASE` work in MySQL and Db2, but not in PostgreSQL.*
In PostgreSQL use `UPPER` and `LOWER` (they also work in MySQL, Db2 and SQLite).

Upper- and lowercase functions are good to use in the `WHERE` clause.
*MySQL:* `WHERE LCASE(name) = 'john'`, *PostgreSQL:* `WHERE LOWER(name) = 'john'`
matches independent of capitalization.

### Date and time functions

The database has the data types `DATE`, `TIME`, `TIMESTAMP`
and functions such as `YEAR()`, `MONTH()`, `DAY()`.
*These functions work in MySQL and Db2, but not in PostgreSQL:*
```sql
SELECT DAY(date_column) FROM table_name;
```
In PostgreSQL use `EXTRACT` (also works in MySQL):
```sql
SELECT EXTRACT(DAY FROM date_column) FROM table_name;
-- similarly EXTRACT(YEAR FROM ...), EXTRACT(MONTH FROM ...)
```

You can perform operations with dates and get the current date and time,
e.g. return the difference between today and a date column in years, months, days.
*`FROM_DAYS` and `DATEDIFF` only work in MySQL:*
```sql
SELECT FROM_DAYS(DATEDIFF(CURRENT_DATE, date_column)) FROM table_name;
```
In PostgreSQL:
```sql
SELECT AGE(CURRENT_DATE, date_column) FROM table_name;   -- e.g. 51 years 9 mons 24 days
SELECT CURRENT_DATE - date_column FROM table_name;       -- difference in days, e.g. 18925
```