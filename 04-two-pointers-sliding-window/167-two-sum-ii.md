# 167. Two Sum II — Input Array Is Sorted

**Problem:** https://leetcode.com/problems/two-sum-ii-input-array-is-sorted/  
**Difficulty:** Medium  
**Status:** ✅ Solved — Binary Search and Two Pointers  
**Important:** Input is sorted in **non-decreasing order**, and the answer uses **1-based indices**. Use two different positions.

## Problem in simple English

Find two values whose sum equals `target` and return their positions (starting from 1).

Example: `numbers = [2,7,11,15]`, `target = 9` → `[1,2]`.

## Approach 1 — Binary Search (my solution)

Fix the first number at index `i`. Calculate its complement `target - numbers[i]`, then binary-search for that complement **only to the right** of `i`.

```javascript
var twoSum = function(arr, target) {
    for(let i=0 ; i < arr.length; i++){
        let remainVal = target - arr[i];
        //now i have to seach for the remainVal in remaining array

        let start = i+1;
        let end = arr.length -1;

        while(start <= end){
            let mid = start + Math.floor((end -start) / 2);
            if(remainVal == arr[mid] ){
                return [i+1 , mid+1];
            }
            else if(arr[mid] > remainVal){
                end = mid-1
            }else start = mid+1;
        }
    }
};
```

**Note:** `===` is preferred over `==` in JavaScript; both compare correctly here because the array and target contain numbers.

### Binary Search dry run

`arr = [2,7,11,15]`, `target = 9`. Fix `i=0`, value `2`, complement `7`.

| Step | start | end | mid | arr[mid] | Decision |
|---|---:|---:|---:|---:|---|
| 1 | 1 | 3 | 2 | 11 | 11 > 7 → end = 1 |
| 2 | 1 | 1 | 1 | 7 | Match → return [1,2] |

**Time: O(n log n) worst case.** Outer loop can run O(n) times, with O(log n) binary search per iteration. **Extra space: O(1)** — only a few variables.

## Approach 2 — Two Pointers (BEST / optimized)

Put `left` at the smallest value and `right` at the largest value. Calculate their sum:

- If `sum === target`: found the pair; return 1-based indices.
- If `sum > target`: move `right--` to decrease the sum.
- If `sum < target`: move `left++` to increase the sum.

```javascript
var twoSum = function(arr, target) {
    let left = 0;
    let right = arr.length - 1;

    while (left < right) {
        let sum = arr[left] + arr[right];

        if (sum === target) {
            return [left + 1, right + 1];
        }

        if (sum > target) {
            right--;
        } else {
            left++;
        }
    }
};
```

### Why should I choose Two Pointers here?

The array is already **sorted**. This gives a powerful guarantee:

- At fixed `left`, if `arr[left] + arr[right]` is **too large**, every pair using that `right` with an index between `left` and `right` is also too large or equal. So discarding `right` is safe.
- At fixed `right`, if the sum is **too small**, every pair using that `left` with an index between `left` and `right` is also too small or equal. So discarding `left` is safe.
- We eliminate one impossible boundary per iteration **without searching again**. Both pointers move only inward and never revisit discarded positions.
- We do not need a Hash Map, extra storage, or sorting; sorting again is unnecessary and would complicate original indices.

### Two Pointers dry run

`arr = [2,7,11,15]`, `target = 9`

```text
Indices:  0   1   2   3
Values:  [2,  7, 11, 15]
          L           R    2 + 15 = 17 > 9 → R--
          L       R        2 + 11 = 13 > 9 → R--
          L   R            2 +  7 =  9 → return [1,2]
```

| Step | Left value | Right value | Sum | Action |
|---|---:|---:|---:|---|
| 1 | 2 | 15 | 17 | right-- |
| 2 | 2 | 11 | 13 | right-- |
| 3 | 2 | 7 | 9 | return [1,2] |

**Time: O(n) worst case.** The gap between pointers shrinks by one on every iteration, so there are at most n−1 iterations. **Extra space: O(1)**.

## Time and space comparison

| Property | Binary Search | Two Pointers — BEST |
|---|---|---|
| Main idea | Fix a value; search complement | Adjust sum using sorted order |
| Best-case time | O(1) | O(1) |
| Worst-case time | O(n log n) | **O(n)** |
| Extra space | O(1) | **O(1)** |
| Requires sorted array | Yes | Yes |
| Number of traversals | Multiple binary searches | One inward scan |
| Recommended for this problem | Valid | **Yes — faster asymptotically** |

> **Interview takeaway:** If a pair sum is requested from an already sorted array, think about opposite-direction two pointers. Binary Search works, but Two Pointers uses the sorted order to eliminate candidates in linear time. Two Sum I on an **unsorted** array is different: Hash Map is a natural O(n)-time choice.

## Edge cases to test

| Input | target | Output |
|---|---:|---|
| `[2,7,11,15]` | 9 | `[1,2]` |
| `[2,3,4]` | 6 | `[1,3]` |
| `[-5,-2,3,8]` | 3 | `[1,4]` |
| `[0,0]` | 0 | `[1,2]` |
| `[1,2,3,4,6]` | 6 | `[2,4]` |

Both solutions rely on LeetCode's guarantee that exactly one valid pair exists.

[← Practice sheet](README.md) · [Dashboard](../DASHBOARD.md)
