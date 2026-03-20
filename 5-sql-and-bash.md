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
As already observed, it is insert every row separately into the tables of your database.

Given the 'rough data', you can write a script which automates this process for you.

In the [workshop](sql_and_bash/workshop1), we are given `csv`-files
with rough data on students and their 
The `cat <filename>` command in bash prints the whole file
into the terminal.
You can use this in a `while` loop to run through the entries in the table. 
