# Pattern Recognition Drill

> 60 problem statements. Name the pattern in **under 10 seconds** each. This is the drill you do daily for five minutes, and in the twenty minutes before an OA.
> Do not solve them. Only name the pattern.

## Statements

1. Find two numbers in an unsorted array that sum to a target.
2. Find two numbers in a **sorted** array that sum to a target.
3. Longest substring with no repeating characters.
4. Count subarrays whose sum equals k, with negative numbers present.
5. Maximum sum of any contiguous subarray.
6. Maximum in every window of size k.
7. The k-th largest element in an array.
8. Merge k sorted linked lists.
9. Median of a stream of integers.
10. Minimum eating speed to finish all bananas within h hours.
11. Smallest capacity to ship all packages within D days.
12. The first index where the array value is at least x.
13. For each day, how many days until a warmer temperature.
14. Largest rectangle in a histogram.
15. Trapping rain water.
16. All subsets of a set of 15 elements.
17. All valid arrangements of n queens on an n×n board.
18. Minimum number of coins to make an amount.
19. Number of distinct ways to climb n stairs.
20. Can this array be split into two subsets of equal sum?
21. Edit distance between two strings.
22. Longest increasing subsequence, n = 10⁵.
23. Number of islands in a grid.
24. Minimum minutes until every orange is rotten.
25. Can all courses be finished given prerequisites?
26. Shortest path in a weighted graph with positive weights.
27. Are these two accounts the same person (shared emails)?
28. Minimum cost to connect all points.
29. Shortest transformation sequence between two words.
30. Merge all overlapping intervals.
31. Minimum meeting rooms required.
32. Minimum arrows to burst all balloons.
33. The element that appears once while all others appear twice.
34. Count set bits for every number from 0 to n.
35. Detect a cycle in a linked list and find its start.
36. Design a cache with O(1) get and put and LRU eviction.
37. All words in a dictionary starting with a given prefix.
38. Find all words from a list present in a character grid.
39. Range sum queries on an array that also receives point updates.
40. Range minimum queries on an array that never changes.
41. Validate that a binary tree is a BST.
42. Lowest common ancestor of two nodes in a binary tree.
43. Serialize and deserialize a binary tree.
44. Maximum path sum in a binary tree.
45. Rotate an array by k positions using O(1) extra space.
46. Sort an array containing only 0s, 1s and 2s in one pass.
47. Maximum XOR of any two numbers in an array.
48. Longest substring containing at most k distinct characters.
49. Number of subarrays containing exactly k distinct integers.
50. Given n ≤ 18 cities, find the shortest tour visiting all of them.
51. Minimum number of jumps to reach the end of an array.
52. Can you reach the last index of the array?
53. Best time to buy and sell a stock at most k times.
54. Group a list of words into anagram sets.
55. Find the duplicate in an array of n+1 values from 1..n, O(1) space.
56. Cheapest flight from A to B using at most k stops.
57. Apply 10⁵ range increments, then read the final array.
58. The k closest points to the origin.
59. Longest palindromic substring.
60. Number of ways to make change for an amount, order irrelevant.

---

## Answers

1. Hash map, complement lookup · 2. Two pointers · 3. Sliding window (last-index jump) · 4. Prefix sum + hash map · 5. Kadane
6. Monotonic deque · 7. Min-heap of size k, or quickselect · 8. Heap of list heads · 9. Two heaps · 10. Binary search on the answer
11. Binary search on the answer · 12. Binary search, lower_bound · 13. Monotonic stack · 14. Monotonic stack · 15. Two pointers or monotonic stack
16. Backtracking or bitmask · 17. Backtracking with diagonal sets · 18. Unbounded knapsack DP · 19. 1-D DP · 20. Subset-sum DP
21. String DP (2-D) · 22. Patience sorting + binary search, O(n log n) · 23. Grid DFS/BFS · 24. Multi-source BFS · 25. Topological sort
26. Dijkstra · 27. Union-Find · 28. MST (Prim or Kruskal) · 29. BFS on an implicit graph · 30. Sort by start, merge
31. Sweep line, or a min-heap of end times · 32. Sort by end, greedy · 33. XOR everything · 34. DP, `dp[i>>1] + (i&1)` · 35. Floyd's fast/slow pointers
36. Hash map + doubly linked list · 37. Trie · 38. Trie + grid DFS · 39. Fenwick tree · 40. Sparse table
41. Preorder with bounds passed down · 42. Postorder split · 43. Preorder with null markers · 44. Postorder, return branch gain, track through-path · 45. Triple reversal
46. Dutch national flag · 47. Binary trie · 48. Sliding window, at-most-k · 49. atMost(k) − atMost(k−1) · 50. Bitmask DP (TSP)
51. Greedy level expansion (BFS) · 52. Greedy furthest reach · 53. State-machine DP · 54. Hash map with a canonical key · 55. Floyd's cycle detection on values
56. Bellman-Ford limited to k+1 rounds · 57. Difference array · 58. Bounded max-heap · 59. Expand around centre · 60. Counting knapsack, coins outer

---

## Scoring

| Correct in 10s | Verdict |
|---|---|
| 55–60 | OA-ready on recognition |
| 45–54 | Solid; drill the misses |
| 30–44 | Recognition is your bottleneck, not knowledge |
| < 30 | Go back to the folders; do not add new problems yet |

Record the misses in `problems-solved.md` — a pattern you cannot *name* is a pattern you will never *apply* under a timer.
