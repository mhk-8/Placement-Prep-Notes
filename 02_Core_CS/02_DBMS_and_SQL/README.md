# DBMS & SQL

> Folder: `02_Core_CS/02_DBMS_and_SQL`

## Why this matters
SQL is tested directly in OAs (query-writing sections) and DBMS theory in MCQs. For data/ML roles this is non-negotiable.

## Must-know checklist
- [ ] ER model → relational schema conversion
- [ ] Keys: super, candidate, primary, foreign, composite
- [ ] Normalisation: 1NF→BCNF, and when to denormalise
- [ ] Functional dependencies, closure, lossless decomposition
- [ ] Relational algebra basics
- [ ] SQL: joins (all types), GROUP BY/HAVING, subqueries, window functions, CTEs
- [ ] Indexes: B+ tree, hash, clustered vs non-clustered, when an index hurts
- [ ] Transactions: ACID, isolation levels, dirty/non-repeatable/phantom reads
- [ ] Concurrency control: 2PL, timestamp ordering, MVCC
- [ ] SQL vs NoSQL; CAP theorem
- [ ] Query plans and optimisation basics

## Online-assessment angle
Expect 2-4 SQL queries to write against a given schema, usually involving joins + aggregation + a window function. Time-box each.

## Interview angle
'N-th highest salary', 'find duplicates', 'self-join for manager hierarchy', 'explain isolation levels with an anomaly'.

## Suggested files in this folder
- `concepts.md`
- `numericals.md`
- `rapid-fire-qa.md`
- `diagrams.md`
- `flashcards.md`

---
Revision rule: after every session, move anything you got wrong into `/10_Mistake_Log_and_Revision/`.
