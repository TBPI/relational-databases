# Python and SQL

## Accessing databases using Python

### API calls

The Python program talks to the database via API calls:
```text
User <--> Jupyter Notebook / Python program <--API calls--> DBMS
```
Calls: `CONNECT`, `SEND`, `EXECUTE`, `STATUS_CHECK` (OK comes back), `DISCONNECT`.

Each database system has its own DB API:

| Database               | DB API                         |
|------------------------|--------------------------------|
| MySQL                  | MySQL C API (low-level access) |
| PostgreSQL             | `psycopg2`                     |
| IBM Db2                | `ibm_db`                       |
| any database (in Java) | JDBC                           |

### DB-API

**DB-API** = Python's standard API for accessing relational databases.
- easy to implement and understand,
- consistent (same code for different databases),
- broader reach of database connectivity from Python:
  DB-API is a common standard which many database modules (e.g. `sqlite3`, `psycopg2`, `ibm_db`) follow.
  So with the same Python code pattern (connect, cursor, execute, fetch, close)
  you can reach many different databases, and switching the database mostly means changing the import and the connection.

**Main concepts**

1) *Connection objects:*
   - database connection,
   - manage transactions.

   Methods:
   - `.cursor()`: creates a cursor object,
   - `.commit()`: commits any pending transaction,
   - `.rollback()`: rolls back to the start of any pending transaction,
   - `.close()`: closes the database connection.
2) *Cursor objects:*
   - database queries,
   - scroll through the result set,
   - retrieve results.

   Methods: `.callproc()`, `.execute()`, ...

   You open a cursor to get access to the database and make queries.
   A cursor works like a file handle: you gain access to the query result.

Close the connection to avoid unused connections taking up resources.

**How to code**
```python
from dbmodule import connect
```
e.g. using `sqlite3`:
```python
import sqlite3
```

### Cheat sheet for SQLite

```python
import sqlite3

con = sqlite3.connect("INSTRUCTOR.db")  # connect to a database

# execute SQL statements -> need cursor object
cursor_obj = con.cursor()
cursor_obj.execute("...")
```

### SQL magic

Magic commands are not valid Python code but are used in notebooks.

1) *Line magics:* command prefixed with `%`, i.e. a command line in the shell.
   - `%pwd`,
   - `%ls`,
   - `%history` = command history,
   - `%lsmagic` = lists which magics exist.
2) *Cell magics:* prefixed with `%%`, apply to the whole cell.
   - `%%timeit` = time for executing the cell,
   - `%%HTML` = render the cell as HTML code,
   - `%%js` = run the cell as JavaScript.

To use SQL magic:
1) Install it: `pip install --user ipython-sql`.
2) Load it: `%load_ext sql`, then you can use `%sql` (line) and `%%sql` (cell).
3) Establish a connection to the database.

### Analyzing data with Python

- `df.to_sql("table_name", con)`: turns a pandas DataFrame object into an SQL table.
- `df = pd.read_sql("SQL QUERY", ...)`: read the result of an SQL query into a pandas DataFrame.

### Real-world datasets

- Often `.csv` files (comma-separated values).
- Splitting a query over multiple lines with `%sql`: use `\` at the end of the line.
- E.g. you can use `pandas.read_sql(...)`.
  Use `"..."` (double quotes) for column names, e.g. if they contain spaces or mixed case.

**Getting table and column names.**
*The table `sqlite_master` only exists in SQLite:*
```sql
SELECT name FROM sqlite_master WHERE type = 'table';
```
The standard way in PostgreSQL (and MySQL) is
```sql
SELECT table_name FROM information_schema.tables WHERE table_schema = 'public';
```
In `psql` you can also use `\dt` (tables) and `\d table_name` (columns of a table).
