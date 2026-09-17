# Hashing

> Folder: `01_DSA/02_Hashing`

## Why this matters
Turns most O(n^2) scans into O(n). Also the source of the most common OA trap: hash collisions and ordering assumptions.

## Must-know checklist
- [ ] Hash map / hash set APIs and average vs worst case
- [ ] Frequency maps, counting, first-unique patterns
- [ ] Two-sum family and k-sum reduction
- [ ] Prefix-sum + hashmap (subarray sum equals k)
- [ ] Custom hash for pairs/tuples; hashing structs
- [ ] When hashing fails: need ordering → use a tree map

## Online-assessment angle
Anti-hash tests exist on Codeforces-style OAs; know a randomised custom hash for C++ unordered_map.

## Interview angle
Explain collision resolution (chaining vs open addressing) and load factor — a standard warm-up question.

## Suggested files in this folder
- `concepts.md`
- `patterns.md`
- `problems-solved.md`
- `pitfalls.md`
- `flashcards.md`

---
Revision rule: after every session, move anything you got wrong into `/10_Mistake_Log_and_Revision/`.
