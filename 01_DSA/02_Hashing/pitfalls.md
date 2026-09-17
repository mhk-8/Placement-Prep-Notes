# Hashing — Pitfalls

## The prefix + hash-map family
- **Forgetting `seen[0] = 1`.** Every subarray that starts at index 0 is silently missed. This is the single most common bug in the whole pattern.
- Inserting the current prefix **before** querying it — an element gets counted against itself.
- Storing the last index instead of the first when you want the *longest* subarray.
- Forgetting to normalise a negative modulus in C++/Java: `((run % k) + k) % k`.

## Two Sum family
- Inserting before checking, so `x` pairs with itself when `target == 2x`.
- Returning values when the problem wants indices, or vice versa.
- Using a hash map for 3Sum and then mishandling duplicates — sort + two pointers is cleaner.

## C++ specifics
- `mp[key]` on a missing key **creates** it with a default value. Inside a membership test this silently doubles memory and corrupts counts. Use `.count()` or `.find()`.
- `unordered_map` has no ordering — iterating it produces an arbitrary order that may differ between runs. Never rely on it for output order.
- `pair`/`vector` keys need a custom hash for `unordered_map`.
- Anti-hash tests can force O(n²). Use a randomised hash (see `patterns.md`) on adversarial judges.

## Python specifics
- Lists and sets are unhashable; convert to `tuple` / `frozenset`.
- `dict` preserves insertion order (3.7+) — convenient, but do not depend on it in an interview answer without saying so.
- `defaultdict(int)` creates keys on *read*. `if seen[k]:` inserts `k`. Use `seen.get(k, 0)` when you must not mutate.
- Mutating a dict while iterating it raises. Iterate over `list(d.items())`.

## Java specifics
- Overriding `equals` without `hashCode` (or vice versa) breaks every map lookup — equal objects land in different buckets.
- `Integer` autoboxing caches only −128..127; `==` on boxed values outside that range compares references. Always use `.equals`.
- `HashMap` allows one null key; `Hashtable` and `ConcurrentHashMap` do not.

## Reasoning traps
- Claiming O(1) worst-case lookup. It is O(1) **average**, O(n) worst.
- Using a hash map where a 26- or 128-length array is simpler and faster.
- Reaching for a hash map when the data is sorted — two pointers is O(1) space.
- Using a hash map when ordering, floor/ceiling or range queries are needed.
- Hashing floating-point values (precision makes equality unreliable).
