# Relational databases

This repository contains:

- **[Notes](notes/0-contents.md):** my notes on SQL and related topics, basics, joins, advanced SQL and using SQL from Python or Bash.
  Start with the [table of contents](notes/0-contents.md).
- **[LeetCode challenges](leet-challenges/):** my solutions to the LeetCode *Top SQL 50* study plan written down in Jupyter notebooks using SQL magic.
  [0-intro.ipynb](leet-challenges/0-intro.ipynb) explains the setup,
  and [blueprint.ipynb](leet-challenges/blueprint.ipynb) is the template for each exercise.
- **[freeCodeCamp projects](free-code-camp/):** Bash scripts and SQL database dumps from the freeCodeCamp *Relational Databases* course.

# Running the code locally

## Prerequisites

- [PostgreSQL](https://www.postgresql.org/download/) running on `localhost:5432` with the user `postgres`,
- [uv](https://docs.astral.sh/uv/) (installs Python 3.12 as pinned in `.python-version`),
- Bash (on Windows e.g. Git Bash or WSL) for the freeCodeCamp scripts.

## Notebooks

Install the dependencies (`jupysql`, `psycopg`, `pgspecial` and `ipykernel`) into a local virtual environment:
```bash
uv sync
```
Open a notebook, e.g. in VS Code, and select the kernel of `.venv`.

## freeCodeCamp scripts

The `.sql` files are dumps created with `pg_dump`.
Restore a database (e.g. `bikes`) and run the matching script:
```bash
psql -U postgres < free-code-camp/sql-and-bash/bike-shop/bikes.sql
cd free-code-camp/sql-and-bash/bike-shop
bash bike-shop.sh
```
The scripts connect via the `PSQL` variable at their top.
The scripts in `workshop1` use the user `freecodecamp` from the course environment.

# Sources
- [freeCodeCamp](https://www.freecodecamp.org/learn/relational-databases-v9),
- [W3](https://www.w3schools.com/sql/),
- [Coursera](https://www.coursera.org/learn/sql-data-science),
- [LeetCode](https://leetcode.com/studyplan/top-sql-50/).
