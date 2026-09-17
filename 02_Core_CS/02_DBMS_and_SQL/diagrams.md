# DBMS — Diagrams

> Draw from memory. Interviewers ask for the ER diagram and the B+ tree by name.

---

## 1. ER notation

```
   ┌──────────┐            ◇              ┌──────────┐
   │  ENTITY  │────────< RELATIONSHIP >────│  ENTITY  │
   └──────────┘            ◇              └──────────┘
        │
     ○ attribute          ╔══════════╗  weak entity  (double rectangle)
     ◎ multivalued        ╚══════════╝
     ⊙ derived            <<relationship>> identifying (double diamond)
     _underlined_ = key   ═══ double line = TOTAL participation
```

**Cardinality on the line:** `1`, `N`, `M`. **Participation:** a single line is partial, a double line is total.

**Example**

```
   ┌────────────┐        1      ┌──────────────┐      N   ┌────────────┐
   │ DEPARTMENT │──────────────◇│  works_in    │◇─────────│  EMPLOYEE  │
   └────────────┘               └──────────────┘          └────────────┘
     _dept_id_                                              _emp_id_
      name                                                   name
      location                                               salary
                                                                │
                                                          ╔═════▼══════╗
                                                          ║  DEPENDENT ║  weak
                                                          ╚════════════╝
                                                            _name_ (partial key)
```

**Conversion rules:**
- 1:N → foreign key on the **N** side (`Employee.dept_id`)
- M:N → **new junction table**, primary key = both foreign keys
- Weak entity → its own table with the owner's key as part of a composite primary key
- Multivalued attribute → its own table

---

## 2. Normal forms as a ladder

```
   ┌─────────────────────────────────────────────────────────┐
   │ 1NF   atomic values, no repeating groups                │
   │ ┌─────────────────────────────────────────────────────┐ │
   │ │ 2NF   + no PARTIAL dependency                       │ │
   │ │       (non-prime attr on part of a composite key)   │ │
   │ │ ┌─────────────────────────────────────────────────┐ │ │
   │ │ │ 3NF  + no TRANSITIVE dependency                 │ │ │
   │ │ │      X→Y ⇒ X is a superkey OR Y is prime        │ │ │
   │ │ │ ┌─────────────────────────────────────────────┐ │ │ │
   │ │ │ │ BCNF   X→Y ⇒ X is a SUPERKEY, always        │ │ │ │
   │ │ │ │ ┌─────────────────────────────────────────┐ │ │ │ │
   │ │ │ │ │ 4NF   + no non-trivial MVD              │ │ │ │ │
   │ │ │ │ └─────────────────────────────────────────┘ │ │ │ │
   │ │ │ └─────────────────────────────────────────────┘ │ │ │
   │ │ └─────────────────────────────────────────────────┘ │ │
   │ └─────────────────────────────────────────────────────┘ │
   └─────────────────────────────────────────────────────────┘
```

The gap between 3NF and BCNF is exactly the case `X → Y` where X is not a super key but Y *is* prime — allowed by 3NF, forbidden by BCNF.

---

## 3. SQL logical execution order

```
   FROM        pick the driving table
     │
   JOIN        combine, producing the row fan-out
     │
   WHERE       filter ROWS            ← no aggregates, no SELECT aliases
     │
   GROUP BY    collapse into groups
     │
   HAVING      filter GROUPS          ← aggregates allowed here
     │
   SELECT      compute expressions, assign aliases
     │
   DISTINCT    de-duplicate
     │
   ORDER BY    sort                   ← SELECT aliases ARE visible here
     │
   LIMIT       truncate
```

This single diagram answers "why can't I use my alias in WHERE?", "why is my aggregate rejected in WHERE?", and "why is my LIMIT applied after the sort?".

---

## 4. Join types

```
   A          B            INNER            LEFT             FULL OUTER
  ┌───┐    ┌───┐          ┌───┐            ┌───┐            ┌───┐
  │ ▓▓│▓▓  │   │          │   │▓▓│         │▓▓▓│▓▓│         │▓▓▓│▓▓│▓▓▓│
  │   │    │   │           only the         all of A          everything
  └───┘    └───┘           overlap        + matches          from both
```

**Anti-join** (in A, not in B):
```sql
SELECT a.* FROM A LEFT JOIN B ON a.k = b.k WHERE b.k IS NULL;
```

---

## 5. B+ tree index

```
                        ┌─────────────┐
                        │   30 │ 60   │            internal: keys only
                        └──┬───┬───┬──┘
              ┌────────────┘   │   └────────────┐
        ┌─────▼─────┐   ┌──────▼─────┐   ┌──────▼─────┐
        │ 10 │ 20   │   │  40 │ 50   │   │  70 │ 80   │
        └──┬─┬────┬─┘   └──┬──┬────┬─┘   └──┬──┬────┬─┘
           │ │    │        │  │    │        │  │    │
   ┌───────▼─▼────▼────────▼──▼────▼────────▼──▼────▼───────┐
   │ [10]→[20]→[30]→[40]→[50]→[60]→[70]→[80]→[90]           │  LEAVES:
   └────────────────────────────────────────────────────────┘  keys + row ptrs
              linked list → makes range scans sequential
```

**Why this and not a B tree:** internal nodes hold no data, so they pack more keys per page and the tree is shallower (3–4 levels for millions of rows); and the linked leaves turn `BETWEEN` into one descent plus a sequential walk.

---

## 6. Isolation levels and anomalies

```
                  │ Dirty  │ Non-repeat │ Phantom │
  ────────────────┼────────┼────────────┼─────────┤
  READ UNCOMMITTED│   ✗    │     ✗      │    ✗    │   ✗ = anomaly possible
  READ COMMITTED  │   ✓    │     ✗      │    ✗    │   ✓ = prevented
  REPEATABLE READ │   ✓    │     ✓      │    ✗*   │
  SERIALIZABLE    │   ✓    │     ✓      │    ✓    │
                  └────────┴────────────┴─────────┘
       * InnoDB prevents phantoms here too, using next-key locks
```

More isolation ⇒ fewer anomalies ⇒ less concurrency. That trade-off is the whole design space.

---

## 7. Two-phase locking

```
  locks
  held
    │        ┌──────────────┐
    │       ╱                ╲
    │      ╱                  ╲
    │     ╱   GROWING          ╲  SHRINKING
    │    ╱   (acquire only)     ╲ (release only)
    │   ╱                        ╲
    └──┴──────────────┬───────────┴──────────> time
                   LOCK POINT
```

**The rule:** once any lock is released, no new lock may be acquired. **Strict 2PL** keeps every exclusive lock until commit — the vertical drop moves to the commit instant, which is what prevents cascading aborts.
