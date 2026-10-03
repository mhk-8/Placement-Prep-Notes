
# Adobe — Prep Plan, Question Bank and Debriefs

---

## 1. Topic priorities ⭐⭐

| Priority | Topic | Source | Status |
|---|---|---|---|
| **P1** | **C/C++ output prediction** ⭐⭐⭐ — 40 snippets | `../../03_Languages/CPP/gotchas.md` | ☐ |
| **P1** | OOP — virtual functions, destructor order, slicing, SOLID | `../../06_Online_Assessments/05_MCQ_Core_CS_Banks/OOPs.md` | ☐ |
| **P1** | DSA Medium — arrays, strings, hash maps, trees | `../../01_DSA/` | ☐ |
| **P2** | **DBMS** ⚠️ your thinnest fundamentals area | `../../06_Online_Assessments/05_MCQ_Core_CS_Banks/DBMS.md` | ☐ |
| **P2** | OS fundamentals | `../../06_Online_Assessments/05_MCQ_Core_CS_Banks/Operating_Systems.md` | ☐ |
| **P2** | Matrix / geometry problems ⭐ | `../../01_DSA/` | ☐ |
| **P2** | **DP** ⚠️ | `../../01_DSA/` | ☐ |
| **P3** | Aptitude (light) | `../../06_Online_Assessments/02_Aptitude_and_Quant/` | ☐ |

---

## 2. The two-week plan ⭐⭐⭐

| Date | Task | Done |
|---|---|---|
| D-14 | Research protocol; confirm the role (MTS vs ML) and the section weights | ☐ |
| D-13 | **C/C++ output prediction: 20 snippets, traced on paper** ⭐ | ☐ |
| D-12 | **20 more snippets** — focus on pointers, sizeof, precedence, static | ☐ |
| D-11 | OOP MCQ bank, all 20 questions | ☐ |
| D-10 | DSA: 3 Medium. Arrays and strings | ☐ |
| D-9 | **DBMS MCQ bank** ⚠️ — normalisation, ACID, isolation, indexing | ☐ |
| D-8 | Matrix/geometry: rotate image, spiral, set zeroes, flood fill, overlapping rectangles | ☐ |
| D-7 | OS MCQ bank | ☐ |
| D-6 | DSA: 3 Medium. Trees and hash maps | ☐ |
| D-5 | DP ⚠️: coin change, LCS, edit distance | ☐ |
| D-4 | **Mock: CS MCQ rapid-fire + 2 coding problems in 90 min** | ☐ |
| D-3 | Image pipeline project: 5-minute version; the align-corners explanation ⭐ | ☐ |
| D-2 | Adobe Research — one paper abstract; 2 questions to ask | ☐ |
| D-1 | Pre-interview routine only. Nothing new ⚠️ | ☐ |

---

## 3. Seeded question bank

### C/C++ output prediction ⭐⭐⭐ — the Adobe signature
```
POINTERS AND ARRAYS
  □ *(arr + i) vs arr[i] vs i[arr]  (all the same — and why)
  □ sizeof(array) vs sizeof(pointer); array decay in a function parameter
  □ Pointer arithmetic on different types
  □ char* vs char[]; modifying a string literal (UB)
  □ Double pointers; arrays of pointers vs pointers to arrays

SIZEOF AND PADDING
  □ Compute sizeof a struct with mixed types  ⭐ very common
  □ Struct vs union size
  □ Effect of member ORDER on struct size

PRECEDENCE AND SEQUENCING ⚠️ mostly UB
  □ i++ + ++i ; a = a++ ; printf("%d %d", i++, i++)
  □ ++i vs i++ inside expressions and function arguments
  □ Short-circuit evaluation: a && b++ ; a || b++
  □ Comma operator

STORAGE AND LINKAGE
  □ static local variables retaining value across calls  ⭐
  □ static global vs extern
  □ Order of initialisation of globals

C++ OOP
  □ Constructor / destructor ORDER with inheritance and member objects  ⭐
  □ Virtual function dispatch; what happens WITHOUT virtual
  □ Virtual destructor — what leaks without one  ⭐⭐
  □ Object slicing on assignment by value  ⭐
  □ Calling a virtual function from a constructor (⚠️ dispatches to the base version)
  □ Diamond problem and virtual inheritance
  □ Operator overloading; the copy constructor vs assignment operator
  □ const member functions; const correctness
  □ Function hiding vs overriding vs overloading
  □ RAII; what the destructor does during exception unwinding
```
⚠️ **Trace on paper.** Eyeballing these is how you get them wrong.

