---
title: All you should know about Postgresql and sql in general
date: 2019-07-01 19:30:50
categories: developer
permalink: /:categories/psql
---

## Data Modelling: Day 1

- What are the Entities?
- How are they related?

### DDL
- inserts, creates, alters, constraints, transactions, sequences, exclusion constraints

### Exclusion constraints specifics


## DML: Day 2

SELECT   - projection, reads from left to right
FROM     - working set, this where you should start reading from
WHERE    - filters, only select some from above working set that match these conditions, operate on any value expressions not only on columns, depends on Truthy value
GROUP BY - aggregating the results of working set after filtering by where
HAVING   - works on results of the aggregates
ORDER BY - ordering that is done finally and has access to all aliases

Order in which an sql is executed
FROM -> WHERE -> AGGREGATES -> HAVING -> SELECT -> ORDER


### Indexes
Design considerations for creating an Index
- read/write ratio
- indexes can be created on value expressions
- avoid creating indexes on large value columns (like json), rather extract the value and add new columns instead

### Locks
Use it to avoid inconsistent state.

Types
1. Exclusive Row Locks (Pessimistic lock)
2. Advisory Locks
3. Optimistic Locks

In general it's a bad idea to use a lock without a transaction.

Use case for locks are typically banking transactions.

### Isolation Levels
<Need to check>

## Migrations
- Always make sure to add a new column that references the old column so that data/information loss in avoided. Always maintain the old data so that it can be referenced back.


Some other keywords to check out later
- common table expressions
- exclusion constraints
- views (materialised specifically)

Some takeaways
- working set, tables first
- tables are table like structure
