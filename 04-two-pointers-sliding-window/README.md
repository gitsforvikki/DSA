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
| ✅ | 160 | [Intersection of Two Linked Lists](https://leetcode.com/problems/intersection-of-two-linked-lists/) | Easy |
| ⬜ | 125 | [Valid Palindrome](https://leetcode.com/problems/valid-palindrome/) | Easy |
| ⬜ | 15 | [3Sum](https://leetcode.com/problems/3sum/) | Medium |
| ⬜ | 11 | [Container With Most Water](https://leetcode.com/problems/container-with-most-water/) | Medium |
| ⬜ | 283 | [Move Zeroes](https://leetcode.com/problems/move-zeroes/) | Easy |
| ⬜ | 643 | [Maximum Average Subarray I](https://leetcode.com/problems/maximum-average-subarray-i/) | Easy |
| ⬜ | 3 | [Longest Substring Without Repeating Characters](https://leetcode.com/problems/longest-substring-without-repeating-characters/) | Medium |
| ⬜ | 209 | [Minimum Size Subarray Sum](https://leetcode.com/problems/minimum-size-subarray-sum/) | Medium |
| ⬜ | 567 | [Permutation in String](https://leetcode.com/problems/permutation-in-string/) | Medium |

**Progress: 5 / 13 solved.** Do not mark unchecked questions as completed.

## Saved solutions

| Problem | Approaches | Notes |
|---|---|---|
| [1. Two Sum](1-two-sum.md) | Brute force O(n²), optimized Hash Map O(n) | [Code, dry runs, diagram, comparison](1-two-sum.md) |
| [167. Two Sum II](167-two-sum-ii.md) | Binary Search O(n log n), **Two Pointers O(n) — best** | [Why two pointers, both codes, dry runs, comparison](167-two-sum-ii.md) |
| [392. Is Subsequence](392-is-subsequence.md) | Two Pointers O(n) time, O(1) space | [Original code, explanation, dry run, edge cases](392-is-subsequence.md) |
| [28. Find First Occurrence](28-find-first-occurrence.md) | Two Pointers + Backtracking, Sliding Window, **KMP O(n+m) (optimal)** | [All 3 codes, LPS explanation, KMP dry runs, comparison](28-find-first-occurrence.md) |

| [160. Intersection of Two Linked Lists](#160-intersection-of-two-linked-lists--two-solutions) | Length Alignment O(m+n), Pointer Switching O(m+n) | [Both original codes, diagrams, dry runs, edge cases](#160-intersection-of-two-linked-lists--two-solutions) |

## 160. Intersection of Two Linked Lists — Two Solutions

**Problem:** https://leetcode.com/problems/intersection-of-two-linked-lists/ · **Easy** · **Solved**

Find the first **shared node reference** between two singly linked lists, or return `null`. Nodes with equal `val` are *not* necessarily the same node.

### Approach 1: Two Pointers + Length Difference (original solution)

```javascript
var getIntersectionNode = function(headA, headB) {
   let lengthA = 0;
   let lengthB = 0;

    let pA = headA;
    let pB = headB;
// calculate length of list A
   while(pA){
    pA = pA.next;
    lengthA++;
   }
// calculate length list B
   while(pB){
    pB = pB.next;
    lengthB++;
   }

   let diff  = Math.abs(lengthA - lengthB);

 // now move the long pointer to by difference
   pA = headA;
   pB = headB;
   if(lengthA > lengthB){
        for(let i = 0; i < diff ; i++){
            pA = pA.next;
        }
   }else{
        for(let i = 0; i < diff ; i++){
                pB = pB.next;
        }
   }

   // both pointer would be at same distance from intersecting node.
   while(pA){
    if(pA == pB){
        return pA;
    }
    pA = pA.next;
    pB = pB.next;
   }

   return null;
};
```

**Explanation:** Count the length of each list. Move the pointer in the longer list forward by the absolute length difference. Both pointers then have an equal number of nodes left to traverse. Advance them together; the first time their **references** match, return that node. Otherwise, return `null`.

```text
A: [4] → [1] ───────────┐
                       ↓
                      [8] → [4] → [5] → null
                       ↑
B: [5] → [6] → [1] ────┘

A length = 5; B length = 6; diff = 1.
Advance pB by one node:

pA: [4] → [1] → [8] → [4] → [5]
pB: [6] → [1] → [8] → [4] → [5]
                 ↑ both eventually reach SAME node 8
```

| Step | pA | pB | Same node reference? |
|---:|---|---|---|
| 1 | A's 4 | B's 6 | No |
| 2 | A's 1 | B's 1 | No — same value, different nodes |
| 3 | shared 8 | shared 8 | **Yes → return shared 8** |

**Time:** O(m+n). **Extra space:** O(1).

### Approach 2: Two Pointers + Pointer Switching (original solution)

```javascript
var getIntersectionNode = function(headA, headB) {

    let pA = headA;
    let pB = headB;
    while( pA != pB){
        pA = pA == null ? headB : pA.next;
        pB = pB == null ? headA : pB.next;
    }
    return pA;
}
```

**Explanation:** Start one pointer at each head. Every step, advance both pointers. When `pA` reaches `null`, redirect it to `headB`; when `pB` reaches `null`, redirect it to `headA`. Each pointer then walks a combined distance of **A+B** or **B+A**, balancing unequal list lengths without measuring them. If there is an intersection, both meet at the first shared node. If there isn't, both eventually become `null` at the same time and the loop stops.

**Pointer dry run (same example):**

| Step | pA | pB |
|---:|---|---|
| 0 | A4 | B5 |
| 1 | A1 | B6 |
| 2 | C8 | B1 |
| 3 | C4 | C8 |
| 4 | C5 | C4 |
| 5 | null | C5 |
| 6 | B5 | null |
| 7 | B6 | A4 |
| 8 | B1 | A1 |
| 9 | **C8** | **C8** |

Here `C8` is the **same shared node object**, not merely equal values.

**Time:** O(m+n). **Extra space:** O(1).

### Comparison

| Property | Length Alignment | Pointer Switching |
|---|---|---|
| Measure list lengths | Yes | No |
| Time | O(m+n) | O(m+n) |
| Extra space | O(1) | O(1) |
| Key idea | Align remaining distances | Traverse A+B and B+A |
| No intersection | Return null | Both pointers meet at null |

**Edge cases:** Empty lists; heads are already the same node; equal lengths; shared last node; same values but distinct nodes; no intersection. Use `===` instead of `==` as a JavaScript style improvement when comparing references.

**Counting:** Two approaches to LeetCode 160 count as **one** solved problem.

[← Curriculum](../README.md) · [Dashboard](../DASHBOARD.md)