### DSA (Medium)
```
- Arrays: rotate, rearrange, missing/duplicate, max subarray
- Strings: palindromes, anagrams, compression, longest substring
- Hash maps: two sum, group anagrams, subarray sum = k, top-K
- Trees: traversals, validate BST, LCA, level order, diameter
- MATRIX ⭐: rotate in place, spiral order, set zeroes, search a 2-D matrix, flood fill
- GEOMETRY ⭐: overlapping rectangles, area of union, point in polygon
- DP: coin change, LCS, edit distance, grid paths
- Linked lists, stacks, heaps
```

### OOP (MCQ and interview)
```
- Four pillars; abstraction vs encapsulation
- Overloading vs overriding — the full table
- Why a static method cannot be overridden (it is hidden; resolved by reference type)
- Abstract class vs interface
- Virtual destructor; object slicing; vtable
- SOLID, with Liskov and the Rectangle/Square example  ⭐
- Composition over inheritance, and why
```

### OS / DBMS
```
OS   : process vs thread, deadlock, scheduling, virtual memory, paging, thrashing
DBMS : normalisation to BCNF, ACID, isolation levels, indexing, joins, DELETE vs TRUNCATE
       ⚠️ your thinnest area — do the full MCQ bank
```

### Project deep dive
```
- "Walk me through the image preprocessing pipeline."  ⭐ lead here
- "What is align-corners and why does it matter?"  ⭐⭐ the question you want
- "Why HWC rather than CHW?"
- "Why a tolerance rather than exact equality in validation?"
- "How would you fuse the four stages, and why didn't you?"
- "Tell me about the multi-task vision model — how did you balance three losses?"
```

### Behavioural
```
- Why Adobe?
- Why Mechanical → CS?
- Why is your M.Tech CGPA 7.5?
- Tell me about a bug that was hard to find
```

---

## 5. Debriefs ⭐⭐⭐

Full template: `../../07_Interviews/06_Post_Interview_Debriefs/Debrief_Template.md`

### Round 1 — `<date>` — `<type>`
```
INTERVIEWER        :
STRUCTURE / TIMING :
QUESTIONS ASKED (verbatim):
  1.
  2.
  3.
CODING PROBLEM     :
  My approach     :
  Correct approach (write it out NOW):
WHAT I FUMBLED, AND THE RIGHT ANSWER:
  1.
  2.
TONE / STYLE FOR THE NEXT ROUND:
OUTCOME            :
THREE ACTIONS:
  1.
  2.
  3.
```

### Round 2 — `<date>` — `<type>`
```
(same structure)
```

### Round 3 — `<date>` — `<type>`
```
(same structure)
```

---

## 6. Questions I collected myself ⭐⭐⭐

| Date | Source (senior / my attempt) | Round | Question | Topic |
|---|---|---|---|---|
| | | | | |
| | | | | |
| | | | | |

---

## 7. Corrections to make to `00-process-and-oa.md`

```
□
□
```


---

## 8. The 30-second pre-interview refresh ⭐

```
□ Struct padding: members align to their own size; total pads to the largest member's alignment
□ Constructor order: BASE → members in DECLARATION order → derived body. Destructors reverse
□ No virtual destructor + delete through base pointer = only ~Base runs = leak (UB)
□ Object slicing: assigning Derived to Base BY VALUE loses the derived part and polymorphism
□ A virtual call from a constructor dispatches to the BASE version
□ i++ + ++i and friends are UNDEFINED — give the expected answer in an MCQ, say "UB" in an interview
□ My lead project here is the IMAGE PIPELINE, and the hook is align-corners
```
