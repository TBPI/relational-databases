# Relational databases

This is taken from [freeCodeCamp](https://www.freecodecamp.org/learn/relational-databases-v9).

We follow their structure:

1) [Code Editors](1-code-editors.md)
2) [Bash Fundamentals](2-bash-fundamentals.md)
3) [SQL and PostgreSQL](3-sql-and-postgresql.md)
4) [Bash scripting](4-bash-scripting.md)
5) [SQL and Bash](5-sql-and-bash.md)
6) [Git](git.md)

There is as well a [document](older-work.md) containing basic SQL commands from a course of [W3](https://www.w3schools.com/sql/).

*Remark.*
We add the package `jupysql` (which is the successor of `ipython-sql`) so that `%sql` and `%%sql` works.
In order to work with PostgreSQL we use the flag `psycopg`.
The full command is
```
uv add jupysql "psycopg[binary]"
```
