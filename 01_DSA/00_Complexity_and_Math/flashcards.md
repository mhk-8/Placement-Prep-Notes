# Complexity & Math — Flashcards

> Cover the answers. Write each answer before revealing. This is the D+1/D+3/D+7/D+21 material for this folder.

## Questions

1. State the three cases of the master theorem.
2. What is the intuition behind comparing a to b^d?
3. Solve T(n) = 2T(n/2) + O(n).
4. Solve T(n) = T(n/2) + O(n).
5. Why is dynamic-array push-back O(1) amortised?
6. What is the amortised cost of a Union-Find operation with both optimisations?
7. What does Σ_{i=1..n} n/i evaluate to, and where does it appear?
8. What is the comparison-sort lower bound, and what is the argument?
9. How do you avoid overflow in `mid = (lo + hi) / 2`?
10. Why is 10⁹+7 chosen as a modulus?
11. State Fermat's little theorem and its use for modular inverse.
12. How do you compute a modular inverse when the modulus is not prime?
13. What is the time complexity of the sieve of Eratosthenes?
14. Why does the sieve's inner loop start at i·i?
15. How do you get nCr mod p in O(1) per query?
16. State the pigeonhole principle and one problem it solves.
17. What is the difference between O, Θ and Ω?
18. What is the worst case of hash-map lookup, and why?
19. Is building a heap O(n) or O(n log n)?
20. What does `-7 % 3` evaluate to in Python and in C++?

---

## Answers

1. For T(n)=a·T(n/b)+O(n^d): a<b^d → Θ(n^d); a=b^d → Θ(n^d log n); a>b^d → Θ(n^{log_b a}).
2. n^d is the work at the root; n^{log_b a} is the number of leaves. Whichever grows faster dominates; a tie means every level costs the same, and there are log n levels.
3. Θ(n log n) — merge sort. (a=2, b=2, d=1 → a = b^d.)
4. Θ(n) — the work halves each level, so the geometric sum is dominated by the first term. Average quickselect.
5. Doubling on overflow means total copy work across n pushes is 1+2+4+…+n < 2n, so O(1) per push over the sequence.
6. O(α(n)), inverse Ackermann — at most 4 for any realistic n. Effectively O(1).
7. n·Hₙ ≈ Θ(n log n). It is the harmonic bound behind the sieve and divisor-enumeration loops.
8. Ω(n log n). A comparison sort is a decision tree with n! leaves, so its height is ≥ log₂(n!) = Θ(n log n).
9. Write `lo + (hi - lo) / 2`.
10. It is prime (so Fermat's inverse works) and just under 2³⁰, so products of two residues fit in 64 bits.
11. If p is prime and gcd(b,p)=1 then b^{p−1} ≡ 1 (mod p), hence b⁻¹ ≡ b^{p−2} (mod p).
12. Extended Euclid: solve ax + my = gcd(a,m); the inverse exists only when gcd(a,m)=1.
13. O(n log log n) time, O(n) space.
14. Every composite below i·i already has a smaller prime factor and has been marked.
15. Precompute factorials and inverse factorials to N in O(N) (inverse factorials by a single backward pass), then nCr = fact[n]·inv_fact[r]·inv_fact[n−r].
16. n+1 items in n boxes forces a box with ≥ 2. Used for "a duplicate must exist" and cycle/collision arguments.
17. O is an upper bound, Ω a lower bound, Θ both (tight). They are orthogonal to best/average/worst case.
18. O(n) — if every key hashes to the same bucket. The O(1) is average-case.
19. Building is O(n) (bottom-up heapify); n successive pushes are O(n log n).
20. Python: 2. C++: −1. Python floors towards −∞; C++ truncates towards 0.
