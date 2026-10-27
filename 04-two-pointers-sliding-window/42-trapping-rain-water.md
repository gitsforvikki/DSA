# 42. Trapping Rain Water

**LeetCode:** https://leetcode.com/problems/trapping-rain-water/  
**Difficulty:** Hard  
**Status:** Solved — two original approaches  
**Pattern:** Prefix maximum + suffix maximum arrays

## Problem

Given nonnegative bar heights, calculate the total units of rainwater trapped between the bars. Each bar has width 1.

At index `i`, water is limited by the **shorter of the tallest bars on its left and right**:

```text
water[i] = min(maxL[i], maxR[i]) - height[i]
```

Here `maxL[i]` and `maxR[i]` **include the current bar**, so the expression is never negative for valid nonempty inputs. We can sum it across all indices.

## Approach 1 — Two separate preprocessing loops (original solution)

First build left maxima, then right maxima, then calculate trapped water.

```javascript
/**
 * @param {number[]} height
 * @return {number}
 */
var trap = function(arr) {
    let n = arr.length;
    /**
        maxL[i] denote the left maximun elem/value/bar (including its self) for the ith element in arr;
     */
    maxL = [];
    maxL[0] = arr[0];

    for(let i = 1; i < n; i++){
        maxL[i] = Math.max(arr[i] , maxL[i-1]);
    }

    /**
        maxR[i] denote the right maximun elem/value/bar (including its self) for the ith element in arr;
     */
    let maxR = [];
    maxR[n-1] = arr[n-1];

    for(let i = n-2; i >= 0; i--){
        maxR[i] = Math.max(arr[i] , maxR[i + 1]);
    }

    let totalWaterTrapped = 0;
    for(let i = 0; i < n; i++){
        let waterOnIthBar = Math.min(maxL[i] , maxR[i]) - arr[i];
        totalWaterTrapped += waterOnIthBar > 0 ? waterOnIthBar : 0;
    }

    return totalWaterTrapped;
    
};
```

**Important JavaScript correction:** The original first attempt uses `maxL = []` without declaring it. In strict mode, that throws a `ReferenceError`; otherwise it can create a global. **Change to `let maxL = [];`**. The original is preserved above for learning history; use this correction when running it.

### Walkthrough

1. Initialize `maxL[0]` to the first height. For each subsequent index, store the greater of the current height and previous left maximum.
2. Initialize `maxR[n-1]` to the last height. Move right-to-left, storing the greater of the current height and the next right maximum.
3. At each bar, calculate `min(maxL[i], maxR[i]) - arr[i]` and add it to the total.

## Approach 2 — Build both maxima in one loop (updated original solution)

Both arrays are filled during **one loop**: `maxL` from left to right, and `maxR` from right to left.

```javascript
/**
 * @param {number[]} height
 * @return {number}
 */
var trap = function(arr) {
    let n = arr.length;
    /**
        maxL[i] denote the left maximun elem/value/bar (including its self) for the ith element in arr;
     */
    let maxL = [];
    let maxR = [];

    maxL[0] = arr[0];
    maxR[n-1] = arr[n-1];

    for(let i = 1; i < n; i++){
        maxL[i] = Math.max(arr[i] , maxL[i-1]);
        maxR[n-i-1] = Math.max(arr[n-i-1] , maxR[n-i]);
    }

    /**
        maxR[i] denote the right maximun elem/value/bar (including its self) for the ith element in arr;
     */
    // let maxR = [];
    // maxR[n-1] = arr[n-1];

    // for(let i = n-2; i >= 0; i--){
    //     maxR[i] = Math.max(arr[i] , maxR[i + 1]);
    // }

    let totalWaterTrapped = 0;
    for(let i = 0; i < n; i++){
        let waterOnIthBar = Math.min(maxL[i] , maxR[i]) - arr[i];
        totalWaterTrapped += waterOnIthBar > 0 ? waterOnIthBar : 0;
    }

    return totalWaterTrapped;
    
};
```

### Understanding the reverse index

With `n = 6`:

| Loop `i` | Left index `i` | Right index `n-i-1` | Previously computed right neighbor `n-i` |
|---:|---:|---:|---:|
| 1 | 1 | 4 | 5 |
| 2 | 2 | 3 | 4 |
| 3 | 3 | 2 | 3 |
| 4 | 4 | 1 | 2 |
| 5 | 5 | 0 | 1 |

This is safe because the right neighbor has already been initialized or computed in the previous iteration.

```text
arr indices:  0   1   2   3   4   5
maxL builds: →   →   →   →   →   →
maxR builds: ←   ←   ←   ←   ←   ←

Each iteration fills one new position in each array.
```

## Complete dry run

Input: `[4,2,0,3,2,5]`

```text
index:     0  1  2  3  4  5
height:    4  2  0  3  2  5
maxL:      4  4  4  4  4  5
maxR:      5  5  5  5  5  5
water:     0  2  4  1  2  0
```

| Index | Height | Left max | Right max | Water |
|---:|---:|---:|---:|---:|
| 0 | 4 | 4 | 5 | 0 |
| 1 | 2 | 4 | 5 | 2 |
| 2 | 0 | 4 | 5 | 4 |
| 3 | 3 | 4 | 5 | 1 |
| 4 | 2 | 4 | 5 | 2 |
| 5 | 5 | 5 | 5 | 0 |
| **Total** | | | | **9** |

### Why the formula works

Water over a bar cannot be higher than the tallest available boundary on **either side**. The lower boundary determines the water level. Subtracting the bar's own height leaves only the water above it. The two arrays make this calculation O(1) per index.

## Test cases

| Input | Expected output |
|---|---:|
| `[4,2,0,3,2,5]` | 9 |
| `[0,1,0,2,1,0,1,3,2,1,2,1]` | 6 |
| `[1,2,3,4]` | 0 |
| `[3,0,3]` | 3 |
| `[0]` | 0 |
| `[]` | 0 (with optional empty-array guard) |

**Defensive improvement for both approaches:** Put `if (n === 0) return 0;` immediately after computing `n`. The original implementations assume at least one element, which matches LeetCode's constraints.

## Complexity comparison

| Property | Approach 1: separate loops | Approach 2: combined preprocessing |
|---|---|---|
| Preprocessing | 2 loops | 1 loop |
| Total array passes | 3 | 2 |
| Time | **O(n)** | **O(n)** |
| Extra space | **O(n)** | **O(n)** |
| Simplicity | More explicit | Compact indexing |

Combining loops reduces the number of loop traversals but **does not change Big-O complexity**. It is not guaranteed to be faster on every JS engine.

### Future optimization (not yet solved here)

A two-pointer method can achieve **O(n) time and O(1) extra space** using `leftMax` and `rightMax` scalar variables. This is a next challenge, **not** counted as a third solved approach.

## Personal mistake-pattern tracking

- **Improved:** The second version correctly declares `let maxL` and `let maxR`, fixing the undeclared-variable issue from the first.
- **Improved:** Reverse indices `n-i-1` and `n-i` are consistent and in range for `n >= 1`.
- **Watch next time:** Explicitly handle empty input if constraints allow it, and avoid undeclared variables.
- **Correct insight:** Both versions use the same water-level invariant; the second reorganizes preprocessing rather than changing the algorithm.

[← Two Pointers & Sliding Window practice sheet](README.md) · [Dashboard](../DASHBOARD.md)
