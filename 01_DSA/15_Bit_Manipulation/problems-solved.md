# Bit Manipulation — Problems Solved

| # | Problem | Trick | Done | Unaided | Time | Insight |
|---|---|---|---|---|---|---|
| 1 | Single Number | XOR everything | ☐ | ☐ | | |
| 2 | Single Number II (thrice) | ones/twos accumulators | ☐ | ☐ | | |
| 3 | Single Number III (two singles) | `xor & -xor` partition | ☐ | ☐ | | |
| 4 | Missing Number | XOR indices and values | ☐ | ☐ | | |
| 5 | Number of 1 Bits | Kernighan | ☐ | ☐ | | |
| 6 | Counting Bits | `dp[i>>1] + (i&1)` | ☐ | ☐ | | |
| 7 | Power of Two / Four | `x & (x-1)` | ☐ | ☐ | | |
| 8 | Reverse Bits | Shift and accumulate | ☐ | ☐ | | |
| 9 | Sum of Two Integers | XOR + carry loop | ☐ | ☐ | | |
| 10 | Bitwise AND of Numbers Range | Common prefix | ☐ | ☐ | | |
| 11 | Subsets (bitmask version) | Enumerate masks | ☐ | ☐ | | |
| 12 | Maximum Product of Word Lengths | 26-bit masks, no-overlap test | ☐ | ☐ | | |
| 13 | Maximum XOR of Two Numbers | Binary trie (`11`) | ☐ | ☐ | | |
| 14 | Gray Code | `i ^ (i >> 1)` | ☐ | ☐ | | |
| 15 | Total Hamming Distance | Per-bit counting | ☐ | ☐ | | |
| 16 | Divide Two Integers | Shift-and-subtract | ☐ | ☐ | | |
| 17 | Partition to K Equal Sum Subsets | Bitmask DP | ☐ | ☐ | | |
| 18 | Beautiful Arrangement | Bitmask DP | ☐ | ☐ | | |

## Re-derive without notes
- [ ] `x & (x-1)` and `x & -x`, with the reason each works
- [ ] Single Number III
- [ ] Submask enumeration and the 3ⁿ bound
- [ ] Sum of Two Integers, including Python's 32-bit masking

## Notes / scratch
