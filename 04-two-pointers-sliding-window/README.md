# Two Pointers & Sliding Window — Practice Sheet

Practice problems in JavaScript. **No pattern hints in the question table.** Solutions are linked only after being solved.

> Two Sum (#1) is included as the first completed exercise at the user's request. Its optimal approach uses a **Hash Map**, not two pointers or sliding window.

## Problems

| Done | # | Question | Difficulty |
|---|---:|---|---|
| ✅ | 1 | [Two Sum](1-two-sum.md) | Easy |
| ✅ | 167 | [Two Sum II — Input Array Is Sorted](167-two-sum-ii.md) | Medium |
| ✅ | 392 | [Is Subsequence](392-is-subsequence.md) | Easy |
| ✅ | 28 | [Find the Index of the First Occurrence in a String](28-find-first-occurrence.md) | Easy |
| ✅ | 160 | [Intersection of Two Linked Lists](160-intersection-of-two-linked-lists.md) | Easy |
| ⬜ | 125 | [Valid Palindrome](https://leetcode.com/problems/valid-palindrome/) | Easy |
| ✅ | 15 | [3Sum](15-3sum.md) | Medium |
| ✅ | 11 | [Container With Most Water](11-container-with-most-water.md) | Medium |
| ✅ | 42 | [Trapping Rain Water](42-trapping-rain-water.md) | Hard |
| ⬜ | 283 | [Move Zeroes](https://leetcode.com/problems/move-zeroes/) | Easy |
| ⬜ | 643 | [Maximum Average Subarray I](https://leetcode.com/problems/maximum-average-subarray-i/) | Easy |
| ✅ | 3 | [Longest Substring Without Repeating Characters](3-longest-substring-without-repeating-characters.md) | Medium |
| ⬜ | 209 | [Minimum Size Subarray Sum](https://leetcode.com/problems/minimum-size-subarray-sum/) | Medium |
| ⬜ | 567 | [Permutation in String](https://leetcode.com/problems/permutation-in-string/) | Medium |

**Progress: 9 / 14 solved.** Do not mark unchecked questions as completed.

## Saved solutions

| Problem | Approaches | Notes |
|---|---|---|
| [1. Two Sum](1-two-sum.md) | Brute force O(n²), optimized Hash Map O(n) | [Code, dry runs, diagram, comparison](1-two-sum.md) |
| [167. Two Sum II](167-two-sum-ii.md) | Binary Search O(n log n), **Two Pointers O(n) — best** | [Why two pointers, both codes, dry runs, comparison](167-two-sum-ii.md) |
| [392. Is Subsequence](392-is-subsequence.md) | Two Pointers O(n) time, O(1) space | [Original code, explanation, dry run, edge cases](392-is-subsequence.md) |
| [28. Find First Occurrence](28-find-first-occurrence.md) | Two Pointers + Backtracking, Sliding Window, **KMP O(n+m) (optimal)** | [All 3 codes, LPS explanation, KMP dry runs, comparison](28-find-first-occurrence.md) |
| [160. Intersection of Two Linked Lists](160-intersection-of-two-linked-lists.md) | Length Alignment O(m+n), Pointer Switching O(m+n) | [Both original codes, diagrams, dry runs, edge cases](160-intersection-of-two-linked-lists.md) |
| [11. Container With Most Water](11-container-with-most-water.md) | Two Pointers O(n) time, O(1) space | [Original code, why shorter pointer moves, diagram, dry run](11-container-with-most-water.md) |
| [15. 3Sum](15-3sum.md) | Sorting + Two Pointers O(n²) | [Original code, duplicate handling, diagram, dry run, edge cases](15-3sum.md) |
| [42. Trapping Rain Water](42-trapping-rain-water.md) | Prefix/suffix maxima: separate loops and combined loop, O(n) time / O(n) space | [Both original codes, indexing diagram, dry run, edge cases](42-trapping-rain-water.md) |

| [3. Longest Substring Without Repeating Characters](3-longest-substring-without-repeating-characters.md) | Sliding Window + Set, O(n) time | [Original code, corrected declaration, invariant, dry run, mistakes](3-longest-substring-without-repeating-characters.md) |

[← Curriculum](../README.md) · [Dashboard](../DASHBOARD.md)
