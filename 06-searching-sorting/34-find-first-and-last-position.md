# LeetCode 34 — Find First and Last Position of Element in Sorted Array

**Topic:** Binary Search · **Difficulty:** Medium · **Status:** Solved (two personal approaches)

[Problem link](https://leetcode.com/problems/find-first-and-last-position-of-element-in-sorted-array/)

## Problem

Given a sorted integer array `nums` and a `target`, return `[firstIndex, lastIndex]` of the target. If missing, return `[-1,-1]`. The algorithm must run in `O(log n)` time.

```text
index:   0  1  2  3  4  5
nums:   [5, 7, 7, 8, 8, 10]
                  ^  ^
               first last
target = 8  →  [3, 4]
```

## Approach 1 — Binary Search with Neighbor Checking (my original approach)

**Idea:** Binary-search for a target. When found, check the immediate neighbor to know whether this is the first or last occurrence. Stop immediately if the boundary is found.

This is my original idea with the necessary boundary checks and midpoint fix.

```javascript
/**
 * @param {number[]} nums
 * @param {number} target
 * @return {number[]}
 */
var searchRange = function(arr, target) {
    let ans = [-1, -1];
    let start = 0;
    let end = arr.length - 1;

    // First occurrence
    while (start <= end) {
        let mid = start + Math.floor((end - start) / 2);

        if (arr[mid] >= target) {
            if (
                arr[mid] === target &&
                (mid === 0 || arr[mid - 1] < target)
            ) {
                ans[0] = mid;
                break;
            }
            end = mid - 1;
        } else {
            start = mid + 1;
        }
    }

    // Last occurrence
    start = 0;
    end = arr.length - 1;

    while (start <= end) {
        let mid = start + Math.floor((end - start) / 2);

        if (arr[mid] <= target) {
            if (
                arr[mid] === target &&
                (mid === arr.length - 1 || arr[mid + 1] > target)
            ) {
                ans[1] = mid;
                break;
            }
            start = mid + 1;
        } else {
            end = mid - 1;
        }
    }

    return ans;
};
```

### Explanation

**First occurrence:** If `arr[mid] < target`, search right. Otherwise search left unless `arr[mid] === target` and either `mid === 0` or the previous value is smaller. That means `mid` is the **first** target.

**Last occurrence:** If `arr[mid] > target`, search left. Otherwise search right unless `arr[mid] === target` and either `mid` is the last index or the next value is greater. That means `mid` is the **last** target.

The `mid === 0` and `mid === arr.length - 1` guards are essential: JavaScript returns `undefined` for out-of-bounds array reads, and comparisons with `undefined` are false.

### Dry run — target 8

```text
nums = [5, 7, 7, 8, 8, 10]

First occurrence:
  start=0 end=5 mid=2 → nums[2]=7 < 8 → start=3
  start=3 end=5 mid=4 → nums[4]=8, previous=8 → end=3
  start=3 end=3 mid=3 → nums[3]=8, previous=7 → first=3, break

Last occurrence:
  start=0 end=5 mid=2 → nums[2]=7 < 8 → start=3
  start=3 end=5 mid=4 → nums[4]=8, next=10 → last=4, break

Answer = [3,4]
```

**Advantage:** Early exit can save iterations when the midpoint is already a boundary.  
**Tradeoff:** More conditional checks per iteration and explicit array-edge handling.

---

## Approach 2 — Boundary-Based Binary Search (my latest approach ⭐ Recommended)

**Idea:** Don't inspect neighbors. Narrow the range to the **leftmost** and **rightmost** candidate positions, then verify that the target exists.

```javascript
/**
 * @param {number[]} nums
 * @param {number} target
 * @return {number[]}
 */
var searchRange = function(arr, target) {
    let ans = [-1, -1];
    let start = 0;
    let end = arr.length - 1;

    // Find the first index (lower bound)
    while (start < end) {
        let mid = start + Math.floor((end - start) / 2);
        if (arr[mid] < target) {
            start = mid + 1;
        } else {
            end = mid;
        }
    }
    ans[0] = arr[start] == target ? start : -1;

    // Find the last index
    start = 0;
    end = arr.length - 1;

    while (start < end) {
        let mid = start + Math.ceil((end - start) / 2);
        if (arr[mid] > target) {
            end = mid - 1;
        } else {
            start = mid;
        }
    }
    ans[1] = arr[start] == target ? start : -1;

    return ans;
};
```

### Explanation

**First occurrence:** If `arr[mid] < target`, every position up to `mid` is too small, so `start = mid + 1`. Otherwise `mid` might be the first target, so keep it: `end = mid`. The loop ends at the first index whose value is `>= target` (or a candidate that fails the final equality check).

**Last occurrence:** If `arr[mid] > target`, discard `mid` and everything right: `end = mid - 1`. Otherwise `mid` might be the last target, so keep it: `start = mid`. The loop ends at the last index whose value is `<= target` (or a candidate that fails the equality check).

**Why different rounding?**

```text
First boundary: start=3, end=4
  floor midpoint = 3
  end=mid → end=3  ✓ makes progress

Last boundary: start=3, end=4
  ceil midpoint = 4
  start=mid → start=4 ✓ makes progress

If last-boundary used floor here:
  mid=3, start=mid=3 → same interval forever! ✗
```

### Dry run — target 8

| Search | start | end | mid | Observation | Update |
|---|---:|---:|---:|---|---|
| First | 0 | 5 | 2 | 7 < 8 | start=3 |
| First | 3 | 5 | 4 | 8 >= 8 | end=4 |
| First | 3 | 4 | 3 | 8 >= 8 | end=3 |
| First | 3 | 3 | — | 8 == target | first=3 |
| Last | 0 | 5 | 3 | 8 <= 8 | start=3 |
| Last | 3 | 5 | 4 | 8 <= 8 | start=4 |
| Last | 4 | 5 | 5 | 10 > 8 | end=4 |
| Last | 4 | 4 | — | 8 == target | last=4 |

**Advantage:** No neighbor indexing, concise, and teaches a reusable binary-search boundary pattern.  
**Tradeoff:** No early `break` when a boundary is already found.

---

## Which one is better?

| Criteria | Approach 1 — Neighbor Check | Approach 2 — Boundary Search |
|---|---|---|
| Worst-case time | O(log n) | O(log n) |
| Extra space | O(1) | O(1) |
| Early exit | Yes | No |
| Comparisons in an iteration | Can be more (neighbor checks) | Usually fewer |
| Best-case iterations | Can find boundary on first midpoint | Narrows range until pointers meet |
| Boundary guards | Needed | Not needed |
| Readability / reuse | Good | **Better** |

**Important nuance:** Approach 1 may be faster on some inputs because it stops immediately when it finds the boundary. Approach 2 has simpler checks and is more reusable. Same Big-O does **not** mean identical real-world comparisons or runtime. Neither is universally faster for every input.

### Edge cases

| nums | target | Answer |
|---|---:|---|
| `[]` | 8 | `[-1,-1]` |
| `[8]` | 8 | `[0,0]` |
| `[8]` | 5 | `[-1,-1]` |
| `[8,8,8,8]` | 8 | `[0,3]` |
| `[1,2,3]` | 1 | `[0,0]` |
| `[1,2,3]` | 3 | `[2,2]` |
| `[1,3,5]` | 2 | `[-1,-1]` |
| `[5,7,7,8,8,10]` | 8 | `[3,4]` |

## Complexity

Both solutions perform **two binary searches**:

- **Time:** O(log n) + O(log n) = **O(log n)**
- **Extra space:** **O(1)** (fixed two-element answer array)

## Interview revision

1. Explain why finding *any* target index is not enough when duplicates exist.
2. Explain why first-occurrence search uses `Math.floor` with `end = mid`.
3. Explain why last-occurrence search uses `Math.ceil` with `start = mid`.
4. Explain how Approach 1 achieves early exit and why it needs neighbor boundary guards.
5. Explain why the final equality check is needed in Approach 2 when the target is absent.

**My learning takeaway:** The first solution detects a boundary by looking at neighbors; the second **converges to a boundary** through pointer movement.
