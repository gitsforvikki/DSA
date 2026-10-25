# LeetCode 852 — Peak Index in a Mountain Array

**Topic:** Binary Search · **Difficulty:** Medium · **Status:** ✅ Solved

[LeetCode problem](https://leetcode.com/problems/peak-index-in-a-mountain-array/)

## Problem in simple English

A **mountain array** strictly increases up to one peak and then strictly decreases. Return the **index** of the peak (not its value).

```text
Index:    0   1   2   3   4   5
Value:    0   2   5   9   3   1
                     ^
                peak index 3

Shape:
                   9
                /     \
              5         3
            /             \
          2                 1
        /
      0
```

The problem guarantees `arr.length >= 3` and a valid mountain, so the peak is **never** at index 0 or index `n - 1`.

## My solution — Binary Search with neighbor checks

This is my original approach:

```javascript
/**
 * @param {number[]} arr
 * @return {number}
 */
var peakIndexInMountainArray = function(arr) {
    let l = 0;
    let r = arr.length - 1;

    while (l <= r) {
        let m = l + Math.floor((r - l) / 2);

        if (arr[m - 1] < arr[m] && arr[m + 1] < arr[m]) {
            return m;
        }

        if (arr[m - 1] > arr[m]) {
            r = m - 1;
        } else {
            l = m + 1;
        }
    }
};
```

### Why it works

1. Calculate the middle index `m`.
2. **Peak found:** `arr[m - 1] < arr[m]` and `arr[m + 1] < arr[m]` means `m` is bigger than both neighbors. Return `m`.
3. **Descending slope:** `arr[m - 1] > arr[m]` means we are to the right of the peak. Search left: `r = m - 1`.
4. **Ascending slope:** otherwise we are to the left of the peak. Search right: `l = m + 1`.

The mountain shape gives a **direction** even though the whole array is not sorted.

## Detailed dry run

Input: `arr = [0, 2, 5, 9, 3, 1]`

| Iteration | l | r | m | arr[m-1] | arr[m] | arr[m+1] | Decision |
|---|---:|---:|---:|---:|---:|---:|---|
| 1 | 0 | 5 | 2 | 2 | 5 | 9 | Rising → `l = 3` |
| 2 | 3 | 5 | 4 | 9 | 3 | 1 | Falling → `r = 3` |
| 3 | 3 | 3 | 3 | 5 | 9 | 3 | Peak! `return 3` |

```text
Iteration 1:
[0, 2, 5, 9, 3, 1]
       m  → →  peak must be to the right

Iteration 2:
         [9, 3, 1]
             m
         ← ← peak must be to the left

Iteration 3:
         [9]
          m
       return 3
```

## Important question — can `m` become 0 or n-1?

The code accesses `arr[m - 1]` and `arr[m + 1]`. In a *general* binary search, these could be out of bounds. But **for valid mountain arrays and this exact algorithm**, the search reaches the interior peak before it can move to either endpoint:

- The peak is guaranteed to be an interior index.
- On an increasing slope, moving right cannot skip the peak.
- On a decreasing slope, moving left cannot skip the peak.
- Once `m` equals the peak, the function returns.

So the original code is correct under LeetCode 852's constraints. For readability and explicit neighbor safety, we can instead initialize:

```javascript
let l = 1;
let r = arr.length - 2;
```

This defensive variation is **optional**, not a fix required for valid mountain arrays.

## Optional alternative — compare `m` and `m + 1`

Another common binary search keeps the peak in the interval and does not need two neighbor comparisons:

```javascript
var peakIndexInMountainArray = function(arr) {
    let l = 0;
    let r = arr.length - 1;

    while (l < r) {
        let m = l + Math.floor((r - l) / 2);

        if (arr[m] < arr[m + 1]) {
            l = m + 1;
        } else {
            r = m;
        }
    }

    return l;
};
```

This is a **reference alternative**, separate from my original solution. If the slope rises, peak is right; otherwise the peak is at `m` or left. The loop ends at the peak.

## Edge cases

| Input | Peak index | Reason |
|---|---:|---|
| `[0, 1, 0]` | 1 | Minimum valid length |
| `[0, 5, 4, 3, 2]` | 1 | Peak near the beginning |
| `[0, 1, 2, 5, 3]` | 3 | Peak near the end |
| `[0, 2, 5, 9, 3, 1]` | 3 | Typical mountain |

## Complexity

- **Time:** `O(log n)` — discard half the remaining indices each iteration.
- **Extra space:** `O(1)` — only three index variables.

## Interview takeaway

Binary search does **not** require the *entire* array to be sorted. It requires a property that tells us **which half can be safely discarded**. For mountain arrays, the slope (rising or falling) provides that property.

**Revision question:** If `arr[m - 1] > arr[m]`, why can the peak not be to the right of `m`?
